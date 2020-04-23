---
title: Atributos sincronizados por Azure AD Connect [ Ligação AD] Microsoft Docs
description: Lista os atributos sincronizados com o Diretório Ativo Azure.
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
ms.openlocfilehash: ab51030ad39e1360cabc7d63390af7c1654d2891
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082123"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Sincronização do Azure AD Connect: atributos sincronizados com o Azure Active Directory
Este tópico lista os atributos sincronizados pela sincronização Azure AD Connect.  
Os atributos são agrupados pela aplicação Azure AD relacionada.

## <a name="attributes-to-synchronize"></a>Atributos para sincronizar
Uma questão comum é *qual é a lista de atributos mínimos para sincronizar*. A abordagem padrão e recomendada é manter os atributos padrão para que uma GAL completa (Global Address List) possa ser construída na nuvem e obter todas as funcionalidades em cargas de trabalho do Office 365. Em alguns casos, existem alguns atributos que a sua organização não quer sincronizada na nuvem, uma vez que estes atributos contêm dados sensíveis ou PII (informação pessoalmente identificável), como neste exemplo:  
![atributos maus](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

Neste caso, comece com a lista de atributos neste tópico e identifique os atributos que contenham dados sensíveis ou PII e não possam ser sincronizados. Em seguida, desmarque esses atributos durante a instalação utilizando [a aplicação Azure AD e atribua a filtragem](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Ao desseleccionar atributos, deve ser cauteloso e apenas desseleccionar esses atributos absolutamente não é possível sincronizar. A desselecção de outros atributos pode ter um impacto negativo nas funcionalidades.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Nome do atributo | Utilizador | Comentário |
| --- |:---:| --- |
| contaEnabled |X |Define se uma conta está ativada. |
| cn |X | |
| displayName |X | |
| SIDobjeto |X |propriedade mecânica. Identificador de utilizador aD usado para manter a sincronização entre Azure AD e AD. |
| pwdLastSet |X |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. Usado por ambos os sincroninos de hash de senha, autenticação pass-through e federação. |
|NomeContaSam|X| |
| fonteÂncora |X |propriedade mecânica. Identificador imutável para manter a relação entre ADDS e Azure AD. |
| utilizaçãoLocalização |X |propriedade mecânica. País/região do utilizador. Usado para atribuição de licença. |
| userPrincipalName |X |A UPN é o ID de login para o utilizador. Na maioria das vezes o mesmo que o valor [do correio]. |

## <a name="exchange-online"></a>Exchange Online
| Nome do atributo | Utilizador | Contacto | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |
| contaEnabled |X | | |Define se uma conta está ativada. |
| assistente |X |X | | |
| altRecipient |X | | |Requer azure AD Connect construir 1.1.552.0 ou depois. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| empresa |X |X | | |
| paísCódigo |X |X | | |
| departamento |X |X | | |
| descrição | | |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensãoAtribuir1 |X |X |X | |
| extensãoAtribuído10 |X |X |X | |
| extensãoAtribuído11 |X |X |X | |
| extensãoAtribuído12 |X |X |X | |
| extensãoAtribuído13 |X |X |X | |
| extensãoAtribuído14 |X |X |X | |
| extensãoAtribuído15 |X |X |X | |
| extensãoAtribuir2 |X |X |X | |
| extensãoAtribuir3 |X |X |X | |
| extensãoAtribuir4 |X |X |X | |
| extensãoAtribuir5 |X |X |X | |
| extensãoAtribuir6 |X |X |X | |
| extensãoAtribuir7 |X |X |X | |
| extensãoAtribuir8 |X |X |X | |
| extensãoAtribuir9 |X |X |X | |
| facsimilephonenumber |X |X | | |
| nomeDado |X |X | | |
| homePhone |X |X | | |
| informações |X |X |X |Este atributo não é atualmente consumido para grupos. |
| Iniciais |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailApelido |X |X |X | |
| managedBy | | |X | |
| gestor |X |X | | |
| membro | | |X | |
| dispositivo móvel |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchDelegateDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Disponível na versão 1.1.1.524.0 do Azure AD Connect |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpiraySuspensionEnd |X | | | |
| msExchELCExpiraySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeração |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Este atributo não é atualmente consumido pela Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Este atributo não é atualmente consumido pela Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Este atributo não é atualmente consumido pela Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Este atributo não é atualmente consumido pela Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Este atributo não é atualmente consumido pela Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditAuditAgeAgeLimit |X | | | |
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
| msExchSenderHintTradus |X |X |X | |
| msExchTeamMailboxExpiração |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-IsOrganizational | | |X | |
| SIDobjeto |X | |X |propriedade mecânica. Identificador de utilizador aD usado para manter a sincronização entre Azure AD e AD. |
| oOFAnswerToOriginator | | |X | |
| outrosFacsimilePhone |X |X | | |
| outroHomePhone |X |X | | |
| outro Telefone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Código postal |X |X | | |
| proxyAddresses |X |X |X | |
| delegados públicos |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. Usado tanto pela sincronização de palavras-passe como pela federação. |
| reportToOriginator | | |X | |
| relatórioToOwner | | |X | |
| sn |X |X | | |
| fonteÂncora |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre ADDS e Azure AD. |
| SC |X |X | | |
| streetAddress |X |X | | |
| endereço alvo |X |X | | |
| telefoneAssistente |X |X | | |
| telefoneNúmero |X |X | | |
| miniaturafoto |X |X | | |
| título |X |X | | |
| unauthOrig |X |X |X | |
| utilizaçãoLocalização |X | | |propriedade mecânica. País/região do utilizador. Usado para atribuição de licença. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |A UPN é o ID de login para o utilizador. Na maioria das vezes o mesmo que o valor [do correio]. |
| userSMIMECertificados |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Nome do atributo | Utilizador | Contacto | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |
| contaEnabled |X | | |Define se uma conta está ativada. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| empresa |X |X | | |
| paísCódigo |X |X | | |
| departamento |X |X | | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensãoAtribuir1 |X |X |X | |
| extensãoAtribuído10 |X |X |X | |
| extensãoAtribuído11 |X |X |X | |
| extensãoAtribuído12 |X |X |X | |
| extensãoAtribuído13 |X |X |X | |
| extensãoAtribuído14 |X |X |X | |
| extensãoAtribuído15 |X |X |X | |
| extensãoAtribuir2 |X |X |X | |
| extensãoAtribuir3 |X |X |X | |
| extensãoAtribuir4 |X |X |X | |
| extensãoAtribuir5 |X |X |X | |
| extensãoAtribuir6 |X |X |X | |
| extensãoAtribuir7 |X |X |X | |
| extensãoAtribuir8 |X |X |X | |
| extensãoAtribuir9 |X |X |X | |
| facsimilephonenumber |X |X | | |
| nomeDado |X |X | | |
| hideDLMembership | | |X | |
| homephone |X |X | | |
| informações |X |X |X | |
| iniciais |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| correio |X |X |X | |
| apelido de correio |X |X |X | |
| managedBy | | |X | |
| gestor |X |X | | |
| membro | | |X | |
| nome do meio |X |X | | |
| dispositivo móvel |X |X | | |
| msExchTeamMailboxExpiração |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| SIDobjeto |X | |X |propriedade mecânica. Identificador de utilizador aD usado para manter a sincronização entre Azure AD e AD. |
| oOFAnswerToOriginator | | |X | |
| outrosFacsimilePhone |X |X | | |
| outroHomePhone |X |X | | |
| outroIpPhone |X |X | | |
| outrosMóvel |X |X | | |
| outroPager |X |X | | |
| outro Telefone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Código postal |X |X | | |
| postOfficeBox |X |X | |Este atributo não é atualmente consumido pelo SharePoint Online. |
| língua preferida |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. Usado por ambos os sincroninos de hash de senha, autenticação pass-through e federação. |
| reportToOriginator | | |X | |
| relatórioToOwner | | |X | |
| sn |X |X | | |
| fonteÂncora |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre ADDS e Azure AD. |
| SC |X |X | | |
| streetAddress |X |X | | |
| endereço alvo |X |X | | |
| telefoneAssistente |X |X | | |
| telefoneNúmero |X |X | | |
| miniaturafoto |X |X | | |
| título |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| utilizaçãoLocalização |X | | |propriedade mecânica. País/região do utilizador
. Usado para atribuição de licença. |
| userPrincipalName |X | | |A UPN é o ID de login para o utilizador. Na maioria das vezes o mesmo que o valor [do correio]. |
| wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Equipas e Skype para Negócios Online
| Nome do atributo | Utilizador | Contacto | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |
| contaEnabled |X | | |Define se uma conta está ativada. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| empresa |X |X | | |
| departamento |X |X | | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| facsimilephonenumber |X |X |X | |
| nomeDado |X |X | | |
| homephone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| correio |X |X |X | |
| mailApelido |X |X |X | |
| managedBy | | |X | |
| gestor |X |X | | |
| membro | | |X | |
| dispositivo móvel |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| SIDobjeto |X | |X |propriedade mecânica. Identificador de utilizador aD usado para manter a sincronização entre Azure AD e AD. |
| outro Telefone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Código postal |X |X | | |
| língua preferida |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. Usado por ambos os sincroninos de hash de senha, autenticação pass-through e federação. |
| sn |X |X | | |
| fonteÂncora |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre ADDS e Azure AD. |
| SC |X |X | | |
| streetAddress |X |X | | |
| telefoneNúmero |X |X | | |
| miniaturafoto |X |X | | |
| título |X |X | | |
| utilizaçãoLocalização |X | | |propriedade mecânica. País/região do utilizador. Usado para atribuição de licença. |
| userPrincipalName |X | | |A UPN é o ID de login para o utilizador. Na maioria das vezes o mesmo que o valor [do correio]. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Nome do atributo | Utilizador | Contacto | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |
| contaEnabled |X | | |Define se uma conta está ativada. |
| cn |X | |X |Nome comum ou pseudónimo. Na maioria das vezes o prefixo do valor [do correio]. |
| displayName |X |X |X |Uma cadeia que representa o nome frequentemente mostrado como o nome amigável (nome do primeiro nome). |
| correio |X |X |X |endereço de e-mail completo. |
| membro | | |X | |
| SIDobjeto |X | |X |propriedade mecânica. Identificador de utilizador aD usado para manter a sincronização entre Azure AD e AD. |
| proxyAddresses |X |X |X |propriedade mecânica. Usado pela Azure AD. Contém todos os endereços de e-mail secundários para o utilizador. |
| pwdLastSet |X | | |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. |
| fonteÂncora |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre ADDS e Azure AD. |
| utilizaçãoLocalização |X | | |propriedade mecânica. País/região do utilizador. Usado para atribuição de licença. |
| userPrincipalName |X | | |Esta UPN é o ID de login para o utilizador. Na maioria das vezes o mesmo que o valor [do correio]. |

## <a name="intune"></a>Intune
| Nome do atributo | Utilizador | Contacto | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |
| contaEnabled |X | | |Define se uma conta está ativada. |
| c |X |X | | |
| cn |X | |X | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| correio |X |X |X | |
| apelido de correio |X |X |X | |
| membro | | |X | |
| SIDobjeto |X | |X |propriedade mecânica. Identificador de utilizador aD usado para manter a sincronização entre Azure AD e AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. Usado por ambos os sincroninos de hash de senha, autenticação pass-through e federação. |
| fonteÂncora |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre ADDS e Azure AD. |
| utilizaçãoLocalização |X | | |propriedade mecânica. País/região do utilizador. Usado para atribuição de licença. |
| userPrincipalName |X | | |A UPN é o ID de login para o utilizador. Na maioria das vezes o mesmo que o valor [do correio]. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Nome do atributo | Utilizador | Contacto | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |
| contaEnabled |X | | |Define se uma conta está ativada. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| empresa |X |X | | |
| paísCódigo |X |X | | |
| descrição |X |X |X | |
| displayName |X |X |X | |
| facsimilephonenumber |X |X | | |
| nomeDado |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| gestor |X |X | | |
| membro | | |X | |
| dispositivo móvel |X |X | | |
| SIDobjeto |X | |X |propriedade mecânica. Identificador de utilizador aD usado para manter a sincronização entre Azure AD e AD. |
| physicalDeliveryOfficeName |X |X | | |
| Código postal |X |X | | |
| língua preferida |X | | | |
| pwdLastSet |X | | |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. Usado por ambos os sincroninos de hash de senha, autenticação pass-through e federação. |
| sn |X |X | | |
| fonteÂncora |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre ADDS e Azure AD. |
| SC |X |X | | |
| streetAddress |X |X | | |
| telefoneNúmero |X |X | | |
| título |X |X | | |
| utilizaçãoLocalização |X | | |propriedade mecânica. País/região do utilizador. Usado para atribuição de licença. |
| userPrincipalName |X | | |A UPN é o ID de login para o utilizador. Na maioria das vezes o mesmo que o valor [do correio]. |

## <a name="3rd-party-applications"></a>Candidaturas de terceiros
Este grupo é um conjunto de atributos usados como os atributos mínimos necessários para uma carga de trabalho ou aplicação genérica. Pode ser usado para uma carga de trabalho não listada noutra secção ou para uma aplicação não Microsoft. É explicitamente utilizado para o seguinte:

* Yammer (apenas o Utilizador é consumido)
* [Cenários de colaboração cross-org híbridos (B2B) oferecidos por recursos como o SharePoint](https://go.microsoft.com/fwlink/?LinkId=747036)

Este grupo é um conjunto de atributos que podem ser usados se o diretório Azure AD não for usado para apoiar o Office 365, Dynamics ou Intune. Tem um pequeno conjunto de atributos fundamentais. Note que o simples sign-on ou o fornecimento de algumas aplicações de terceiros requer configurar a sincronização de atributos para além dos atributos aqui descritos. Os requisitos de aplicação são descritos no tutorial da [aplicação SaaS](../saas-apps/tutorial-list.md) para cada aplicação.

| Nome do atributo | Utilizador | Contacto | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |
| contaEnabled |X | | |Define se uma conta está ativada. |
| cn |X | |X | |
| displayName |X |X |X | |
| IDdefuncionário |X |  |  | |
| nomeDado |X |X | | |
| correio |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| membro | | |X | |
| SIDobjeto |X | | |propriedade mecânica. Identificador de utilizador aD usado para manter a sincronização entre Azure AD e AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |propriedade mecânica. Costumava saber quando invalidar fichas já emitidas. Usado por ambos os sincroninos de hash de senha, autenticação pass-through e federação. |
| sn |X |X | | |
| fonteÂncora |X |X |X |propriedade mecânica. Identificador imutável para manter a relação entre ADDS e Azure AD. |
| utilizaçãoLocalização |X | | |propriedade mecânica. País/região do utilizador. Usado para atribuição de licença. |
| userPrincipalName |X | | |A UPN é o ID de login para o utilizador. Na maioria das vezes o mesmo que o valor [do correio]. |

## <a name="windows-10"></a>Windows 10
Um computador (dispositivo) unido ao domínio do Windows 10 sincroniza alguns atributos ao Azure AD. Para obter mais informações sobre os cenários, consulte [o Connect dispositivos ligados ao Domínio Para AD Azure para experiências do Windows 10](../active-directory-azureadjoin-devices-group-policy.md). Estes atributos sincronizam sempre e o Windows 10 não aparece como uma aplicação que pode desseleccionar. Um computador unido ao domínio do Windows 10 é identificado por ter o utilizador atributoCertificado preenchido.

| Nome do atributo | Dispositivo | Comentário |
| --- |:---:| --- |
| contaEnabled |X | |
| dispositivoTrustType |X |Valor codificado para computadores ligados ao domínio. |
| displayName |X | |
| ms-DS-CreatorSID |X |Também chamado RegistradoOwnerReference. |
| objectGUID |X |Também chamado de dispositivoID. |
| SIDobjeto |X |Também chamado noLocalsSecurityIdentifier. |
| sistema operativo |X |Também chamado dispositivoOSType. |
| operatingSystemVersion |X |Também chamado dispositivoOSVersion. |
| userCertificate |X | |

Estes atributos para **o utilizador** são além das outras aplicações que selecionou.  

| Nome do atributo | Utilizador | Comentário |
| --- |:---:| --- |
| domínioFQDN |X |Também chamado dnsDomainName. Por exemplo, contoso.com. |
| domínioNetBios |X |Também chamado netBiosName. Por exemplo, CONTOSO. |
| msDS-KeyCredentialLink |X |Uma vez que o utilizador esteja inscrito no Windows Hello for Business. | 

## <a name="exchange-hybrid-writeback"></a>Troca de recoudação híbrida
Estes atributos são reescritos de Azure AD para no local Ative Directory quando seleciona para ativar o **Exchange hybrid**. Dependendo da sua versão Exchange, menos atributos podem ser sincronizados.

| Nome do atributo (AD no local) | Nome do atributo (Ligar UI) | Utilizador | Contacto | Grupo | Comentário |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directório-Object-Id |X | | |Derivado da cloudAnchor em Azure AD. Este atributo é novo no Exchange 2016 e no Windows Server 2016 AD. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |Arquivo Online: Permite aos clientes arquivar o correio. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Filtração: Escreve de volta no local a filtragem e os dados de remetentes seguros e bloqueados online dos clientes. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Filtração: Escreve de volta no local a filtragem e os dados de remetentes seguros e bloqueados online dos clientes. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Filtração: Escreve de volta no local a filtragem e os dados de remetentes seguros e bloqueados online dos clientes. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Ativar mensagens unificadas (UM) - Correio de voz online: Utilizado pela integração do Microsoft Lync Server para indicar ao Lync Server no local que o utilizador tem correio de voz em serviços online. |
| msExchUserHoldPolicies| ms-Exch-UserHoldPolicies |X | | |Detenção de litígios: Permite aos serviços de nuvem determinar quais os utilizadores que estão sob o domínio do Contencioso. |
| proxyAddresses| proxyAddresses |X |X |X |Apenas o endereço x500 do Exchange Online está inserido. |
| delegados públicos| ms-Exch-Public-Delegados  |X | | |Permite que seja concedida uma caixa de correio Online de troca de direitos aos utilizadores com caixa de correio de troca no local. Requer azure AD Connect construir 1.1.552.0 ou depois. |

## <a name="exchange-mail-public-folder"></a>Pasta Pública de Troca de Correio
Estes atributos são sincronizados a partir do diretório ativo no local para o Anúncio Azure quando seleciona para ativar a **pasta pública**de correio de troca .

| Nome do atributo | Pasta Pública | Comentário |
| --- | :---:| --- |
| displayName | X |  |
| correio | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| endereço alvo | X |  |

## <a name="device-writeback"></a>Repetição de escrita do dispositivo
Os objetos do dispositivo são criados em Diretório Ativo. Estes objetos podem ser dispositivos unidos a computadores Azure AD ou windows 10 unidos pelo domínio.

| Nome do atributo | Dispositivo | Comentário |
| --- |:---:| --- |
| altIdentidades de Segurança |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Apenas com esquema ad 2016 do Windows Server 2016 |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| MsDS-RegisteredOwner |X | |

## <a name="notes"></a>Notas
* Ao utilizar um ID alternativo, o utilizador principal do utilizador no local é sincronizado com o atributo Azure AD noPremisesUserPrincipalName. O atributo de ID alternativo, por exemplo, correio, é sincronizado com o atributo Azure AD userPrincipalName.
* Nas listas acima, o tipo de objeto **Utilizador** também se aplica ao tipo de objeto **iNetOrgPerson**.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a configuração de [sincronização azure AD Connect.](how-to-connect-sync-whatis.md)

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
