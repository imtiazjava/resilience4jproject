# RESILIENCE4J CIRCUITBREAKER WITH SPRING BOOT

![image](https://user-images.githubusercontent.com/26134506/236116010-177b38dd-c59b-4953-aa20-a8fe1e99e162.png)


# What is Resilience4j?
- Resilience4j is a fault tolerance library inspired by Netflix Hystrix, that offers implementations for many microservices stability/fault tolerances patterns.
- It is a light weight.
- Hystrix is not supported in the latest spring cloud releases.

# Resilience4j Core Modules
- Circuit Breaker
- Ratelimiter
- Bulkhead
- Timelimiter
- Retry

![image](https://user-images.githubusercontent.com/26134506/236135034-7228d6c5-24b4-46e3-bbbf-5f52c7f6e279.png)



# Circuit Breaker
- At home a circuit breaker protects the home and electrical appliances by breaking the circuit and stopping the flow of electricity when there is excess current. 
  The same pattern can be applied in software to protect the system and individual microservices from huge failures.

  ![image](https://user-images.githubusercontent.com/26134506/236138378-4fb688c9-481c-4b5f-8020-0b27acecf79a.png)

 - The CircuitBreaker is implemented via a finite state machine with three normal states: 
 
         1. CLOSED
         2. OPEN
         3. HALF_OPEN and two special states DISABLED and FORCED_OPEN.
 
  1. CLOSED 
  
  > This is the normal state in which all requests flow through the circuit without any restriction.
  
  2. OPEN
  
  >  For the past n number of requests (or) n number of seconds, if the failures / slow response rate is equal to or greater than a configurable threshold, the circuit opens. 
     In this state all calls will be restricted with CallNotPermittedException.
