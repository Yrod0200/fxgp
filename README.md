# FXGP/1.0: Federated Extensible Guild Protocol


# 1. Indroduction
Federated eXtensible Guild Protocol (FXGP) is a TCP-based communication protocol for real-time online messaging. It supports federation between servers and dynamic extensibility via GuildScript (.fxgs, .fxgh).  

The FXGP defines rules for:

- Client-server communication
- Optional End-to-end content cryptography 
- Guild-based information transferring
- Extensible, module-based configuration

## 1.1 Transport
Transfer Protocol: TCP
Communication: Full-duplex
Encoding: JSON

## 1.2 Terminology

Server: An instance that manages users, guilds and the interaction between them, along interpreting .fxgs files.

Client: An application that connects to a server and allows the user to interact with guilds.

Guild: A logical and organized space that allows people to communicate, it should include:
- Category separated channels, where the connected users can send, reply and react to messages;
- A role-based permission system with hierarchy, where specified user groups can do configured things, such as sending messages, ban users and delete messages;
- Publicly available server metadata, including: name, description, user amount and server UUID;
- A valid authentication system, which can validate user sessions.

User: An authenticated entity with a unique global UUID.

Federation: Interaction between multiple FXGP servers.

Hook: A GuildScript-defined programmable event triggered by the server when a relevant action occurs.

GuildScript: A key-based markup language with scripting capabilities, used by the Server to configurate and extend functionality.

Module: A GuildScript file which contains configurations and/or extra functionalities for the server

Session: A expirable data structure that can confirm whether the client is connected to the server 



# 2. Connection Lifecycle


## 2.1 - Disconnected
The client is not connected.

## 2.2 - Connected (Pre-Auth) 

The client should request public server information.

Request type:

    PUBLICINFO

Response:

    Server metadata, such as:
    - Name
    - Description
    - UUID
    - User Count

    The server could satisfy the request with additional info, which may or may not be ignored by the client.

## 2.3 - Authenticating

Request type:
    
    CONNECT

Payload:

    UUID (if previously assigned)

Behavior:

    The server may require additional authentication steps (e.g., captcha or password)

    Authentication logic may be expanded via GuildScript


Response:

    Global User UUID

    Initial server-wide user data (if not previously assigned)

    User session validation

## 2.4 - Authenticated

In this step, the client may execute:

- LIST

- JOIN_GUILD


## 2.5 - In Guild

After JOIN_GUILD, the client enters in a guild context, where various actions can be executed (considering permissions):

    - MESSAGE

    - REACT

    - REPLY

    - DELETE_MESSAGE

    - BAN_USER

New actions may be added via modules.


# 3. Identity Model


## 3.1 - Global UUID

Each user has:
    - A unique UUID
    - An issuer (the server that signed the Identity)

Format:
    <uuid>@<issuer>

## 3.2 - Signature

User identity must include a digital signature:

Fields:
    - UUID
    - Issuer
    - Signature

The signature is generated using the server private key, thus can be verified by other servers using the matching pulbic key.

## 3.3 - Trust Model

A FXGP Server MUST mantain:
    - A list of trusted server with their associated pubkeys.


## 4. Extensibility

FXGP supports dynamic expansibility via .fxgs (GuildScript) scripting

## 4.1 - Properties Expansion

Custom fields may be added to:

    - Users, such as: PFP, COLOR, PRONOUNS
    - Guilds, such as: TAGS, AGE GROUPS
    - Messages, such as: MESSAGE_TYPE, DELETE_TIME
    - Roles, such as: ROLE_COLOR, ROLE_OWNER
    - Channels, such as: CHANNEL_TYPE

## 4.2 - Hooks

Hooks are triggered when something relevant happens, which could be used for:
    - Moderation
    - Automation
    - Interaction


# 5. Bots

FXGP does not define bots as separated or inherited entity.

Thus, All automation MUST be implemented from scripts.

# 6. Federation

FXGP supports federated communication between servers.

Federation Enables:
    - Cross-server identity validation
    - Shared user identity shared via signed UUIDs.

Servers MUST verify:
    - Identity signatures
    - Trust relationships with issuing servers


# 7. Versioning 

FXGP version MUST be included in communication and scripting, following the FXGP/<Version> formula.

Future versions MUST be retrocompatible where possible.




