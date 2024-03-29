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

rps = app instances / avg response time†

† avg response time - you're only as good as your slowest requests so consider using 95th percentile instead.  Also depends on response time variance.  Less variance is more predicable.  Other factors like I/O and CPU also play a role and can cause deviations from Little's Law.  See https://www.speedshop.co/2015/07/29/scaling-ruby-apps-to-1000-rpm.html

### Scaling Up

Only scales throughput, not performance.  Only matters when requests are starting to queue.  Also impacts database which can only scale vertically.

What's a good ratio of app instances?  Somewhere in the 35-40% range, actual usage vs theoretical.

3 levers
 - # application instances
 - response time
 - response time variability

https://www.speedshop.co/2017/10/12/appserver.html

Passenger Request Load Balancing - https://www.phusionpassenger.com/library/indepth/ruby/request_load_balancing.html
Request Queueing - https://stackoverflow.com/questions/20402801/what-is-optimal-value-for-phusion-passenger-passengermaxrequestqueuesize
passenger-status - https://www.phusionpassenger.com/library/admin/apache/overall_status_report.html
passenger memory usage - https://www.phusionpassenger.com/library/indepth/accurately_measuring_memory_usage.html
