# -*- mode: ruby -*-
# vi: set ft=ruby :

$update_yum = <<SCRIPT
# update package lists from the server
yum update -y
SCRIPT

$install_repose_dependencies = <<SCRIPT
# install potentially missing packages needed to install Repose
yum install -y wget
wget http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el6.rf.x86_64.rpm
rpm -Uvh rpmforge-release-0.5.3-1.el6.rf.x86_64.rpm
yum install -y daemonize

# add openrepose.org repository
wget -O /etc/yum.repos.d/openrepose.repo http://repo.openrepose.org/el/openrepose.repo
SCRIPT

$install_nodejs = <<SCRIPT
# yum install -y epel-release  # TODO DELETE THIS
wget https://rpm.nodesource.com/pub_0.12/el/7/x86_64/nodejs-0.12.7-1nodesource.el7.centos.x86_64.rpm
rpm -Uvh nodejs-0.12.7-1nodesource.el7.centos.x86_64.rpm
SCRIPT

$install_fake_identity = <<SCRIPT
mkdir -p /opt/FakeIdentity
cd /opt/FakeIdentity

# file contents taken from: https://github.com/rackerlabs/repose-perf-infrastructure/blob/master/src/repose_performance/tracing/master/package.json
cat > package.json << EOF
{
  "name": "responders",
  "version": "0.1.0",
  "description": "Mock responders",
  "author": "Dimitry Ushakov <dimitry.ushakov@rackspace.com>",
  "contributors": [
  {
    "name": "Jenny Vo",
    "email": "jenny.vo@rackspace.om"
  },
  {
    "name": "James Combs",
    "email": "james.combs@rackspace.com"
  }],
  "engines": {
    "node": "0.10.x",
    "npm": "1.4.x"
  },
  "dependencies": {
    "sleep": "1.2.x",
    "express": "4.2.x",
    "libxmljs": "0.13.x",
    "date-utils": "1.2.x"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/rackerlabs/repose-ansible-stuff"
  },
  "license": "MIT"
}
EOF

# file contents taken from: https://github.com/rackerlabs/repose-perf-infrastructure/blob/master/src/repose_performance/tracing/master/auth_backend.js.j2
cat > app.js << EOF
var express = require('express');
var libxmljs = require('libxmljs');
require('date-utils');

var app = express();
app.disable('etag');

app.post('/tokens', function(req, res){
  var body = req.body;
  res.set('Content-Type','application/xml');
  res.send(200, '<?xml version="1.0" encoding="UTF-8" standalone="yes"?><access xmlns="http://docs.openstack.org/identity/api/v2.0" xmlns:os-ksadm="http://docs.openstack.org/identity/api/ext/OS-KSADM/v1.0" xmlns:os-ksec2="http://docs.openstack.org/identity/api/ext/OS-KSEC2/v1.0" xmlns:rax-ksqa="http://docs.rackspace.com/identity/api/ext/RAX-KSQA/v1.0" xmlns:rax-kskey="http://docs.rackspace.com/identity/api/ext/RAX-KSKEY/v1.0"><token id="this-is-the-admin-token" expires="2013-08-03T19:46:54Z"><tenant id="this-is-the-admin-tenant" name="this-is-the-admin-tenant"/></token><user xmlns:rax-auth="http://docs.rackspace.com/identity/api/ext/RAX-AUTH/v1.0" id="67890" name="admin_username" rax-auth:defaultRegion="the-default-region"><roles><role id="684" name="compute:default" description="A Role that allows a user access to keystone Service methods" serviceId="0000000000000000000000000000000000000001" tenantId="12345"/><role id="5" name="object-store:default" description="A Role that allows a user access to keystone Service methods" serviceId="0000000000000000000000000000000000000002" tenantId="12345"/><role id="6" name="cloudfeeds:service-admin" description="A Role that allows a user access to keystone Service methods" serviceId="0000000000000000000000000000000000000005" tenantId="12345"/></roles></user><serviceCatalog><service type="rax:object-cdn" name="cloudFilesCDN"><endpoint region="DFW" tenantId="this-is-the-admin-tenant" publicURL="https://cdn.stg.clouddrive.com/v1/this-is-the-admin-tenant"/><endpoint region="ORD" tenantId="this-is-the-admin-tenant" publicURL="https://cdn.stg.clouddrive.com/v1/this-is-the-admin-tenant"/></service><service type="object-store" name="cloudFiles"><endpoint region="ORD" tenantId="this-is-the-admin-tenant" publicURL="https://storage.stg.swift.racklabs.com/v1/this-is-the-admin-tenant" internalURL="https://snet-storage.stg.swift.racklabs.com/v1/this-is-the-admin-tenant"/><endpoint region="DFW" tenantId="this-is-the-admin-tenant" publicURL="https://storage.stg.swift.racklabs.com/v1/this-is-the-admin-tenant" internalURL="https://snet-storage.stg.swift.racklabs.com/v1/this-is-the-admin-tenant"/></service></serviceCatalog></access>');
});

app.get('/tokens/:token_id', function(req,res){
  var token = req.params.token_id;
  var tempDate = new Date();

  var date = tempDate.addDays(1).toFormat('YYYY-MM-DDTHH24:MI:SSZ');
  var user_id = token.substr(0, 10);
  res.set('Content-Type','application/xml');
  res.send(200,'<?xml version="1.0" encoding="UTF-8" standalone="yes"?><access xmlns="http://docs.openstack.org/identity/api/v2.0" xmlns:os-ksadm="http://docs.openstack.org/identity/api/ext/OS-KSADM/v1.0" xmlns:os-ksec2="http://docs.openstack.org/identity/api/ext/OS-KSEC2/v1.0" xmlns:rax-ksqa="http://docs.rackspace.com/identity/api/ext/RAX-KSQA/v1.0" xmlns:rax-kskey="http://docs.rackspace.com/identity/api/ext/RAX-KSKEY/v1.0"><token id="' + token + '" expires="' + date + '"><tenant id="' + user_id + '" name="' + user_id + '"/></token><user xmlns:rax-auth="http://docs.rackspace.com/identity/api/ext/RAX-AUTH/v1.0" id="' + user_id + '" name="username" rax-auth:defaultRegion="the-default-region"><roles><role id="684" name="compute:default" description="A Role that allows a user access to keystone Service methods" serviceId="0000000000000000000000000000000000000001" tenantId="' + user_id + '"/><role id="685" name="observer" description="A Role that allows a user access to keystone Service methods" serviceId="0000000000000000000000000000000000000001" tenantId="' + user_id + '"/><role id="6" name="cloudfeeds:service-admin" description="A Role that allows a user access to keystone Service methods" serviceId="0000000000000000000000000000000000000005" tenantId="12345"/><role id="5" name="object-store:default" description="A Role that allows a user access to keystone Service methods" serviceId="0000000000000000000000000000000000000002" tenantId="' + user_id + '"/></roles></user><serviceCatalog><service type="rax:object-cdn" name="cloudFilesCDN"><endpoint region="DFW" tenantId="this-is-the-tenant" publicURL="https://cdn.stg.clouddrive.com/v1/this-is-the-tenant"/><endpoint region="ORD" tenantId="this-is-the-tenant" publicURL="https://cdn.stg.clouddrive.com/v1/this-is-the-tenant"/></service><service type="object-store" name="cloudFiles"><endpoint region="ORD" tenantId="this-is-the-tenant" publicURL="https://storage.stg.swift.racklabs.com/v1/this-is-the-tenant" internalURL="https://snet-storage.stg.swift.racklabs.com/v1/this-is-the-tenant"/><endpoint region="DFW" tenantId="this-is-the-tenant" publicURL="https://storage.stg.swift.racklabs.com/v1/this-is-the-tenant" internalURL="https://snet-storage.stg.swift.racklabs.com/v1/this-is-the-tenant"/></service></serviceCatalog></access>');
});

app.get('/users/:user_id/RAX-KSGRP', function(req,res){
  res.set('Content-Type','application/xml');
  var user = req.params.user_id;
  res.send(200,'<?xml version="1.0" encoding="UTF-8" standalone="yes"?><groups xmlns="http://docs.rackspace.com/identity/api/ext/RAX-KSGRP/v1.0"><group id="0" name="Default"><description>Default Limits</description></group></groups>');
});

app.get('/', function(req, res){
  res.send('hello world');
});

app.listen(9090);
EOF

# download and install the Fake Identity app dependencies
npm install
SCRIPT

$run_fake_identity = <<SCRIPT
cd /opt/FakeIdentity
node app.js &
SCRIPT

$install_httpbin = <<SCRIPT
yum install -y curl python-setuptools
easy_install pip
pip install httpbin gunicorn
SCRIPT

$run_httpbin = <<SCRIPT
gunicorn httpbin:app &
SCRIPT

$install_httpie = <<SCRIPT
yum install -y curl python-setuptools
easy_install pip
pip install --upgrade pip setuptools
pip install --upgrade httpie
SCRIPT

$repose_install_instructions = <<INSTRUCTIONS
To log into your Vagrant box, run:
vagrant ssh

To install the latest version of Repose, run:
sudo yum install -y repose-valve repose-filter-bundle repose-extensions-filter-bundle

To install a sepcific version of Repose (e.g. 7.2.1.0-1), run:
sudo yum install -y repose-valve-7.2.1.0-1 repose-filter-bundle-7.2.1.0-1 repose-extensions-filter-bundle-7.2.1.0-1

To see the list of available Repose versions to install, run:
yum --showduplicates list repose-valve | expand
INSTRUCTIONS

$other_instructions = <<INSTRUCTIONS
Fake Identity is listening on port 9090
httpbin is listening on port 8000

For more details on using httpbin, see: http://httpbin.org/
For more details on using HTTPie, see: https://github.com/jkbrzt/httpie
INSTRUCTIONS

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "bento/centos-7.1"

  config.vm.provision "shell", inline: $update_yum
  config.vm.provision "shell", inline: $install_repose_dependencies
  config.vm.provision "shell", inline: $install_nodejs
  config.vm.provision "shell", inline: $install_fake_identity
  config.vm.provision "shell", inline: $install_httpbin
  config.vm.provision "shell", inline: $install_httpie
  config.vm.provision "shell", inline: $run_fake_identity
  config.vm.provision "shell", inline: $run_httpbin

  puts $repose_install_instructions
  puts $other_instructions

  # config.vm.network "forwarded_port", guest: 8080, host: 8080
  # config.vm.synced_folder "../data", "/vagrant_data"
end
