# CAMARA Security and Interoperability Profile

## Table of Contents
<!-- TOC -->
* Introduction
* Audience
* Conventions
* General Considerations
    * Transport Security
* OIDC Authorization Code Flow
    * Cross-Site Request Forgery Protection
* Client-Initiated Backchannel Authentication Flow
    * Authentication Request
* Client Credentials Flow
    * Access Token Request
* The Scope Parameter
* Client Authentication
* References
<!-- TOC -->


## Introduction

This document is the CAMARA Security and Interoperability Profile. To ensure interoperability and increased security, this technical specification restricts some options available in OIDC and CIBA, but does not change these standards.

The CAMARA document sharpens the following for interoperability and security: 

* Of the flows defined in OIDC CAMARA uses the [OIDC Authorization Code Flow](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)

* Scope Parameter. Recommendations about the format of the scope parameter used in  CAMARA APIs.

* Client Authentication. Specifications for client authentication within CAMARA.

By encapsulating these elements within this document, it aims to provide a comprehensive guide for developers and operators, ensuring consistent implementation and adherence to standardized security measures across the CAMARA ecosystem. The defined OIDC profile not only facilitates the integration process, but also serves as a basic framework for developers wishing to leverage the CAMARA APIs while maintaining security and interoperability.


## Audience

The target audience for this document is the service/technical departments of operators exposing network functions via standard CAMARA APIs. And applications or client systems that consume CAMARA standard APIs to make use of the operator's network capabilities.


## Conventions

The keywords "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

## General Considerations

### Transport Security
All network connections MUST use TLS 1.2 or better.

## OIDC Authorization Code Flow

The OIDC Authorization Code Flow is defined in [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)

### Cross-Site Request Forgery Protection

CAMARA REQUIRES cross-site request forgery protection.

CAMARA RECOMMENDS PKCE for CSRF protection. 
CAMARA Authorization Servers MUST implement PKCE. If PKCE is not used by the Client then the CAMARA AZ must handle **state** and **nonce** as defined in OAuth2.

CAMARA Clients SHOULD use PKCE.

If nonce for CSRF-protection is used then implementers must ensure that sufficient entropy is present in the nonce value.
Please see [OAuth 2.0 Security Best Current Practice](https://oauthstuff.github.io/draft-ietf-oauth-security-topics/draft-ietf-oauth-security-topics.html#name-protecting-redirect-based-f).

## Client-Initiated Backchannel Authentication Flow

The [Client-Initiated Backchannel Authentication (CIBA)](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html) flow is used to initiate an out-of-band authentication of the end-user.

Communication with the Backchannel Authentication Endpoint MUST utilize TLS.

CIBA allows the Client to get the authentication result in three ways: poll, ping, or push. This profile allows clients to use the *poll* mode. In the Poll mode, the authentication result is retrieved by the Client by polling the OP's token endpoint using the new grant type. 

### Authentication Request

This CAMARA document makes the login_hint parameter REQUIRED if CIBA is used in a CAMARA context.

This CAMARA document clarifies the values used in login_hint in the following way:

    * **_tel_**

      For phone numbers. The `login_hint` must be a tel URI as defined in [RFC 3966](https://www.rfc-editor.org/info/rfc3966) for global phone numbers without visual separators in [E.164](https://www.itu.int/rec/T-REC-E.164-201011-I/en) format. For example, `tel:+34666666666`.

    * **_ipport_**

      For IPv4 and IPv6 addresses, that can optionally include a port. For example, `ipport:80.90.34.2:16790`, `ipport:80.90.34.2`, `ipport:[2001:db8::1]:8080` or `ipport:[2001:db8::1]`.


The client MUST authenticate with the authorization server as described in [Client Authentication Section](#client-authentication).

## Client Credentials Flow

The [OAuth 2.0 Client Credentials](https://datatracker.ietf.org/doc/html/rfc6749#section-4.4) grant type is used to obtain a 2-legged Access Token that does not represent a user. This grant type can only be used when no personal user data is processed, and it is only a valid option to access the CAMARA APIs for these specific scenarios.


### Access Token Request

This CAMARA document makes the scope parameter REQUIRED for the OAuth2 Client Credentials Grant.

The client MUST authenticate with the authorization server as described in [Client Authentication Section](#client-authentication).

## The Scope Parameter

Scope values determine the specific CAMARA services being requested by the Service Provider, subject to the SP being registered to use those services. The scope values must be documented in the API OAS files by all Camara API subprojects.

How to apply the concept of purpose with scope can be found in [Section Purposes of CAMARA APIs access and user consent management](https://github.com/camaraproject/IdentityAndConsentManagement/blob/main/documentation/CAMARA-API-access-and-user-consent.md).

## ID Token sub claim

The subject identifier must be globally unique identifier and is a case-sensitive ASCII string with a maximum length of 255. The sub value MUST not contain the MSISDN. Within Camara this is populated by a Pseudonymous Customer Reference (PCR) which is a system-facing unique identifier that links a User’s MSISDN to a ASP. It is generated by the OP.

## Client Authentication

This CAMARA document allows **one** client authentication method, private_key_jwt, as defined in OIDC
[OIDC Client Authentication](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication)
[JSON Web Token (JWT) Profile for OAuth 2.0 Client Authentication and Authorization Grants](https://www.rfc-editor.org/rfc/rfc7523.html#section-2.2)

## Sender-constraint Access Token

This CAMARA document RECOMMENDS sender-constraint access tokens.

### DPoP Requirements for Camara Clients

Camara clients SHOULD utilize DPoP as described in [RFC9449].

If Camara clients use DPoP, they shall support the server provided nonce mechanism (as defined in Section 8 of [RFC9449])

### DPoP Requirements for Camara Authorizatin Servers

Camara Authorization Servers MUST support DPoP as described in [RFC9449].

### DPoP Requirements for Resource Servers

Camara Resource Servers MUST support and verify sender-constrained access tokens using DPoP as described in [RFC9449].

## References

* [Data Privacy Vocabulary (DPV)](https://w3c.github.io/dpv/dpv/)
* [E.164 - The international public telecommunication numbering plan](https://www.itu.int/rec/T-REC-E.164-201011-I/en)
* [OpenID Connect Core 1.0 specification](https://openid.net/specs/openid-connect-core-1_0.html)
* [OpenID Connect Client-Initiated Backchannel Authentication Flow - Core 1.0](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html)
* [RFC 2617 - HTTP Authentication: Basic and Digest Access Authentication](https://www.rfc-editor.org/info/rfc2617)
* [RFC 3986 - Uniform Resource Identifier (URI): Generic Syntax](https://www.rfc-editor.org/info/rfc3986)
* [RFC 3966 - The tel URI for Telephone Numbers](https://www.rfc-editor.org/info/rfc3966)
* [RFC 6749 - The OAuth 2.0 Authorization Framework](https://www.rfc-editor.org/info/rfc6749)
* [RFC 7515 - JSON Web Signature (JWS)](https://www.rfc-editor.org/info/rfc7515)
* [RFC 7519 - JSON Web Token (JWT)](https://www.rfc-editor.org/info/rfc7519)
* [RFC 7521 - Assertion Framework for OAuth 2.0 Client Authentication and Authorization Grants](https://www.rfc-editor.org/info/rfc7521)
* [RFC 7523 - JSON Web Token (JWT) Profile for OAuth 2.0 Client Authentication and Authorization Grants](https://www.rfc-editor.org/info/rfc7523)
* [RFC 8259 - The JavaScript Object Notation (JSON) Data Interchange Format](https://www.rfc-editor.org/info/rfc8259)
* [RFC 8414 - OAuth 2.0 Authorization Server Metadata](https://www.rfc-editor.org/info/rfc8414)
* [RFC 9449 - OAuth 2.0 Demonstrating Proof of Possession (DPoP)](https://www.rfc-editor.org/info/rfc9449)

