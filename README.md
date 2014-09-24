# puppet-sinopia module

## Overview

Install sinopia npm-cache-server (https://github.com/rlidwka/sinopia) for Debian, Ubuntu, Fedora, and RedHat.

## Dependencies

This module depends on the changes of a not yet merged pull request in willdurand/puppet-nodejs: https://github.com/willdurand/puppet-nodejs/pull/65.
Until they are merged you have to explicitely specify the dependency to nodejs in your librarian-puppet Puppetfile:


```bash
  # dependency nodejs
  # replace it with:
  mod "willdurand/nodejs",
  :git => "https://github.com/saheba/puppet-nodejs.git",
  :ref => 'npm_management'  
```

## Usage

There are two variants to install sinopia using this Puppet module: Apply-mode (with puppet-apply and no puppetmaster setup needed) or Master-Agent-mode (with puppet-agent accessing your configuration through the puppetmaster). In both variants you have to explicitely call "class nodejs {}" in your puppet script because the puppet-sinopia module only defines this as a requirement, so you have all the flexibility you want when installing nodejs. Scroll down for details about both variants.

### General usage

#### class sinopia

Installs sinopia + required npms in one defined directory and integrates the sinopia as a service (/etc/init.d/sinopia). It also creates a user to run the sinopia server (default: sinopia). If you wish, you can change the username, see examples below.

Examples:

minimal:

```bash
  class { '::sinopia':
    conf_admin_pw_hash => 'your-pw-hash',
  }
```

You can generate the admin password hash according to https://github.com/rlidwka/sinopia via command-line:

```bash
  $ node
  > crypto.createHash('sha1').update('your-admin-password').digest('hex')
```

You can also override several configuration parameters.

```bash
  class { '::sinopia':
    install_root       	    => '/usr/local',
    install_dir        	    => 'sinopiaxy',
    conf_admin_pw_hash 	    => 'your-pw-hash',
    conf_port          	    => '8080',
    deamon_user        	    => 'sinopiaxy',
    conf_listen_to_address 	=> '127.0.0.1',
    conf_max_body_size	    => '10mb',
    conf_max_age_in_sec	    => '604800',
    install_as_service	    => false,
  }
```

The default values for all so far configurable parameters are:

```bash  
  class { '::sinopia':
    install_root       	      => '/opt',
    install_dir        	      => 'sinopia',
    deamon_user        	      => 'sinopia',
    conf_listen_to_address    => '0.0.0.0',
    conf_port          	      => '4783',
    conf_admin_pw_hash
    conf_user_pw_combinations => undef,
    conf_max_body_size	      => '1mb',
    conf_max_age_in_sec	      => '86400',
    install_as_service	      => true,
  }
```

### Apply-mode installation

Here is an example for an Apply-mode installation in Debian Wheezy:

1. Install puppet and facter according to https://docs.puppetlabs.com/guides/install_puppet/pre_install.html#next-install-puppet:
```bash  
  wget https://apt.puppetlabs.com/puppetlabs-release-wheezy.deb
  dpkg -i puppetlabs-release-wheezy.deb
  apt-get update
  apt-get -y install puppet facter
```
2. Download/clone all required modules into a modules directory:
```bash  
  mkdir -p /opt/pp-sinopia/modules ; cd /opt/pp-sinopia/modules
  
  git clone https://github.com/puppetlabs/puppetlabs-stdlib.git stdlib
  git clone https://github.com/puppetlabs/puppetlabs-apt.git apt
  # for RHEL/Fedora/CentOS you will probably need yumrepo instead of apt
  
  git clone https://github.com/saheba/puppet-nodejs.git nodejs
  
  git clone https://github.com/saheba/puppet-sinopia.git sinopia
```

3. Switch to the appropriate of the puppet-sinopia module:
```bash  
  cd /opt/pp-sinopia/modules/sinopia
  git fetch --all
  git checkout npm_management
```

4. Create a puppet script file:
```bash  
  vim /opt/pp-sinopia/run.pp
```

5.run.pp content example:
```bash  
  class { 'nodejs':
    # this automatically installs nodejs and npm
    make_default => true,
  }
  class { '::sinopia':
    conf_admin_pw_hash => 'your-pw-hash',
  }
```

6. run puppet-apply with your script and the downloaded modules:
```bash  
  puppet apply --modulepath=/opt/pp-sinopia/modules /opt/pp-sinopia/run.pp
```


### Master-Agent-mode installation

In your puppet script for your agent add:
```bash  
  class { 'nodejs':
    # this automatically installs nodejs and npm
    make_default => true,
 }
  class { '::sinopia':
    conf_admin_pw_hash => 'your-pw-hash',
  }
```

## Supported Platforms

The module has been tested on the following operating systems. Testing and patches for other platforms are welcome.

* RedHat EL6.
* Debian Wheezy.
