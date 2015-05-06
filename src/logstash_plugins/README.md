## Logstash filter plugin

Usage

- Install the [CF CLI](http://docs.cloudfoundry.org/devguide/installcf/) and add it to the system path.

- Add the following filter configuration to the logstash configuration.

```
filter {
  .
  .
  cfapp {
    cfapi => "<cloudfoundry api target>"
    cfuser => "<user that has access to all orgs and spaces>"
    cfpassword => "<password>"
    default_org => "<the default org for the above user>"
    default_space => "<a default space in the default org>"
    cache_purge_interval => <optional default is '10m' for 10 minutes>
    cache_age_time => <optional default is '600' for 10 minutes>
    skip_ssl_validation => <optional default is true>
  }
  .
  .
}
```

- Start logstash with the ```--pluginpath``` option pointing to a folder where 
the ```cfapp.rb``` is or copy it to ```<logstash_home>/lib/logstash/filters```.
