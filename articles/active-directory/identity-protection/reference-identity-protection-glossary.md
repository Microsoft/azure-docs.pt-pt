---
title: Azure Active Directory Identity Protection Glossary
description: Azure Active Directory Identity Protection Glossary
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232349"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory Identity Protection Glossary

### <a name="at-risk-user"></a>At risk (User)
A user with one or more active risk detections. 

### <a name="atypical-sign-in-location"></a>Atypical sign-in location
A sign-in from a geographic location that is not typical for the specific user, similar users, or the tenant.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
A security module of Azure Active Directory that provides a consolidated view into risk detections and potential vulnerabilities affecting an organization’s identities.

### <a name="conditional-access"></a>Acesso Condicional
A policy for securing access to resources. Conditional Access rules are stored in the Azure Active Directory and are evaluated by Azure AD before granting access to the resource.  Example rules include restricting access based on user location, device health, or user authentication method.

### <a name="credentials"></a>Credenciais
Information that includes identification and proof of identification that is used to gain access to local and network resources. Examples of credentials are user names and passwords, smart cards, and certificates.

### <a name="event"></a>Evento
A record of an activity in Azure Active Directory.

### <a name="false-positive-risk-detection"></a>False-positive (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the risk detection was investigated and was incorrectly flagged as a risk detection.

### <a name="identity"></a>Identidade
A person or entity that must be verified by means of authentication, based on criteria such as password or a certificate.

### <a name="identity-risk-detection"></a>Identity risk detection
Azure AD event that was flagged as anomalous by Identity Protection, and may indicate that an identity has been compromised.

### <a name="ignored-risk-detection"></a>Ignored (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the risk detection is closed without taking a remediation action.

### <a name="impossible-travel-from-atypical-locations"></a>Impossible travel from atypical locations
A risk detection triggered when two sign-ins for the same user are detected, where at least one of them is from an atypical sign-in location, and where the time between the sign-ins is shorter than the minimum time it would take to physically travel between these locations.  

### <a name="investigation"></a>Investigação
The process of reviewing the activities, logs, and other relevant information related to a risk detection to decide whether remediation or mitigation steps are necessary, understand if and how the identity was compromised, and understand how the compromised identity was used.

### <a name="leaked-credentials"></a>Leaked credentials
A risk detection triggered when current user credentials (user name and password) are found posted publicly in the Dark   web by our researchers.

### <a name="mitigation"></a>Mitigação
An action to limit or eliminate the ability of an attacker to exploit a compromised identity or device without restoring the identity or device to a safe state. A mitigation does not resolve previous risk detections associated with the identity or device.

### <a name="multi-factor-authentication"></a>Autenticação multifator
An authentication method that requires two or more authentication methods, which may include something the user has, such a certificate; something the user knows, such as user names, passwords, or pass phrases; physical attributes, such as a thumbprint; and personal attributes, such as a personal signature.

### <a name="offline-detection"></a>Offline detection
The detection of anomalies and evaluation of the risk of an event such as sign-in attempt after the fact, for an event that has already happened.

### <a name="policy-condition"></a>Policy condition
A part of a security policy, which defines the entities (groups, users, apps, device platforms, Device states, IP ranges, client types) included in the policy or excluded from it.

### <a name="policy-rule"></a>Policy rule
The part of a security policy that describes the circumstances that would trigger the policy, and the actions taken when the policy is triggered.

### <a name="prevention"></a>Prevenção
An action to prevent damage to the organization through abuse of an identity or device suspected or know to be compromised. A prevention action does not secure the device or identity, and does not resolve previous risk detections.

### <a name="privileged-user"></a>Privileged (user)
A user that at the time of a risk detection, had permanent or temporary admin permissions to one or more resources in Azure Active Directory, such as a Global Administrator, Billing Administrator, Service Administrator, User administrator, and Password Administrator. 

### <a name="real-time"></a>Real-time
See Real-time detection.

### <a name="real-time-detection"></a>Real-time detection
The detection of anomalies and evaluation of the risk of an event such as sign-in attempt before the event is allowed to proceed.

### <a name="remediated-risk-detection"></a>Remediated (risk detection)
A risk detection status set automatically by Identity Protection, indicating that the risk detection was remediated using the standard remediation action for this type of risk detection. For example, when the user password is reset, many risk detections that indicate that the previous password was compromised are automatically remediated.

### <a name="remediation"></a>Remediation
An action to secure an identity or a device that were previously suspected or known to be compromised. A remediation action restores the identity or device to a safe state, and resolves previous risk detections associated with the identity or device.

### <a name="resolved-risk-detection"></a>Resolved (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the user took an appropriate remediation action outside Identity Protection, and that the risk detection should be considered closed.

### <a name="risk-detection-status"></a>Risk detection status
A property of a risk detection, indicating whether the event is active, and if closed, the reason for closing it.

### <a name="risk-detection-type"></a>Risk detection type
A category for the risk detection, indicating the type of anomaly that caused the event to be considered risky.

### <a name="risk-level-risk-detection"></a>Risk level (risk detection)
An indication (High, Medium, or Low) of the severity of the risk detection to help Identity Protection users prioritize the actions they take to reduce the risk to their organization. 

### <a name="risk-level-sign-in"></a>Risk level (sign-in)
An indication (High, Medium, or Low) of the likelihood that for a specific sign-in, someone else is attempting to use the user’s identity.

### <a name="risk-level-user-compromise"></a>Risk level (user compromise)
An indication (High, Medium, or Low) of the likelihood that an identity has been compromised.

### <a name="risk-level-vulnerability"></a>Risk level (vulnerability)
An indication (High, Medium, or Low) of the severity of the vulnerability to help Identity Protection users prioritize the actions they take to reduce the risk to their organization.

### <a name="secure-identity"></a>Secure (identity)
Take remediation action such as a password change or machine reimaging to restore a potentially compromised identity to an uncompromised state.

### <a name="security-policy"></a>Política de segurança
A collection of policy rules and condition. A policy can be applied to entities such as users, groups, apps, devices, device platforms, device states, IP ranges, and Auth2.0 client types. When a policy is enabled, it is evaluated whenever an entity included in the policy is issued a token for a resource.

### <a name="sign-in-v"></a>Sign in (v)
To authenticate to an identity in Azure Active Directory.

### <a name="sign-in-n"></a>Sign-in (n)
The process or action of authenticating an identity in Azure Active Directory, and the event that captures this operation.

### <a name="sign-in-from-anonymous-ip-address"></a>Sign in from anonymous IP address
A risk detection triggered after a successful sign-in from IP address that has been identified as an anonymous proxy IP address.

### <a name="sign-in-from-infected-device"></a>Sign in from infected device
A risk detection triggered when a sign-in originates from an IP address, which is known to be used by one or more compromised devices, which are actively attempting to communicate with a bot server.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Sign in from IP address with suspicious activity
A risk detection triggered after a successful sign-in from an IP address with a high number of failed login attempts across multiple user accounts over a short period of time.

### <a name="sign-in-from-unfamiliar-location"></a>Sign in from unfamiliar location
A risk detection triggered when a user successfully signs in from a new location (IP, Latitude/Longitude, and ASN).

### <a name="sign-in-risk"></a>Sign-in risk
See Risk level (sign-in)

### <a name="sign-in-risk-policy"></a>Sign-in risk policy
A Conditional Access policy that evaluates the risk to a specific sign-in and applies mitigations based on predefined conditions and rules.

### <a name="user-compromise-risk"></a>User compromise risk
See Risk level (user compromise)

### <a name="user-risk"></a>User risk
See Risk level (user compromise).

### <a name="user-risk-policy"></a>User risk policy
A Conditional Access policy that considers the sign-in and applies mitigations based on predefined conditions and rules.

### <a name="users-flagged-for-risk"></a>Utilizadores marcados em risco
Users that have risk detections, which are either active or remediated

### <a name="vulnerability"></a>Vulnerability
A configuration or condition in Azure Active Directory, which makes the directory susceptible to exploits or threats.

## <a name="see-also"></a>Ver também

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
