---
title: Atributos sincronizados pelo Azure AD Connect | Documentos da Microsoft
description: Apresenta uma lista de atributos que são sincronizados para o Azure Active Directory.
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
ms.openlocfilehash: e6120637bdf52db4b6863fb87feb1d52c78ca088
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508118"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Sincronização do Azure AD Connect: Atributos sincronizados com o Azure Active Directory
Este tópico lista os atributos que estão sincronizados com a sincronização do Azure AD Connect.  
Os atributos são agrupados por do Azure relacionado aplicação AD.

## <a name="attributes-to-synchronize"></a>Atributos para sincronizar
É uma pergunta comum *o que é a lista de atributos mínimo para sincronizar*. A predefinição e a abordagem recomendada é manter os atributos padrão, para que uma total GAL (lista de endereços Global) pode ser criada na cloud e obtenha todas as funcionalidades em cargas de trabalho do Office 365. Em alguns casos, há alguns atributos que sua organização não quer sincronizados para a cloud, uma vez que esses atributos contêm confidencial ou dados de PII (informações de identificação pessoal), como neste exemplo:  
![atributos incorretos](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

Neste caso, comece com a lista de atributos neste tópico e identificar esses atributos que iria conter sensível ou dados PII e não podem ser sincronizados. Em seguida, desmarque esses atributos durante a instalação usando [aplicação do Azure AD e filtragem de atributos](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Quando desmarcando atributos, deve ter cuidado e apenas desmarcar esses atributos absolutamente não é possíveis sincronizar. Desmarcá-lo de outros atributos pode ter um impacto negativo sobre os recursos.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Nome do Atributo | Utilizador | Comentário |
| --- |:---:| --- |
| accountEnabled |X |Define se uma conta está ativada. |
| CN |X | |
| displayName |X | |
| objectSID |X |propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| pwdLastSet |X |propriedade mecânica. Utilizado para saber quando invalidar tokens emitidos já. Utilizado por sincronização de hash de palavra-passe, autenticação pass-through e Federação. |
|samAccountName|X| |
| sourceAnchor |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| usageLocation |X |propriedade mecânica. . O país/região o utilizador. Utilizado para a atribuição de licenças. |
| userPrincipalName |X |UPN é o ID de início de sessão do utilizador. Com mais freqüência é o valor igual a [email]. |

## <a name="exchange-online"></a>Exchange Online
| Nome do Atributo | Utilizador | Contacto | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está ativada. |
| Assistente |X |X | | |
| altRecipient |X | | |Requer o Azure AD Connect compilação 1.1.552.0 ou depois. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| Co |X |X | | |
| Empresa |X |X | | |
| countryCode |X |X | | |
| Departamento |X |X | | |
| description |X |X |X | |
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
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| informações |X |X |X |Atualmente, este atributo é consumido não para grupos. |
| Iniciais |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| gestor |X |X | | |
| membro | | |X | |
| móvel |X |X | | |
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
| msExchBypassModerationLink | | |X |Disponível no Azure AD Connect na versão 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Atualmente, este atributo é consumido não pelo Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Atualmente, este atributo é consumido não pelo Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Atualmente, este atributo é consumido não pelo Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Atualmente, este atributo é consumido não pelo Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Atualmente, este atributo é consumido não pelo Exchange Online. |
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
| msOrg-IsOrganizational | | |X | |
| objectSID |X | |X |propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| Pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Utilizado para saber quando invalidar tokens emitidos já. Utilizado por sincronização de palavra-passe e a Federação. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| título |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |propriedade mecânica. . O país/região o utilizador. Utilizado para a atribuição de licenças. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN é o ID de início de sessão do utilizador. Com mais freqüência é o valor igual a [email]. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Nome do Atributo | Utilizador | Contacto | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está ativada. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| Co |X |X | | |
| Empresa |X |X | | |
| countryCode |X |X | | |
| Departamento |X |X | | |
| description |X |X |X | |
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
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| homephone |X |X | | |
| informações |X |X |X | |
| Iniciais |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| gestor |X |X | | |
| membro | | |X | |
| middleName |X |X | | |
| móvel |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| Pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |Este atributo não atualmente é consumido pelo SharePoint Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Utilizado para saber quando invalidar tokens emitidos já. Utilizado por sincronização de hash de palavra-passe, autenticação pass-through e Federação. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| título |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |propriedade mecânica. . O país/região o utilizador
. Utilizado para a atribuição de licenças. |
| userPrincipalName |X | | |UPN é o ID de início de sessão do utilizador. Com mais freqüência é o valor igual a [email]. |
| wWWHomePage |X |X | | |

## <a name="lync-online-subsequently-known-as-skype-for-business"></a>O Lync Online (, em seguida, conhecido como o Skype para empresas)
| Nome do Atributo | Utilizador | Contacto | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está ativada. |
| c |X |X | | |
| CN |X | |X | |
| Co |X |X | | |
| Empresa |X |X | | |
| Departamento |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| homephone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| gestor |X |X | | |
| membro | | |X | |
| móvel |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Utilizado para saber quando invalidar tokens emitidos já. Utilizado por sincronização de hash de palavra-passe, autenticação pass-through e Federação. |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| título |X |X | | |
| usageLocation |X | | |propriedade mecânica. . O país/região o utilizador. Utilizado para a atribuição de licenças. |
| userPrincipalName |X | | |UPN é o ID de início de sessão do utilizador. Com mais freqüência é o valor igual a [email]. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Nome do Atributo | Utilizador | Contacto | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está ativada. |
| CN |X | |X |Nome comum ou alias. Com mais freqüência é o prefixo do valor de [email]. |
| displayName |X |X |X |Uma cadeia de caracteres que representa o nome, muitas vezes, é apresentado como o nome amigável (nome do último nome próprio). |
| mail |X |X |X |endereço de e-mail completo. |
| membro | | |X | |
| objectSID |X | |X |propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| proxyAddresses |X |X |X |propriedade mecânica. Utilizado pelo Azure AD. Contém todos os endereços de e-mail secundário para o utilizador. |
| pwdLastSet |X | | |propriedade mecânica. Utilizado para saber quando invalidar tokens emitidos já. |
| sourceAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| usageLocation |X | | |propriedade mecânica. . O país/região o utilizador. Utilizado para a atribuição de licenças. |
| userPrincipalName |X | | |Este UPN é o ID de início de sessão do utilizador. Com mais freqüência é o valor igual a [email]. |

## <a name="intune"></a>Intune
| Nome do Atributo | Utilizador | Contacto | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está ativada. |
| c |X |X | | |
| CN |X | |X | |
| description |X |X |X | |
| displayName |X |X |X | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| membro | | |X | |
| objectSID |X | |X |propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Utilizado para saber quando invalidar tokens emitidos já. Utilizado por sincronização de hash de palavra-passe, autenticação pass-through e Federação. |
| sourceAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| usageLocation |X | | |propriedade mecânica. . O país/região o utilizador. Utilizado para a atribuição de licenças. |
| userPrincipalName |X | | |UPN é o ID de início de sessão do utilizador. Com mais freqüência é o valor igual a [email]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Nome do Atributo | Utilizador | Contacto | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está ativada. |
| c |X |X | | |
| CN |X | |X | |
| Co |X |X | | |
| Empresa |X |X | | |
| countryCode |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| gestor |X |X | | |
| membro | | |X | |
| móvel |X |X | | |
| objectSID |X | |X |propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |propriedade mecânica. Utilizado para saber quando invalidar tokens emitidos já. Utilizado por sincronização de hash de palavra-passe, autenticação pass-through e Federação. |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| título |X |X | | |
| usageLocation |X | | |propriedade mecânica. . O país/região o utilizador. Utilizado para a atribuição de licenças. |
| userPrincipalName |X | | |UPN é o ID de início de sessão do utilizador. Com mais freqüência é o valor igual a [email]. |

## <a name="3rd-party-applications"></a>3º aplicativos de terceiros
Este grupo é um conjunto de atributos utilizado como os atributos mínimos necessários para uma aplicação ou carga de trabalho genérica. Ele pode ser usado para uma carga de trabalho não listado na outra secção ou para uma aplicação de terceiros. Explicitamente é utilizado para o seguinte:

* Yammer (apenas o utilizador é consumido)
* [Oferecidos pelos recursos dos cenários de colaboração de entre organizações para empresa-empresa (B2B) híbrida como o SharePoint](https://go.microsoft.com/fwlink/?LinkId=747036)

Este grupo é um conjunto de atributos que pode ser utilizado se o diretório do Azure AD não é utilizado para suportar o Office 365, Dynamics ou no Intune. Ele tem um pequeno conjunto de atributos principais.

| Nome do Atributo | Utilizador | Contacto | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está ativada. |
| CN |X | |X | |
| displayName |X |X |X | |
| employeeID |X |  |  | |
| givenName |X |X | | |
| mail |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| membro | | |X | |
| objectSID |X | | |propriedade mecânica. Identificador de utilizador do AD utilizado para manter a sincronização entre o Azure AD e AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Utilizado para saber quando invalidar tokens emitidos já. Utilizado por sincronização de hash de palavra-passe, autenticação pass-through e Federação. |
| sn |X |X | | |
| sourceAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| usageLocation |X | | |propriedade mecânica. . O país/região o utilizador. Utilizado para a atribuição de licenças. |
| userPrincipalName |X | | |UPN é o ID de início de sessão do utilizador. Com mais freqüência é o valor igual a [email]. |

## <a name="windows-10"></a>Windows 10
Um computer(device) associados a um domínio do Windows 10 sincroniza alguns atributos para o Azure AD. Para obter mais informações sobre os cenários, consulte [ligar dispositivos associados a um domínio ao Azure AD para experiências do Windows 10](../active-directory-azureadjoin-devices-group-policy.md). Esses atributos sempre sincronizar e Windows 10 não é apresentada como uma aplicação que pode anular a seleção. Um computador associado a um domínio do Windows 10 é identificado por ter o userCertificate de atributo preenchido.

| Nome do Atributo | Dispositivo | Comentário |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Valor inserido no código para computadores associados a um domínio. |
| displayName |X | |
| ms-DS-CreatorSID |X |Também denominado registeredOwnerReference. |
| objectGUID |X |Também denominado deviceID. |
| objectSID |X |Também denominado onPremisesSecurityIdentifier. |
| operatingSystem |X |Também denominado deviceOSType. |
| operatingSystemVersion |X |Também denominado deviceOSVersion. |
| userCertificate |X | |

Esses atributos para **utilizador** estão além de outras aplicações que selecionou.  

| Nome do Atributo | Utilizador | Comentário |
| --- |:---:| --- |
| domainFQDN |X |Também denominado dnsDomainName. Por exemplo, contoso.com. |
| domainNetBios |X |Também denominado netBiosName. Por exemplo, CONTOSO. |
| msDS-KeyCredentialLink |X |Assim que o utilizador estiver inscrito no Windows Hello para empresas. | 

## <a name="exchange-hybrid-writeback"></a>Repetição de escrita do Exchange híbrido
Esses atributos são repetidos do AD do Azure Active Directory no local quando optar por ativar **híbrida do Exchange**. Dependendo da versão do Exchange, poderão estar sincronizados menos atributos.

| Nome de atributo (AD no local) | Nome de atributo (ligar a interface do Usuário) | Utilizador | Contacto | Agrupar | Comentário |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Deriva cloudAnchor no Azure AD. Este atributo é novo no Exchange 2016 e Windows Server 2016 AD. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |Arquivo online: Permite aos clientes arquivar emails. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Filtragem: Grava de volta na filtragem de dados local e online remetente seguro e bloqueados de clientes. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Filtragem: Grava de volta na filtragem de dados local e online remetente seguro e bloqueados de clientes. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Filtragem: Grava de volta na filtragem de dados local e online remetente seguro e bloqueados de clientes. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Ative a unificação de mensagens (UM) - correio de voz Online: Utilizada pelo Microsoft Lync Server integração para indicar ao Lync Server no local que o utilizador tem o correio de voz nos serviços online. |
| msExchUserHoldPolicies| ms-Exc-hUserHoldPolicies |X | | |Espera litígio: Serviços para determinar quais os utilizadores que estão sob o litígio manter em nuvem ativa. |
| proxyAddresses| proxyAddresses |X |X |X |Apenas o x500 endereço do Exchange Online é inserido. |
| publicDelegates| ms-Exch-Public-Delegates  |X | | |Permite que uma caixa de correio do Exchange Online ser concedida SendOnBehalfTo direitos para os utilizadores com a caixa de correio do Exchange no local. Requer o Azure AD Connect compilação 1.1.552.0 ou depois. |

## <a name="exchange-mail-public-folder"></a>Pasta de públicas de correio do Exchange
Esses atributos são sincronizados a partir do Active Directory no local para o Azure AD Quando seleciona para habilitar **pastas públicas de correio Exchange**.

| Nome do Atributo | PublicFolder | Comentário |
| --- | :---:| --- |
| displayName | X |  |
| mail | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Repetição de escrita de dispositivos
Objetos de dispositivo são criados no Active Directory. Estes objetos podem ser dispositivos associados ao Azure AD ou a computadores associados a domínios do Windows 10.

| Nome do Atributo | Dispositivo | Comentário |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Apenas com o esquema do AD do Windows Server 2016 |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Notas
* Quando utilizar um ID alternativo, o atributo userPrincipalName no local está sincronizado com o onPremisesUserPrincipalName de atributo do Azure AD. O atributo ID alternativo, para email de exemplo, está sincronizado com o atributo userPrincipalName do Azure AD.
* Nas listas acima, o tipo de objeto **usuário** também se aplica para o tipo de objeto **iNetOrgPerson**.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o [do Azure AD Connect](how-to-connect-sync-whatis.md) configuração.

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
