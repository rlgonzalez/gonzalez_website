# The Gonzalez Lab Site

This repository contains all of the HTML, CSS, and Javascript code for the Gonzalez Lab website, as well as all of the images.

Due to CUNIX space constraints, the pdfs are NOT included, and any PDFs that you'd like to add to the website must be uploaded manually via SFTP software like Cyberduck or WinSCP.

This README will go over how to make changes to and deploy to the site.

## Setting up your system 

Please note that this section assumes you have write access to the /www/data/cu/chemistry/groups/gonzalez/.

This guide is written for users on a Mac or Unix system but you can also work with Windows as long as you know what you're doing with git and if you have SSH software like puTTY.

First, go to the directory where you'd like the gonzalez-site to live. For example
	
	cd ~
	
Next, clone the Github repository by executing:

	git clone https://github.com/GAdamp/gonzalez-site.git

You will be prompted for your Github username and password. 

After the clone is completed, you will see a gonzalez-site directory wherever you cloned the repo. This is where all of the code lives. This is the code you will be changing if you need to make site changes.

Now you need to set up a bare git repository in your CUNIX personal folder to act as a target for changes you push to the actual website.

Using an SSH client, connect to CUNIX with your UNI. On Mac or a UNIX system, you can do this with:

	ssh <your_uni>@cunix.columbia.edu
	
After typing in your UNI and password, run `cd ~` to ensure that you are in your home directory.

Now, create a bare git repository for the gonzalez site as follows:

	mkdir gonzalez-deploy.git
	cd gonzalez-deploy.git
	git init --bare
	
This will create an empty git repository that you can push to from your actual system.

Now the fun part, we must create a git "hook" that will execute whenever this repository receives data.

	cd hooks
	<your_favorite_editor> post-receive
	
In the post-receive hook, add the following two lines of code:

	#!/bin/sh
	GIT_WORK_TREE=/www/data/cu/chemistry/groups/gonzalez/ git checkout -f
	
This will update the webserver any time you push to this repository without revealing any of the git metadata to the web. To make this work, make sure the post-receive script is executable with this command:

	chmod +x post-receive
	
As long as you've set everything up correctly, you can exit CUNIX, and you shouldn't need to SSH back into CUNIX again after this setup process.

Once you're out of CUNIX and back in your local git repository, set up the remote target with the following command:

	git remote add web <your_uni>@cunix.columbia.edu:~/gonzalez-deploy.git
	
Now you should be all set! You'll be able to make changes to the site following the rest of the guides in this README. Just make sure that you ***ALWAYS*** test your changes locally before pushing them to the web.


## Pushing Changes

***MAKE SURE YOU'VE TESTED ALL OF YOUR CHANGES LOCALLY BEFORE PUSHING!!!*** 

***DO NOT COMMIT ANY LARGE FILES SUCH AS PDFs TO THE REPO, YOU WILL RUN OUT OF SPACE IN YOUR CUNIX HOME DIRECTORY. THOSE FILES MUST BE UPLOADED TO THE SERVER MANUALLY***

First, make sure that all of your changes have been committed in your local git repository.

If you don't know how to commit changes in a git repository, this [page](http://git-scm.com/book/en/Git-Basics-Recording-Changes-to-the-Repository) gives a good overview. I highly recommened that you read at least that page before making any changes to the site. Essentially you'll need to `git add` all of the files you've made changes to, you'll need to `git status` to make sure you're committing the correct things, and you'll need to `git commit` and provide an informative commit message.

Once your changes are committed, you should first push to Github. This is important because we always want everyone with access to the site to be working with the same version of the code. This command will push your code changes to the Github repository. 

	git push origin
	
***It's very important that this step succeeds before you push to the web.*** If this step did not succeed, make sure you fix the problem before proceeding with pushing to the web. The most common reasons for this to fail are incorrect Github username/password or your local repository not being up to date with all of the changes in the Github repository. 

Once you've fixed any issues and you're absolutely certain that your changes have been pushed to the Github repository (I would double check to be sure), you're ready to push changes to the web.

If it's the first time you're doing this, execute the following command, which might take a while:

	git push web +master:refs/heads/master
	
Otherwise, you can just execute
	
	git push web
	
Both commands will prompt you for your UNI password
	
If this succeeds, it means your changes were successfully pushed to CUNIX and the webserver.

If the command failed, it means your changes weren't successfully pushed and there's a problem that needs to be fixed. Usually it's simple, but if it's because your CUNIX directory ran out of space it means that someone will have to fix the repository.

Now just go to the webpage to make sure your changes were made and you didn't break the site.

## Using SFTP software to manually upload files

If you need to manually upload files (such as PDFs) that can't go in the git repository, or if `git push web` isn't working and you need to upload a quick fix, you need to use a piece of SFTP software such as Cyberduck or WinSCP.

In Cyberduck, you can connect to the webserver with the "Open Connection" button and specifying the following options:

	At the top: SFTP(SSH File Transfer Protocol)
	Server: cunix.columbia.edu
	Username: <your_uni>
	Password: <your_uni_password>
	
And in more options:
	
	Path: /www/data/cu/chemistry/groups/gonzalez/
	
In the directory that appears you can drag and drop files and they will instantly update the live site.

Just be aware that this is not linked at all to Git and changes made here will not be saved in any revision control. Any files that you add via this method can be overwritten by Git (but you don't need to worry about files, like PDFs, that won't be tracked in Git)


	




	

