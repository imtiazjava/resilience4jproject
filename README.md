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
     
   3. HALF_OPEN
   
   > After a configurable wait time in OPEN State, the circuit breaker allows a small number of (configurable) requests to pass through. In the HALF_OPEN state
  
   > * If the failures / slow responses are above the threshold, it moves back to OPEN state and wait again.
   > * If the failures / slow response are below the threshold, it moves to CLOSED state and functions normally.
  
* * *
    
# How to configure circuit breaker in spring boot:
### Step1:
> Add the 3 jars files inside the pom.xml file

```
<dependency>
   <groupId>io.github.resilience4j</groupId>
   <artifactId>resilience4j-spring-boot2</artifactId>
   <version>1.7.0</version>
</dependency>
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
### Step2:
> **Then we need to define the configs for a circuitbreaker in application.yml. Here we are going to OPEN the circuit if:**
> * **50% of the calls failed (failureRateThreshold: 50) (or) 100% of the calls (slowCallRateThreshold: 100) took greater than 2 seconds (slowCallDurationThreshold: 2000) – in a 60 second window (slidingWindowSize: 60 & slidingWindowType: TIME_BASED).**
> * **Once the Circuit OPEN s it rejects the requests with CallNotPermittedException for 5 seconds (waitDurationInOpenState:5s) and then moves to HALF_OPEN state.**
> * **It permits 3 calls in HALF_OPEN state (permittedNumberOfCallsInHalfOpenState: 3) and if the failure or slow call rate is still above threshold it moves to OPEN state again/**
> * **nimumNumberOfCalls: 10 mentions the minimum number of calls to calculate the failure or slow call rates for the Circuitbreaker to move from CLOSED to OPEN state. It is not applicable when the Circuitbreaker is in HALF_OPEN state. For example if there are only 9 calls in a 60 second window and even if all of them failed, the circuit will not OPEN.**
> * **noreExceptions: specifies a list of exceptions that will be ignored when counting the failure.**

### The following is the configuration inside the application.yaml file

```
resilience4j.circuitbreaker:
  instances:
    greetingClientCB:
      registerHealthIndicator: true
      slidingWindowSize: 60
      slidingWindowType: TIME_BASED
      permittedNumberOfCallsInHalfOpenState: 3
      minimumNumberOfCalls: 10
      waitDurationInOpenState: 5s
      slowCallRateThreshold: 100
      slowCallDurationThreshold: 2000
      failureRateThreshold: 50
      ignoreExceptions:
        - com.jsession4d.feignclient.BusinessException
        
```
* * *

**Note:** The default slidingWindowType is COUNT_BASED. If it is COUNT_BASED then slidingWindowSize represents the number of requests instead of number of seconds.

* * *

### Step3: Develop the controller and at the handler method add the  @CircuitBreaker(name="greetingClientCB") and alsoe add the fallBackMethod.

* * *
