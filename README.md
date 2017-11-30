# Description

This repository contains the puppet infrastructure code to set up a Nginx server that performs both as reverse and forward proxy.

### Prerequisites
* Install ruby (>= 2.2) on your system.
* You will also need Puppet >= 3.8, Facter >= 1.6, Hiera and librarian-puppet. They all can be installed with your platform's package manager (if using OSX, packages are available via `brew`)
* Puppet Nginx module is installed using Puppetfile's module definitions. To this effect, run `librarian-puppet install` install to pull the module and its dependencies from the respository. This command will create and populate the subdir `modules-contrib`.
* For puppet to be able to fetch the proxy's role definition, you need to set the parameter `domain_role` to `domain_proxy`.

### Functionality
This puppet code takes the community mantained module voxpupuli/puppet-nginx to perform the following:

1. Creates a proxy to redirect requests for https://domain.com to 10.10.10.10 and redirect requests for https://domain.com/resoure2 to 20.20.20.20. These are created as Nginx server blocks that forward requests to an upstream backend, therefore allowing for extra members for load-balancing and passive health checks.
2. Creates a forward proxy to log HTTP requests going from the internal network to the Internet including: request protocol, remote IP and time take to serve the request. To this effect, all HTTP requests pointing to `forward.domain.com:8888` as proxy server will be forwarded to the external network and logged accordingly.
3. Implements proxy passive health checks on all defined upstream backend members. Timeout and retries may be configured with the parameters `upstream_fail_timeout` and `upstream_max_fails`.

### Customization
* All configuration is done via hiera, please refer to the role yaml file `domain_proxy.yaml` to set up custom values,  or `proxy.domain.com.yaml` for host-specific configuration.
