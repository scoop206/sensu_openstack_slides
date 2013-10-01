!SLIDE 
# Sensu #
## a monitoring solution for OpenStack ##


!SLIDE bullets incremental
# State of OS Monitoring  #

* It's a grab bag
* Nagios and it's forks seem to be dominant


!SLIDE bullets incremental
# Ceilometer? #

* bringing in Synaps for Havana
* requires MongoDB
* <span style='font-size:60%;'>“A dedicated host for storing the Ceilometer database is recommended, as it can generate lots of writes [2]. Production scale metering is estimated to have 386 writes per second and 33,360,480 events a day, which would require 239 Gb of volume for storing statistics per month.”</span> <br /> <span style='font-size:50%;'> - Mirantis Blogpost "OpenStack Metering Using Ceilometer"</span>


!SLIDE bullets incremental
# What is Sensu ? #

* monitoring framework
* a metrics bus

!SLIDE bullets
# Backstory #

* written by Sean Porter while at Sonian in 2011
![sonian](sonianLogo.png)

!SLIDE 
# Nagios #
![nagios](sad-eeyore.jpg)

!SLIDE bullets incremental
# Nagios Challenges #

* adding a node required Nagios restarts up the line
* too many false positives
* strange scheduling issues

!SLIDE bullets incremental

# Emotionally Blocked #
![nagios](scared.jpeg)

!SLIDE center
![sensu](sensu-logo.png)
![rabbit](sockjs-rabbit-cf.png)

!SLIDE bullets incremental
# Goals #
* Automatic client registration
* CM aware
* Hackable
* API

!SLIDE bullets incremental
# Sensu Stack

* Unix Principles
* MIT Licensed

![rabbit](sockjs-rabbit-cf.jpg)

![ruby](ruby.jpg)

![redis](redis.png)

!SLIDE bullets incremental
# Highlevel Pieces #

* Sensu Agent - runs on each client
* Sensu Server
* Check
* Handler

!SLIDE bullets incremental
# Sensu Workflow #

* Sensu Server publishes a check
* Sensu Agents picks up the check and executes it.
* Publishes output back onto the Results queue
* Sensu Server pulls the result off the queue and passes to the Handler(s) specified in the check.

!SLIDE bullets incremental
# Check #
* copies the Nagios NRPE Plugin model
* shell out, and return STDOUT and exit status
* compatible with Nagios plugins

!SLIDE code
# Check
/etc/sensu/conf.d/checks/check.disk.json
    @@@ javascript
    {
      "checks": {
        "check_disk": {
           "command": "check-disk.rb -w 85 -c 95",
           "subscribers": [
             "all"
            ],
            "handlers": [
              "email",
              "pagerduty"
            ],
           "interval": 60
        }
      }
    }


!SLIDE code 
# Sensu Plugin - a wrapper
    @@@ruby
    #/etc/sensu/plugins/check-file-exist.rb

    require 'sensu-plugin/check/cli'

    class CheckFileExist < Sensu::Plugin::Check::CLI

      check_name 'check-file-exist' # defaults to class name
      option :file, :short => '-f' # Mixlib::CLI is included

      def run
        if File.exist?(config[:file])
          ok "All is well"
        else
          crit “#{config[:file]} does not exist”
        end
      end
    end
    
!SLIDE
# Demo

    @@@html
         https://github.com/sandfish8/sensu_demo_vm

!SLIDE bullets incremental
# Handlers and Metrics #

* pipe
* metric
* amqp

!SLIDE bullets
# Handlers and Metrics #

* pipe
* metric
* amqp > Graphite

!SLIDE 
# Community Metrics Checks #
cassandra<br />
elasticsearch<br />
elb-latency<br />
aws/autoscaling-instance-count-metrics<br />
haproxy<br />
http/metrics-curl<br />
memcached<br />
mongodb<br />
mysql
network<br />
newrelic<br />
nginx
percona-cluster<br />
postgres<br />
processes<br />
rabbitmq<br />
redis
resque<br />
riak
solr<br />
check-cpu<br />
disk-capacity<br />
interface<br />
iostat<br />
load
memory<br />
temperature<br />
varnish<br />


!SLIDE bullet incremental
# OpenStack Metrics

* logins to Horizon
* API calls
* cpus per tenant

!SLIDE bullets incremental
# Niceties #

* Omnibus package for discrete ruby env
* excellent logging
* active community

!SLIDE bullets
* Sam Cooper
* scooper@bluebox.net
* @sandfish8
