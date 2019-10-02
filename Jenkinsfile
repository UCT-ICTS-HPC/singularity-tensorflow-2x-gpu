// Defining Pipeline 
env.SW_LOCATION = '/opt/exp_soft/singularity-containers/tensorflow'
env.CONTAINER_NAME = 'tensorflow-2x-gpu.sif'
node('p100') {

    stage ('Checkout code') {checkout scm}
         
    stage('Build') {
       if (fileExists('container')) {
             echo 'Directory exists'
             } else {
             sh "mkdir container" 
           }
       // Running with --notest as 'singularity build ' does not feature the --nv for GPU and executes %test scriptlet during the build.
       sh "sudo /usr/bin/singularity build --notest container/$CONTAINER_NAME-$BUILD_NUMBER tensorflow-gpu-build.def"
     }

    stage('Container Cleanup') {
       // Cleaning up unwanted files from the container.
       sh "/usr/bin/singularity cache clean --name container/$CONTAINER_NAME-$BUILD_NUMBER"
     }

    stage('Running Tests') {
       // Execute the %test scriptlet.
       sh "/usr/bin/singularity test --nv container/$CONTAINER_NAME-$BUILD_NUMBER "
     }

    stage('Delivery to HPC Software Repository') {
      // Make application available to HPC users 
      sh "cp container/$CONTAINER_NAME-$BUILD_NUMBER $SW_LOCATION"
      dir('/opt/exp_soft/singularity-containers/tensorflow') {
         sh "ln -sf $SW_LOCATION/$CONTAINER_NAME-$BUILD_NUMBER tensorflow-2x-gpu.sif-latest"
      }
    }   
}
