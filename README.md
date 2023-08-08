# DemoAmi
The purpose of this AMI is to have apache in it and started
# main.pkr.hcl
In this file you need to configure you're data source image which in this case would be "name = "base-image" change this to you're own base image or just get a start up amazon linux image and use it as a base image, another thing you need to know is the commit_id variable this will automatically be set through the jenkinsfile, when you have your web-hook trigger set up, everytime you push you're code jenkinsfile will grab the commit id for your build and run the packer build by changing the variable "commit_id" to your actually commit id
# Jenkinsfile
AMI_NAME and COMMIT_ID is automatically set to null, the values for these will be set later on.
the function "/demo_ami\.([^\n]+)/" is to manipulate strings so first it'll find the literal text "demo_ami" than everything that comes after it that isnt on a new line, if you do decide to change the main.pkr.hcl's base image you have to change "/demo_ami\.([^\n]+)/" along with it
- post
defines one or more additional steps that are run after completetion of the pipeline or stage run's the conditions that post has in total are "always, changed, fixed, regression, aborted, failure, success, unstable, unsuccessful, and cleanup."
but in this i've used success and failure
- build job
with build job you can trigger another pipeline and furthure more pass down things from your current pipeline to the next pipeline that your going to trigger
