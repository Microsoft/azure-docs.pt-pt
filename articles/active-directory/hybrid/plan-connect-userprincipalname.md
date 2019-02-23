---
title: População UserPrincipalName do Azure AD
description: O documento seguinte descreve como o atributo UserPrincipalName é preenchido.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: c198b329f07c5c7459f25165b2dc0a3bfa032276
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672854"
---
# <a name="azure-ad-userprincipalname-population"></a>População UserPrincipalName do Azure AD

Este artigo descreve como o atributo UserPrincipalName é preenchido no Azure Active Directory (Azure AD).
O valor do atributo UserPrincipalName é o nome de utilizador do Azure AD para as contas de utilizador.

## <a name="upn-terminology"></a>Terminologia UPN
A seguinte terminologia é usada neste artigo:

|Termo|Descrição|
|-----|-----|
|Domínio Inicial|O domínio predefinido (onmicrosoft.com) no inquilino do Azure AD. Por exemplo, contoso.onmicrosoft.com.|
|Microsoft Online encaminhamento endereço de E-Mail (MOERA)|O Azure AD calcula MOERA do atributo MailNickName do Azure AD e o domínio inicial do Azure AD como &lt;MailNickName&gt;&#64;&lt;domínio inicial&gt;.|
|Atributo de mailNickName no local|Um atributo no Active Directory, o valor que representa o alias de um utilizador numa organização do Exchange.|
|Atributo de correio no local|Um atributo no Active Directory, o valor que representa o endereço de e-mail de um utilizador|
|Endereço SMTP principal|O endereço de e-mail principal de um objeto de destinatário do Exchange. Por exemplo, SMTP:user\@contoso.com.|
|ID de início de sessão alternativo|Um atributo no local que não seja o UserPrincipalName, por exemplo, o atributo de correio, utilizado para início de sessão.|

## <a name="what-is-userprincipalname"></a>O que é o UserPrincipalName?
UserPrincipalName é um atributo que é um nome de início de sessão de estilo da Internet para um utilizador com base no padrão de Internet [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>Formato UPN
Um UPN é constituído por um prefixo UPN (nome de conta de utilizador) e um sufixo UPN (um nome de domínio DNS). O prefixo está associado com o sufixo usando o "\@" símbolo. Por exemplo, "alguém\@example.com". Um UPN tem de ser exclusivo entre todos os objetos de principal de segurança dentro de uma floresta do diretório. 

## <a name="upn-in-azure-ad"></a>UPN no Azure AD 
O UPN é utilizado pelo Azure AD para permitir que os utilizadores para início de sessão.  O UPN que um utilizador pode utilizar, depende se pretende ou não ter sido verificado o domínio.  Se o domínio foi verificado, em seguida, um utilizador com esse sufixo terá permissão para início de sessão para o Azure AD.  

O atributo é sincronizado pelo Azure AD Connect.  Durante a instalação, pode ver os domínios que foram verificados e aqueles que não o tiver feito.

   ![Domínios não verificados](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>ID de início de sessão alternativo
Em alguns ambientes, os utilizadores finais poderá apenas estar cientes do respetivo endereço de e-mail e não respetivo UPN.  A utilização do endereço de e-mail pode ser devido a uma política da empresa ou uma dependência de aplicação de linha de negócio no local.

ID de início de sessão alternativo permite-lhe configurar uma experiência de início de sessão em que os utilizadores podem iniciar sessão com um atributo que não seja o respetivo UPN, como o mail.

Para ativar o ID de início de sessão alternativo com o Azure AD, os passos não configurações adicionais são necessários quando utilizar o Azure AD Connect. ID alternativo pode ser configurado diretamente a partir do assistente. Ver configuração início de sessão do Azure AD para os seus utilizadores na secção sincronização. Sob o **Nome Principal de utilizador** pendente, selecione o atributo para o ID de início de sessão alternativo.

![Domínios não verificados](./media/plan-connect-userprincipalname/altloginid.png)  

Para obter mais informações, consulte [ID de início de sessão alternativo configurar](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) e [configuração do início de sessão no Azure AD](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Sufixo do UPN não verificado
Se o sufixo do ID de início de sessão alternativa/atributo do locais UserPrincipalName não é verificado com o inquilino do Azure AD, em seguida, o valor do atributo UserPrincipalName do Azure AD estiver definido como MOERA. O Azure AD calcula MOERA do atributo MailNickName do Azure AD e o domínio inicial do Azure AD como &lt;MailNickName&gt;&#64;&lt;domínio inicial&gt;.

## <a name="verified-upn-suffix"></a>Sufixo do UPN verificado
Se o UserPrincipalName no local. o atributo/alternativa sufixo do ID de início de sessão é verificado com o inquilino do Azure AD, em seguida, o valor do atributo UserPrincipalName do Azure AD vai ser o mesmo que o valor de ID de início de sessão de atributo/alternativa de UserPrincipalName de no local.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Cálculo do valor de atributo MailNickName do AD do Azure
Uma vez que o valor do atributo UserPrincipalName do Azure AD foi possível definir a MOERA, é importante compreender como o valor do atributo MailNickName do Azure AD, que é o prefixo MOERA, é calculado.

Quando um objeto de utilizador está sincronizado a um inquilino do Azure AD pela primeira vez, o Azure AD verifica os seguintes itens na ordem apresentada e define o valor do atributo MailNickName ao primeiro existente:

- Atributo de mailNickName no local
- Prefixo de endereço SMTP principal
- Prefixo do atributo de correio no local
- Prefixo de ID de início de sessão de atributo/alternativa de userPrincipalName no local
- Prefixo de endereço de smtp secundário

Quando as atualizações a um objeto de utilizador são sincronizadas com o inquilino do Azure AD, o Azure AD atualiza o valor do atributo MailNickName apenas no caso de uma atualização para o valor do atributo mailNickName no local.

>[!IMPORTANT]
>O Azure AD recalcula o valor do atributo UserPrincipalName apenas no caso de uma atualização para o valor de ID de início de sessão do locais UserPrincipalName atributo/alternativa está sincronizada com o inquilino do Azure AD. 
>
>Sempre que o Azure AD recalcula o atributo UserPrincipalName, ele recalcula também o MOERA. 

## <a name="upn-scenarios"></a>Cenários UPN
Seguem-se cenários de exemplo de como o UPN é calculado com base num determinado cenário.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Cenário 1: Sufixo do UPN não verificado – sincronização inicial

![Scenario1](./media/plan-connect-userprincipalname/example1.png)

Objeto de utilizador no local:
- mailNickName: &lt;não definido&gt;
- proxyAddresses        : {SMTP:us1@contoso.com}
- email: us2@contoso.com
- userPrincipalName : us3@contoso.com`

Sincronizar o objeto de utilizador para o inquilino do Azure AD pela primeira vez
- Defina o atributo MailNickName do Azure AD para o prefixo de endereço SMTP principal.
- Definir MOERA &lt;MailNickName&gt;&#64;&lt;domínio inicial&gt;.
- Defina o atributo UserPrincipalName do Azure AD para MOERA.

Objeto de utilizador do inquilino do AD do Azure:
- MailNickName      : us1           
- UserPrincipalName : us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Cenário 2: Sufixo do UPN não verificado – conjunto locais atributo mailNickName

![Scenario2](./media/plan-connect-userprincipalname/example2.png)

Objeto de utilizador no local:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us1@contoso.com}
- email: us2@contoso.com
- userPrincipalName : us3@contoso.com

Sincronizar atualizações no atributo de mailNickName no local para o inquilino do Azure AD
- Atualize o atributo MailNickName do Azure AD com o atributo mailNickName no local.
- Porque não existe nenhuma atualização para o atributo de userPrincipalName no local, não há nenhuma alteração ao atributo UserPrincipalName do Azure AD.

Objeto de utilizador do inquilino do AD do Azure:
- MailNickName      : us4
- UserPrincipalName : us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Cenário 3: Sufixo do UPN não verificado – atualização no local atributo userPrincipalName

![Scenario3](./media/plan-connect-userprincipalname/example3.png)

Objeto de utilizador no local:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us1@contoso.com}
- email: us2@contoso.com
- userPrincipalName : us5@contoso.com

Sincronizar atualizações no atributo de userPrincipalName no local para o inquilino do Azure AD
- Atualização no atributo de userPrincipalName no local aciona o recálculo do atributo MOERA e UserPrincipalName do Azure AD.
- Definir MOERA &lt;MailNickName&gt;&#64;&lt;domínio inicial&gt;.
- Defina o atributo UserPrincipalName do Azure AD para MOERA.

Objeto de utilizador do inquilino do AD do Azure:
- MailNickName      : us4
- UserPrincipalName : us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Cenário 4: Atributo de correio não verificado sufixo UPN – endereço SMTP principal de atualização e no local

![Scenario4](./media/plan-connect-userprincipalname/example4.png)

Objeto de utilizador no local:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us6@contoso.com}
- email: us7@contoso.com
- userPrincipalName : us5@contoso.com

Sincronizar a atualização no atributo de correio no local e o endereço SMTP principal para o inquilino do Azure AD
- Após a sincronização inicial de objeto do usuário, de atualizações para o atributo de correio no local e o endereço SMTP principal não afetarão o MailNickName do AD do Azure ou o atributo UserPrincipalName.

Objeto de utilizador do inquilino do AD do Azure:
- MailNickName      : us4
- UserPrincipalName : us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Cenário 5: Sufixo do UPN verificado – atualização no local sufixo de atributo userPrincipalName

![Scenario5](./media/plan-connect-userprincipalname/example5.png)

Objeto de utilizador no local:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us6@contoso.com}
- email: us7@contoso.com
- userPrincipalName : us5@verified.contoso.com

Sincronizar atualizações no atributo de userPrincipalName no local para o inquilino do Azure AD
- Atualização no local userPrincipalName atributo acionadores recálculo de atributo UserPrincipalName do Azure AD.
- Defina o atributo UserPrincipalName do Azure AD para o atributo de userPrincipalName no local como o sufixo UPN é verificado com o inquilino do Azure AD.

Objeto de utilizador do inquilino do AD do Azure:
- MailNickName      : us4     
- UserPrincipalName : us5@verified.contoso.com

## <a name="next-steps"></a>Próximos Passos
- [Integrar os diretórios no local com o Azure Active Directory](whatis-hybrid-identity.md)
- [Instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md)
