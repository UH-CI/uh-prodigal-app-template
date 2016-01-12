This is a AGAVE template for a UH HPC Prodigal application. 

Getting Setup
To get started with using AGAVE via the command line you will need the command line tools (CLI). You can download them with Git this way:

>git clone https://bitbucket.org/taccaci/foundation-cli
You can either add the foundation-cli "bin" folder to your PATH or call the commands with their absolute directory path - whatever your preferences.

Now that you have the CLI tools you can also need AGAVE credentials. At this time UH does not have our own authorization for AGAVE so you need to go to iPlant and create an account which will then provide you the AGAVE account you need to move forward. Go to the iPlant user account registration page here - https://user.iplantcollaborative.org/register/ and create and account.

Now that you have the AGAVE account (via iPlant) you can move into actually using AGAVE.

First you need to create an API application instance to work within. Run the followoing AGAVE CLI command:

> clients-create -S -v -N my_cli_app -D "Client app used for scripting up cool stuff" 

The command will ask you for an API Username and Password - these are the iPlant username and password you just created (or already had).

Now that you have an application API instance, you need to fetch an API-token that will authorize the rest of this session (these tokens do expire so you need to do this whenever you want to start a session)

> auth-tokens-create -S -v

You now have an authentication token stored on your computer so you can now use the AGAVE apis in your current application context.

Now you can register storage and execution systems related to the UH HPC with your account so you can use them with this application.  We have provide JSON files that define the systems this template application uses - you simply need to update the USERNAME and PASSWORD credentials and your directory username to use them. Then you add them to your AGAVE environment as follows (You only need to to this once for you user and you can then utilize these systems for other apps in the future)

For the UH HPC home directory file system:

>systems-addupdate -v -F sftp.storage.uhhpc1.its.hawaii.edu.json

For the UH HPC Lustre Scratch filesystem:

>systems-addupdate -v -F sftp.lustre.storage.uhhpc1.its.hawaii.edu.json

For the UH HPC Sandbox partition execution system:

>systems-addupdate -v -F sb.execute.uhhpc1.its.hawaii.edu.json

Now we need to move the AGAVE application file to the UH HPC.

First create the apps directory on your Lustre scratch space:
>files-mkdir -N apps/uh-prodigal-2.6.2 -S sftp.lustre.storage.uhhpc1.its.hawaii.edu

Move the wrapper.sh file to Lustre:
>files-upload -F wrapper.sh -S sftp.lustre.storage.uhhpc1.its.hawaii.edu apps/uh-prodigal-2.6.2

Move the test folder contents to Lustre:
>files-upload -F test -S sftp.lustre.storage.uhhpc1.its.hawaii.edu apps/uh-prodigal-2.6.2

Now you can login to the UH HPC.

Create file in your ~/lus directory called input.prodigal it can be blank for the test if you just want to be sure prodigal launches. Be sure to specify a real prodigal input file for the default value - the current value will be a input.prodigal file in you ~/lus directory if you put your prodigal file there and name it "input.prodigal" this app will use it.

 Now navigate to your ~/lus/apps/uh-prodigal-2.6.2/test folder and run the test.sh script to be sure it runs - if should create a input and output folder and copy the prodigal input file to the input folder and write the standard out  to a file in the output folder.  Please do this in an interactive sandbox session vs on the login nodes.


 If the test worked you are good to register the app to your AGAVE account.

 >apps-addupdate -F app.json

 Now you can create a submit template - or use the provide one which is submit.json (be sure to modify the USERNAME and email address for notifications)

 >jobs-template -A uh-prodigal-2.6.2 > mysubmit.json

 Now you can submit the job to the HPC using AGAVE - you may need to add the authorization section from the submit.json file with your USERNAME.

 >jobs-submit -W -F submit.json

 OR
 >jobs-submit -W -F mysubmit.json

 AGAVE will take care of creating a SLURM batch script with the appropriate setting and running it in the sandbox partition on the cluster.  The results should be written to your scratch space in an "archive" folder.  You should get an email notification when the job Finishes.  


