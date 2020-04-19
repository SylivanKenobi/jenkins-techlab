Lab 7: Build Parameters
=======================

Jobs can be parametrized with user provided input, e.g. to select a deployment target.
When such a job gets started through the web interface a form is presented to the user
where he can provide the necessary arguments.

?Remove or add a not to lab 5.3 about the parameters, otherwise it is confusing?

Lab 7.1: Build Parameters (Declarative Syntax)
-----------------------------------------------

In declarative pipelines parameters are declared with the ``parameters`` section.
Create a new branch named ``lab-7.1`` from branch ``lab-2.1`` and change the contents of the ``Jenkinsfile`` to:

```groovy
pipeline {
    agent any
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
        timeout(time: 10, unit: 'MINUTES')
        timestamps()  // Requires the "Timestamper Plugin"
    }
    triggers {
        pollSCM('H/5 * * * *')
    }
    parameters {
        string(name: 'Greetings_to', defaultValue: 'Jenkins Techlab', description: 'Who to greet?')
    }
    stages {
        stage('Greeting') {
            steps {
                echo "Hello, ${params.Greetings_to}!"
            }
        }
    }
}
```

Helper methods like ``string`` or ``booleanParam`` are used to declare the parameter types.
The corresponding documentation should soon be available here: <https://jenkins.io/doc/book/pipeline/syntax/#parameters>.
There is an open issue for the parameter documentation: [INFRA-1053](https://issues.jenkins-ci.org/browse/INFRA-1053).
In the meantime use the snippet generator like described in the next lab to see all available types.

**Note:** Use the "Build with Parameters" action on Jenkins master and change the greetings value. The build log output will show the changed greeting.

Lab 7.2: Build Parameters (Scripted Syntax)
===========================================

In scripted pipelines build parameters are configured as part of the ``properties`` step.
Create a new branch named ``lab-7.2`` from branch ``lab-2.1`` and change the contents of the ``Jenkinsfile`` to:

```groovy
properties([
    buildDiscarder(logRotator(numToKeepStr: '5')),
    pipelineTriggers([
        pollSCM('H/5 * * * *')
    ]),
    parameters([string(defaultValue: 'Jenkins Techlab', description: 'Who to greet?', name: 'Greetings_to')])
])

timestamps() {
    timeout(time: 10, unit: 'MINUTES') {
        node {
            stage('Greeting') {
                echo 'Hello, ' + params.Greetings_to + '!'
            }
        }
    }
}
```

?Why are we introducing this '+' string concatenation syntax now? Move it to lab 5?

Parameter types are declared through the same helper methods as in declarative pipelines.
Use the snippet generator for the ``properties`` step, option "This build is parametrized" to see
all available types.

**Note:** Use the "Build with Parameters" action on Jenkins master and change the greetings value. The build log output will show the changed greeting.

---

**End of Lab 7**

<p width="100px" align="right"><a href="08_tools.md">Tools →</a></p>

[← back to overview](../README.md)
