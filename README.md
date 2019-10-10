# DevOps

## Observability

https://twitter.com/mipsytipsy/status/1024608798336475136 

## Deployment

https://charity.wtf/2019/05/01/friday-deploy-freezes-are-exactly-like-murdering-puppies/amp/

https://charity.wtf/2018/08/19/shipping-software-should-not-be-scary/

## Source Control

> Source control is a communication mechanism

https://twitter.com/jezhumble/status/1175171015900426240

### Trunk-based dev
* https://cloud.google.com/solutions/devops/devops-tech-trunk-based-development

# Kubernetes

## Liveness and Readiness Probes

https://srcco.de/posts/kubernetes-liveness-probes-are-dangerous.html

## JVM Tuning

https://medium.com/adorsys/jvm-memory-settings-in-a-container-environment-64b0840e1d9e

# Ruby/Rails

## Performance
Passenger uses nginx reverse proxy to route requests to the least busy passenger child process.  Traditional load balancers (e.g. ELB) that are not embedded with Ruby will typically do round-robin load balancing which doesn't take into account "business" of processes.  Max processing concurrency per ruby process is 1 by default (because of the Ruby GIL).   It's possible to set the concurrency model to `thread` for thread-safe applications.  Passenger uses a single request queue across all processes to ensure one processes doesn't unfairly get queued against while others are fine.  If the request queue overflows, additional requests will be dropped (passenger_max_request_queue_size).

### Memory Usage

200-400MB per ruby process is typical.  Utilize pre-loading to ensure the entire application is loaded into memory before forking child processes. This will save memory.

passenger-status memory comprises:
 - private dirty RSS - like the RSS, minus any shared memory
 - process memory written to swap
 
Why not just RSS from ps?
 - may include OS shared memory (inflated)
 - does not include swap (defalted)
 
### Little's Law

rps = app instances / avg response time

https://www.speedshop.co/2017/10/12/appserver.html

Passenger Request Load Balancing - https://www.phusionpassenger.com/library/indepth/ruby/request_load_balancing.html
Request Queueing - https://stackoverflow.com/questions/20402801/what-is-optimal-value-for-phusion-passenger-passengermaxrequestqueuesize
passenger-status - https://www.phusionpassenger.com/library/admin/apache/overall_status_report.html
passenger memory usage - https://www.phusionpassenger.com/library/indepth/accurately_measuring_memory_usage.html
