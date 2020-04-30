---
title: População UserPrincipalName do Azure AD
description: O seguinte documento descreve como o atributo userprincipalname é povoado.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c748df10e432e3bebbce0dc8cb39dd2101d52e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680030"
---
# <a name="azure-ad-userprincipalname-population"></a>População UserPrincipalName do Azure AD

Este artigo descreve como o atributo userprincipalname é povoado em Azure Ative Directory (Azure AD).
O valor do atributo userPrincipalName é o nome de utilizador da AD Azure para as contas de utilizador.

## <a name="upn-terminology"></a>Terminologia UPN
Neste artigo é utilizada a seguinte terminologia:

|Termo|Descrição|
|-----|-----|
|Domínio inicial|O domínio padrão (onmicrosoft.com) no Azure AD Tenant. Por exemplo, contoso.onmicrosoft.com.|
|Endereço de encaminhamento de e-mail online da Microsoft (MOERA)|A Azure AD calcula o MOERA a partir do atributo Do AD NickName azure e do domínio inicial da AD Azure como &lt;MailNickName&gt;&#64;&lt;domínio&gt;inicial .|
|No local correioNickName atributo|Um atributo em Diretório Ativo, do qual representa o pseudónimo de um utilizador numa organização de Intercâmbio.|
|Atribuição de correio no local|Um atributo em Diretório Ativo, qual representa o endereço de e-mail de um utilizador|
|Endereço SMTP Principal|O endereço de e-mail primário de um objeto de destinatário de troca. Por exemplo, sMTP:utilizador\@contoso.com.|
|Id de login alternativo|Um atributo no local que não seja o UserPrincipalName, como o atributo de correio, utilizado para o início de sessão.|

## <a name="what-is-userprincipalname"></a>O que é UserPrincipalName?
UserPrincipalName é um atributo que é um nome de login ao estilo da Internet para um utilizador baseado no [RFC 822](https://www.ietf.org/rfc/rfc0822.txt)padrão da Internet . 

### <a name="upn-format"></a>Formato UPN
A UPN é constituída por um prefixo UPN (nome da conta de utilizador) e um sufixo UPN (um nome de domínio DNS). O prefixo é unido ao sufixo\@usando o símbolo " . Por exemplo,\@"alguém example.com". A UPN deve ser única entre todos os objetos principais de segurança dentro de uma floresta de diretórios. 

## <a name="upn-in-azure-ad"></a>UPN em Azure AD 
A UPN é utilizada pela Azure AD para permitir que os utilizadores se inscrevam.  A UPN que um utilizador pode utilizar depende se o domínio foi verificado ou não.  Se o domínio tiver sido verificado, então um utilizador com esse sufixo será autorizado a iniciar sessão no Azure AD.  

O atributo é sincronizado pela Azure AD Connect.  Durante a instalação, pode ver os domínios verificados e os que não o fizeram.

   ![Domínios não verificados](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Id de login alternativo
Em alguns ambientes, os utilizadores finais só podem estar cientes do seu endereço de e-mail e não da sua UPN.  A utilização de endereços de e-mail pode dever-se a uma política corporativa ou a uma dependência de aplicação de aplicações no local.

O ID de login alternativo permite configurar uma experiência de login onde os utilizadores podem iniciar sessão com um atributo diferente do seu UPN, como o correio.

Para ativar o ID de login alternativo com o Azure AD, não são necessários passos de configuração adicionais quando utilizar o Azure AD Connect. ID alternativo pode ser configurado diretamente a partir do assistente. Consulte a configuração de entrada de AD Azure para os seus utilizadores na secção Sync. Sob o nome principal do **utilizador,** selecione o atributo para id de login alternativo.

![Domínios não verificados](./media/plan-connect-userprincipalname/altloginid.png)  

Para mais informações, consulte [Configure Alternate login ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) e [Azure AD configuração de login](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Sufixo UPN não verificado
Se o atributo de nome de utilizador/identificação de login alternativo não for verificado com o Azure AD Tenant, então o valor do atributo do Nome principal do utilizador da AD Azure está definido para MOERA. A Azure AD calcula o MOERA a partir do atributo Azure AD MailNickName e do domínio inicial da AD Azure &lt;como&gt; MailNickName&#64;&lt;domínio&gt;inicial .

## <a name="verified-upn-suffix"></a>Sufixo UPN verificado
Se o atributo de nome principal/identificação de login alternativo for verificado com o Inquilino AD Azure, então o valor do atributo do Nome principal do Utilizador Da AD Azure será o mesmo que o atributo de identificação de userprincipalname/nome alternativo de login no local.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Cálculo do valor do atributo da AD Azure ADNickName
Uma vez que o valor do atributo do Nome de Ad Azure pode ser definido para moera, é importante entender como é calculado o valor do atributo Do AD MailNickName, que é o prefixo MOERA.

Quando um objeto de utilizador é sincronizado com um Inquilino AD Azure pela primeira vez, a Azure AD verifica os seguintes itens na ordem dada e define o valor do atributo MailNickName ao primeiro existente:

- No local correioNickName atributo
- Prefixo do endereço SMTP primário
- Prefixo do atributo de correio no local
- Prefixo do utilizador no localPrincipalName atribui/ID de login alternativo
- Prefixo do endereço smtp secundário

Quando as atualizações a um objeto de utilizador são sincronizadas para o Inquilino AD Azure, a Azure AD atualiza o valor de atributo MailNickName apenas no caso de existir uma atualização para o valor de atributo do MailNickName no local.

>[!IMPORTANT]
>A Azure AD recalcula o valor do atributo do UserPrincipalName apenas no caso de uma atualização ao atributo userprincipalname/valor de ID de login alternativo ser sincronizado para o Inquilino AD Azure. 
>
>Sempre que a AD Azure recalcula o atributo userprincipalname, também recalcula o MOERA. 

## <a name="upn-scenarios"></a>Cenários UPN
Seguem-se exemplos de como a UPN é calculada com base no cenário determinado.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Cenário 1: Sufixo UPN não verificado – sincronização inicial

![Cenário1](./media/plan-connect-userprincipalname/example1.png)

Objeto de utilizador no local:
- mailNickName &lt;: não definido&gt;
- proxyAddresses :SMTP:us1@contoso.com{ }
- correio:us2@contoso.com
- userPrincipalName :us3@contoso.com

Sincronizado o objeto de utilizador ao Azure AD Tenant pela primeira vez
- Definir aatribuição de MailNickName Azure AD ao prefixo de endereço SMTP primário.
- Desloque &lt;o&gt; MOERA para MailNickName&#64;&lt;domínio&gt;inicial .
- Definir aatribuição de nome de utilizador ad ad azure ao MOERA.

Objeto de utilizador do Inquilino Azure AD:
- MailNickName : us1           
- Nome principal do utilizador :us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Cenário 2: Sufixo UPN não verificado – definido no local mailNickName atributo

![Cenário2](./media/plan-connect-userprincipalname/example2.png)

Objeto de utilizador no local:
- mailNickName : us4
- proxyAddresses :SMTP:us1@contoso.com{ }
- correio:us2@contoso.com
- userPrincipalName :us3@contoso.com

Sincronizar a atualização no mail nickname no local atribuiao ao Inquilino AD Azure
- Atualização Azure AD MailNickName atribui com atributo nickname no local.
- Uma vez que não existe nenhuma atualização para o atributo do userPrincipalName no local, não existe qualquer alteração ao atributo do Nome principal do utilizador da AD Azure.

Objeto de utilizador do Inquilino Azure AD:
- MailNickName : us4
- Nome principal do utilizador :us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Cenário 3: Sufixo UPN não verificado – atualização no local userPrincipalName atributo

![Cenário3](./media/plan-connect-userprincipalname/example3.png)

Objeto de utilizador no local:
- mailNickName : us4
- proxyAddresses :SMTP:us1@contoso.com{ }
- correio:us2@contoso.com
- userPrincipalName :us5@contoso.com

Sincronizar a atualização sobre o utilizador no localPrincipalName atribui ao Inquilino AD Azure
- Atualização no utilizador no localPrincipalName atributo desencadeia o recálculo do atribuidor MOERA e Azure AD UserPrincipalName.
- Desloque &lt;o&gt; MOERA para MailNickName&#64;&lt;domínio&gt;inicial .
- Definir aatribuição de nome de utilizador ad ad azure ao MOERA.

Objeto de utilizador do Inquilino Azure AD:
- MailNickName : us4
- Nome principal do utilizador :us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Cenário 4: Sufixo UPN não verificado – endereço primário de SMTP e atributo de correio no local

![Cenário4](./media/plan-connect-userprincipalname/example4.png)

Objeto de utilizador no local:
- mailNickName : us4
- proxyAddresses :SMTP:us6@contoso.com{ }
- correio:us7@contoso.com
- userPrincipalName :us5@contoso.com

Sincronizar a atualização do atributo de correio no local e o endereço SMTP primário ao Inquilino AD Azure
- Após a sincronização inicial do objeto de utilizador, as atualizações para o atributo de correio no local e o endereço SMTP primário não afetarão o MailNickName Azure AD ou o atributo UserPrincipalName.

Objeto de utilizador do Inquilino Azure AD:
- MailNickName : us4
- Nome principal do utilizador :us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Cenário 5: Sufixo UPN verificado – atualização no local userPrincipalName atributo sufixo

![Cenário5](./media/plan-connect-userprincipalname/example5.png)

Objeto de utilizador no local:
- mailNickName : us4
- proxyAddresses :SMTP:us6@contoso.com{ }
- correio:us7@contoso.com
- userPrincipalName :us5@verified.contoso.com

Sincronizar a atualização sobre o utilizador no localPrincipalName atribui ao Inquilino AD Azure
- Atualização no utilizador no localPrincipalName atributo desencadeia o recálculo do atributo do Nome principal do utilizador da AD Azure.
- Definir aatribuição de nome de utilizador adato de adsônio a utilizador no local PrincipalName, uma vez que o sufixo UPN é verificado com o Inquilino AD Azure.

Objeto de utilizador do Inquilino Azure AD:
- MailNickName : us4     
- Nome principal do utilizador :us5@verified.contoso.com

## <a name="next-steps"></a>Passos Seguintes
- [Integrar os diretórios no local com o Azure Active Directory](whatis-hybrid-identity.md)
- [Instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md)
