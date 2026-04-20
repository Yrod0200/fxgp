# FXGP/1.1: Federated Extensible Guild Protocol


# 1. Introduction
Federated eXtensible Guild Protocol (FXGP) is a TCP-based communication protocol for real-time online messaging. It supports federation between Servers and dynamic extensibility via GuildScript (.fxgs, .fxgh).  

The FXGP defines rules for:

- Client-Server communication
- Optional End-to-end content cryptography 
- Guild-based information exchange
- Extensible, module-based configuration

## 1.1 Transport
Transfer Protocol: TCP
Communication: Full-duplex
Encoding: JSON

## 1.2 Terminology

Server: An instance that manages users, guilds and the interaction between them, while interpreting .fxgs files.

Client: An application that connects to a Server and allows the user to interact with guilds.

Guild: A logical and organized space that allows people to communicate, it SHOULD include:
- Category separated channels, where the connected users can send, reply and react to messages;
- A role-based permission system with hierarchy, where specified user groups can do configured things, such as sending messages, ban users and delete messages;
- Publicly available Server metadata, including: name, description, user amount and Server UUID;
- A valid authentication system, which can validate user sessions.
- Capability to be expanded in any way with GuildScript

User: An authenticated entity with a unique FXID.


FXID: A globally and unique identifier used for user authentication and moderation across all FXGP federated Servers.

Federation: Interaction between multiple FXGP Servers.

Hook: A GuildScript-defined programmable event triggered by the Server when a relevant action occurs.

GuildScript: A key-based markup language with scripting capabilities, used by the Server to configure and extend functionality.

Module: A GuildScript file which contains configurations and/or extra functionalities for the Server

Session: A data structure with expiration that confirms whether the Client is connected to the Server 



# 2. Connection Lifecycle


## 2.1 - Disconnected
The Client is not connected.

## 2.2 - Connected (Pre-Auth) 

The Client SHOULD request public Server information.

Request type:

    PUBLICINFO

Response:

    Server metadata, such as:
    - Name
    - Description
    - UUID
    - User Count

    The Server could satisfy the request with additional info, which MAY or MAY not be ignored by the Client.

## 2.3 - Authenticating

Request type:
    
    CONNECT

Payload:

    FXID

Behavior:

    The Server MAY require additional authentication steps (e.g., captcha or password)



    Authentication logic MAY be expanded via GuildScript


Response:

    FXID

    Initial Server-wide user data (if not previously assigned)

    User session validation

## 2.4 - Authenticated

In this step, the Client MAY execute:

- LIST

- JOIN_GUILD


## 2.5 - In Guild

After JOIN_GUILD, the Client enters a guild context, where various actions MAY be performed (considering permissions), such as:

    - MESSAGE

    - REACT

    - REPLY

    - DELETE_MESSAGE

    - BAN_USER

    - MUTE_USER

New actions MAY be added via modules.


# 3. Identity Model


## 3.1 - FXID


Each user has a FXID. It is composed of:
    - UUID
    - An issuer (the Server that generated the UUID)
    - A signature 

## 3.2 - FXID Signature

The FXID MUST include a signature.

The signature MUST be generated using a Server private key over the UUID field.

The signature MUST be verified by other Servers using the public key 



## 3.3 - Trust Model

A FXGP Server MUST maintain:
    - A list of trusted Servers with their associated public keys.

To verify a requesting user, the Server MUST:
    - Validate the FXID structure
    - Verify the signature using the issuer's public key
    - Ensure the issuer is in the trusted Server list


If the FXID can't be verified, the connection MUST be closed.

## 3.4 - FXID Format

The FXID format SHOULD be standardized by the following formula:

UUID@issuer::signature

Example:
    - 550e8400-e29b-41d4-a716-446655440000@auth.fxgp.org::MEUCIQDf8v7kX9l3Y2exampleFakeSignatureBase64k3l9s2QIgY8exampleFakeSignature==

Or in JSON:

 ```
    {
  "uuid": "550e8400-e29b-41d4-a716-446655440000",
  "issuer": "auth.fxgp.org",
  "signature": "MEUCIQDf8v7kX9l3Y2exampleFakeSignatureBase64k3l9s2QIgY8exampleFakeSignature=="
    }
 ```

## 4. Extensibility

FXGP supports dynamic extensibility via .fxgs (GuildScript) scripting

## 4.1 - Properties Expansion

Custom fields MAY be added to:

    - Users, such as: PFP, COLOR, PRONOUNS
    - Guilds, such as: TAGS, AGE GROUPS
    - Messages, such as: MESSAGE_TYPE, DELETE_TIME
    - Roles, such as: ROLE_COLOR, ROLE_OWNER
    - Channels, such as: CHANNEL_TYPE

## 4.2 - Hooks

Hooks are triggered when something relevant happens, for example deleting a message, which could be used for:
    - Moderation
    - Automation
    - Interaction




# 5. Bots

FXGP does not define bots as separated or inherited entity.

Thus, all automation SHOULD be implemented from scripts.

# 6. Federation

FXGP supports federated communication between Servers.

Federation Enables:
    - Cross-Server identity validation
    - Shared user identity via signed UUIDs.

Servers MUST verify:
    - Identity signatures
    - Trust relationships with issuing Servers

# 7. Versioning 

FXGP version MUST be included in communication and scripting, following the FXGP/Version formula.

Future versions MUST be backwards compatible where possible.


# 8. Licensing 

the FXGP protocol is licensed under the GNU General Public License v3,

Additional terms MAY apply as described in the FXGP Additional Terms document.




