## Terraform custom plugin

#### V0.0.1
Code for compiling a custom plugin and using it in terraform.
#### V0.0.2
Adds code for testing the output of already created plugin

**The idea of this repo is to run locally a custom plugin.**

We are going to use this custom plugin:
https://github.com/petems/terraform-provider-extip

Terraform provider for getting your current external IP as a data source.

### Prerequisites:

- You have Vagrant installed
- You have Terraform installed and you are familiar with running a sample code, i.e. null provider
- You have done any Golang script and know how to setup golang environment

### Steps to complete this task:

1. Fork and clone the repo to your laptop
2. You have a Vagrant file that downloads my vagrant Ubuntu base box and provision it with the `provision.sh` script from scripts folder. (provisioning script installs Golang environment, Terraform and GIT CLI)
3. Create an empty `main.tf` file.
4. `vagrant up` builds and provisions the VM
5. After VM is ready you have to `vagrant ssh` in order to login to the machine.

**Now it is time to compile the custom plugin**

6. Run the below commands one after another:

```
# go get github.com/petems/terraform-provider-extip
# cd ~/go/src/github.com/petems/terraform-provider-extip
# make build
# ls -al ~/go/bin/
```

You should now have `terraform-provider-extip` binary in `~/go/bin/` folder

7. Create the following directory:
`mkdir -p /vagrant/terraform.d/plugins/linux_amd64`
8. Copy the binadry that you just compiled to that directory
`cp ~/go/bin/terraform-provider-extip /vagrant/terraform.d/plugins/linux_amd64/`

`/terraform.d/plugins/linux_amd64/` directory is where terraform is going to search for the custom plugin in case it is not available on the official Terraform plugin repository.
`/vagrant` is your shared folder with the one that you cloned the repo in, where you Vagrantfile is located as also the empty `main.tf`.

9. Go to the `/vagrant` folder: `cd /vagrant`
10. Update your `main.tf` file so it looks like this:

```
data "extip" "external_ip" {}

output "external_ip" {
  value = "${data.extip.external_ip.ipaddress}"
}
```
11. It is time to test if you are going to get your external IP as an output:
`terraform init`
`terraform apply`

### Testing part

Code in V0.0.2 which adds testing, changes the provisioning script to install Ruby, ruby-dev and Bundler gem

When you have already done the 11 steps above you need to do the following in order to test your output:
```
vagrant ssh
cd /vagrant
bundle install
bundle exec kitchen test
```

Test is located in test/integration/default/control folder and you can edit and try something different.
