---
title: Federação direta com um fornecedor de identidade para b2B - Azure AD
description: Federar diretamente com um SAML ou WS-Fed fornecedor de identidade para que os hóspedes possam iniciar sação nas suas aplicações AD AZure
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 598cbf303c8a87675833b8d87f05055771e46f55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687248"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Federação direta com FS AD e fornecedores de terceiros para utilizadores convidados (pré-visualização)

> [!NOTE]
>  A federação direta é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo descreve como criar uma federação direta com outra organização para a colaboração B2B. Pode criar uma federação direta com qualquer organização cujo fornecedor de identidade (IdP) suporte o protocolo SAML 2.0 ou WS-Fed.
Quando configurar uma federação direta com o IdP de um parceiro, novos utilizadores convidados desse domínio podem usar a sua própria conta organizacional gerida pelo IdP para se inscreverem no seu inquilino Azure AD e começar a colaborar consigo. Não há necessidade de o utilizador convidado criar uma conta AD Azure separada.

## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Quando é que um utilizador convidado é autenticado com federação direta?
Depois de configurar uma federação direta com uma organização, quaisquer novos utilizadores convidados que convidar serão autenticados através da federação direta. É importante notar que a criação de uma federação direta não altera o método de autenticação para utilizadores convidados que já tenham resgatado um convite de si. Eis alguns exemplos:
 - Se os utilizadores convidados já terem resgatado convites de si, e posteriormente criarem uma federação direta com a sua organização, esses utilizadores convidados continuarão a utilizar o mesmo método de autenticação que usaram antes de criarem a federação direta.
 - Se criar uma federação direta com uma organização parceira e convidar utilizadores convidados, e então a organização parceira mais tarde se muda para a Azure AD, os utilizadores convidados que já resgataram convites continuarão a usar a federação direta, desde que exista a política direta da federação no seu inquilino.
 - Se eliminar a federação direta com uma organização parceira, qualquer utilizadores convidados que utilizem a federação direta não poderá iniciar sposição.

Em qualquer um destes cenários, pode atualizar o método de autenticação de um utilizador convidado, eliminando a conta de utilizador do hóspede do seu diretório e revitalizá-la.

A federação direta está ligada a espaços de nome de domínio, como contoso.com e fabrikam.com. Ao estabelecer uma configuração direta da federação com FS AD ou um IdP de terceiros, as organizações associam um ou mais espaços de nome de domínio a estes IdPs. 

## <a name="end-user-experience"></a>Experiência do utilizador final 
Com a federação direta, os utilizadores convidados inscrevem-se no seu inquilino Azure AD usando a sua própria conta organizacional. Quando estão a aceder a recursos partilhados e são solicitados para a entrada, os utilizadores diretos da federação são redirecionados para o seu IdP. Após o sucesso da sação, são devolvidos à Azure AD para aceder aos recursos. As fichas de atualização dos utilizadores da federação direta são válidas por 12 horas, o [comprimento padrão para o token de atualização passthrough](../develop/active-directory-configurable-token-lifetimes.md#exceptions) em Azure AD. Se o IdP federado tiver SSO ativado, o utilizador experimentará SSO e não verá qualquer pedido de inscrição após a autenticação inicial.

## <a name="sign-in-endpoints"></a>Pontos finais de inscrição

Os utilizadores de hóspedes da federação direta podem agora iniciar sposição nas suas aplicações multi-inquilinas ou na Microsoft, utilizando um [ponto final comum](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (por outras palavras, um URL de aplicações gerais que não inclua o contexto do seu inquilino). Durante o processo de entrada, o utilizador convidado escolhe **as opções de Inscrição** e, em seguida, seleciona **Iniciar sação para uma organização**. Em seguida, o utilizador escreve o nome da sua organização e continua a iniciar sessão com as suas próprias credenciais.

Os utilizadores convidados da federação direta também podem usar pontos finais de aplicação que incluem informações do seu inquilino, por exemplo:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Também pode dar aos utilizadores convidados da federação direta uma ligação direta a uma aplicação ou recurso, incluindo as informações do seu inquilino, por `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` exemplo.

## <a name="limitations"></a>Limitações

### <a name="dns-verified-domains-in-azure-ad"></a>Domínios verificados pelo DNS em Azure AD
O domínio com o que pretende federar ***não*** deve ser verificado em Azure AD. Você está autorizado a criar uma federação direta com inquilinos não geridos (verificados por e-mail ou "virais") Azure AD porque eles não são verificados PELO DNS.

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

Por exemplo, ao configurar a federação direta para **fabrikam.com,** o URL de autenticação `https://fabrikam.com/adfs` passará a validação. Um hospedeiro no mesmo domínio também passará, por `https://sts.fabrikam.com/adfs` exemplo. No entanto, o URL de autenticação `https://fabrikamconglomerate.com/adfs` ou para o mesmo domínio não `https://fabrikam.com.uk/adfs` passará.

### <a name="signing-certificate-renewal"></a>Assinatura de renovação de certificado
Se especificar o URL dos metadados nas definições do fornecedor de identidade, o Azure AD renovará automaticamente o certificado de assinatura quando expirar. No entanto, se o certificado for rotativo por qualquer motivo antes do prazo de validade, ou se não fornecer um URL de metadados, a Azure AD não poderá renová-lo. Neste caso, terá de atualizar manualmente o certificado de assinatura.

### <a name="limit-on-federation-relationships"></a>Limite nas relações da federação
Atualmente, um máximo de 1.000 relações da federação é apoiado. Este limite inclui tanto as [federações internas](/powershell/module/msonline/set-msoldomainfederationsettings) como as federações diretas.

### <a name="limit-on-multiple-domains"></a>Limite em vários domínios
Não apoiamos a federação direta com vários domínios do mesmo inquilino.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Posso criar uma federação direta com um domínio para o qual existe um inquilino não gerido (verificado por e-mail) ? 
Sim. Se o domínio não tiver sido verificado e o inquilino não tiver sido submetido a [uma aquisição de administração,](../enterprise-users/domains-admin-takeover.md)pode criar uma federação direta com esse domínio. Os inquilinos não geridos, ou verificados por e-mail, são criados quando um utilizador resgata um convite B2B ou realiza uma inscrição de self-service para a AD Azure usando um domínio que não existe atualmente. Pode configurar uma federação direta com estes domínios. Se tentar configurar uma federação direta com um domínio verificado pelo DNS, quer no portal Azure, quer através do PowerShell, verá um erro.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Se a federação direta e a autenticação de código de acesso de e-mail estiverem ativados, que método tem precedência?
Quando a federação direta é estabelecida com uma organização parceira, tem precedência sobre a autenticação de senha de e-mail para novos utilizadores convidados dessa organização. Se um utilizador convidado resgatar um convite utilizando a autenticação de código de acesso único antes de configurar a federação direta, continuará a utilizar a autenticação de código de acesso único. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>A federação direta aborda problemas de inscrição devido a um arrendamento parcialmente sincronizado?
Não, a funcionalidade [de código de acesso de e-mail](one-time-passcode.md) uma vez deve ser usada neste cenário. Um "arrendamento parcialmente sincronizado" refere-se a um inquilino Azure AD parceiro onde as identidades dos utilizadores no local não estão totalmente sincronizadas com a nuvem. Um hóspede cuja identidade ainda não existe na nuvem mas que tente resgatar o seu convite B2B não poderá assinar. A funcionalidade de código de acesso único permitiria que este hóspede assinasse. A funcionalidade da federação direta aborda cenários em que o hóspede tem a sua própria conta organizacional gerida pelo IdP, mas a organização não tem nenhuma presença da AD AZure.
### <a name="once-direct-federation-is-configured-with-an-organization-does-each-guest-need-to-be-sent-and-redeem-an-individual-invitation"></a>Uma vez que a Federação Direta é configurada com uma organização, cada hóspede precisa ser enviado e resgatar um convite individual?
A criação de uma federação direta não altera o método de autenticação para utilizadores convidados que já tenham resgatado um convite de si. Pode atualizar o método de autenticação de um utilizador convidado, eliminando a conta de utilizador do hóspede do seu diretório e novamente convidando-a.
## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Passo 1: Configurar o fornecedor de identidade da organização parceira
Em primeiro lugar, a sua organização parceira precisa de configurar o seu fornecedor de identidade com as reivindicações necessárias e confiar nas confianças das partes. 

> [!NOTE]
> Para ilustrar como configurar um fornecedor de identidade para a federação direta, usaremos os Serviços da Federação de Diretórios Ativos (AD FS) como exemplo. Consulte o artigo [Configurar federação direta com AD FS,](direct-federation-adfs.md)que dá exemplos de como configurar a AD FS como um SAML 2.0 ou WS-Fed fornecedor de identidade em preparação para federação direta.

### <a name="saml-20-configuration"></a>Configuração SAML 2.0

O Azure AD B2B pode ser configurado para federar com fornecedores de identidade que utilizam o protocolo SAML com requisitos específicos listados abaixo. Para obter mais informações sobre a criação de uma confiança entre o seu fornecedor de identidade SAML e a AD Azure, consulte utilizar um Fornecedor de  [Identidade SAML 2.0 (IdP) para uma única Sposição](../hybrid/how-to-connect-fed-saml-idp.md).  

> [!NOTE]
> O domínio-alvo da federação direta não deve ser verificado em Azure AD. O domínio URL de autenticação deve coincidir com o domínio alvo ou deve ser o domínio de um fornecedor de identidade permitido. Consulte a secção [Limitações](#limitations) para mais detalhes. 

#### <a name="required-saml-20-attributes-and-claims"></a>Atributos e reclamações saml 2.0 exigidos
As tabelas a seguir mostram requisitos para atributos e alegações específicos que devem ser configurados no fornecedor de identidade de terceiros. Para criar uma federação direta, os seguintes atributos devem ser recebidos na resposta SAML 2.0 do fornecedor de identidade. Estes atributos podem ser configurados ligando-os ao ficheiro XML do serviço de segurança online ou introduzindo-os manualmente.

Atributos necessários para a resposta SAML 2.0 do IdP:

|Atributo  |Valor  |
|---------|---------|
|Serviço De Afirmação     |`https://login.microsoftonline.com/login.srf`         |
|Audiência     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O emitente URI do IdP parceiro, por exemplo `http://www.example.com/exk10l6w90DHM0yi...`         |


Reclamações necessárias para o token SAML 2.0 emitido pelo IdP:

|Atributo  |Valor  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|endereço de e-mail     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed configuração 
O Azure AD B2B pode ser configurado para federar com fornecedores de identidade que usam o protocolo WS-Fed com alguns requisitos específicos, conforme listado abaixo. Atualmente, os dois fornecedores de WS-Fed foram testados para compatibilidade com a Azure AD incluem AD FS e Shibboleth. Para obter mais informações sobre a criação de uma confiança entre uma parte WS-Fed fornecedor conforme com a Azure AD, consulte o "StS Integration Paper using WS Protocols" disponível nos [Docs de Compatibilidade do Fornecedor de Identidade Azure AD](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> O domínio-alvo da federação direta não deve ser verificado em Azure AD. O domínio URL de autenticação deve coincidir quer com o domínio alvo quer com o domínio de um fornecedor de identidade permitido. Consulte a secção [Limitações](#limitations) para mais detalhes. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Atributos e reclamações de WS-Fed necessários

As tabelas a seguir mostram requisitos para atributos e alegações específicos que devem ser configurados no fornecedor de identidade WS-Fed de terceiros. Para criar uma federação direta, os seguintes atributos devem ser recebidos na mensagem WS-Fed do fornecedor de identidade. Estes atributos podem ser configurados ligando-os ao ficheiro XML do serviço de segurança online ou introduzindo-os manualmente.

Atributos exigidos na mensagem WS-Fed do IdP:
 
|Atributo  |Valor  |
|---------|---------|
|PassivoRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Audiência     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O emitente URI do IdP parceiro, por exemplo `http://www.example.com/exk10l6w90DHM0yi...`         |

Reclamações necessárias para o WS-Fed ficha emitida pelo IdP:

|Atributo  |Valor  |
|---------|---------|
|ImutávelID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|endereço de e-mail     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Passo 2: Configure a Federação Direta em Azure AD 
Em seguida, você vai configurar a federação com o fornecedor de identidade configurado no passo 1 em AZure AD. Pode utilizar o portal AD Azure ou o PowerShell. Pode levar 5 a 10 minutos até que a política direta da federação entre em vigor. Durante este tempo, não tente resgatar um convite para o domínio da federação direta. São necessários os seguintes atributos:
- Emitente URI do parceiro IdP
- Ponto final de autenticação passiva do parceiro IdP (apenas https é suportado)
- Certificado

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Para configurar a federação direta no portal AD AZure

1. Aceda ao [Portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Identidades Externas**  >  **Todos os fornecedores de identidade**.
3. Selecione **Novo IdP SAML/WS-Fed**.

    ![Screenshot mostrando botão para adicionar um novo SAML ou WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp.png)

4. Na página **New SAML/WS-Fed IdP,** no âmbito **do protocolo de fornecedor de identidade,** selecione **SAML** ou **WS-FED**.

    ![Screenshot mostrando botão de parse na página de IdP SAML ou WS-Fed](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Insira o nome de domínio da sua organização parceira, que será o nome de domínio alvo para federação direta
6. Pode enviar um ficheiro de metadados para preencher detalhes de metadados. Se optar por inserir metadados manualmente, insira as seguintes informações:
   - Nome de domínio do IdP do parceiro
   - ID de entidade do parceiro IdP
   - Ponto final passiva do IdP do parceiro
   - Certificado
   > [!NOTE]
   > O URL dos metadados é opcional, no entanto recomendamos vivamente. Se fornecer o URL de metadados, o Azure AD pode renovar automaticamente o certificado de assinatura quando este expirar. Se o certificado for rodado por qualquer motivo antes do prazo de validade ou se não fornecer um URL de metadados, a Azure AD não poderá renová-lo. Neste caso, terá de atualizar manualmente o certificado de assinatura.

7. Selecione **Guardar**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Para configurar a federação direta em Azure AD usando a PowerShell

1. Instale a versão mais recente do Azure AD PowerShell para módulo Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview) (Se necessitar de passos detalhados, o arranque rápido para adicionar um utilizador convidado inclui a secção [Instalar o mais recente módulo AzureADPreview](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Execute o seguinte comando: 
   ```powershell
   Connect-AzureAD
   ```
1. Na 00da vez, inscreva-se na conta gerida do Administrador Global. 
2. Executar os seguintes comandos, substituindo os valores do ficheiro de metadados da federação. Para o AD FS Server e Okta, o ficheiro da federação é federationmetadata.xml, por exemplo: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml` . 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Passo 3: Teste federação direta em Azure AD
Agora teste a configuração da sua federação direta convidando um novo utilizador convidado B2B. Para mais detalhes, consulte os utilizadores de [colaboração Add AD B2B no portal Azure](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Como edito uma relação direta da federação?

1. Aceda ao [Portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **identidades externas**.
3. Selecione **Todos os fornecedores de identidade**
4. Sob **os fornecedores de identidade SAML/WS-Fed,** selecione o fornecedor.
5. No painel de detalhes do fornecedor de identidade, atualize os valores.
6. Selecione **Guardar**.


## <a name="how-do-i-remove-direct-federation"></a>Como retiro a federação direta?
Pode remover a configuração direta da federação. Se o fizer, os utilizadores convidados da federação direta que já resgataram os seus convites não poderão assinar o seu contrato. Mas pode dar-lhes acesso aos seus recursos novamente, eliminando-os do diretório e os convidando novamente. Remover a federação direta com um fornecedor de identidade no portal AD AZure:

1. Aceda ao [Portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **identidades externas**.
3. Selecione **Todos os fornecedores de identidade**.
4. Selecione o fornecedor de identidade e, em seguida, **selecione Delete**. 
5. Selecione **Sim** para confirmar a eliminação. 

Para remover a federação direta com um fornecedor de identidade utilizando o PowerShell:
1. Instale a versão mais recente do Azure AD PowerShell para módulo Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Execute o seguinte comando: 
   ```powershell
   Connect-AzureAD
   ```
3. Na 00da vez, inscreva-se na conta gerida do Administrador Global. 
4. Introduza o seguinte comando:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a [experiência de resgate de convites](redemption-experience.md) quando utilizadores externos iniciarem sôms com vários fornecedores de identidade.
