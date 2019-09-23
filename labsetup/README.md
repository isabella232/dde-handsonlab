# DDE Hands on Lab, Lab Setup

If you are following this lab within a Docker sponsored classroom environment
there is no setup required. The wonderful elves have done it for you :) 

If you are doing this lab as a lone ranger you wil need to set up a few things.

1) Please make sure you have Docker Desktop Enterprise setup and configured on
   your environment. For more details see
   https://docs.docker.com/docker-for-windows/install/ or
   https://docs.docker.com/ee/desktop/admin/install/windows/. 

2) For the best experience with this lab it is advised to a have a local copy of
   all of the guides. Therefore we advise users to `clone` this repository
   locally and then work through it.

   ```bash
   git clone https://github.com/ollypom/dde-handsonlab
   ```

3) Applicaion Designer Library 

   The Docker Desktop Enterprise Application Designer by is configured with a
   stock library of Application templates. You will need to add an additional
   custom library for this hands on lab.

   The library configuration file is located at:

   * `/home/<user>/.docker/application-template/preferences.yaml`
   * `C:\Users\<user>\.docker\application-template\preferences.yaml`

   This file should look like:

   ```yaml
   apiVersion: v1alpha1
   disableFeedback: true
   kind: Preferences
   repositories:
   - name: custom-services
     url: https://raw.githubusercontent.com/ollypom/dde-handsonlab/master/labsetup/appdesignertemplates/library.yaml
   - name: library
     url: https://docker-application-template.s3.amazonaws.com/production/v0.1.5/library.yaml
   ```

4) Your good to go. Enjoy. Please submit issues / PRs if you find ways to
   improve the lab :) 