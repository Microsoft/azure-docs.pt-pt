---
title: Configurar a Federação direta com um fornecedor de identidade para B2B - Azure Active Directory | Documentos da Microsoft
description: Federar diretamente com um fornecedor de identidade SAML ou WS-Fed, para que os convidados podem iniciar sessão às suas aplicações do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bc3c1325e8379082134e2cbec1586f7d338ee61
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797928"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Federação direta com o AD FS e fornecedores de terceiros para os utilizadores convidados (pré-visualização)
|     |
| --- |
| Federação direta é uma funcionalidade de pré-visualização pública do Azure Active Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Este artigo descreve como configurar a Federação direta com outra organização colaboração B2B. Pode configurar a Federação direta em qualquer organização cujo fornecedor de identidade (IdP) suporta o protocolo SAML 2.0 ou WS-Fed.
Quando configurar a Federação direta com IdP um parceiro, novos utilizadores convidados de que o domínio podem utilizar a sua própria conta institucional geridos pelo IdP para iniciar sessão no inquilino do Azure AD e começar a colaborar consigo. Não é necessário para o utilizador convidado criar uma conta do Azure AD.
> [!NOTE]
> Direcionar os utilizadores convidados de federação tem de iniciar sessão através de uma ligação que inclui o contexto do inquilino (por exemplo, `https://myapps.microsoft.com/?tenantid=<tenant id>` ou `https://portal.azure.com/<tenant id>`, ou no caso de um domínio verificado, `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`). Ligações diretas para aplicativos e recursos também funcionam, desde que eles incluem o contexto do inquilino. Os utilizadores de Federação direto estão atualmente não é possível iniciar sessão com pontos de extremidade comuns que não têm nenhum contexto de inquilino. Por exemplo, utilizando `https://myapps.microsoft.com`, `https://portal.azure.com`, ou `https://teams.microsoft.com` resultará num erro.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Quando um utilizador convidado é autenticado com a Federação direta?
Depois de configurar a Federação direta com uma organização, a quaisquer novos utilizadores convidados que convidar serão autenticados utilizando o Federação direta. É importante observar que a configuração de Federação direta não altera o método de autenticação para utilizadores convidados que já tiver resgatado um convite de utilizador. Eis alguns exemplos:
 - Se os utilizadores convidados já resgatou a convites da e, em seguida, configurar a Federação direta com a respetiva organização, esses utilizadores convidados irão continuar a utilizar o mesmo método de autenticação usaram antes de configurar o Federação direta.
 - Se configurar a Federação direta com uma organização de parceiro e convidar utilizadores e, em seguida, a organização do parceiro mais tarde se move para o Azure AD, os utilizadores de convidado que já resgatou a convites vão continuar a utilizar o Federação direta, quanto direto existe a política de Federação no seu inquilino.
 - Se eliminar o Federação direta com uma organização de parceiro, todos os utilizadores convidados a utilizar atualmente o Federação direta será possível iniciar sessão.

Em qualquer um destes cenários, pode atualizar o método de autenticação de um utilizador convidado ao eliminar a conta de utilizador convidado do diretório e reinviting-los.

Federação direta está associada aos espaços de nomes de domínio, como contoso.com e fabrikam.com. Ao estabelecer uma configuração de Federação direta com o AD FS ou um IdP de terceiros, as organizações associar uma ou mais namespaces de domínio para estas IdPs. 

## <a name="end-user-experience"></a>Experiência do utilizador final 
Com a Federação direta, os utilizadores convidados inicie sessão no inquilino do Azure AD com a sua própria conta organizacional. Quando estão a aceder a recursos partilhados e recebem um pedido de início de sessão, utilizadores de Federação direto são redirecionados para o IdP. Após o início de sessão com êxito, são devolvidos para o Azure AD para aceder aos recursos. Direcionar os tokens de atualização dos utilizadores de Federação são válidas durante 12 horas, o [predefinido comprimento para o token de atualização de pass-through](../develop/active-directory-configurable-token-lifetimes.md#exceptions) no Azure AD. Se o IdP federada tem SSO ativado, este será a experiência SSO e não verá qualquer linha de início de sessão após a autenticação inicial.

## <a name="limitations"></a>Limitações

### <a name="dns-verified-domains-in-azure-ad"></a>Domínios verificados de DNS no Azure AD
Direcionar o Federação só é permitida para domínios que estão ***não*** DNS verificados no Azure AD. Federação direta é permitida para não-gerenciado (verificado por e-mail ou "viral") do Azure AD inquilinos porque eles não são verificados de DNS.
### <a name="authentication-url"></a>URL de autenticação
Direta Federação apenas é permitida para as políticas em que o domínio do URL de autenticação corresponde ao domínio de destino ou em que o URL de autenticação é um destes fornecedores de identidade permitidas (esta lista está sujeita a alterações):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Por exemplo, quando configurar a Federação direta para **fabrikam.com**, o URL de autenticação `https://fabrikam.com/adfs` passará a validação. Um anfitrião no mesmo domínio também será aprovado, por exemplo `https://sts.fabrikam.com/adfs`. No entanto, o URL de autenticação `https://fabrikamconglomerate.com/adfs` ou `https://fabrikam.com.uk/adfs` para não passa o mesmo domínio.

### <a name="signing-certificate-renewal"></a>Renovação do certificado de assinatura
Se especificar o URL de metadados nas definições do fornecedor de identidade, o Azure AD serão renovadas automaticamente o certificado de assinatura quando este expirar. No entanto, se o certificado é girado por qualquer motivo, antes da hora de expiração, ou se não fornecer um URL de metadados, do Azure AD que não poderá renová-la. Neste caso, terá de atualizar manualmente o certificado de assinatura.
## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Posso configurar a Federação direta com um domínio para o qual um inquilino não gerido (verificado por e-mail) existe? 
Sim. Se o domínio ainda não foi verificado e o inquilino não tenha sofrido um [obtenção do controlo administrativo](../users-groups-roles/domains-admin-takeover.md), pode configurar a Federação direta. Os inquilinos não geridos ou verificado por e-mail, são criados quando um utilizador redeems um convite de B2B ou executa uma inscrição Self-Service do Azure AD através de um domínio que não existe atualmente. Pode configurar a Federação direta com estes domínios. Se tentar configurar a Federação direta com um domínio verificado de DNS, no portal do Azure ou através do PowerShell, verá um erro.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Se o Federação direta e a autenticação de código de acesso único por e-mail estiverem ativadas, o qual o método tem precedência?
Quando o Federação direta é estabelecida com uma organização de parceiro, ela terá precedência sobre a autenticação de código de acesso único de e-mail para novos utilizadores convidados dessa organização. Se um utilizador convidado resgatado um convite através da autenticação de código de acesso único antes de configurar o Federação direta, eles irá continuar a utilizar a autenticação de código de acesso único. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Direcionar o Federação endereço início de sessão problemas devido a um inquilinos parcialmente sincronizados?
Não, o [código de acesso único de e-mail](one-time-passcode.md) funcionalidade deve ser utilizada neste cenário. Um "parcialmente sincronizados inquilinos" refere-se a um inquilino do Azure AD de parceiro onde os identidades de utilizador no local não são totalmente sincronizadas para a cloud. Uma convidada cuja identidade ainda não existe na cloud, mas quem tenta resgatar o convite de B2B não será possível iniciar sessão. A funcionalidade de código de acesso único permitiria que este convidado iniciar sessão. A funcionalidade de Federação direto aborda cenários em que a convidada tem sua própria conta institucional IdP gerido, mas a organização tiver sem presença do Azure AD em todos os.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Passo 1: Configurar o fornecedor de identidade da organização de parceiro
Em primeiro lugar, sua organização de parceiro tem de configurar o seu fornecedor de identidade com as declarações necessárias e confianças de entidades confiadoras. 

> [!NOTE]
> Para ilustrar como configurar um fornecedor de identidade para a Federação direto, vamos utilizar serviços de Federação do Active Directory (AD FS) como exemplo. Consulte o artigo [configurar a Federação direta com o AD FS](direct-federation-adfs.md), que fornece exemplos de como configurar o AD FS como um provedor de identidade de SAML 2.0 ou WS-Fed em preparação para a Federação direta.

### <a name="saml-20-configuration"></a>Configuração de SAML 2.0

O Azure AD B2B pode ser configurado para federar com fornecedores de identidade que utilizam o protocolo SAML com requisitos específicos indicados abaixo. Para obter mais informações sobre como configurar uma confiança entre o seu fornecedor de identidade SAML e o Azure AD, consulte [utilizar um fornecedor de identidade de 2.0 (IdP) SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp).  

> [!NOTE]
> Tenha em atenção o domínio de destino para a Federação direta não pode ser verificado de DNS no Azure AD. O domínio do URL de autenticação tem de corresponder ao domínio de destino ou tem de ser o domínio de um fornecedor de identidade permitidos. Consulte a [limitações](#limitations) secção para obter detalhes. 

#### <a name="required-saml-20-attributes-and-claims"></a>Necessárias afirmações e atributos de SAML 2.0
As tabelas seguintes mostram os requisitos para atributos específicos e as afirmações que devem ser configuradas no fornecedor de identidade de terceiros. Para configurar a Federação direta, os seguintes atributos têm de ser recebidos na resposta SAML 2.0 de fornecedor de identidade. Estes atributos podem ser configurados ao criar uma ligação para o serviço de token de segurança online arquivo XML ou introduzindo-los manualmente.

Atributos necessários para a resposta SAML 2.0 do IdP:

|Atributo  |Valor  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Audiência     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O URI do parceiro IdP, por exemplo do emissor `http://www.example.com/exk10l6w90DHM0yi...`         |


Declarações necessárias para o token SAML 2.0 emitido o IDP utiliza:

|Atributo  |Value  |
|---------|---------|
|Formato NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Configuração de WS-Fed 
O Azure AD B2B pode ser configurado para federar com fornecedores de identidade que utilizam o protocolo WS-Fed com alguns requisitos específicos, conforme indicado abaixo. Atualmente, os dois provedores de WS-Fed foram testados para compatibilidade com o Azure AD incluem o AD FS e do Shibboleth. Para obter mais informações sobre como estabelecer uma fidedignidade de entidade confiadora entre um fornecedor de WS-Fed em conformidade com o Azure AD, consulte a "integração de STS Paper através de protocolos WS" disponível na [do Azure AD Identity Provider compatibilidade Docs](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> O domínio de destino para a Federação direta não pode ser verificado de DNS no Azure AD. O domínio do URL de autenticação tem de corresponder ao domínio de destino ou o domínio de um fornecedor de identidade permitidos. Consulte a [limitações](#limitations) secção para obter detalhes. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Necessárias afirmações e atributos de WS-Fed

As tabelas seguintes mostram os requisitos para atributos específicos e as afirmações que devem ser configuradas no fornecedor de identidade de WS-Fed de terceiros. Para configurar a Federação direta, os seguintes atributos têm de ser recebidos na mensagem de WS-Fed do fornecedor de identidade. Estes atributos podem ser configurados ao criar uma ligação para o serviço de token de segurança online arquivo XML ou introduzindo-los manualmente.

Atributos necessários na mensagem de WS-Fed do IdP:
 
|Atributo  |Value  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Audiência     |`urn:federation:MicrosoftOnline`         |
|Emissor     |O URI do parceiro IdP, por exemplo do emissor `http://www.example.com/exk10l6w90DHM0yi...`         |

Declarações necessárias para o token de WS-Fed emitido o IDP utiliza:

|Atributo  |Value  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Passo 2: Configurar a Federação direta no Azure AD 
Em seguida, irá configurar a Federação com o fornecedor de identidade configurado no passo 1 no Azure AD. Pode utilizar o portal do Azure AD ou o PowerShell. Poderá demorar 5 a 10 minutos antes da política de Federação direto entra em vigor. Durante este período, não tente resgatar um convite para o domínio de Federação direto. Os seguintes atributos são necessários:
- Emissor do URI do parceiro IdP
- Ponto final de autenticação passiva do parceiro IdP (https apenas é suportado)
- Certificado

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Para configurar a Federação direta no portal do Azure AD

1. Aceda ao [Portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **relações organizacionais**.
3. Selecione **fornecedores de identidade**e, em seguida, selecione **IdP novo SAML/WS-Fed**.

    ![Captura de ecrã a Mostrar botão para adicionar um novo SAML ou WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp.png)

4. Sobre o **IdP novo SAML/WS-Fed** página, em **protocolo de fornecedor de identidade**, selecione **SAML** ou **WS-FED**.

    ![Captura de ecrã que mostra parse botão na página de SAML ou WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Introduza o nome de domínio da sua organização de parceiro, que será o nome de domínio de destino para a Federação direto
6. Pode carregar um ficheiro de metadados para preencher os detalhes de metadados. Se optar por metadados de entrada manualmente, introduza as seguintes informações:
   - Nome de domínio do parceiro IdP
   - ID de entidade de parceiro IdP
   - Ponto final do requerente passivo de parceiro IdP
   - Certificado
   > [!NOTE]
   > URL de metadados é opcional, no entanto, é altamente recomendável. Se fornecer o URL de metadados, o Azure AD pode renovar automaticamente o certificado de assinatura quando este expirar. Se o certificado é girado por qualquer motivo, antes da hora de expiração, ou se não fornecer um URL de metadados, do Azure AD que não poderá renová-la. Neste caso, terá de atualizar manualmente o certificado de assinatura.

7. Selecione **Guardar**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Para configurar a Federação direta no Azure AD com o PowerShell

1. Instalar a versão mais recente do Azure AD PowerShell para o módulo de gráfico ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Se precisar de obter os passos detalhados, o guia de introdução para adicionar um utilizador convidado inclui a seção [instalar o módulo mais recente do AzureADPreview](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Execute o seguinte comando: 
   ```powershell
   Connect-AzureAD
   ```
1. No prompt de início de sessão, inicie sessão com a conta de Administrador Global gerenciada. 
2. Execute os seguintes comandos, substituindo os valores do ficheiro de metadados de Federação. Para o servidor do AD FS e o Okta, o ficheiro de Federação é federationmetadata.xml, por exemplo: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Passo 3: Teste o Federação direto no Azure AD
Agora teste a configuração de Federação direto ao convidar um novo utilizador convidado de B2B. Para obter detalhes, consulte [utilizadores de colaboração de adicionar o Azure AD B2B no portal do Azure](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Como faço para editar uma relação de Federação direto?

1. Aceda ao [Portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **relações organizacionais**.
3. Selecione **fornecedores de identidade**
4. Sob **fornecedores de identidade SAML/WS-Fed**, selecione o fornecedor.
5. No painel de detalhes do fornecedor de identidade, atualize os valores.
6. Selecione **Guardar**.


## <a name="how-do-i-remove-direct-federation"></a>Como posso remover o Federação direta?
Pode remover a configuração de Federação direto. Se o fizer, os utilizadores de convidado de Federação direto que já resgatou os seus convites não será possível iniciar sessão. Mas pode lhes dar acesso aos seus recursos novamente, excluí-los a partir do diretório e reinviting-los. Para remover o Federação direta com um fornecedor de identidade no portal do Azure AD:

1. Aceda ao [Portal do Azure](https://portal.azure.com/). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **relações organizacionais**.
3. Selecione **fornecedores de identidade**.
4. Selecione o fornecedor de identidade e, em seguida, selecione **eliminar**. 
5. Selecione **Sim** para confirmar a eliminação. 

Para remover o Federação direta com um fornecedor de identidade com o PowerShell:
1. Instalar a versão mais recente do Azure AD PowerShell para o módulo de gráfico ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Execute o seguinte comando: 
   ```powershell
   Connect-AzureAD
   ```
3. No prompt de início de sessão, inicie sessão com a conta de Administrador Global gerenciada. 
4. Introduza o seguinte comando:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
