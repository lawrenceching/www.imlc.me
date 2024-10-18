# The mindflow of software design


## Requirement

* Business Background
* Business Requirement
* API changes
* Traffic Model changes
* Configuration
* Log

### Non-functional Requirement


## Implementation

TODO:

##  Bakdward/Forward Compatibility

* supported period
* external API
* internal API
* feature
* non-functional feature
* DB schema
* configuration

## Upgrade & Rollback
Impact Analysis if there is traffic impact

## Backup & Restore

## HA

## GeoRed


## Security

### Privacy


### Audit
### GDPR

### GB/T 39276-2020
* sensitive data in encrypted in Database
* sensitive data in masked in log

## Legal Compliance

* contract update

## Test

### Functional Test

| ID | Description | Check Point |
|----|-------------|-------------|
| ServiceA_XXX_YYY_0001 | **Given** XXX **When** YYY **Then** ZZZ | 1. HTTP status code is 200 |

### Non-Functional Test


#### Footprint


| Resource   | Delta Per Pod | Delta Total |
|------------|---------------|-------------|
| CPU        |               |             |
| Memory     |               |             |
| Network IO |               |             |
| Disk IO    |               |             |
| Storage    |               |             |


### Test Case

  * Performance
    - Throughput per x vCPU y GiB Memory
    - CPU/Memory/Disk/Network Overhead
  * Stability
    - TP95 latency
    - TP99 latency
    - Lowest/Highest latency
  * Robustness
    - Container Failure
    - Pod Failure
    - Node Failure
    - Network Failure
    - Disk Failure
  * Scalability
    - Vertical
    - Horizontal
    - No Traffic Failure during scaling
    - Scale Up Duration
  * Overload
    - System not crash

### GB/T 39276-2020
* Ensure data would be deleted when user is deregistered
* Ensure user could export/backup his data