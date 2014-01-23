xBrowse: Laptop Edition
=======================

This repo provides a quick installer for developers to begin contributing to [xBrowse](https://atgu.mgh.harvard.edu/xbrowse). 
Deploying an xBrowse instance in the wild is pretty involved, 
so this repository provides a "getting started" deployment with minimal configuration, smaller reference datasets, etc. 

Note that this installation should **not** be used to deploy xBrowse on your own production servers - that is still in progress. 

## How It Works 

This repository provides code for installing up a lightweight instance 
on your laptop, within a Vagrant virtual machine. 

xBrowse is run entirely in this virtual machine - the "guest" machine - 
but you can visit this version of the xBrowse website on your laptop's web browser. 

The xBrowse application code is mounted on a "shared" directory - meaning it is accessible by both your laptop and the VM. 
So, you follow the standard development workflow: edit the code, refresh your browser, and check out the changes. 
However, no code is actually executed on your laptop - it all happens within the VM. 

## Code Organization

The xBrowse application code is actually separated into two repositories: 

- [xbrowse](https://github.com/xbrowse/xbrowse) contains the analysis code

- [xbrowse-web](https://github.com/xbrowse/xbrowse-web) contains web server code. 

The goal of two repositories was to enforce a strict separation between the science code and the web server code, 
both for organization and to ensure that xBrowse can be run fully on the command line. 
Unfortunately we've been undisciplined, and xbrowse is currently pretty useless without an xbrowse-web installation. 
Command line usage is still on the roadmap, though. 

This repository, [xbrowse-laptop](https://github.com/xbrowse/xbrowse-laptop), will probably be merged into xbrowse-web at some point. 
It is separate for now, because we can't yet commit to dev / prod parity in our deployment code (due to the infrastructure that xBrowse is currently forced to run on).

## Setting up 

The only prerequisite is that you have [Vagrant](http://vagrantup.com) installed. 
Installation is pretty straightforward on Mac. Make sure the vagrant command line utility is on your PATH: 

	$ vagrant -v
	Vagrant 1.4.2

After Vagrant is installed, clone the repository: 

	git clone https://github.com/xbrowse/xbrowse-laptop
	cd xbrowse-laptop

Consider `xbrowse-laptop` the "working" directory for this installation - 
it will be mounted to the VM as a shared directory. 
It's important to get the file paths right, as they are hardcoded within the VM. 

Before you build anything, download and extract a tarball of all the data needed for this deployment. 
(It's 3.8GB, so may take a while...)

	wget ftp://atguftp.mgh.harvard.edu/xbrowse-laptop-downloads.tar.gz
	tar -xzf xbrowse-laptop-downloads.tar.gz

Now it's time to initialize the VM (this will take ~3 hours). 
Run the following command: 

	vagrant up

This command does the following: 

- Creates and provisions a virtual machine that can run xBrowse

- Downloads the xBrowse source code into a shared directory

- Creates and loads an xBrowse project with 1000 genomes samples. 

If curious, all of these steps are contained in `bootstrap.sh`. 
(Indeed, we'll need a much better way to organize these steps, but this was easiest for now.)

### Running xBrowse

When `vagrant up` finishes, visit `http://localhost:8000` in your web browser - you should see the familiar homepage! 

Log in with username `admin` and password `admin` - and you should see a single project *1000 Genomes Test*. 

## Development

See *code organization* above - the application code is located in two directories, `code/xbrowse` and `code/xbrowse-web`. 
Note that these are not git submodules - they are included in .gitignore and are totally separate repositories. 

## Troubleshooting

- Note that the provsioning process is unfortunatley not idempotent. (Does idempotent have an antonym?)
If the provisioning process breaks, it's  best to destroy and rebuild the virutal machine. (`vagrant destroy` then `vagrant up` again) 
Of course, if the error recurs, open an issue. 

- The most common error after provisioning occurs (nondeterministically) when you lose an internet connection - sometimes the guest machine will lose a network connection too that can't be repaired. If this happens, run `vagrant reload` - this restarts the VM. 

- This development version deos need an internet connection (though this is an open issue)
