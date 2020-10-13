---
title: População UserPrincipalName do Azure AD
description: O documento que se segue descreve como o atributo UserPrincipalName é preenchido.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b4bbcac110398ee4ff132b76ce8c4868ee17f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317596"
---
# <a name="azure-ad-userprincipalname-population"></a>População UserPrincipalName do Azure AD

Este artigo descreve como o atributo UserPrincipalName é povoado no Azure Ative Directory (Azure AD).
O valor do atributo UserPrincipalName é o nome de utilizador Azure AD para as contas do utilizador.

## <a name="upn-terminology"></a>Terminologia UPN
É utilizada neste artigo a seguinte terminologia:

|Termo|Descrição|
|-----|-----|
|Domínio inicial|O domínio predefinido (onmicrosoft.com) no Azure AD Tenant. Por exemplo, contoso.onmicrosoft.com.|
|Microsoft Online Email Encaminhamento Endereço (MOERA)|A Azure AD calcula o MOERA a partir do atributo Azure AD MailNickName e do domínio inicial da AZure AD como &lt; MailNickName &gt;&#64;domínio inicial &lt; &gt; .|
|Atributo De Correio no local NickName|Um atributo no Ative Directory, que representa o pseudónimo de um utilizador numa organização de Intercâmbio.|
|Atributo de correio no local|Um atributo no Ative Directory, o valor do qual representa o endereço de e-mail de um utilizador|
|Endereço SMTP Principal|O endereço de e-mail primário de um objeto do destinatário da Bolsa. Por exemplo, SMTP:user \@ contoso.com.|
|ID de login alternativo|Um atributo no local que não seja o UserPrincipalName, como o atributo de correio, utilizado para o s-in.|

## <a name="what-is-userprincipalname"></a>O que é UserPrincipalName?
UserPrincipalName é um atributo que é um nome de login estilo Internet para um utilizador baseado na norma de Internet [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>Formato UPN
Um UPN é composto por um prefixo UPN (o nome da conta do utilizador) e um sufixo UPN (um nome de domínio DNS). O prefixo é associado ao sufixo usando o símbolo " \@ " Por exemplo, "alguém \@ example.com". Um UPN deve ser único entre todos os objetos principais de segurança dentro de uma floresta de diretórios. 

## <a name="upn-in-azure-ad"></a>UPN em Azure AD 
A UPN é utilizada pela Azure AD para permitir que os utilizadores se inscrevam.  A UPN que um utilizador pode usar depende se o domínio foi verificado ou não.  Se o domínio tiver sido verificado, então um utilizador com esse sufixo será autorizado a iniciar sing-in no Azure AD.  

O atributo é sincronizado pelo Azure AD Connect.  Durante a instalação, pode ver os domínios que foram verificados e os que não foram verificados.

   ![Domínios não verificados](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>ID de login alternativo
Em alguns ambientes, os utilizadores finais só podem estar cientes do seu endereço de e-mail e não da sua UPN.  A utilização de endereço de e-mail pode dever-se a uma política corporativa ou a uma dependência de aplicações de linha de negócio no local.

O ID de login alternativo permite-lhe configurar uma experiência de login onde os utilizadores podem iniciar sessão com um atributo diferente do seu UPN, como o correio.

Para ativar o ID de login alternativo com Azure AD, não são necessários passos de configurações adicionais quando utilizar o Azure AD Connect. ID alternativo pode ser configurado diretamente a partir do assistente. Consulte a configuração de insusição AD do Azure para os seus utilizadores na secção Sync. No nome principal do **utilizador,** selecione o atributo para ID de login alternativo.

![Screenshot que realça a lista de nomes principais do utilizador onde seleciona o atributo de ID de login alternativo.](./media/plan-connect-userprincipalname/altloginid.png)  

Para obter mais informações, consulte [configurar configuração de ID de login alternativo](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) e [configuração de login AD Azure](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Sufixo upn não verificado
Se o atributo UserPrincipalName do Utilizador no local não for verificado com o Azure AD Tenant, então o valor do atributo AZURE AD UserPrincipalName é definido como MOERA. A Azure AD calcula o MOERA a partir do atributo Azure AD MailNickName e do domínio inicial Azure AD como &lt; MailNickName &gt;&#64;domínio inicial &lt; &gt; .

## <a name="verified-upn-suffix"></a>Sufixo upn verificado
Se o atributo UserPrincipalName do Utilizador no local for verificado com o Inquilino Azure AD, então o valor do atributo Azure AD UserPrincipalName será o mesmo que o atributo UserPrincipalName no local atribuído/Valor de ID de login alternativo.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Azure AD MailNickName atribui cálculo de valor
Uma vez que o valor do atributo Azure AD AD UserPrincipalName pode ser definido como MOERA, é importante entender como é calculado o valor do atributo Azure AD MailNickName, que é o prefixo MOERA.

Quando um objeto de utilizador é sincronizado com um Inquilino AD Azure pela primeira vez, a Azure AD verifica os seguintes itens na encomenda dada e define o valor de atributo MailNickName ao primeiro existente:

- Atributo De Correio no local NickName
- Prefixo do endereço SMTP primário
- Prefixo do atributo de correio no local
- Prefixo do utilizador no local Atributo PrencipalName/ID de login alternativo
- Prefixo do endereço de smtp secundário

Quando as atualizações a um objeto de utilizador são sincronizadas com o Azure AD Tenant, a Azure AD atualiza o valor do atributo MailNickName apenas no caso de existir uma atualização para o valor de atributo do Correio No local.

>[!IMPORTANT]
>A Azure AD recalcula o valor do atributo UserPrincipalName apenas no caso de uma atualização ao atributo UserPrincipalName no local ser sincronizada com o Azure AD Tenant. 
>
>Sempre que a Azure AD recalcula o atributo UserPrincipalName, também recalcula o MOERA. 

## <a name="upn-scenarios"></a>Cenários UPN
Seguem-se cenários de exemplo de como a UPN é calculada com base no cenário dado.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Cenário 1: Sufixo UPN não verificado – sincronização inicial

![Cenário1](./media/plan-connect-userprincipalname/example1.png)

Objeto de utilizador no local:
- mailNickName : &lt; não definido&gt;
- ProxyAddresses : { SMTP:us1@contoso.com }
- Correio: us2@contoso.com
- nome do utilizadorPrincipal: us3@contoso.com

Sincronizado o objeto do utilizador para Azure AD Tenant pela primeira vez
- Desajuste O prefixo do Correio Ad Azure Atribua ao prefixo primário do endereço SMTP.
- Desabine o MOERA ao  &lt; MailNickName &gt;&#64;domínio inicial &lt; &gt; .
- Desconte o atributo Azure AD UserPrincipalName ao MOERA.

Objeto de utilizador Azure AD Tenant:
- MailNickName : us1           
- Nome do UtilizadorPrincipal: us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Cenário 2: Sufixo upn não verificado – definido no local e-mail AtributoNickName

![Cenário2](./media/plan-connect-userprincipalname/example2.png)

Objeto de utilizador no local:
- mailNickName : us4
- ProxyAddresses : { SMTP:us1@contoso.com }
- Correio: us2@contoso.com
- nome do utilizadorPrincipal: us3@contoso.com

Sincronizar atualização no correio de NickName atribuído ao Azure AD Tenant
- Atualizar A azure AD MailNickName atributo com atributo de Correio no local NickName.
- Como não existe nenhuma atualização para o atributo Do Nome Do Utilizador no Local, não existe qualquer alteração ao atributo Azure AD UserPrincipalName.

Objeto de utilizador Azure AD Tenant:
- MailNickName : us4
- Nome do UtilizadorPrincipal: us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Cenário 3: Sufixo UPN não verificado – atributo de utilizador de atualização no local

![Cenário 3](./media/plan-connect-userprincipalname/example3.png)

Objeto de utilizador no local:
- mailNickName : us4
- ProxyAddresses : { SMTP:us1@contoso.com }
- Correio: us2@contoso.com
- nome do utilizadorPrincipal: us5@contoso.com

Sincronizar atualização no utilizador no local Atribua o Nome Azure AD
- A atualização no atributo Do utilizador No-In-In-InsPrincipalName desencadeia um recálculo do atributo MOERA e Azure AD UserPrincipalName.
- Desabine o MOERA ao &lt; MailNickName &gt;&#64;domínio inicial &lt; &gt; .
- Desconte o atributo Azure AD UserPrincipalName ao MOERA.

Objeto de utilizador Azure AD Tenant:
- MailNickName : us4
- Nome do UtilizadorPrincipal: us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Cenário 4: Sufixo UPN não verificado – atualizar endereço SMTP primário e atributo de correio no local

![Cenário4](./media/plan-connect-userprincipalname/example4.png)

Objeto de utilizador no local:
- mailNickName : us4
- ProxyAddresses : { SMTP:us6@contoso.com }
- Correio: us7@contoso.com
- nome do utilizadorPrincipal: us5@contoso.com

Sincronizar atualização no atributo de correio no local e endereço SMTP primário para Azure AD Tenant
- Após a sincronização inicial do objeto do utilizador, as atualizações ao atributo de correio no local e ao endereço SMTP primário não afetarão o Azure AD MailNickName ou o atributo UserPrincipalName.

Objeto de utilizador Azure AD Tenant:
- MailNickName : us4
- Nome do UtilizadorPrincipal: us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Cenário 5: Sufixo verificado da UPN – atualização no local do utilizadorPrincipalName atribui sufixo

![Cenário5](./media/plan-connect-userprincipalname/example5.png)

Objeto de utilizador no local:
- mailNickName : us4
- ProxyAddresses : { SMTP:us6@contoso.com }
- Correio: us7@contoso.com
- nome do utilizadorPrincipal: us5@verified.contoso.com

Sincronizar atualização no utilizador no local Atribua o Nome principal ao Azure AD Tenant
- A atualização no utilizador No local O atributo Do Nome Principal do Utilizador aciona um recálculo do atributo Azure AD UserPrincipalName.
- Deteta a azure AD UserPrincipalName atributo ao utilizador no local Atribua o Nome Principal como o sufixo UPN é verificado com o Azure AD Tenant.

Objeto de utilizador Azure AD Tenant:
- MailNickName : us4     
- Nome do UtilizadorPrincipal: us5@verified.contoso.com

## <a name="next-steps"></a>Passos Seguintes
- [Integrar os diretórios no local com o Azure Active Directory](whatis-hybrid-identity.md)
- [Instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md)