---
title: Atributos sincronizados por Azure AD Connect | Microsoft Docs
description: Lista os atributos sincronizados ao Azure Ative Directory.
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
ms.date: 04/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ec05c4160c6502904644bf7035bda0bed66cc33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94413195"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Sincronização do Azure AD Connect: atributos sincronizados com o Azure Active Directory
Este tópico lista os atributos que são sincronizados pela sincronização Azure AD Connect.  
Os atributos são agrupados pela aplicação AZure AD relacionada.

## <a name="attributes-to-synchronize"></a>Atributos para sincronizar
Uma questão comum é *qual é a lista de atributos mínimos para sincronizar.* A abordagem predefinida e recomendada é manter os atributos predefinidos para que um GAL completo (Global Address List) possa ser construído na nuvem e obter todas as funcionalidades nas cargas de trabalho da Microsoft 365. Em alguns casos, existem alguns atributos que a sua organização não quer sincronizado na nuvem uma vez que estes atributos contêm dados pessoais sensíveis, como neste exemplo:  
![maus atributos](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

Neste caso, comece com a lista de atributos neste tópico e identifique os atributos que contêm dados pessoais e não podem ser sincronizados. Em seguida, desseleccione esses atributos durante a instalação utilizando [a aplicação AD Azure e atribua a filtragem](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering)de atributos .

> [!WARNING]
> Ao desselecionar atributos, deve ser cauteloso e apenas dessele esses atributos absolutamente não possível sincronizar. Não seleção de outros atributos pode ter um impacto negativo nas funcionalidades.
>
>

## <a name="microsoft-365-apps-for-enterprise"></a>Microsoft 365 Apps para empresa
| Nome do atributo | User | Comentário |
| --- |:---:| --- |
| accountEnabled |X |Define se uma conta está ativada. |
| cn |X | |
| displayName |X | |
| SIDobjeto |X |propriedade mecânica. Identificador de utilizador de AD usado para manter a sincronização entre Azure AD e AD. |
| pwdLastSet |X |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. Usado por sincronização de haxixe de palavra-passe, autenticação de passagem e federação. |
|NomeContaSam|X| |
| fonteAnchor |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| usageLocation |X |propriedade mecânica. País/região do utilizador. Usado para atribuição de licença. |
| userPrincipalName |X |UPN é o ID de login para o utilizador. Na maioria das vezes, o mesmo valor que o valor [do correio]. |

## <a name="exchange-online"></a>Exchange Online
| Nome do atributo | User | Contacto | Group | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está ativada. |
| assistente |X |X | | |
| altRecipient |X | | |Requer a Azure AD Connect construir 1.1.552.0 ou depois. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| empresa |X |X | | |
| paísDesco |X |X | | |
| departamento |X |X | | |
| descrição | | |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensãoAttribute1 |X |X |X | |
| extensãoAttribute10 |X |X |X | |
| extensãoTribui11 |X |X |X | |
| extensãoAttribute12 |X |X |X | |
| extensãoAttribute13 |X |X |X | |
| extensãoAttribute14 |X |X |X | |
| extensãoAttribute15 |X |X |X | |
| extensãoAttribute2 |X |X |X | |
| extensãoAttribute3 |X |X |X | |
| extensãoAttribute4 |X |X |X | |
| extensãoAttribute5 |X |X |X | |
| extensãoAttribute6 |X |X |X | |
| extensãoAttribute7 |X |X |X | |
| extensãoAttribute8 |X |X |X | |
| extensãoAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| nomeDado |X |X | | |
| homePhone |X |X | | |
| informações |X |X |X |Este atributo não é atualmente consumido para grupos. |
| Iniciais |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailSemame |X |X |X | |
| managedBy | | |X | |
| gestor |X |X | | |
| membro | | |X | |
| dispositivo móvel |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDeegate |X | | | |
| msExchAuditDeegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Disponível em Azure AD Connect versão 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDeegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Este atributo não é atualmente consumido pela Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Este atributo não é atualmente consumido pela Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Este atributo não é atualmente consumido pela Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Este atributo não é atualmente consumido pela Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Este atributo não é atualmente consumido pela Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| MSExchImmutableID |X | | | |
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
| SIDobjeto |X | |X |propriedade mecânica. Identificador de utilizador de AD usado para manter a sincronização entre Azure AD e AD. |
| oOFReplyToOriginator | | |X | |
| outrosFacsimileTelephone |X |X | | |
| outrosHomePhone |X |X | | |
| outrostelefones |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| públicosDelegados |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. Usado tanto por sincronização de passwords como pela federação. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| fonteAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| SC |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telefoneSa resistente |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |sincronizado apenas uma vez do Azure AD para o Exchange Online após o qual o Exchange Online se torna fonte de autoridade para este atributo e quaisquer alterações posteriores não podem ser sincronizadas a partir do local. Consulte[(KB)](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)para mais informações.|
| título |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |propriedade mecânica. País/região do utilizador. Usado para atribuição de licença. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN é o ID de login para o utilizador. Na maioria das vezes, o mesmo valor que o valor [do correio]. |
| utilizaçõesMIMECertifica |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Nome do atributo | User | Contacto | Group | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está ativada. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| empresa |X |X | | |
| paísDesco |X |X | | |
| departamento |X |X | | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensãoAttribute1 |X |X |X | |
| extensãoAttribute10 |X |X |X | |
| extensãoTribui11 |X |X |X | |
| extensãoAttribute12 |X |X |X | |
| extensãoAttribute13 |X |X |X | |
| extensãoAttribute14 |X |X |X | |
| extensãoAttribute15 |X |X |X | |
| extensãoAttribute2 |X |X |X | |
| extensãoAttribute3 |X |X |X | |
| extensãoAttribute4 |X |X |X | |
| extensãoAttribute5 |X |X |X | |
| extensãoAttribute6 |X |X |X | |
| extensãoAttribute7 |X |X |X | |
| extensãoAttribute8 |X |X |X | |
| extensãoAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| nomeDado |X |X | | |
| hideDLMembership | | |X | |
| homephone |X |X | | |
| informações |X |X |X | |
| iniciais |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| correio |X |X |X | |
| nome de mailnickname |X |X |X | |
| managedBy | | |X | |
| gestor |X |X | | |
| membro | | |X | |
| nome médio |X |X | | |
| dispositivo móvel |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| SIDobjeto |X | |X |propriedade mecânica. Identificador de utilizador de AD usado para manter a sincronização entre Azure AD e AD. |
| oOFReplyToOriginator | | |X | |
| outrosFacsimileTelephone |X |X | | |
| outrosHomePhone |X |X | | |
| outrosIpPhone |X |X | | |
| otherME |X |X | | |
| outros Pager |X |X | | |
| outrostelefones |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |Este atributo não é atualmente consumido pelo SharePoint Online. |
| preferiuLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. Usado por sincronização de haxixe de palavra-passe, autenticação de passagem e federação. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| fonteAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| SC |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telefoneSa resistente |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |sincronizado apenas uma vez do Azure AD para o Exchange Online após o qual o Exchange Online se torna fonte de autoridade para este atributo e quaisquer alterações posteriores não podem ser sincronizadas a partir do local. Consulte[(KB)](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)para mais informações.|
| título |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |propriedade mecânica. País/região do utilizador
. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é o ID de login para o utilizador. Na maioria das vezes, o mesmo valor que o valor [do correio]. |
| wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Equipas e Skype para Business Online
| Nome do atributo | User | Contacto | Group | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está ativada. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| empresa |X |X | | |
| departamento |X |X | | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| nomeDado |X |X | | |
| homephone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| correio |X |X |X | |
| mailSemame |X |X |X | |
| managedBy | | |X | |
| gestor |X |X | | |
| membro | | |X | |
| dispositivo móvel |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-AplicaçãoOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| SIDobjeto |X | |X |propriedade mecânica. Identificador de utilizador de AD usado para manter a sincronização entre Azure AD e AD. |
| outrostelefones |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferiuLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. Usado por sincronização de haxixe de palavra-passe, autenticação de passagem e federação. |
| sn |X |X | | |
| fonteAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| SC |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |sincronizado apenas uma vez do Azure AD para o Exchange Online após o qual o Exchange Online se torna fonte de autoridade para este atributo e quaisquer alterações posteriores não podem ser sincronizadas a partir do local. Consulte[(KB)](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)para mais informações.|
| título |X |X | | |
| usageLocation |X | | |propriedade mecânica. País/região do utilizador. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é o ID de login para o utilizador. Na maioria das vezes, o mesmo valor que o valor [do correio]. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Nome do atributo | User | Contacto | Group | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está ativada. |
| cn |X | |X |Nome comum ou pseudónimo. Na maioria das vezes, o prefixo do valor [do correio]. |
| displayName |X |X |X |Uma corda que representa o nome frequentemente mostrado como o nome amigável (nome de primeiro nome). |
| correio |X |X |X |endereço de e-mail completo. |
| membro | | |X | |
| SIDobjeto |X | |X |propriedade mecânica. Identificador de utilizador de AD usado para manter a sincronização entre Azure AD e AD. |
| proxyAddresses |X |X |X |propriedade mecânica. Usado por Azure AD. Contém todos os endereços de e-mail secundários para o utilizador. |
| pwdLastSet |X | | |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. |
| fonteAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| usageLocation |X | | |propriedade mecânica. País/região do utilizador. Usado para atribuição de licença. |
| userPrincipalName |X | | |Este UPN é o ID de login para o utilizador. Na maioria das vezes, o mesmo valor que o valor [do correio]. |

## <a name="intune"></a>Intune
| Nome do atributo | User | Contacto | Group | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está ativada. |
| c |X |X | | |
| cn |X | |X | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| correio |X |X |X | |
| nome de mailnickname |X |X |X | |
| membro | | |X | |
| SIDobjeto |X | |X |propriedade mecânica. Identificador de utilizador de AD usado para manter a sincronização entre Azure AD e AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. Usado por sincronização de haxixe de palavra-passe, autenticação de passagem e federação. |
| fonteAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| usageLocation |X | | |propriedade mecânica. País/região do utilizador. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é o ID de login para o utilizador. Na maioria das vezes, o mesmo valor que o valor [do correio]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Nome do atributo | User | Contacto | Group | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está ativada. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| empresa |X |X | | |
| paísDesco |X |X | | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| nomeDado |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| gestor |X |X | | |
| membro | | |X | |
| dispositivo móvel |X |X | | |
| SIDobjeto |X | |X |propriedade mecânica. Identificador de utilizador de AD usado para manter a sincronização entre Azure AD e AD. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferiuLanguage |X | | | |
| pwdLastSet |X | | |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. Usado por sincronização de haxixe de palavra-passe, autenticação de passagem e federação. |
| sn |X |X | | |
| fonteAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| SC |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| título |X |X | | |
| usageLocation |X | | |propriedade mecânica. País/região do utilizador. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é o ID de login para o utilizador. Na maioria das vezes, o mesmo valor que o valor [do correio]. |

## <a name="3rd-party-applications"></a>Candidaturas de terceiros
Este grupo é um conjunto de atributos usados como os atributos mínimos necessários para uma carga de trabalho genérica ou aplicação. Pode ser utilizado para uma carga de trabalho não listada noutra secção ou para uma aplicação não-Microsoft. É explicitamente utilizado para o seguinte:

* Yammer (apenas o Utilizador é consumido)
* [Cenários de colaboração trans-org híbridos (B2B) oferecidos por recursos como o SharePoint](/sharepoint/create-b2b-extranet)

Este grupo é um conjunto de atributos que podem ser usados se o diretório AD Azure não for usado para suportar Microsoft 365, Dynamics ou Intune. Tem um pequeno conjunto de atributos principais. Note que o único sign-on ou provisioning a algumas aplicações de terceiros requer a sincronização configurada de atributos para além dos atributos descritos aqui. Os requisitos de aplicação são descritos no tutorial da [aplicação SaaS](../saas-apps/tutorial-list.md) para cada aplicação.

| Nome do atributo | User | Contacto | Group | Comentário |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Define se uma conta está ativada. |
| cn |X | |X | |
| displayName |X |X |X | |
| IDdefuncionário |X |  |  | |
| nomeDado |X |X | | |
| correio |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| membro | | |X | |
| SIDobjeto |X | | |propriedade mecânica. Identificador de utilizador de AD usado para manter a sincronização entre Azure AD e AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. Usado por sincronização de haxixe de palavra-passe, autenticação de passagem e federação. |
| sn |X |X | | |
| fonteAnchor |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre o ADDS e o Azure AD. |
| usageLocation |X | | |propriedade mecânica. País/região do utilizador. Usado para atribuição de licença. |
| userPrincipalName |X | | |UPN é o ID de login para o utilizador. Na maioria das vezes, o mesmo valor que o valor [do correio]. |

## <a name="windows-10"></a>Windows 10
Um computador (dispositivo) associado a domínio do Windows 10 sincroniza alguns atributos do AZure AD. Para obter mais informações sobre os cenários, consulte [dispositivos ligados ao domínio do Azure para experiências com o Windows 10](../devices/hybrid-azuread-join-plan.md). Estes atributos sincronizam-se sempre e o Windows 10 não aparece como uma aplicação que pode desmarcar. Um computador de domínio do Windows 10 é identificado por ter o atributo userCertificate preenchido.

| Nome do atributo | Dispositivo | Comentário |
| --- |:---:| --- |
| accountEnabled |X | |
| DispositivoTrustType |X |Valor codificado para computadores unidos por domínio. |
| displayName |X | |
| MS-DS-CriadorSID |X |Também chamado de Referência Registada. |
| objectGUID |X |Também chamado dispositivoID. |
| SIDobjeto |X |Também apelou ao Deputado Desegurança. |
| sistema operativo |X |Também chamado dispositivoOSType. |
| sistema operativoVersão |X |Também chamado dispositivoOSVersion. |
| userCertificate |X | |

Estes atributos para **o utilizador** são além das outras aplicações que selecionou.  

| Nome do atributo | User | Comentário |
| --- |:---:| --- |
| domainFQDN |X |Também chamado dnsDomainName. Por exemplo, contoso.com. |
| domainNetBios |X |Também chamado netBiosName. Por exemplo, CONTOSO. |
| MSDS-KeyCredentialLink |X |Uma vez que o utilizador esteja inscrito no Windows Hello for Business. | 

## <a name="exchange-hybrid-writeback"></a>Troca de writeback híbrido
Estes atributos são redigidos de Azure AD para o Ative Directory no local quando selecionar para ativar **o Exchange hybrid**. Dependendo da sua versão Exchange, menos atributos podem ser sincronizados.

| Nome do Atributo (AD no local) | Nome do atributo (Connect UI) | User | Contacto | Group | Comentário |
| --- |:---:|:---:|:---:| --- |---|
| MSDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Derivado da nuvemAnchor em Azure AD. Este atributo é novo no Exchange 2016 e no Windows Server 2016 AD. |
| msExchArchiveStatus| Sra. Exch-ArchiveStatus |X | | |Arquivo Online: Permite que os clientes arquivam o correio. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Filtragem: Volta a escrever no local a filtragem e os dados de sender on-line seguros e bloqueados dos clientes. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Filtragem: Volta a escrever no local a filtragem e os dados de sender on-line seguros e bloqueados dos clientes. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Filtragem: Volta a escrever no local a filtragem e os dados de sender on-line seguros e bloqueados dos clientes. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Ativar mensagens unificadas (UM) - Correio de voz online: Utilizado pela integração do Microsoft Lync Server para indicar ao Lync Server no local que o utilizador tem correio de voz em serviços online. |
| msExchUserHoldPolicies| ms-Exch-UserHoldPolicies |X | | |Hold de litígios: Permite que os serviços na nuvem determinem quais os utilizadores que estão sob o comando de Litígios. |
| proxyAddresses| proxyAddresses |X |X |X |Apenas o endereço x500 do Exchange Online está inserido. |
| públicosDelegados| sra- Exch-Public-Delegados  |X | | |Permite que seja concedida uma caixa de correio Exchange Online aos utilizadores com caixa de correio exchange. Requer a Azure AD Connect construir 1.1.552.0 ou depois. |

## <a name="exchange-mail-public-folder"></a>Troca de Correio Público Pasta
Estes atributos são sincronizados desde o Diretório Ativo para Azure AD quando seleciona para ativar a **Pasta Pública do Correio de Câmbio**.

| Nome do atributo | PublicFolder | Comentário |
| --- | :---:| --- |
| displayName | X |  |
| correio | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Repetição de escrita do dispositivo
Os objetos do dispositivo são criados no Ative Directory. Estes objetos podem ser dispositivos unidos ao Azure AD ou aos computadores do Windows 10 unidos pelo domínio.

| Nome do atributo | Dispositivo | Comentário |
| --- |:---:| --- |
| altSecuritySes |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| MSDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| MSDS-KeyCredentialLink |X |Apenas com o esquema AD do Windows Server 2016 |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Notas
* Ao utilizar um ID Alternativo, o utilizador no local atribui o NomePrincipal é sincronizado com o atributo AZure AD no NomePrincipal DoMises. O atributo De ID Alternativo, por exemplo, por correio, é sincronizado com o utilizador de atributo AZure ADPrincipalName.
* Nas listas acima, o tipo de objeto **O utilizador** também se aplica ao tipo de objeto **iNetOrgPerson**.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a configuração da [sincronização Azure AD Connect.](how-to-connect-sync-whatis.md)

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).