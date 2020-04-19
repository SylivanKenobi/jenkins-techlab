Lab 3: Build Options/Properties
===============================

When using multibranch pipelines you can no longer configure build options (declarative syntax)
or build properties (scripted syntax) through the web interface. For single branch ``Jenkinsfile``
pipelines configuring build options/properties in the ``Jenkinsfile`` is recommended as well in
order to obtain a single source of truth <https://en.wikipedia.org/wiki/Single_source_of_truth>.
This lab shows how this is done.

Lab 3.1: Build Options
----------------------

In declarative pipelines build options/properties are configured through the ``options`` directive.
Only a single ``options`` directive is allowed and must be contained in the ``pipeline`` block.
Create a new branch named ``lab-3.1`` from branch ``lab-2.1`` and change the contents of the ``Jenkinsfile`` to:

```groovy
pipeline {
    agent any
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
        timeout(time: 10, unit: 'MINUTES')
        timestamps()  // Timestamper Plugin
        disableConcurrentBuilds()
    }
    stages {
        stage('Greeting') {
            steps {
                echo 'Hello, World!'
            }
        }
    }
}
```

This pipeline is configured to:
* Keep a maximum of **5** builds
* Timeout builds that run longer than **10 minutes**
* Print timestamps before each build log line
    * Uses the Timestamper Jenkins Plugin <https://wiki.jenkins.io/display/JENKINS/Timestamper>
* Disallow concurrent executions of the Pipeline. Can be useful for preventing multiple simultaneously builds for one ~~feature~~ branch. However locks and milestones are the preferred solution for this because newer builds are allowed to supersede older ones. This is especially important for longer builds and builds requesting user input. Also see [Lab 13](13_stages_locks_milestones.md).
?disableConcurrentBuilds() only prevents builds form the same git ref?

**Note:** The timeout option isn't shown in the configuration on the Jenkins master

Lab 3.2: Build Properties (Scripted Syntax)
-------------------------------------------

In scripted pipelines build options/properties are configured through the ``properties`` step.
The ``properties`` step replaces any existing build properties with the list given,
therefore only a single instance should be used per pipeline.
Create a new branch named ``lab-3.2`` from branch ``lab-2.2`` and change the contents of the ``Jenkinsfile`` to:

```groovy
properties([
  buildDiscarder(logRotator(numToKeepStr: '5')),
  disableConcurrentBuilds()
])

timestamps() {
    timeout(time: 10, unit: 'MINUTES') {
        node {
            stage('Greeting') {
                echo 'Hello, World!'
            }
        }
    }
}
```

This configures the same job properties as the declarative pipeline in [Lab 3.1](#lab-31-build-options).

In scripted pipelines job properties are configured through steps. The usage of a step depends on its implementation. ?configured through steps - what does this mean?
Use the "Snippet Generator" ("Pipeline Syntax" in the sidebar) to find the correct syntax.
Scripted pipelines need to run before Jenkins can pick up changes in build properties. ?do you mean the update in the gui only happens after the run - but the already apply to the run?
Multibranch pipeline support takes care of this and runs any new jobs and any jobs with changes in their ``Jenkinsfile``.

---

**End of Lab 3**

<p width="100px" align="right"><a href="04_build_triggers.md">Build Trigger configuration →</a></p>

[← back to overview](../README.md)
