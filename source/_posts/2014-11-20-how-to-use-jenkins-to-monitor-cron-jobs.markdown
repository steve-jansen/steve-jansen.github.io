---
layout: post
title: "How to use Jenkins to monitor cron jobs"
date: 2014-11-20 17:46:46 -0500
comments: true
categories: 
- devops
- jenkins
- cron
---

Cron jobs have a funny way of being ignored.  Either no one knows the job is failing because the job
doesn't tell anyone.   Or, the job is spamming your e-mail inbox many times a day, regardless of success
or failure, which means you just ignore the e-mails.

I've seen the "Monitor an external job" option for new Jenkins jobs before, and never paid much attention.
Turns out it's a great bucket for storing logs and results of cron jobs.

The [external-monitor-job](https://wiki.jenkins-ci.org/display/JENKINS/Monitoring+external+jobs) plugin
seems to ship with the Jenkins war file.  So, your Jenkins should have it out of the box.

Creating a job is pretty simple.  It's just a name and description.  Click "New Item" in Jenkins and
select the "Monitor an external job" option.  This creates a job of type `hudson.model.ExternalJob`.

The [wiki](https://wiki.jenkins-ci.org/display/JENKINS/Monitoring+external+jobs) describes a
fairly complicated method to download the Jenkins jar files onto the server running
your cron jobs, and then use the Java runtime to run a jar with your cron script as an
argument.  The jar presumably forks your a new shell to run your desired cron command and
sends the output/result to Jenkins.

There's a much easier way to do this.  Redirect or `tee` your job's stdout/stderr output to a 
temp file.  Then post the result code and log file via `curl` to Jenkins.  No need to
download jar files.  No need to even have Java runtime on the server.

Just POST a small XML document with the log contents (binary encoded) and the
exit code to Jenkins @ `/job/:jobName/postBuildResult` where `:jobName` is the
URL encoded name of your monitoring job in Jenkins.


``` bash [example cron script]
#!/bin/sh
# example cron script to post logs to Jenkins

# exit on error
set -e

log=`mktemp -t tmp`
timer=`date +"%s"`
jenkins_job=my_monitoring_job
jenkins_server=http://jenkins.example.com:8080/jenkins/job/$jenkins_job/postBuildResult
# see http://jenkins.example.com:8080/me/configure to get your username and API token
jenkins_username=myusername
jenkins_token=abcdef0123456789fedcba9876543210

function banner() {
  echo $(printf '#%.0s' {1..80}) >> "$log"
}

function report() {
  result=$?
  timer=$((`date +"%s"` - $timer))

  banner
  echo "`whoami`@`hostname -f` `date`: elapsed $timer second(s)" >> "$log"
  echo "exit code $result" >> "$log"

  # binary encode the log file for Jenkins
  msg=`cat "$log" | hexdump -v -e '1/1 "%02x"'`

  # post the log to jenkins
  echo curl -X POST \
       -u "$jenkins_username:$jenkins_token" \
       -d "<run><log encoding=\"hexBinary\">$msg</log><result>$result</result><duration>$timer</duration></run>" \
        $jenkins_server/job/$jenkins_job/postBuildResult
}

trap report EXIT;

banner
echo "hello, world @ `date`!" | tee "$log"
```

``` bash [sample `crontab -e` entry]
MAILTO=""
0 * * * * /bin/sh /your/directory/myjob.sh
```

A sample of the build log on Jenkins with a green/red build status:

![Sample Jenkins Build Log](images/2014-11-20.png)

Credit to [Taytay on Stackoverflow.com](http://stackoverflow.com/a/25611940/1995977)
for figuring out how to use `hexdump` to properly encode the XML for Jenkins.

