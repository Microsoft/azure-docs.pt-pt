---
title: Federação direta com fornecedor de identidade para B2B - Azure AD
description: Federado diretamente com um fornecedor de identidade SAML ou WS-Fed para que os hóspedes possam iniciar sessão nas suas aplicações Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 299b0a677e7ca7bea9481d94ecf98c993af0a6ed
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591221"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Federação direta com AD FS e fornecedores de terceiros para utilizadores convidados (pré-visualização)
|     |
| --- |
| A federação direta é uma característica pública de pré-visualização do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Este artigo descreve como criar uma federação direta com outra organização para a colaboração B2B. Pode criar uma federação direta com qualquer organização cujo fornecedor de identidade (IDP) apoie o protocolo SAML 2.0 ou WS-Fed.
Quando cria uma federação direta com o IDP de um parceiro, os novos utilizadores convidados desse domínio podem usar a sua própria conta organizacional gerida pelo IDP para se inscrever em seu inquilino Azure AD e começar a colaborar consigo. Não há necessidade de o utilizador convidado criar uma conta Azure AD separada.
> [!NOTE]
> Os utilizadores convidados da federação direta devem assinar através de um link que inclua o contexto do inquilino (por exemplo, `https://myapps.microsoft.com/?tenantid=<tenant id>` `https://portal.azure.com/<tenant id>` ou, no caso de um domínio verificado, `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com` ). As ligações diretas às aplicações e recursos também funcionam desde que incluam o contexto do arrendatário. Os utilizadores da federação direta não podem assinar usando pontos finais comuns que não têm contexto de inquilino. Por exemplo, a `https://myapps.microsoft.com` utilização, `https://portal.azure.com` ou `https://teams.microsoft.com` resultará num erro.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Quando é que um utilizador convidado é autenticado com a federação direta?
Depois de configurar a federação direta com uma organização, quaisquer novos utilizadores convidados que convidar serão autenticados usando a federação direta. É importante notar que a criação da federação direta não altera o método de autenticação para os utilizadores convidados que já resgataram um convite de si. Eis alguns exemplos:
 - Se os utilizadores convidados já tiverem reembolsado os seus convites, e posteriormente configurar a federação direta com a sua organização, esses utilizadores convidados continuarão a utilizar o mesmo método de autenticação que utilizaram antes de configurar em direção à federação direta.
 - Se criar uma federação direta com uma organização parceira e convidar utilizadores convidados, e depois a organização parceira se mudar para a Azure AD, os utilizadores convidados que já resgataram convites continuarão a utilizar a federação direta, desde que exista a política direta da federação no seu inquilino.
 - Se eliminar a federação direta com uma organização parceira, qualquer utilizador convidado que utilize atualmente a federação direta não poderá iniciar o seu acesso.

Em qualquer um destes cenários, pode atualizar o método de autenticação de um utilizador convidado, apagando a conta de utilizador convidado do seu diretório e reconvidando-a.

A federação direta está ligada a espaços de nomes de domínio, como contoso.com e fabrikam.com. Ao estabelecer uma configuração direta da federação com AD FS ou um IDP de terceiros, as organizações associam um ou mais espaços de nome de domínio a estes IDPs. 

## <a name="end-user-experience"></a>Experiência do utilizador final 
Com a federação direta, os utilizadores convidados assinam no seu inquilino Azure AD usando a sua própria conta organizacional. Quando estão a aceder a recursos partilhados e são solicitados para o registo, os utilizadores da federação direta são redirecionados para o seu IDP. Após o sucesso da entrada, são devolvidos à Azure AD para aceder aos recursos. Os tokens de atualização dos utilizadores da federação direta são válidos por 12 horas, o [comprimento padrão para passar por token de atualização](../develop/active-directory-configurable-token-lifetimes.md#exceptions) em Azure AD. Se o IdP federado tiver SSO ativado, o utilizador irá experimentar SSO e não verá qualquer pedido de entrada após a autenticação inicial.

## <a name="limitations"></a>Limitações

### <a name="dns-verified-domains-in-azure-ad"></a>Domínios verificados pelo DNS em Azure AD
O domínio com o que pretende federar ***não*** deve ser verificado em Azure AD. Você está autorizado a configurar a federação direta com inquilinos não geridos (verificados por e-mail ou "virais") Azure AD porque eles não são verificados pelo DNS.

### <a name="authentication-url"></a>URL de autenticação
A federação direta só é permitida para políticas em que o domínio do URL de autenticação corresponda ao domínio alvo, ou quando o URL de autenticação é um desses fornecedores de identidade permitidos (esta lista está sujeita a alterações):

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Por exemplo, ao criar uma federação direta para **fabrikam.com,** o URL de autenticação `https://fabrikam.com/adfs` passará na validação. Um hospedeiro no mesmo domínio também passará, por `https://sts.fabrikam.com/adfs` exemplo. No entanto, o URL de autenticação `https://fabrikamconglomerate.com/adfs` ou para o mesmo domínio não `https://fabrikam.com.uk/adfs` passará.

### <a name="signing-certificate-renewal"></a>Renovação de certificado de assinatura
Se especificar o URL dos metadados nas definições do fornecedor de identidade, o Azure AD renovará automaticamente o certificado de assinatura quando expirar. No entanto, se o certificado for rodado por qualquer motivo antes do tempo de validade, ou se não fornecer um URL de metadados, o Azure AD não poderá renová-lo. Neste caso, terá de atualizar manualmente o certificado de assinatura.

### <a name="limit-on-federation-relationships"></a>Limite às relações da federação
Atualmente, um máximo de 1.000 relações da federação é apoiado. Este limite inclui tanto [as federações internas](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) como as federações diretas.

### <a name="limit-on-multiple-domains"></a>Limite em vários domínios
Atualmente não apoiamos a federação direta com vários domínios do mesmo inquilino.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Posso criar uma federação direta com um domínio para o qual existe um inquilino não gerido (verificado por e-mail) ? 
Yes. Se o domínio não tiver sido verificado e o inquilino não tiver sido submetido a uma aquisição de [administração,](../users-groups-roles/domains-admin-takeover.md)pode criar uma federação direta com esse domínio. Os inquilinos não geridos ou verificados por e-mail são criados quando um utilizador resgata um convite B2B ou realiza uma inscrição de self-service para a Azure AD usando um domínio que não existe atualmente. Pode criar uma federação direta com estes domínios. Se tentar configurar uma federação direta com um domínio verificado pelo DNS, seja no portal Azure ou via PowerShell, verá um erro.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Se a autenticação direta da federação e do e-mail de uma só vez estiverem ativadas, qual o método que tem precedência?
Quando a federação direta é estabelecida com uma organização parceira, tem precedência sobre a autenticação de código de senha de e-mail para novos utilizadores convidados dessa organização. Se um utilizador convidado redimir um convite utilizando a autenticação de senha única antes de configurar a federação direta, continuará a utilizar a autenticação de senha única. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>A federação direta aborda os problemas de inscrição devido a um arrendamento parcialmente sincronizado?
Não, a funcionalidade [de senha única de e-mail](one-time-passcode.md) deve ser utilizada neste cenário. Um "arrendamento parcialmente sincronizado" refere-se a um parceiro inquilino da AD Azure onde as identidades dos utilizadores no local não estão totalmente sincronizadas com a nuvem. Um hóspede cuja identidade ainda não existe na nuvem, mas que tenta redimir o seu convite B2B não poderá assinar. A funcionalidade de senha única permitiria que este hóspede assinasse. A federação direta aborda cenários em que o hóspede tem a sua própria conta organizacional gerida pelo IDP, mas a organização não tem qualquer presença de AD Azure.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Passo 1: Configure o fornecedor de identidade da organização parceira
Em primeiro lugar, a sua organização parceira precisa de configurar o seu fornecedor de identidade com as reivindicações necessárias e confiar nos fundos partidários. 

> [!NOTE]
> Para ilustrar como configurar um fornecedor de identidade para a federação direta, usaremos como exemplo os Serviços da Federação de Diretórios Ativos (AD FS). Consulte o artigo [Configure federação direta com AD FS](direct-federation-adfs.md), que dá exemplos de como configurar AD FS como um fornecedor de identidade SAML 2.0 ou WS-Fed em preparação para a federação direta.

### <a name="saml-20-configuration"></a>Configuração SAML 2.0

O Azure AD B2B pode ser configurado para federar com fornecedores de identidade que utilizam o protocolo SAML com requisitos específicos listados abaixo. Para obter mais informações sobre a criação de um fundo entre o seu fornecedor de identidade SAML e a AD Azure, consulte Utilize um Fornecedor de [Identidade SAML 2.0 (IDP) para um único sign-on](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp).  

> [!NOTE]
> O domínio-alvo da federação direta não deve ser verificado em DNS em Azure AD. O domínio URL de autenticação deve corresponder ao domínio-alvo ou deve ser o domínio de um fornecedor de identidade permitido. Consulte a secção [Limitações](#limitations) para mais detalhes. 

#### <a name="required-saml-20-attributes-and-claims"></a>SAML 2.0 atributos e reclamações necessários
As tabelas que se seguem apresentam requisitos para atributos e reivindicações específicos que devem ser configurados no fornecedor de identidade de terceiros. Para criar uma federação direta, os seguintes atributos devem ser recebidos na resposta SAML 2.0 do fornecedor de identidade. Estes atributos podem ser configurados ligando-se ao ficheiro XML do serviço de segurança online ou inserindo-os manualmente.

Atributos necessários para a resposta SAML 2.0 do IDP:

|Atributo  |Valor  |
|---------|---------|
|Serviço de Consumidores de Afirmação     |`https://login.microsoftonline.com/login.srf`         |
|Audiência     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O emitente URI do parceiro IDP, por exemplo`http://www.example.com/exk10l6w90DHM0yi...`         |


Reclamações necessárias para o token SAML 2.0 emitidos pelo IDP:

|Atributo  |Valor  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|endereço de e-mail     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Configuração WS-Fed 
O Azure AD B2B pode ser configurado para federar com fornecedores de identidade que utilizam o protocolo WS-Fed com alguns requisitos específicos listados abaixo. Atualmente, os dois fornecedores da WS-Fed foram testados para compatibilidade com a Azure AD incluem AD FS e Shibboleth. Para obter mais informações sobre o estabelecimento de uma confiança de parte dependente entre um fornecedor compatível com a WS-Fed com a Azure AD, consulte o "Papel de Integração STS utilizando protocolos WS" disponíveis no Docs de Compatibilidade do Fornecedor de [Identidade Azure AD.](https://www.microsoft.com/download/details.aspx?id=56843)

> [!NOTE]
> O domínio-alvo da federação direta não deve ser verificado em DNS em Azure AD. O domínio URL de autenticação deve corresponder ao domínio-alvo ou ao domínio de um fornecedor de identidade permitido. Consulte a secção [Limitações](#limitations) para mais detalhes. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Atributos e reivindicações exigidos da WS-Fed

As tabelas que se seguem mostram requisitos para atributos e alegações específicas que devem ser configurados no fornecedor de identidade WS-Fed de terceiros. Para criar uma federação direta, os seguintes atributos devem ser recebidos na mensagem WS-Fed do fornecedor de identidade. Estes atributos podem ser configurados ligando-se ao ficheiro XML do serviço de segurança online ou inserindo-os manualmente.

Atributos necessários na mensagem WS-Fed do IDP:
 
|Atributo  |Valor  |
|---------|---------|
|Ponto final do PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Audiência     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O emitente URI do parceiro IDP, por exemplo`http://www.example.com/exk10l6w90DHM0yi...`         |

Pedidos necessários para o token WS-Fed emitidos pelo IDP:

|Atributo  |Valor  |
|---------|---------|
|Id imutável     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|endereço de e-mail     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Passo 2: Configure federação direta em Azure AD 
Em seguida, você vai configurar a federação com o fornecedor de identidade configurado no passo 1 em Azure AD. Pode utilizar o portal Azure AD ou o PowerShell. Pode levar 5 a 10 minutos até que a política da federação direta entre em vigor. Durante este tempo, não tente resgatar um convite para o domínio da federação direta. São necessários os seguintes atributos:
- Emitente URI do parceiro IDP
- Ponto final de autenticação passiva do parceiro IdP (apenas https é suportado)
- Certificado

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Para configurar a federação direta no portal Azure AD

1. Vá ao [portal Azure.](https://portal.azure.com/) No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Identidades Externas**  >  **Todos os fornecedores de identidade**.
3. Selecione , e, em seguida, selecione **New SAML/WS-Fed IdP**.

    ![Screenshot mostrando botão para adicionar um novo IdP SAML ou WS-Fed](media/direct-federation/new-saml-wsfed-idp.png)

4. Na página **IdP New SAML/WS-Fed,** ao abrigo do protocolo do **fornecedor de identidade,** selecione **SAML** ou **WS-FED**.

    ![Screenshot mostrando botão de parse na página IdP SAML ou WS-Fed](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Insira o nome de domínio da sua organização parceira, que será o nome de domínio alvo para a federação direta
6. Pode fazer o upload de um ficheiro de metadados para preencher detalhes de metadados. Se optar por inserir metadados manualmente, introduza as seguintes informações:
   - Nome de domínio do parceiro IdP
   - Id de entidade do parceiro IdP
   - Pedido passivo ponto final do parceiro IDP
   - Certificado
   > [!NOTE]
   > O URL de metadados é opcional, no entanto recomendamos vivamente. Se fornecer o URL dos metadados, o Azure AD pode renovar automaticamente o certificado de assinatura quando expirar. Se o certificado for rodado por qualquer motivo antes do tempo de validade ou se não fornecer um URL de metadados, o Azure AD não poderá renová-lo. Neste caso, terá de atualizar manualmente o certificado de assinatura.

7. Selecione **Guardar**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Para configurar a federação direta em Azure AD usando powerShell

1. Instale a versão mais recente do Azure AD PowerShell para módulo gráfico[(AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Se precisar de passos detalhados, o arranque rápido para adicionar um utilizador inclui a secção [Instalar o mais recente módulo AzureADPreview](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Execute o seguinte comando: 
   ```powershell
   Connect-AzureAD
   ```
1. No momento de início de sessão, inscreva-se na conta gerida do Administrador Global. 
2. Executar os seguintes comandos, substituindo os valores do ficheiro de metadados da federação. Para a AD FS Server e Okta, o ficheiro da federação é federationmetadata.xml, por exemplo: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml` . 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Passo 3: Teste da federação direta em Azure AD
Agora teste a sua configuração da federação direta convidando um novo utilizador convidado B2B. Para mais detalhes, consulte os utilizadores de [colaboração Add Azure AD B2B no portal Azure](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Como edito uma relação direta com a federação?

1. Vá ao [portal Azure.](https://portal.azure.com/) No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Identidades Externas**.
3. Selecione **Todos os fornecedores de identidade**
4. Sob os fornecedores de **identidade SAML/WS-Fed,** selecione o fornecedor.
5. No painel de detalhes do fornecedor de identidade, atualize os valores.
6. Selecione **Guardar**.


## <a name="how-do-i-remove-direct-federation"></a>Como retiro a federação direta?
Pode remover a sua configuração da federação direta. Se o fizer, os utilizadores convidados da federação direta que já resgataram os seus convites não poderão inscrever-se. Mas pode dar-lhes acesso aos seus recursos novamente, apagando-os do diretório e reconvidando-os. Remover a federação direta com um fornecedor de identidade no portal Azure AD:

1. Vá ao [portal Azure.](https://portal.azure.com/) No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Identidades Externas**.
3. Selecione **Todos os fornecedores de identidade**.
4. Selecione o fornecedor de identidade e, em seguida, selecione **Eliminar**. 
5. Selecione **Sim** para confirmar a eliminação. 

Para remover a federação direta com um fornecedor de identidade utilizando o PowerShell:
1. Instale a versão mais recente do Azure AD PowerShell para módulo gráfico[(AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Execute o seguinte comando: 
   ```powershell
   Connect-AzureAD
   ```
3. No momento de início de sessão, inscreva-se na conta gerida do Administrador Global. 
4. Introduza o seguinte comando:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
