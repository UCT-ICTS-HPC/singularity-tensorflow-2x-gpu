// Defining Pipeline 
node('p100') {
    stage ('Checkout code') {checkout scm}
         
    stage('Build') {
       if (fileExists('container')) {
             echo 'Directory exists'
             } else {
             sh "mkdir container" 
           }
       // Running with --notest as 'singularity build ' does not feature the --nv for GPU and executes %test scriptlet during the build.
       sh "sudo /usr/bin/singularity build --notest container/$BUILD_DISPLAY_NAME-tensorflow.sif tensorflow-gpu-build.def"
     }

    stage('Container Cleanup') {
       // Cleaning up unwanted files from the container.
       sh "/usr/bin/singularity cache clean --name container/$BUILD_DISPLAY_NAME-tensorflow.sif "
     }

    stage('Running Tests') {
       // Execute the %test scriptlet.
       sh "/usr/bin/singularity test --nv container/$BUILD_DISPLAY_NAME-tensorflow.sif "
     }

    stage('Delivery to HPC Software Repository') {
      // Make application available to HPC users 
    }
       
}
