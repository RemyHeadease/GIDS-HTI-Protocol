# GIDS Health Tools Interoperability 
HTI:core version 0.3

Document version: 0.3  
Date: 22-6-2020  

## Goals and Rationale
The GIDS Health Tool Interoperability protocol (HTI) is inspired by the IMS - Learning Tool Interoperability (LTI) which has had a tremendous proven impact on the relation 
between learner management systems and learning tool providers. The objectives of LTI are: 
 1. To provide a mash-up style deployment model which is easy to configure by URL, key and secret. 
 1. To define a protocol that allows for SSO which preserves the learning context and roles within that context. 
 1. To make links to external applications portable by defining shared data elements.

LTI has simplified the integration of external tools into learner management systems, and a bright new landscape of tool providers has emerged. 
The key concepts the LTI being successful has been:
 1. The core the LTI standard is simple and clear.
 1. The LTI standard in its core form is easy to integrate because it makes use of existing technologies and standards.
 1. The core standard can be extended by profiles; within LTI there are profiles for reading roster information and writing results.

The HTI standard applies these concepts when it comes to defining a successful launch protocol. The key differences are:
 1. Use of JWT instead of OAuth 1.x. JWT is an IETF standard and is more advanced than OAuth 1.x.
 1. Aligned to the, in healthcare widely used, HL7 FHIR standard.
 1. The launch message contains no personal data.
 1. More restrictions on security, privacy and sharing data.
 
The HTI standard has the following goals.

#### Ease of software implementation. 
The protocol should be easy to implement. This is done by making use of a simple core specification and allowing for more complexity and additional functionality in the form of
profiles, and the use of existing standards. 
#### Ease of use and configuration. 
The protocol should be easy to configure from both the module and portals' side. In essence, an exchange of endpoint URL and public key pair should be sufficient.
#### Scalable, decentral, and point to point.
The architecture should not rely on external or central services and should be point-to-point in the sense that parties should be able to connect without relying on other 
parties and it should scale infinitely.
#### Secure
The protocol should mitigate against most common attacks by using proven technologies like JWT and JWE.
#### Privacy by Design
The protocol encourages user consent when exchanging personal information and is designed to minimize the exchange of personal information.
#### Align with existing standards
The standard makes use of existing technologies and standards, such as JWT, JWE and HL7 FHIR.


## About this document
This document makes use of the Requirement Levels as defined in [rfc2119](https://www.ietf.org/rfc/rfc2119.txt), commonly referred to as the MoSCoW method. A summary of the MoSCoW requirement can be found in the 
section  Implementation checklist. We have tried to make use of illustrations to visually enrich the concepts and relations between concepts of the standard. Examples are used 
whenever possible. Besides this document, a testsuite is made available, and reference implementations are published to github to help developers in their implementation journey. 

## Architecture
The health tool interoperability standard (HTI) connects portals with modules like e-health treatments, (serious)games of any other type of functionality. This standard defines 
the concept of launch that entails both a transition from the portal to the module and a start of a new session on the module’s side. The HTI:core standard defines the 
communication protocol. the message format and the message exchange that is required to start a module from a portal in a domain. 

![](images/HTI%20Architecture.png)

### Concepts
 * Portal, the service that links to the module, that is, an application like a tool, a game, or a treatment.
 * Module provider, the service that delivers applications like tools, games, and/or treatments to the portal.
 * Message, the package exchanged between portal and module that contains the relevant launch information. 
 * Domain, the scope of the data shared in the launch.

### Domain model
The launch message consists of a task object. The task object consists of the following fields:
 * A reference to the user.
 * A reference to the definition of the task.
Schematically, the relationship between the entities is displayed below:

![](images/Domain%20model.png)

### Assumptions
There are a number of assumptions on which the HTI:core standard is based. These assumptions are of relevance to the implementation of the standard as they function both as 
the starting point and as guidance to the (technical) decisions made in the realization of this standard.

#### Personal data
First and foremost, the message must not contain any personal information. The message does contain a reference to the user and the task at hand, but this reference must never 
be traceable to the real user. The user MUST be identified by a persistent pseudo identifier. The identifier MUST be unique in the domain. The portal MUST use a different 
identifiers for a user in each domain. The message MUST NOT contain any other identifier like a name, email account or social identification number. The persistent pseudo 
identifier MUST be randomly chosen and MUST contain enough entropy to block brute-force attacks.

![](images/Persistent%20pseudo%20identifiers.png)

#### Self-managed identities
As a consequence of the fact that no personal data may be exchanged by the launch, the module provider MAY query the user for information it needs. This data MAY be linked to the 
persistent pseudo identifier, so the user does not have to provide the same information again. This way the user controls what information it provides to the module provider on 
module launch.

![](images/SSI%20example%201.png)

![](images/SSI%20example%202.png)

![](images/SSI%20example%203.png)

#### Domains and scope of the persistent user identifier
The user identifier **MUST** be unique in each domain, and **MUST NOT** be shared between domains. If the relations between portal and module are between the same two legal 
entities **and** if there is a clear need to link the users identities between those relations **then** the portal **MAY** use one domain for multiple relations of the same user. 
Otherwise each relation **MUST** have each own domain. 

![](images/Domain%20scope.png)

#### User consent 
The HTI:core specification specifically prohibits the exchange of personal data, however specific profiles that extend the HTI:core standard are allowed to exchange personal data. 
Thereby the HTI:core standard states that, If one of the systems in the domain transfers information from one system to another system in the domain, the user **MUST** provide 
consent. When asking consent ,the user **MUST** be informed about all of the following:
 * The source of the data.
 * What data is shared.
 * With whom it will be shared.
 * For what period the data will be shared.
 * For what purpose the data will be shared.
The user **MUST** be informed in a short and straightforward message, that **MUST** be in understandable language of maximum B1 level of the CEFR framework. Preferably the users’ 
primary language SHOULD be used.  It MUST be clear to the user what is asked and for what purpose.
Please note that the notion of a domain does not imply that consent can be shared between module providers and portals with domain level consent. 

#### Profiles
The HTI:core standard defines the core part of the standard, consisting of the module launch. The HTI:core standard MAY be extended with profiles. These profiles MAY do the following:
 * ***Extend*** the specification by adding or modifying fields to the existing data model.
 * ***Replace*** specific parts of the standard, such as the way information is exchanged between systems.
 * ***Define*** additional functions, such as the exchange of information outside the scope of the launch.
A HTI profile MUST be documented properly and be the profile MUST clear about the relationship between HTI:core and the profile. The profile MUST identify themselves with a namespaced identifier, starting with “HTI:”, for example: HTI:smart_on_fhir. The HTI core standard is identified by HTI:core. It is encouraged to create profiles as specific as possible, and create multiple profiles if necessary.

#### To summarize:
 * The launch message must not contain personal data.
 * The module system may identify the user by its own means.
 * All involved must have explicit users’ consent if personal data is disclosed to other parties.
 * The standard may be extended with profiles, the core specification is defined as HTI:core.
 
 ## Implementation guide
The HTI:core standard defines the exchange of a message from the portal to the module. This exchange consists of the following parts:
 * ① The contents of the message: the FHIR task object.
 * ② The serialization of the message into the JWT message format.
 * ③ The exchange of the message, how it is exchanged between the portal and the module.

![](images/Implemention%20steps.png)

### Semantic roles and responsibilities
As the HTI:core specification exists of three parts, these parts each have different roles and responsibilities. These are:
 * The ① FHIR task object MUST only contain information about the functional task, the definition of the task, and the people involved. 
 * The ② JWT message MUST only contain information about the sending system, the recipient system and the message itself.
 * The ③ exchange of the message MUST NOT contain any information that falls in the categories defined by ① and ②. For example, it is not allowed to refer to a treatment by encoding one in the launch URL.
The diagram below summarizes these concepts.

![](images/Semantic%20roles.png)

### ① The FHIR task object
The message consists of a FHIR Task resource. This resource is part of the FHIR STU3 standard and documented [here](https://www.hl7.org/fhir/STU3/task.html). Please note that the STU3 MUST be used, not the latest version.

#### Identifiers and references
By the FHIR standard, each object has a type (resourceType) and identifier (id). When an object is serialized, the id and type are required fields, the diagram below show an example, the resource type and identifier are displayed in the next example:

```json
{
    "resourceType": "Task",
    "id": "a5e57fd0"
}
```
A reference to an object consists of a combination of type and identifier. References to objects in the FHIR standard are notated as follows:
```
resourceType/id
```
In the example below, show an example of such a reference as `ActivityDefinition/a5e58200`:
```json
{
    "resourceType": "Task",
    "id": "a5e57fd0",
    "definitionReference": {
      "reference": "ActivityDefinition/a5e58200"
    }
  }
```
The HTI:core standard defines that the reference format MUST align with the FHIR reference format.

The HTI standard defines different ways of serialization, in the HTI:core standard the encoding MUST be JSON. For the exchange of FHIR resources in other ways than the FHIR Task object as part of the launch, as defined by the HTI:core, we encourage to define a profile. By requiring the encoding of the FHIR task to be JSON, HTI:core is aware of the fact that it is ahead of the decision to make use of the FHIR Rest API and JSON serialization.

#### Mapping of the FHIR task
Conceptually, the FHIR task is mapped to the domain concepts as follows:

| FHIR Field | Mapping |
| ------------- | ------------- |
| id  | A reference to the instance of the task.  |
| activity definition  | A reference to the definition of the task  |
| for  | A reference to the type and persistent pseudo identifier of the user.  |

The table below contains an exclusive list of fields, no additional fields from the STU3 FHIR specification are allowed in the HTI:core standard. If you intend to use any additional fields from the STU3 FHIR standard, you MUST specify a HTI profile to do so.

| FHIR task field | Required | Value |
| ------------- | ------------- | ------------- |
| resourceType | yes | This field should always be populated with the value "Task". |
| id  | yes  | The identifier of the task. More details are described in the [Task id](#the-task-id) section. |
| definitionReference.reference | no | A reference to the task definition. More details are described in the [task definition] section. |
| for | yes | A reference to the user responsible for the task. More details are described in the [person reference] section. |
| intent | yes | This field must be populated with a value from the FHIR value set RequestIntent, if not applicable, use "plan". |
| status | yes | Status of the task. This field must be populated with one of the values defined by the [FHIR value set TaskStatus](https://www.hl7.org/fhir/STU3/valueset-request-intent.html). |

An example of the resulting FHIR task object:

```json
{
    "resourceType": "Task",
    "id": "a5e57fd0",
    "definitionReference": {
      "reference": "ActivityDefinition/a5e58200"
    },
    "for": {
      "reference": "Person/a5e5844e"
    },
    "intent": "plan",
    "status": "requested"
}
```

##### The task id
The task id is a reference to the instance of the task. In relation to the task definition, the task id changes when a user does the same task in a different context or for the second time. The task definition does not. The task id MUST be persistent over the timeframe the task instance is active. The task identifier is an identifier and not a reference, it does not need the resource type to be prepended.

##### The task definition
The reference to the task definition is a reference to the type of task that should be performed. What a task definition means, depends on the context. The reference refers to the type of task. In the case of e-health, the task definition could refer to a e-health treatment. For example, the "Fearfighter" module that offers help to people with phobia, anxiety disorder, or panic disorder. As the reference to the task definition, the reference type MUST be ‘ActivityDefinition’, as a result of this, the reference must  have the format:
```
ActivityDefinition/<Identifier>
```
The task definition is not a required field. However we discourage the omittance of the field, there are scenarios we want to support that do not require a task definition to be present in the launch.

##### Person reference
When referring to persons in the FHIR task object, please keep in mind that FHIR does allow personal details such as e-mail addresses and displayname as part of the FHIR standard, however the HTI:core standard explicitly forbids the personal data to be exchanged by the launch. The user MUST be identified by a persistent pseudo identifier.
The `for` field SHOULD be used for the person for who is actively participating in the launch and is responsible for performing the task. The `for` field is a reference that consists of both resource type and identifier. This implies that the format MUST be:
```
<ResourceType>/<Identifier>
```
The resource type MUST be a FHIR resource type, the FHIR task object does not limit the for field reference to a specific subset of FHIR resource types, so the resource type MAY be any of, and not limited to, the following types:
 * Patient
 * Practitioner
 * RelatedPerson
 * Person
We advise to use the Person type if unsure about the resource type of the `for` field.

Please note that the [3rd party launches](#3rd-party-launches) profile supports launches to tasks that are owned by other persons than the person referred to by the `for` field by making use of the owner field.

#### Configuration and storage requirements
The portal has the following storage and/or configuration requirements:
 * The activity definition (s) of the module (s) need to be configured, having an agreed upon identifier, and optionally the name, image and description. To do so, the FHIR ActivityDefinition object MAY be used.
 * The task id from the FHIR object MUST represent a specific instance of an activity definition performed by a user and MUST be persistent over the timeframe the task instance is active.
 * The user identifier (for field) MUST be both unique and persistent in each domain.

The module provider has the following storage and/or configuration requirements.
 * The activity definition should be agreed upon with the portal application.
 * Any additional information about the user attached to the persistent pseudo identifier, as the FHIR task that is exchanged at the launch is not to contain any personal data.

### ② The message format
The FHIR task object MUST be exchanged as part of a JWT token. The FHIR task object MUST be serialized as JSON and included in the nested field "task". The mapping of the JWT fields is as follows.

| Description | Field | Value |
| ------------- | ------------- | ------------- |
| Issuer | iss | This field MUST be a reference to the portal application that creates the JWT token. It MAY consist of an url or domain name of the portal application. The portal application and module provider MUST agree on the value of the iss field. The module provider MUST validate the message with the public key associated with the iss reference. The portal application MAY disseminate its public keys by the JKWS protocol, in that case the JWT token MUST contain a kid field in the JWT header. |
| Audience | aud | This field MUST be a reference to the module provider for which the JWT token is created for. The reference MAY consist of an url or domain name of the module provider. The portal application and module provider MUST agree on the value of the aud field. The module provider MUST validate the aud field to have the expected value. |
| Unique message id | jti | A unique identifier for this message. This value MUST be treated as a NONCE, a subsequent message with an identical jti MUST be rejected. The jti value must be a random or pseudo number, the jti MUST contain enough entropy to block brute-force attacks. |
| Issue time | iat | The timestamp of generating the JWT token, the value of this field MUST be validated by the module provider to not be in the future. |
| Expiration time | exp | This value MUST be the time-out of the exchange sending it to the client plus the time-out of the exchange used by the client to send it, the value MUST be limited to 5 minutes. This value MUST be validated by the module provider, any value that exceeds the timeout MUST be rejected. |
| Task | task | The FHIR Task object in JSON format. |

#### Example message
```json
{
  "task": {
    "resourceType": "Task",
    "id": "11",
    "definitionReference": {
      "reference": "ActivityDefinition/8"
    },
    "status": "requested",
    "intent": "plan",
    "for": {
      "reference": "Patient/9"
    }
  },
  "iat": 1585564845,
  "aud": "https://module.example.com",
  "iss": "https://portal.example.com",
  "exp": 1585565745,
  "jti": "679e1e4c-bcb9-4fcc-80c4-f36e7063545c"
}
```

#### Message layout
The JWT standard is documented at [jwt.io](https://jwt.io), we refer to the JWT documentation on how to create a JWT token. The figure below displays the JWT token from a conceptual level.

![](images/JWT%20message%20structure.png)

#### Additional security restrictions
 * The JWT MUST use an asymmetric public / private key to sign the JWT tokens. The public key MUST be made available to the module provider, the private key MUST remain private on the portal infrastructure. As stated before, the public key MAY be disseminated by the JWKS protocol. The use of shared secrets is not allowed, because the issuer of the JWT cannot guarantee ownership as the key is shared.
   * The signing MUST make use of an asymmetric algorithm, so all HMAC based algorithms,  algorithms starting with HS, are not allowed).
   * The portal MAY use any of the asymmetric algorithms, the module MUST support at least the following:
     * RS256, RS384, and RS512
     * ES256, ES384, and ES512
 * The JWT token MUST on both the platform as on the module provider side be exchanged over an encrypted connection.

#### Configuration and storage requirements
 * The portal needs to configure the following.
 * The private key for message signing, the public key must be shared with the module provider.
The module provider needs to configure the following
 * The public key of the portal associated with the iss field value for message validation.
 * The jti MUST be validated and stored for replay detection.

![](images/JWT%20signing%20keys.png)

### ③ The message exchange
By the HTI:core specification, the message MUST be posted to the module application as part of a form encoded POST request (application/x-www-form-urlencoded). The token MUST be placed in the “token” field. Additional HTI profiles MAY define alternative means of exchanging the JWT token. The portal SHOULD use the form-post-redirect pattern to exchange the token via the client’s browser. This pattern works by rendering a form on the client's browser that contains the token as a hidden field. This form is submitted by javascript. This exchange MUST be done over the https protocol only.  

Example code

```html
<html>
<head>
</head>
<body onload="document.forms[0].submit();">
<form action="https://module.provider.eu/modules/x" method="post">
<input type="hidden" name="token" value="eyJhbGciO..."/>
</form>
</body>
</html>
```

#### Validation and error conditions
The module provider MUST validate the JWT message according to the rules defined by  ① The FHIR task object and ② The Message format. The form-post-redirect exchange does not allow for any feedback to the portal application. In case the JWT message is invalid, the user MUST be presented with a human understandable error message, the module provider MAY provide technical details, but MUST  be moderate in providing technical details that expose the implementation of the validation system. Besides presenting a human readable error message to the user, the portal application MAY use an http status code in the 400 range (Client error responses). The portal application SHOULD implement a logging facility that makes the validation errors insightful to the application maintainers, and MAY provide the human readable error messages on the screen with an error message code that corresponds to the log message in the application logging facility.
#### Launch configuration requirements
The portal needs to configure the following.
 * The endpoint URL of the module from the module provider.

### Putting it all together
The diagram below displays an overview of all the steps of the HTI launch.

![](images/HTI%20interaction%20diagram.png)

