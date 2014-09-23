# puppet-sinopia module

## Overview

Install sinopia npm-cache-server (https://github.com/rlidwka/sinopia) for Debian, Ubuntu, Fedora, and RedHat.

## Dependencies

This module depends on the changes of a not yet merged pull request in puppetlabs/nodejs: https://github.com/puppetlabs/puppetlabs-nodejs/pull/86.
Until they are merged you have to explicitely specify the dependency to nodejs in your librarian-puppet Puppetfile:


```bash
  # dependency puppetlabs/nodejs
  # replace it with:
  mod "puppetlabs/nodejs",
  :git => "https://github.com/saheba/puppetlabs-nodejs.git",
  :ref => 'exec_user_param'  
```

## Usage

### class sinopia

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
    install_root       	=> '/usr/local',
    install_dir        	=> 'sinopiaxy',
    conf_admin_pw_hash 	=> 'your-pw-hash',
    conf_port          	=> '8080',
    deamon_user        	=> 'sinopiaxy',
    conf_listen_to_address 	=> '127.0.0.1',
    conf_max_body_size	=> '10mb',
    conf_max_age_in_sec	=> '604800',
    install_as_service	=> false,
  }
```

The default values for all so far configurable parameters are:

```bash  
  class { '::sinopia':
    install_root       	=> '/opt',
    install_dir        	=> 'sinopia',
    deamon_user        	   => 'sinopia',
    conf_listen_to_address 	   => '0.0.0.0',
    conf_port          	   => '4783',
    conf_admin_pw_hash
    conf_user_pw_combinations => undef,
    conf_max_body_size	   => '1mb',
    conf_max_age_in_sec	   => '86400',
    install_as_service	   => true,
  }
```

## Supported Platforms

The module has been tested on the following operating systems. Testing and patches for other platforms are welcomed.

* RedHat EL6.
