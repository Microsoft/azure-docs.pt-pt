---
title: Azure Active Directory autenticação e Gerenciador de recursos | Microsoft Docs
description: Um guia do desenvolvedor para autenticação com a API Azure Resource Manager e Azure Active Directory para integrar um aplicativo com outras assinaturas do Azure.
author: dushyantgill
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: dugill
ms.openlocfilehash: 033f3ca9ca79903f884c625dc694b06a3e4fd04c
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263019"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Usar a API de autenticação do Gerenciador de recursos para acessar assinaturas

Se você é um desenvolvedor de software que precisa criar um aplicativo que gerencia os recursos do Azure de um cliente, este artigo mostra como se autenticar com as APIs de Azure Resource Manager e obter acesso a recursos em outras assinaturas.

Seu aplicativo pode acessar as APIs do Resource Manager de duas maneiras:

1. **Acesso de usuário + aplicativo**: para aplicativos que acessam recursos para um usuário conectado. Essa abordagem funciona para aplicativos, como aplicativos Web e ferramentas de linha de comando, que lidam apenas com "gerenciamento interativo" de recursos do Azure.
2. **Acesso somente de aplicativo**: para aplicativos que executam serviços de daemon e trabalhos agendados. A identidade do aplicativo recebe acesso direto aos recursos. Essa abordagem funciona para aplicativos que precisam de acesso sem periférico (autônomo) a longo prazo no Azure.

Este artigo fornece instruções passo a passo para criar um aplicativo que empregue ambos esses métodos de autorização. Ele mostra como fazer cada etapa com a API REST ou C#. O aplicativo MVC completo do ASP.NET está disponível em [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

## <a name="what-the-web-app-does"></a>O que o aplicativo Web faz

O aplicativo Web:

1. Entra em um usuário do Azure.
2. Solicita que o usuário conceda o acesso do aplicativo Web ao Gerenciador de recursos.
3. Obtém o token de acesso do usuário + aplicativo para acessar o Resource Manager.
4. Usa o token (da etapa 3) para atribuir a entidade de serviço do aplicativo a uma função na assinatura. Esta etapa fornece ao aplicativo acesso de longo prazo à assinatura.
5. Obtém o token de acesso somente de aplicativo.
6. Usa o token (da etapa 5) para gerenciar recursos na assinatura por meio do Resource Manager.

Este é o fluxo do aplicativo Web.

![Fluxo de autenticação do Gerenciador de recursos](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Como usuário, você fornece a ID de assinatura para a assinatura que deseja usar:

![fornecer ID da assinatura](./media/resource-manager-api-authentication/sample-ux-1.png)

Selecione a conta a ser usada para fazer logon.

![selecionar conta](./media/resource-manager-api-authentication/sample-ux-2.png)

Forneça suas credenciais.

![fornecer credenciais](./media/resource-manager-api-authentication/sample-ux-3.png)

Conceda ao aplicativo acesso às suas assinaturas do Azure:

![Conceder acesso](./media/resource-manager-api-authentication/sample-ux-4.png)

Gerencie suas assinaturas conectadas:

![Conectar assinatura](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Registrar aplicativo
Antes de começar a codificar, Registre seu aplicativo Web com o Azure Active Directory (AD). O registro do aplicativo cria uma identidade central para seu aplicativo no Azure AD. Ele contém informações básicas sobre seu aplicativo, como ID do cliente OAuth, URLs de resposta e credenciais que seu aplicativo usa para autenticar e acessar Azure Resource Manager APIs. O registro do aplicativo também registra as várias permissões delegadas que seu aplicativo precisa ao acessar as APIs da Microsoft para o usuário.

Para registrar seu aplicativo, consulte [início rápido: registrar um aplicativo com a plataforma de identidade da Microsoft](../active-directory/develop/quickstart-register-app.md). Dê um nome ao aplicativo e selecione **contas em qualquer diretório organizacional** para os tipos de conta com suporte. Para a URL de redirecionamento, forneça um domínio associado à sua Azure Active Directory.

Para entrar como o aplicativo do AD, você precisa da ID do aplicativo e de um segredo. A ID do aplicativo é exibida na visão geral do aplicativo. Para criar um segredo e solicitar permissões de API, consulte [início rápido: configurar um aplicativo cliente para acessar APIs da Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md). Forneça um novo segredo do cliente. Para permissões de API, selecione **Gerenciamento de serviços do Azure**. Selecione **permissões delegadas** e **user_impersonation**.

### <a name="optional-configuration---certificate-credential"></a>Configuração opcional-credencial do certificado
O AD do Azure também dá suporte a credenciais de certificado para aplicativos: você cria um certificado autoassinado, mantém a chave privada e adiciona a chave pública ao registro do aplicativo do Azure AD. Para autenticação, seu aplicativo envia uma carga pequena para o Azure AD assinada usando sua chave privada e o Azure AD valida a assinatura usando a chave pública que você registrou.

Para obter informações sobre como criar um aplicativo do AD com um certificado, consulte [usar Azure PowerShell para criar uma entidade de serviço para acessar recursos](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority) ou [usar CLI do Azure para criar uma entidade de serviço para acessar recursos](resource-group-authenticate-service-principal-cli.md).

## <a name="get-tenant-id-from-subscription-id"></a>Obter ID de locatário da ID de assinatura
Para solicitar um token que possa ser usado para chamar o Gerenciador de recursos, seu aplicativo precisa saber a ID do locatário do Azure AD que hospeda a assinatura do Azure. Provavelmente, os usuários conhecem suas IDs de assinatura, mas talvez não saibam suas IDs de locatário para Azure Active Directory. Para obter a ID de locatário do usuário, peça ao usuário a ID da assinatura. Forneça essa ID de assinatura ao enviar uma solicitação sobre a assinatura:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

A solicitação falha porque o usuário ainda não fez logon, mas você pode recuperar a ID do locatário da resposta. Nessa exceção, recupere a ID do locatário do valor do cabeçalho de resposta para **www-Authenticate**. Você vê essa implementação no método [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Obter o token de acesso do usuário + aplicativo
Seu aplicativo redireciona o usuário para o Azure AD com uma solicitação de autorização do OAuth 2,0-para autenticar as credenciais do usuário e obter um código de autorização. Seu aplicativo usa o código de autorização para obter um token de acesso para o Resource Manager. O método [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) cria a solicitação de autorização.

Este artigo mostra as solicitações da API REST para autenticar o usuário. Você também pode usar bibliotecas auxiliares para autenticar em seu código. Para obter mais informações sobre essas bibliotecas, consulte [Azure Active Directory bibliotecas de autenticação](../active-directory/active-directory-authentication-libraries.md). Para obter orientação sobre como integrar o gerenciamento de identidades em um aplicativo, consulte [Azure Active Directory guia do desenvolvedor](../active-directory/develop/v1-overview.md).

### <a name="auth-request-oauth-20"></a>Solicitação de autenticação (OAuth 2,0)
Emita uma solicitação de autorização Open ID Connect/OAuth 2.0 para o ponto de extremidade de autorização do Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Os parâmetros de cadeia de caracteres de consulta disponíveis para essa solicitação são descritos no artigo [solicitar um código de autorização](../active-directory/develop/v1-protocols-oauth-code.md#request-an-authorization-code) .

O exemplo a seguir mostra como solicitar a autorização do OAuth 2.0:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

O Azure AD autentica o usuário e, se necessário, solicita que o usuário Conceda permissão ao aplicativo. Ele retorna o código de autorização para a URL de resposta do seu aplicativo. Dependendo do response_mode solicitado, o Azure AD envia de volta os dados na cadeia de caracteres de consulta ou como dados de postagem.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Solicitação de autenticação (Open ID Connect)
Se você não quiser apenas acessar Azure Resource Manager para o usuário, mas também permitir que o usuário entre no seu aplicativo usando sua conta do Azure AD, emita uma solicitação de autorização do Open ID Connect. Com o Open ID Connect, seu aplicativo também recebe um id_token do Azure AD que seu aplicativo pode usar para conectar o usuário.

Os parâmetros de cadeia de caracteres de consulta disponíveis para essa solicitação são descritos no artigo [Enviar solicitação de entrada](../active-directory/develop/v1-protocols-openid-connect-code.md#send-the-sign-in-request) .

Um exemplo de solicitação Open ID Connect é:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

O Azure AD autentica o usuário e, se necessário, solicita que o usuário Conceda permissão ao aplicativo. Ele retorna o código de autorização para a URL de resposta do seu aplicativo. Dependendo do response_mode solicitado, o Azure AD envia de volta os dados na cadeia de caracteres de consulta ou como dados de postagem.

Um exemplo de resposta do Open ID Connect é:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Solicitação de token (fluxo de concessão de código do OAuth 2.0)
Agora que seu aplicativo recebeu o código de autorização do Azure AD, é hora de obter o token de acesso para Azure Resource Manager.  Poste uma solicitação de token de concessão de código OAuth 2.0 para o ponto de extremidade de token do Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Os parâmetros de cadeia de caracteres de consulta disponíveis para essa solicitação são descritos no artigo [usar o código de autorização](../active-directory/develop/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) .

O exemplo a seguir mostra uma solicitação de token de concessão de código com credencial de senha:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Ao trabalhar com credenciais de certificado, crie um token Web JSON (JWT) e assine (RSA SHA256) usando a chave privada da credencial do certificado do aplicativo. A criação desse token é mostrada no [fluxo de credenciais do cliente](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#second-case-access-token-request-with-a-certificate).

Consulte a [especificação do Open ID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) para obter detalhes sobre a autenticação do cliente.

O exemplo a seguir mostra uma solicitação de token de concessão de código com credencial de certificado:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Uma resposta de exemplo para o token de concessão de código:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Manipular resposta de token de concessão de código
Uma resposta de token bem-sucedida contém o token de acesso (usuário + aplicativo) para Azure Resource Manager. Seu aplicativo usa esse token de acesso para acessar o Resource Manager para o usuário. O tempo de vida dos tokens de acesso emitidos pelo Azure AD é de uma hora. É improvável que seu aplicativo Web precise renovar o token de acesso (usuário + aplicativo). Se precisar renovar o token de acesso, use o token de atualização que seu aplicativo recebe na resposta do token. Poste uma solicitação de token OAuth 2.0 para o ponto de extremidade de token do Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Os parâmetros a serem usados com a solicitação de atualização são descritos em [atualizando o token de acesso](../active-directory/develop/v1-protocols-oauth-code.md#refreshing-the-access-tokens).

O exemplo a seguir mostra como usar o token de atualização:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Embora os tokens de atualização possam ser usados para obter novos tokens de acesso para Azure Resource Manager, eles não são adequados para acesso offline pelo seu aplicativo. O tempo de vida dos tokens de atualização é limitado e os tokens de atualização são associados ao usuário. Se o usuário sair da organização, o aplicativo que usa o token de atualização perderá o acesso. Essa abordagem não é adequada para aplicativos que são usados por equipes para gerenciar seus recursos do Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Verificar se o usuário pode atribuir acesso à assinatura
Seu aplicativo agora tem um token para acessar Azure Resource Manager para o usuário. A próxima etapa é conectar seu aplicativo à assinatura. Após a conexão, seu aplicativo poderá gerenciar essas assinaturas mesmo quando o usuário não estiver presente (acesso offline de longo prazo).

Para cada assinatura para se conectar, chame a API de [permissões da lista do Gerenciador de recursos](https://docs.microsoft.com/rest/api/authorization/permissions) para determinar se o usuário tem direitos de gerenciamento de acesso para a assinatura.

O método [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) do aplicativo de exemplo MVC do ASP.net implementa essa chamada.

O exemplo a seguir mostra como solicitar as permissões de um usuário em uma assinatura. 83cfe939-2402-4581-b761-4f59b0a041e4 é a ID da assinatura.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Um exemplo da resposta para obter as permissões do usuário na assinatura é:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

A API de permissões retorna várias permissões. Cada permissão consiste em ações permitidas (**ações**) e ações não permitidas (**ação**). Se uma ação estiver presente nas ações permitidas de qualquer permissão e não estiver presente nas ações não permitidas dessa permissão, o usuário poderá fazer essa ação. **Microsoft. Authorization/RoleAssignments/Write** é a ação que concede direitos de gerenciamento de acesso. Seu aplicativo deve analisar o resultado da permissão para procurar uma correspondência Regex nessa cadeia de caracteres de ação nas **ações** e em **ação** de cada permissão.

## <a name="get-app-only-access-token"></a>Obter token de acesso somente de aplicativo
Agora, você sabe se o usuário pode atribuir acesso à assinatura do Azure. As próximas etapas são:

1. Atribua a função RBAC apropriada à identidade do seu aplicativo na assinatura.
2. Valide a atribuição de acesso consultando a permissão do aplicativo na assinatura ou acessando o Resource Manager usando o token somente de aplicativo.
3. Registre a conexão na estrutura de dados de "assinaturas conectadas" de seus aplicativos – persistindo a ID da assinatura.

Vamos examinar mais de perto a primeira etapa. Para atribuir a função RBAC apropriada à identidade do aplicativo, você deve determinar:

* A ID de objeto da identidade do seu aplicativo no Azure Active Directory do usuário
* O identificador da função RBAC que seu aplicativo requer na assinatura

Quando o aplicativo autentica um usuário de um Azure AD, ele cria um objeto de entidade de serviço para seu aplicativo no Azure AD. O Azure permite que as funções RBAC sejam atribuídas às entidades de serviço para conceder acesso direto aos aplicativos correspondentes nos recursos do Azure. Essa ação é exatamente o que você deseja fazer. Consulte o API do Graph do Azure AD para determinar o identificador da entidade de serviço do seu aplicativo no Azure AD do usuário conectado.

Você tem apenas um token de acesso para Azure Resource Manager-você precisa de um novo token de acesso para chamar o API do Graph do Azure AD. Cada aplicativo no Azure AD tem permissão para consultar seu próprio objeto de entidade de serviço, portanto, um token de acesso somente de aplicativo é suficiente.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Obter o token de acesso somente de aplicativo para o Azure AD API do Graph

Para autenticar seu aplicativo e obter um token para o Azure AD API do Graph, emita uma credencial de cliente conceder solicitação de token de fluxo OAuth 2.0 para o ponto de extremidade de token do Azure AD (**https: \//login. microsoftonline. com/{directory_domain_name}/OAuth2/token**).

O método [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) do aplicativo de exemplo MVC do ASP.net Obtém um token de acesso somente de aplicativo para API do Graph usando o biblioteca de autenticação do Active Directory para .net.

Os parâmetros de cadeia de caracteres de consulta disponíveis para essa solicitação são descritos no artigo [solicitar um token de acesso](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#request-an-access-token) .

Uma solicitação de exemplo para o token de concessão de credencial do cliente:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Uma resposta de exemplo para o token de concessão de credencial do cliente:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Obter ObjectId da entidade de serviço do aplicativo no usuário do Azure AD
Agora, use o token de acesso somente de aplicativo para consultar a API de [entidades de serviço do Azure ad Graph](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) para determinar a ID de objeto da entidade de serviço do aplicativo no diretório.

O método [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) do aplicativo de exemplo MVC do ASP.net implementa essa chamada.

O exemplo a seguir mostra como solicitar a entidade de serviço de um aplicativo. a0448380-c346-4f9f-b897-c18733de9394 é a ID do cliente do aplicativo.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

O exemplo a seguir mostra uma resposta à solicitação para a entidade de serviço de um aplicativo

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Obter identificador de função do RBAC do Azure
Para atribuir a função RBAC apropriada à entidade de serviço, você deve determinar o identificador da função RBAC do Azure.

A função RBAC correta para seu aplicativo:

* Se seu aplicativo monitorar apenas a assinatura, sem fazer nenhuma alteração, ele exigirá apenas permissões de leitura na assinatura. Atribua a função **leitor** .
* Se seu aplicativo gerencia a assinatura do Azure, criando/modificando/excluindo entidades, ele requer uma das permissões de colaborador.
  * Para gerenciar um determinado tipo de recurso, atribua as funções de colaborador específicas do recurso (colaborador da máquina virtual, colaborador da rede virtual, colaborador da conta de armazenamento, etc.)
  * Para gerenciar qualquer tipo de recurso, atribua a função **colaborador** .

A atribuição de função para seu aplicativo fica visível para os usuários; portanto, selecione o privilégio mínimo exigido.

Chame a [API de definição de função do Gerenciador de recursos](https://docs.microsoft.com/rest/api/authorization/roledefinitions) para listar todas as funções RBAC do Azure e, em seguida, itere sobre o resultado para localizar a definição de função por nome.

O método [Getroleid](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) do aplicativo de exemplo MVC ASP.net implementa essa chamada.

O exemplo de solicitação a seguir mostra como obter o identificador de função RBAC do Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb é a ID da assinatura.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

A resposta está no seguinte formato:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Você não precisa chamar essa API em uma base contínua. Depois de determinar o GUID bem conhecido da definição de função, você pode construir a ID de definição de função como:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Aqui estão os identificadores das funções internas comumente usadas:

| Função | VOLUME |
| --- | --- |
| Leitor |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Contribuinte |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Colaborador da máquina virtual |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Colaborador de rede virtual |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Colaborador da conta de armazenamento |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Colaborador do site |de139f84-1756-47ae-9be6-808fbbe84772 |
| Colaborador do plano da Web |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Colaborador de SQL Server |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Colaborador do BD SQL |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Atribuir função RBAC ao aplicativo
Você tem tudo o que precisa para atribuir a função RBAC apropriada à entidade de serviço usando a API de [atribuição de função CREATE ROLE do Gerenciador de recursos](https://docs.microsoft.com/rest/api/authorization/roleassignments) .

O método [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) do aplicativo de exemplo MVC do ASP.net implementa essa chamada.

Uma solicitação de exemplo para atribuir a função de RBAC ao aplicativo:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

Na solicitação, os seguintes valores são usados:

| GUID | Descrição |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |a ID da assinatura |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |a ID de objeto da entidade de serviço do aplicativo |
| b24988ac-6180-42a0-ab88-20f7382dd24c |a ID da função colaborador |
| 4f87261d-2816-465d-8311-70a27558df4c |um novo GUID criado para a nova atribuição de função |

A resposta está no seguinte formato:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Obter token de acesso somente de aplicativo para Azure Resource Manager
Para validar que o aplicativo pode acessar a assinatura, faça uma tarefa de teste na assinatura usando um token somente de aplicativo.

Para obter um token de acesso somente de aplicativo, siga as instruções da seção [obter token de acesso somente de aplicativo para o Azure AD API do Graph](#app-azure-ad-graph), com um valor diferente para o parâmetro de recurso:

    https://management.core.windows.net/

O método [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) do aplicativo de exemplo MVC do ASP.net Obtém um token de acesso somente de aplicativo para Azure Resource Manager usando o biblioteca de autenticação do Active Directory para .net.

#### <a name="get-applications-permissions-on-subscription"></a>Obter as permissões do aplicativo na assinatura
Para verificar se seu aplicativo pode acessar uma assinatura do Azure, você também pode chamar a API de [permissões do Gerenciador de recursos](https://docs.microsoft.com/rest/api/authorization/permissions) . Essa abordagem é semelhante a como você determinou se o usuário tem direitos de gerenciamento de acesso para a assinatura. No entanto, desta vez, chame a API de permissões com o token de acesso somente de aplicativo que você recebeu na etapa anterior.

O método [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) do aplicativo de exemplo MVC do ASP.net implementa essa chamada.

## <a name="manage-connected-subscriptions"></a>Gerenciar assinaturas conectadas
Quando a função RBAC apropriada é atribuída à entidade de serviço do seu aplicativo na assinatura, seu aplicativo pode manter o monitoramento/gerenciamento usando tokens de acesso somente de aplicativo para Azure Resource Manager.

Se um proprietário de assinatura remover a atribuição de função do aplicativo usando o portal ou as ferramentas de linha de comando, seu aplicativo não poderá mais acessar essa assinatura. Nesse caso, você deve notificar o usuário de que a conexão com a assinatura foi severa de fora do aplicativo e dar a eles uma opção para "reparar" a conexão. "Reparar" recriaria a atribuição de função que foi excluída offline.

Assim como você habilitou o usuário para conectar assinaturas ao seu aplicativo, você deve permitir que o usuário desconecte assinaturas também. De um ponto de vista de gerenciamento de acesso, desconectar significa remover a atribuição de função que a entidade de serviço do aplicativo tem na assinatura. Opcionalmente, qualquer estado no aplicativo para a assinatura pode ser removido também.
Somente os usuários com permissão de gerenciamento de acesso na assinatura podem desconectar a assinatura.

O [método RevokeRoleFromServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) do aplicativo de exemplo MVC do ASP.net implementa essa chamada.

É isso: os usuários agora podem facilmente conectar e gerenciar suas assinaturas do Azure com seu aplicativo.
