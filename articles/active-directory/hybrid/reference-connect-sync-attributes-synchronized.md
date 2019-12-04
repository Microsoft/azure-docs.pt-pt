---
title: Atributos sincronizados por Azure AD Connect | Microsoft Docs
description: Lista os atributos que são sincronizados com Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a82766be01476890bbf18b518ce21febe0d07f1
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766094"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Sincronização de Azure AD Connect: atributos sincronizados com Azure Active Directory
Este tópico lista os atributos que são sincronizados pelo Azure AD Connect sincronização.  
Os atributos são agrupados pelo aplicativo do Azure AD relacionado.

## <a name="attributes-to-synchronize"></a>Atributos para sincronizar
Uma pergunta comum é *qual é a lista de atributos mínimos a serem sincronizados*. A abordagem padrão e recomendada é manter os atributos padrão para que uma GAL completa (lista de endereços global) possa ser construída na nuvem e obter todos os recursos em cargas de trabalho do Office 365. Em alguns casos, há alguns atributos que sua organização não deseja sincronizar com a nuvem, já que esses atributos contêm dados confidenciais ou PII (informações de identificação pessoal), como neste exemplo:  
![atributos inválidos](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

Nesse caso, comece com a lista de atributos neste tópico e identifique os atributos que conteriam dados confidenciais ou PII e não podem ser sincronizados. Em seguida, desmarque esses atributos durante a instalação usando o [aplicativo do Azure AD e a filtragem de atributos](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Ao desmarcar atributos, você deve ter cuidado e apenas desmarcar os atributos absolutamente não é possível sincronizar. Cancelar a seleção de outros atributos pode ter um impacto negativo sobre os recursos.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Nome do atributo | Utilizador | Comentário |
| --- |:---:| --- |
| accountEnabled |X |Define se uma conta está habilitada. |
| Hong |X | |
| displayName |X | |
| objectSID |X |Propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o Azure AD e o AD. |
| pwdLastSet |X |Propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de hash de senha, pela autenticação de passagem e pela Federação. |
|samAccountName|X| |
| sourceAnchor |X |Propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| usageLocation |X |Propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userPrincipalName |X |UPN é a ID de logon do usuário. Muitas vezes, o mesmo que o valor de [email]. |

## <a name="exchange-online"></a>Exchange Online
| Nome do atributo | Utilizador | Contacte-nos | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está habilitada. |
| Assistant |X |X | | |
| altRecipient |X | | |Requer Azure AD Connect Build 1.1.552.0 ou After. |
| authOrig |X |X |X | |
| & |X |X | | |
| Hong |X | |X | |
| colabora |X |X | | |
| corporativa |X |X | | |
| countryCode |X |X | | |
| inteiros |X |X | | |
| descrição | | |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimileTelephoneNumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| detalhes |X |X |X |Este atributo não está consumido atualmente para grupos. |
| Iniciais |X |X | | |
| debug |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| Manager |X |X | | |
| associado | | |X | |
| móveis |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Disponível no Azure AD Connect versão 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Este atributo não está consumido no momento pelo Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Este atributo não está consumido no momento pelo Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Este atributo não está consumido no momento pelo Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Este atributo não está consumido no momento pelo Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Este atributo não está consumido no momento pelo Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-isorganizacional | | |X | |
| objectSID |X | |X |Propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o Azure AD e o AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |Propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de senha e pela Federação. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| SN |X |X | | |
| sourceAnchor |X |X |X |Propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| ThumbNailPhoto |X |X | | |
| título |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |Propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN é a ID de logon do usuário. Muitas vezes, o mesmo que o valor de [email]. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Nome do atributo | Utilizador | Contacte-nos | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está habilitada. |
| authOrig |X |X |X | |
| & |X |X | | |
| Hong |X | |X | |
| colabora |X |X | | |
| corporativa |X |X | | |
| countryCode |X |X | | |
| inteiros |X |X | | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimileTelephoneNumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| HomePhone |X |X | | |
| detalhes |X |X |X | |
| iniciais |X |X | | |
| ipPhone |X |X | | |
| debug |X |X | | |
| mescla |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| Manager |X |X | | |
| associado | | |X | |
| middleName |X |X | | |
| móveis |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |Propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o Azure AD e o AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |Este atributo não está consumido no momento pelo SharePoint Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de hash de senha, pela autenticação de passagem e pela Federação. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| SN |X |X | | |
| sourceAnchor |X |X |X |Propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| ThumbNailPhoto |X |X | | |
| título |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |Propriedade mecânica. O país/região do usuário
. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é a ID de logon do usuário. Muitas vezes, o mesmo que o valor de [email]. |
| wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Equipes e Skype for Business Online
| Nome do atributo | Utilizador | Contacte-nos | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está habilitada. |
| & |X |X | | |
| Hong |X | |X | |
| colabora |X |X | | |
| corporativa |X |X | | |
| inteiros |X |X | | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| facsimileTelephoneNumber |X |X |X | |
| givenName |X |X | | |
| HomePhone |X |X | | |
| ipPhone |X |X | | |
| debug |X |X | | |
| mescla |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| Manager |X |X | | |
| associado | | |X | |
| móveis |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-linha |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-userhabilitado |X |X | | |
| objectSID |X | |X |Propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o Azure AD e o AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de hash de senha, pela autenticação de passagem e pela Federação. |
| SN |X |X | | |
| sourceAnchor |X |X |X |Propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| ThumbNailPhoto |X |X | | |
| título |X |X | | |
| usageLocation |X | | |Propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é a ID de logon do usuário. Muitas vezes, o mesmo que o valor de [email]. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Nome do atributo | Utilizador | Contacte-nos | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está habilitada. |
| Hong |X | |X |Nome comum ou alias. Geralmente, o prefixo do valor [mail]. |
| displayName |X |X |X |Uma cadeia de caracteres que representa o nome geralmente mostrado como o nome amigável (nome sobrenome). |
| mescla |X |X |X |Endereço de email completo. |
| associado | | |X | |
| objectSID |X | |X |Propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o Azure AD e o AD. |
| proxyAddresses |X |X |X |Propriedade mecânica. Usado pelo Azure AD. Contém todos os endereços de email secundários para o usuário. |
| pwdLastSet |X | | |Propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. |
| sourceAnchor |X |X |X |Propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| usageLocation |X | | |Propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userPrincipalName |X | | |Esse UPN é a ID de logon do usuário. Muitas vezes, o mesmo que o valor de [email]. |

## <a name="intune"></a>Intune
| Nome do atributo | Utilizador | Contacte-nos | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está habilitada. |
| & |X |X | | |
| Hong |X | |X | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| mescla |X |X |X | |
| mailNickname |X |X |X | |
| associado | | |X | |
| objectSID |X | |X |Propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o Azure AD e o AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de hash de senha, pela autenticação de passagem e pela Federação. |
| sourceAnchor |X |X |X |Propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| usageLocation |X | | |Propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é a ID de logon do usuário. Muitas vezes, o mesmo que o valor de [email]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Nome do atributo | Utilizador | Contacte-nos | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está habilitada. |
| & |X |X | | |
| Hong |X | |X | |
| colabora |X |X | | |
| corporativa |X |X | | |
| countryCode |X |X | | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| facsimileTelephoneNumber |X |X | | |
| givenName |X |X | | |
| debug |X |X | | |
| managedBy | | |X | |
| Manager |X |X | | |
| associado | | |X | |
| móveis |X |X | | |
| objectSID |X | |X |Propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o Azure AD e o AD. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |Propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de hash de senha, pela autenticação de passagem e pela Federação. |
| SN |X |X | | |
| sourceAnchor |X |X |X |Propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| título |X |X | | |
| usageLocation |X | | |Propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é a ID de logon do usuário. Muitas vezes, o mesmo que o valor de [email]. |

## <a name="3rd-party-applications"></a>aplicativos de terceiros
Esse grupo é um conjunto de atributos usados como os atributos mínimos necessários para uma carga de trabalho ou aplicativo genérico. Ele pode ser usado para uma carga de trabalho não listada em outra seção ou para um aplicativo que não seja da Microsoft. Ele é usado explicitamente para o seguinte:

* Yammer (somente o usuário é consumido)
* [Cenários de colaboração entre organizações híbridas entre empresas (B2B) oferecidos por recursos como o SharePoint](https://go.microsoft.com/fwlink/?LinkId=747036)

Esse grupo é um conjunto de atributos que podem ser usados se o diretório do Azure AD não for usado para dar suporte ao Office 365, Dynamics ou Intune. Ele tem um pequeno conjunto de atributos de núcleo.

| Nome do atributo | Utilizador | Contacte-nos | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está habilitada. |
| Hong |X | |X | |
| displayName |X |X |X | |
| Funcionário |X |  |  | |
| givenName |X |X | | |
| mescla |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| associado | | |X | |
| objectSID |X | | |Propriedade mecânica. Identificador de usuário do AD usado para manter a sincronização entre o Azure AD e o AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |Propriedade mecânica. Usado para saber quando invalidar tokens já emitidos. Usado pela sincronização de hash de senha, pela autenticação de passagem e pela Federação. |
| SN |X |X | | |
| sourceAnchor |X |X |X |Propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| usageLocation |X | | |Propriedade mecânica. O país/região do usuário. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é a ID de logon do usuário. Muitas vezes, o mesmo que o valor de [email]. |

## <a name="windows-10"></a>Windows 10
Um computador (dispositivo) ingressado no domínio do Windows 10 sincroniza alguns atributos para o Azure AD. Para obter mais informações sobre os cenários, consulte [conectar dispositivos ingressados no domínio ao Azure ad para experiências com o Windows 10](../active-directory-azureadjoin-devices-group-policy.md). Esses atributos sempre são sincronizados e o Windows 10 não aparece como um aplicativo que você pode cancelar a seleção. Um computador ingressado no domínio do Windows 10 é identificado por ter o atributo userCertificate populado.

| Nome do atributo | Dispositivo | Comentário |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Valor codificado para computadores ingressados no domínio. |
| displayName |X | |
| ms-DS-CreatorSID |X |Também chamado de registeredOwnerReference. |
| objectGUID |X |Também chamado de DeviceID. |
| objectSID |X |Também chamado de onPremisesSecurityIdentifier. |
| Operacional |X |Também chamado de deviceOSType. |
| operatingSystemVersion |X |Também chamado de deviceOSVersion. |
| userCertificate |X | |

Esses atributos para o **usuário** são além dos outros aplicativos que você selecionou.  

| Nome do atributo | Utilizador | Comentário |
| --- |:---:| --- |
| domainFQDN |X |Também chamado de NomeDomínioDNS. Por exemplo, contoso.com. |
| domainNetBios |X |Também chamado de NetBiosName. Por exemplo, CONTOSO. |
| msDS-KeyCredentialLink |X |Depois que o usuário é registrado no Windows Hello para empresas. | 

## <a name="exchange-hybrid-writeback"></a>Write-back híbrido do Exchange
Esses atributos são gravados de volta do Azure AD para o local Active Directory quando você seleciona para habilitar o **Exchange híbrido**. Dependendo da versão do Exchange, menos atributos podem ser sincronizados.

| Nome do atributo (AD local) | Nome do atributo (interface do usuário do Connect) | Utilizador | Contacte-nos | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-external-Directory-Object-ID |X | | |Derivado de cloudAnchor no Azure AD. Esse atributo é novo no Exchange 2016 e no Windows Server 2016 AD. |
| msExchArchiveStatus| Ms-Exch-ArchiveStatus |X | | |Arquivo online: permite que os clientes arquivem emails. |
| msExchBlockedSendersHash| Ms-Exch-BlockedSendersHash |X | | |Filtragem: grava novamente a filtragem local e os dados do remetente bloqueado e seguro online de clientes. |
| msExchSafeRecipientsHash| Ms-Exch-SafeRecipientsHash  |X | | |Filtragem: grava novamente a filtragem local e os dados do remetente bloqueado e seguro online de clientes. |
| msExchSafeSendersHash| Ms-Exch-SafeSendersHash  |X | | |Filtragem: grava novamente a filtragem local e os dados do remetente bloqueado e seguro online de clientes. |
| msExchUCVoiceMailSettings| Ms-Exch-UCVoiceMailSettings |X | | |Habilitar o UM (Unificação de mensagens)-correio online de voz: usado pela integração do Microsoft Lync Server para indicar ao Lync Server local que o usuário tem o correio de voz em serviços online. |
| msExchUserHoldPolicies| Ms-Exch-UserHoldPolicies |X | | |Suspensão de litígio: permite que os serviços de nuvem determinem quais usuários estão em espera de litígio. |
| proxyAddresses| proxyAddresses |X |X |X |Somente o endereço X500 do Exchange Online é inserido. |
| publicDelegates| Ms-Exch-Public-delegates  |X | | |Permite que uma caixa de correio do Exchange Online receba direitos SendOnBehalfTo para usuários com caixa de correio do Exchange local. Requer Azure AD Connect Build 1.1.552.0 ou After. |

## <a name="exchange-mail-public-folder"></a>Pasta pública de email do Exchange
Esses atributos são sincronizados do Active Directory local ao Azure AD quando você seleciona para habilitar a **pasta pública do Exchange mail**.

| Nome do atributo | PublicFolder | Comentário |
| --- | :---:| --- |
| displayName | X |  |
| mescla | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Repetição de escrita do dispositivo
Os objetos de dispositivo são criados no Active Directory. Esses objetos podem ser dispositivos ingressados no Azure AD ou computadores com Windows 10 ingressados no domínio.

| Nome do atributo | Dispositivo | Comentário |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| ignora |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Somente com o esquema do AD do Windows Server 2016 |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Notas
* Ao usar uma ID alternativa, o atributo local userPrincipalName é sincronizado com o atributo onPremisesUserPrincipalName do Azure AD. O atributo de ID alternativo, por exemplo, mail, é sincronizado com o atributo userPrincipalName do Azure AD.
* Nas listas acima, o tipo de objeto **User** também se aplica ao tipo de objeto **inetOrgPerson**.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a configuração de [sincronização de Azure ad Connect](how-to-connect-sync-whatis.md) .

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
