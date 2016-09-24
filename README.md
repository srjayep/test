[\[Top\]](../README.md)

# Automation of On-boarding process

## Overview

Adobe.io on-boarding process may seem daunting with lots of dependent tasks and moving parts. Since there are a number of components and configuration types
that need to be dealt with on-boarding, one of the goals of automating on-boarding process is to provide a consistent workflow ,developing agreeable standards,
standardize redundant tasks and free resources for other valuable tasks.

It is important to throughly understand the current process with on-boarding before moving or attempt to automate the process.

## General Approach

In the spirit of keeping Adobe.io's on-boarding process interaction as simple as possible, this automation process is organized into tasks.

Refer below diagram for pictorial representation 
[OnboardingAutomationlink]: ./On-boardingAutomation.png?raw

Current manual process and possibility of automation explained below :

```diff
+1. Collect all necessary information required for on-boarding services from customer into  wiki?
2. On-boarding panel discusses services coming on-board
3. Prioritize on-boarding of services based on some magic formula (????)
4. Create EPIC for on-boarding the service  (Can be automated)
5. Assign EPIC to DevOps (Can be automated)
6. EPIC may contain
    > Create VPC-Peering        -   Can be automated
    > DNS-Alaises               -   Can be automated
    > Create new publisher org  -   Can be automated but not aware of any option : Can be an API call
    > Create service,           -   Too complicated to automate
    > Create facade service,    -   Too complicated to automate
    > Create policies,          -   Can be automated but not aware of any option : ??
    > White-listing entries,    -   Can be automated
    > Deploy service to Stage   -   Partial automation can be achieved
    > Deploy service to Prod    -   Partial automation can be achieved
    > Communication to customer -   Manual / Partial automation can be achieved
```

### Requirements for Automation

#### <p style='color:blue'>1.  VPC Peering</p>
```
    (&#x1F535;)- Input
    a. Requires Acceptor and Requestor VPCIDs.
    b. Requires Requestor Account ID
    c. Requires Requestor CIDR Block

    - Validation
    a. Is VPCID given is valid
    b. Is CIDR block given is valid
    c. Is Account number given is valid

    - Action
    a. Create VPC
    b. Email notification

    - Integration
    a. Is VPC created with state 'pending acceptance'
    b. Is Routing entries added to the peering connection
    c. Is Routing entries added are in state 'Black Hole'

    - Acceptance
    a. Is VPC peering state active
    b. Is Port 443 used by Requestor VPCID open for API-GW traffic
    c. Is Acceptor VPC owned instances communicates with Requestor VPC owned load balancer
```
####  2. DNS Aliases
```
     - Input
      a. Requires Hosted domain name
      b. Requires Name of the Alias
      c. Alias DNS name / Load balancer name
      d. Routing policy

     - Validation
      a. Is hosted domain name exists
      b. Is ELB name given valid

     - Action
      a. Create Alias record set
      b. Email notification

     - Integration
      a. Is Alias crated exists in zone record sets

     - Acceptance
      a. Is Alias record resolvable
      b. Is latency acceptable
```

####  3. Create new publisher Org.
```
      - Input
      a. Require name of the Org
      b. Require Gateway collection to associate
      c. Require an ID
      d. Require customer email addresses for invitation

      - Validation
      a. Is Org already exists
      b. Is GW collection to associate exists
      c. Are email addresses valid

      - Action
      a. Create Publisher Org.
      b. Send an invite to email addresses

      - Integration
      a. Is Org created

      - Acceptance
      a. Is Org created exists in GW
      b. Email invitations accepted?
```
####  4. Create a service definition
```
    - Input
      a. Require name of the service
      b. Require context id
      c. Require sub-domain name
      d. Require backend url name
      e. Require IMS env.
      d. Require endpoint
      f. Require endpoint validation method
      g. Require endpoint RestFul method
      h. Require resource name
      i. Custom settings (except for CCEco)

    - Action
      a. Use Portal to create definition

    - Validation
      a. Is Publisher Org exists
      b. Is sub-domain exists and reachable
      c. Is backend url exists and reachable
      d. Is endpoint regex valid
      e. Is endpoint validation method , API key is selected
      f. Is at-least one RestFul method selected

    - Integration
      a. Is service created
      b. Is service ready for publish
      c. Is Resource name exists

    - Acceptance
      a. Is service created valid and ready for publish
```
####  5. Create facade service
```
    - Input
      a. Require name of the service
      b. Require context id
      c. Require sub-domain name
      d. Require backend url name
      d. Require endpoint
      f. Require endpoint validation method
      g. Require endpoint RestFul method
      h. Require resource name
      i. Require custom settings (except for CCEco)

      - Action
        a. Use Portal to create definition

    - Validation
      a. Is Publisher Org exists
      b. Is sub-domain exists and reachable
      c. Is backend url exists and reachable
      d. Is endpoint regex valid
      e. Is endpoint validation method , API key is selected
      f. Is at-least one RestFul method selected

    - Integration
      a. Is service created
      b. Is service ready for publish
      c. Is Resource name exists
      d. Is sub-domain contain service definition's resource-name
      f. Is backend url contains literals

    - Acceptance
      a. Is service created valid and ready for publish
```

####  6.  Create Throttling policy
```
    - Input
      a. Require name
      b. Require description
      c. Require Time
      d. Require span
      d. Require soft & hard limit
      f. Require category & value

      - Action
        a. Use Portal to create definition

    - Validation
      a. Name, Description , Time, Span , Soft & Hard limit should not be empty and contain at-least one category and value .
      a. Time should be in Seconds or Minutes
      b. Category service value should be with in the list

    - Integration
      a. Time should not be more than an hour
      b. Span should not be more than an hour

    - Acceptance
      a. Traffic should not be accepted above threshold values.
```
####  7. Create Routing policy
```
    - Input
      a. Require name
      b. Require description
      c. Require category & value
      d. Require backend

    - Validation
      a. Backend must be valid and reachable

    - Action
      a. Use Portal to create definition

    - Integration
        a. List should contain the category value entered
        b. Backend should be reachable (collect use case from client)

    - Acceptance
      a. Are the requests routing through GW
      b. Is caching latency within the acceptable range
```
####  8. Create White-listing (Application) entries
```
      - Input
        a. Require name
        b. Require description
        c. Require IMS client ID / API key
        d. Require service subscription name

      - Validation
          a. Name, Description , IMS client ID / API key should not be empty
          b. Subscription service name required

      - Action
          a. Use Portal to create definition

      - Integration
              a. Subscription service name must already present

      - Acceptance
        a. <use case required>
```
####    9. Deploy service to Stage
```
      - Input
        a. Require VersionId
        b. Require Context name

      - Validation
        a. Validate VersionId is loaded

      - Action
        a. Deploy configuration to the environment

      - Integration
        a. Deployed Config should exist in S3 bucket
        b. Deployed Config should sync to Ec2-instane from S3
        c. Deployed Config must be free of errors

      - Acceptance
        a. API Call to the GW configuration using service sub-domain must return valid response
        b. API Call to the Service endpoint must return valid response
```
####    10. Deploy service to Prod
```
    - Input
      a. Require service present in GW stage
      b. Require publisher service org name

    - Validation
      a. Validate Publisher org exists in portal

    - Action
      a. Deploy configuration to the environment

    - Integration
      a. Deployed Config should exist in S3 bucket
      b. Deployed Config should sync to Ec2-instane from S3
      c. Deployed Config must be free of errors

    - Acceptance
      a. API Call to the GW configuration using service sub-domain must return valid response
      b. API Call to the Service endpoint must return valid response
```
####  Optional )
```
  11.  Jira ticket automation

    - Input
      a. Require summary
      b. Require description
      c. Require type
      d. Require component
      f. Require project
      g. Require assignee

    - Validate
      a. Summary , description, type , component, project and assignee should not be empty

    - Action
      a. Gojira

    - Integration
      a. List story or issue created present in Jira

    - Acceptance
      a. Story must be assigned to start on on-boarding process with DevOps
  ```

[\[Top\]](../README.md)

#### High-Level View (On-boarding automation process)

![Onboarding Automation][OnboardingAutomationlink]
