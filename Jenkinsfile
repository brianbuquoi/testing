  podTemplate(
    {
    containers: [
      containerTemplate(
        name: 'jnlp',
        image: 'jenkinsci/jnlp-slave:3.10-1-alpine',
        args: '${computer.jnlpmac} ${computer.name}'
      ),
      containerTemplate(
        name: 'build',
        image: 'docker',
        command: 'cat',
        ttyEnabled: true
      ),
    ]
    volumes: [
      hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'), 
    ]
    }
  )    
  node {

    stage ('Build image') {
      container('build') {
        sh """
        mkdir myproj
        cd myproj
        echo 'FROM alpine:3.7' > Dockerfile
        docker build -t myalpine:latest .
        """
      }
    }

    stage ('Prisma Cloud scan') {
      twistlockScan ca: '',
                    cert: '',
                    compliancePolicy: 'critical',
                    dockerAddress: 'unix:///var/run/docker.sock',
                    gracePeriodDays: 0,
                    ignoreImageBuildTime: true,
                    image: 'myalpine:latest',
                    key: '',
                    logLevel: 'true',
                    policy: 'warn',
                    requirePackageUpdate: false,
                    timeout: 10
    }

    stage ('Prisma Cloud publish') {
      twistlockPublish ca: '',
                       cert: '',
                       dockerAddress: 'unix:///var/run/docker.sock',
                       ignoreImageBuildTime: true,
                       image: 'myalpine:latest',
                       key: '',
                       logLevel: 'true',
                       timeout: 10
    }

  }
