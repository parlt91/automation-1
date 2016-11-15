This repository contains various scripts which SUSE uses to automate
development, testing, and CI (continuous integration) of the various
components of SUSE Cloud, i.e. OpenStack and Crowbar.

# Developer Environments:

* For using the automation repository for development, testing and training please refer
  the mkcloudruns folder in this repository. Check the readme in there for more details.

# Scripts

This project has several scripts for different automated tasks, some of them are:

* create-vm.sh, creates a fresh KVM VM via libvirt.
* crowbar-prep.sh, prepare a host for Crowbar admin node installation.
* [`/scripts/mkcloud`](docs/mkcloud.md), script used to build a SUSE Cloud environment
  for development or testing purposes.
* repochecker, try to solve runtime dependencies for a given repository

# Documentation

Find out more about configuration/usage

* [`/scripts/mkcloud`](docs/mkcloud.md)

# License

Files in this repository are licensed under the Apache 2.0 license,
unless stated otherwise. See [the LICENSE file](LICENSE) for details.

# Contributing

This project uses the pull requests to process contributions,
[travis-ci](http://travis-ci.org/) to test that your changes are OK to be
merged.

It's recommended to read
[Contributing to Open Source on GitHub](https://guides.github.com/activities/contributing-to-open-source)
and [Forking Projects](https://guides.github.com/activities/forking) if you
want to get a better understanding of how GitHub pull requests work.

## Testing your changes

The syntax of the shell scripts is checked using
[bashate](https://github.com/openstack-dev/bashate), you can install it running.

```
$ sudo pip install bashate
```

Once you have installed bashate and the changes you wanted, you should check the
syntax of the shell scripts running `make test`, here is an example output of
a successful execution:

```
$ make test
cd scripts ; for f in *.sh mkcloud mkchroot jenkins/{update_automation,*.sh} ; do echo "checking $f" ; bash -n $f || exit 3 ; bash8 --ignore E010,E020 $f || exit 4 ; done
checking compare-crowbar-upstream.sh
checking create-vm.sh
checking crowbar-prep.sh
checking mkcloud-crowbar-logs.sh
checking qa_crowbarsetup.sh
checking setenv.2.sh
checking setenv.sh
checking mkcloud
checking mkchroot
checking jenkins/update_automation
checking jenkins/qa_openstack.sh
checking jenkins/qa_tripleo.sh
checking jenkins/track-upstream-and-package.sh
checking jenkins/update_tempest.sh
cd scripts ; for f in *.pl jenkins/{apicheck,jenkins-job-trigger,*.pl} ; do perl -c $f || exit 2 ; done
analyse-py-module-deps.pl syntax OK
jenkins/apicheck syntax OK
jenkins/jenkins-job-trigger syntax OK
jenkins/cloud-trackupstream-matrix.pl syntax OK
jenkins/jenkinsnotify.pl syntax OK
jenkins/openstack-unittest-testconfig.pl syntax OK
jenkins/track-upstream-and-package.pl syntax OK
```

# jenkins jobs
There are manually maintained jobs and some jobs are now using
[jenkins-job-builder](http://docs.openstack.org/infra/jenkins-job-builder/)
which defines jobs in yaml format. New jobs should always be defined
in yaml format.

The jenkins-job-builder jobs are deployed automatically (once per day) via the jenkins
job cloud-update-ci.

They can also be deployed manually via Makefile targets
```
make cisd_deploy # deploys ci.suse.de jobs
make cioo_deploy # deploys ci.opensuse.org jobs
```
This requires to setup jenkins job builder locally:

* Install SUSE CA certificate package as described in http://ca.suse.de/gettingstarted/
* Install jenkins-job-builder:
```
zypper in python-jenkins-job-builder
```
* Get the APIKEY from the CI web UI (Profile / Configure / Show API Key)
* Create jenkins_jobs.ini as described below

Both Makefile targets need a valid `jenkins_jobs` ini file.
* `make cisd_deploy` looks for /etc/jenkins_jobs/jenkins_jobs-cisd.ini
* `make cioo_deploy` looks for /etc/jenkins_jobs/jenkins_jobs-cioo.ini

See [`/scripts/jenkins/jenkins_jobs.ini.sample`](scripts/jenkins/jenkins_jobs.ini.sample)

To update a single job on ci.suse.de, run:
```
jenkins-jobs --ignore-cache update \
    jenkins/ci.suse.de/:jenkins/ci.suse.de/templates/ <name-of-one-job>
```

For this you need a local ini file and add it via the `--conf` parameter to
the above command.
