# vagrant-first-run

## What does it do?
This is a very simple script that will read your Vagrantfile, determine how many Virtual Box machines are in it and will initialise the machines that are not yet created while simultaneously fixng the permissions errors that occur when Vagrant creates new keys.

## How does it do this?
The script runs `vagrant up` on each machine listed in the Vagrantfile. This is a standard startup and the initialisation will fail in the same way as normal. Once it has failed, the script then fixes the permissions and reloads the VM, allowing provisioning to continue from where it normally fails.

## Why is it needed?
Security updates on a number of OS's have recently prevented `vagrant ssh` from using the SSH keys that Vagrant auto-generates when it builds a VM for the first time. This also prevents the *vagrant* folder from being synched between the host and the VM.

## Who is it for?
People who use Vagrant on a system where `vagrant up` tends to generate errors like this:

```
==> vm1: Rsyncing folder: /path/to/my/vagrant/vms/ => /vagrant
There was an error when attempting to rsync a synced folder.
Please inspect the error message below for more info.

Host path: /path/to/my/vagrant/vms/
Guest path: /vagrant
Command: "rsync" "--verbose" "--archive" "--delete" "-z" "--copy-links" "--no-owner" "--no-group" "--rsync-path" "sudo rsync" "-e" "ssh -p 2200 -o LogLevel=FATAL  -o ControlMaster=auto -o ControlPath=/tmp/ssh.850 -o ControlPersist=10m  -o IdentitiesOnly=yes -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -i '/path/to/my/vagrant/vms/.vagrant/machines/vm1/virtualbox/private_key'" "--exclude" ".vagrant/" "/path/to/my/vagrant/vms/" "vagrant@127.0.0.1:/vagrant"
Error: Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
rsync: connection unexpectedly closed (0 bytes received so far) [sender]
rsync error: unexplained error (code 255) at io.c(226) [sender=3.1.1]
```

and `vagrant ssh -- -vvv` tends to generate errors such as

```
debug1: Trying private key: /path/to/my/vagrant/vms/.vagrant/machines/vm1/virtualbox/private_key
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0644 for '/path/to/my/vagrant/vms/.vagrant/machines/vm1/virtualbox/private_key' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "/path/to/my/vagrant/vms/.vagrant/machines/vm1/virtualbox/private_key": bad permissions
debug2: we did not send a packet, disable method
debug1: No more authentication methods to try.
Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
```

## How do I use it?
This script only needs to be used when Vagrant needs to create VMs for you. This is most commonly when you use a new Vagrant file for the first time or can also be after you have run `vagrant destroy`.
Usage:
1. Copy the file *vagrant_first_start* file to the folder that contains your Vagrantfile.
2. Make the script executable via `chmod +x vagrant_first_start`.
3. Run the script via `./vagrant_first_start`.
4. Wait patiently - each vm will need to launch, fail and reload.

That's it! From the second time you need these VMs, `vagrant up` or `vagrant resume` is all you need.

## Troubleshooting
**Nothing happens when I run the script**
 - Most probably your VMs have already been created. If you cannot access them via `vagrant ssh` then you should probably try `vagrant destroy` and continue the above Usage instructions from step 3. (Disclaimer, `vagrant destroy` will permanently delete your VMs and lose any configuration not in your Vagrantfile. Use at your own risk).
 
**I get this error**
```
A Vagrant environment or target machine is required to run this
command. Run `vagrant init` to create a new Vagrant environment. Or,
get an ID of a target machine from `vagrant global-status` to run
this command on. A final option is to change to a directory with a
Vagrantfile and to try again.
```
 - It looks like you are trying to run the script in a folder that does not contain a Vagrantfile. Please check make sure that this script is run from the same folder as the Vagrantfile you wish to use.
