## Archaeology 

In the beginning there was the cli. If you wanted to know what was going on, you went to your box and wrote `show interface FastEthernet1` and looked at the counters. lol.

The [SNMP](https://datatracker.ietf.org/doc/html/rfc1157) standard existed to enable get/set semantics on the devices, but the MIB Tree was considered elvish and impenetrable, and only those with development skills and time to invest were able to leverage this. Commercial products like Solarwinds were spawned and sold at premium prices to offer operators with monetary resources to access this information in a visual way.

Then an enterprising young whipper-snapper from Switzerland named [Tobias Oetker](https://www.oetiker.ch/en_US/home/oss/projects) created RRD to store time series data in a progressive way and MRTG to visualise it. Rather than monetise this work he released it free and opensource. The autoconfiguration scripts largely worked for the most common devices, and the community flocked to it immediately. Suddenly everyone had access to the insights only the big bois could previously leverage. 

MRTG was a revelation, but it still had the issue that you needed to know what MIBs to target and much fettling was required to customise the dashboards to format and present this data in a consumable way. The community started to leverage RRD/MRTG in the backend of their own applications that would be more accessible to users. It became ubiquitous.

One key project in the transformation of opensource telemetry was Observium. It took MRTG and packaged it inside of a PHP application that would "discover" devices with a minimal SNMP walk, match that to a MIB profile and then schedule polling of 100s of targeted MIBs that were device specific. It would then normalise this SNMP return data into its own data structures so that the front end could be built once, and offer a consistent visualisation of all these metrics across multiple vendors. 

At some point the original team behind Observium decided to change their licence and steer users towards a paid subscription for regular updates. A faction of the userbase were unhappy with this and forked observium into what is now known as [LibreNMS](https://www.librenms.org/). Their goals remain the same in that they offer a unified front end over a RRD backend with normalised metrics across a very large amount of vendors. 

## The problem statement

Right now, if you want to get to an equivalent experience of LibreNMS, but with streaming telemetry, you either buy a proprietary solution for many bucks, or you have a very large uphill battle. History is repeating itself. 

Much like when Tobi released RRD and MRTG, we have [telegraf](https://www.influxdata.com/time-series-platform/telegraf/) and [gnmic](https://gnmic.openconfig.net/) as two primary examples of good capture tooling, [prometheus](https://prometheus.io/) as a very mature time series database, and [grafana](https://grafana.com/) as the industry standard visualisation tool. The time consuming part is mapping of the metric paths to the device/version and the normalisation of that data to a single model. 

In the "time is a flat circle" analogy, we are back where we were before Observium made SNMP "point and shoot".

[OpenConfig](https://www.openconfig.net/) is a relatvely mature abstraction, which many device vendors now support as well. The challenge has been how far the device vendors have gone in their support of OC models, and how consistent the implementation is. 

And so frustatingly, every organisation that wants to go down this path has had to start from scratch and since no network is fully single vendor, it is not so easy to buy a vendor tool and "get to value" with money alone. In two previous roles I have invested significant resources into this process of establishing GNMI telemetry, and producing the dashboards to match. I have always librenms in parallel and the zero to value on both of these approaches is regretably too far apart.

So here we are with another enterprising young whipper-snapper from Switzerland (John) attempting to democratise streaming telemetry.

The goal of this project is to reproduce the experience of minimal input data (hostname:port + credentials) being used to "discover" the device, mapping the make/model/firmware to a telemetry profile and then initiating a stream of metrics that are stored in prometheus and visualised in Grafana.
