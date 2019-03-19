---
title: Autenticação do Active Directory e Gestor de recursos do Azure | Documentos da Microsoft
description: Guia de um desenvolvedor para autenticação com a API do Azure Resource Manager e o Azure Active Directory para integrar uma aplicação com outras subscrições do Azure.
services: azure-resource-manager,active-directory
documentationcenter: na
author: dushyantgill
manager: timlt
editor: tysonn
ms.assetid: 17b2b40d-bf42-4c7d-9a88-9938409c5088
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2018
ms.author: dugill
ms.openlocfilehash: 138367eb7eb0d4be2e0a7bec57d1bce551a5e829
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107057"
---
# <a name="use-resource-manager-authentication-api-to-access-subscriptions"></a>Utilize o recurso de API do Gerenciador de autenticação para aceder a subscrições

Se for um desenvolvedor de software que precisa para criar uma aplicação que gere recursos de um cliente do Azure, este artigo mostra-lhe como autenticar com as APIs do Azure Resource Manager e obter acesso aos recursos de outras subscrições.

Seu aplicativo pode acessar as APIs do Resource Manager em duas formas:

1. **Acesso à aplicação de utilizador +**: para aplicações que acedam aos recursos em nome de um utilizador com sessão iniciada. Essa abordagem funciona para aplicações, tais como aplicações web e ferramentas de linha de comando, que lidam com apenas "gestão interativa" dos recursos do Azure.
2. **Acesso só de aplicação**: para aplicativos que executam os serviços de daemon e tarefas agendadas. A identidade da aplicação é concedida acesso direto aos recursos. Essa abordagem funciona para aplicações que precisam de acesso longo prazo de (automático) sem interface para o Azure.

Este artigo fornece instruções passo a passo para criar uma aplicação que utiliza estes métodos de autorização. Ele mostra como realizar cada passo com a REST API ou c#. A aplicação ASP.NET MVC completa está disponível em [ https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense ](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-the-web-app-does"></a>O que faz a aplicação web

A aplicação web:

1. Inicia sessão um utilizador do Azure.
2. Pede o utilizador para conceder o acesso da aplicação web para o Resource Manager.
3. Obtém o token de acesso da aplicação de utilizador + para aceder ao Gestor de recursos.
4. Utiliza o token (a partir do passo 3) para atribuir o principal de serviço da aplicação a uma função na subscrição. Este passo fornece o acesso de longo prazo da aplicação para a subscrição.
5. Obtém acesso só de aplicação token.
6. Utiliza o token (a partir do passo 5) para gerir os recursos na subscrição através do Resource Manager.

Eis o fluxo do aplicativo web.

![Fluxo de autenticação de Gestor de recursos](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Como um utilizador, é possível fornecer o ID de subscrição para a subscrição que pretende utilizar:

![Forneça o ID de subscrição](./media/resource-manager-api-authentication/sample-ux-1.png)

Selecione a conta a utilizar para iniciar sessão.

![Selecione a conta](./media/resource-manager-api-authentication/sample-ux-2.png)

Forneça as credenciais.

![fornecer credenciais](./media/resource-manager-api-authentication/sample-ux-3.png)

Conceda o acesso de aplicação às suas subscrições do Azure:

![Conceder acesso](./media/resource-manager-api-authentication/sample-ux-4.png)

Gerir as subscrições de ligada:

![Ligar a subscrição](./media/resource-manager-api-authentication/sample-ux-7.png)

## <a name="register-application"></a>Registar aplicação
Antes de começar a codificar, registe a sua aplicação web com o Azure Active Directory (AD). O registo de aplicações cria uma identidade central para a sua aplicação no Azure AD. Ele contém informações básicas sobre a sua aplicação, como ID de cliente OAuth, URLs de resposta e as credenciais que a aplicação utiliza para autenticar e aceder às API do Azure Resource Manager. O registo de aplicações também regista as várias permissões de delegado que seu aplicativo precisa ao aceder a APIs da Microsoft em nome do utilizador.

Porque a sua aplicação acede a outra subscrição, tem de configurá-la como uma aplicação multi-inquilino. Para passar na validação, forneça um domínio associado com o Azure Active Directory. Para ver os domínios associados com o Azure Active Directory, inicie sessão no portal.

O exemplo seguinte mostra como registar a aplicação com o Azure PowerShell. Tem de ter a versão mais recente (Agosto de 2016) do Azure PowerShell para este comando funcione.

```azurepowershell-interactive
$app = New-AzADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true
```

Para iniciar sessão como a aplicação do AD, precisa do ID da aplicação e da palavra-passe. Para ver o ID da aplicação que é devolvido do comando anterior, utilize:

```azurepowershell-interactive
$app.ApplicationId
```

O exemplo seguinte mostra como registar a aplicação com a CLI do Azure.

```azurecli-interactive
az ad app create --display-name {app name} --homepage https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available-to-other-tenants true
```

Os resultados incluem o AppId, que precisa durante a autenticação da aplicação.

### <a name="optional-configuration---certificate-credential"></a>Configuração opcional - credenciais de certificado
O Azure AD também suporta credenciais de certificado para aplicações: criar um certificado autoassinado, mantenha a chave privada e adicione a chave pública para o registo de aplicação do Azure AD. Para a autenticação, a aplicação envia um payload pequeno para o Azure AD assinado utilizando a chave privada e do Azure AD valida a assinatura usando a chave pública que registrou.

Para obter informações sobre como criar uma aplicação AD com um certificado, consulte [utilize o Azure PowerShell para criar um principal de serviço para aceder aos recursos](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority) ou [utilização do Azure CLI para criar um principal de serviço para aceder aos recursos](resource-group-authenticate-service-principal-cli.md) .

## <a name="get-tenant-id-from-subscription-id"></a>Obter o ID de inquilino do ID de subscrição
Para pedir um token que pode ser utilizado para chamar o Resource Manager, a sua aplicação precisa de saber o ID de inquilino do inquilino do Azure AD que aloja a subscrição do Azure. Provavelmente, os utilizadores sabem suas IDs de subscrição, mas eles talvez não saiba respetivo inquilino IDs para o Azure Active Directory. Para obter o ID de inquilino do utilizador, pedir ao utilizador para o ID de subscrição. Forneça essa subscrição ID ao enviar um pedido sobre a subscrição:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

O pedido falha porque o utilizador não iniciou sessão ainda, mas pode recuperar o ID de inquilino da resposta. Nessa exceção, obter o ID de inquilino do valor de cabeçalho de resposta para **WWW-Authenticate**. Vê nessa implementação do [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) método.

## <a name="get-user--app-access-token"></a>Obter utilizador + token de acesso de aplicação
A aplicação redireciona o utilizador para o Azure AD com um OAuth 2.0 autorizar pedido - autenticar as credenciais do usuário e obter um código de autorização. Seu aplicativo usa o código de autorização para obter um token de acesso para o Resource Manager. O [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) método cria o pedido de autorização.

Este artigo mostra as solicitações da REST API para autenticar o utilizador. Também pode utilizar bibliotecas auxiliares para efetuar a autenticação no seu código. Para obter mais informações sobre estas bibliotecas, consulte [bibliotecas de autenticação do Azure Active Directory](../active-directory/active-directory-authentication-libraries.md). Para obter orientações sobre como integrar a gestão de identidades num aplicativo, consulte [Guia do programador do Azure Active Directory](../active-directory/develop/v1-overview.md).

### <a name="auth-request-oauth-20"></a>Pedido de autenticação (OAuth 2.0)
Emita uma abrir ID Connect/OAuth2.0 autorizar pedido para o ponto de final de autorização do Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Os parâmetros de cadeia de caracteres de consulta que estão disponíveis para este pedido descritos a [pedir um código de autorização](../active-directory/develop/v1-protocols-oauth-code.md#request-an-authorization-code) artigo.

O exemplo seguinte mostra como OAuth2.0 autorização do pedido:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

O Azure AD autentica o utilizador e, se necessário, pede ao utilizador para conceder permissão à aplicação. Ele retorna o código de autorização para o URL de resposta do seu aplicativo. Dependendo do pedido response_mode, do Azure AD ou envia de volta os dados na cadeia de consulta ou como dados de postagem.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Pedido de autenticação (Abrir ID Connect)
Se não apenas pretende aceder ao Azure Resource Manager em nome do utilizador, mas também permitir que o utilizador iniciar sessão na sua aplicação com a respetiva conta do Azure AD, emita um aberto ligar autorizar pedidos de ID. Com abrir ID Connect, o seu aplicativo receba também um id_token do Azure AD que a aplicação pode utilizar para iniciar a sessão do utilizador.

Os parâmetros de cadeia de caracteres de consulta que estão disponíveis para este pedido descritos a [enviar o pedido de início de sessão](../active-directory/develop/v1-protocols-openid-connect-code.md#send-the-sign-in-request) artigo.

Um exemplo de solicitação abrir ID Connect é:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

O Azure AD autentica o utilizador e, se necessário, pede ao utilizador para conceder permissão à aplicação. Ele retorna o código de autorização para o URL de resposta do seu aplicativo. Dependendo do pedido response_mode, do Azure AD ou envia de volta os dados na cadeia de consulta ou como dados de postagem.

Um exemplo abrir ID Connect resposta é:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Pedido de token (OAuth2.0 código de fluxo de concessão de)
Agora que a aplicação recebeu o código de autorização do Azure AD, chegou a hora para obter o token de acesso para o Azure Resource Manager.  Publicar um OAuth2.0 código concessão Token do pedido para o ponto final do Token do Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Os parâmetros de cadeia de caracteres de consulta que estão disponíveis para este pedido descritos a [utilize o código de autorização](../active-directory/develop/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) artigo.

O exemplo seguinte mostra uma solicitação de token de concessão de código com a credencial de palavra-passe:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Ao trabalhar com as credenciais de certificado, crie um JSON Web Token (JWT) e o início de sessão (RSA SHA256) com a chave privada da credencial de certificado da sua aplicação. Criar este token é mostrada na [fluxo de credenciais de cliente](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#second-case-access-token-request-with a-certificate).  Para referência, consulte a [código de biblioteca de autenticação do Active Directory (.NET)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) para assinar os tokens JWT de asserção de cliente.

Consulte a [spec abrir ID Connect](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) para obter detalhes sobre a autenticação de cliente.

O exemplo seguinte mostra uma solicitação de token de concessão de código com a credencial de certificado:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Uma resposta de exemplo para o token de concessão de código:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Lidar com a resposta de token de concessão de código
Uma resposta com êxito de token contém (utilizador + aplicação) o token de acesso para o Azure Resource Manager. A aplicação utiliza esse token de acesso para aceder ao Gestor de recursos em nome do utilizador. O tempo de vida de tokens de acesso emitido pelo Azure AD é uma hora. É improvável que seu aplicativo web tem de renovar (utilizador + aplicação) token de acesso. Se precisar de renovar o token de acesso, utilize o token de atualização que seu aplicativo recebe na resposta de token. Publicar um OAuth2.0 Token do pedido para o ponto final do Token do Azure AD:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Os parâmetros a utilizar com o pedido de atualização são descritos no [atualização do token de acesso](../active-directory/develop/v1-protocols-oauth-code.md#refreshing-the-access-tokens).

O exemplo seguinte mostra como utilizar a atualização do token:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Embora os tokens de atualização podem ser usados para obter novos tokens de acesso para o Azure Resource Manager, não são adequados para acesso offline pela sua aplicação. O tempo de vida de tokens de atualização é limitado e tokens de atualização são vinculados ao usuário. Se o utilizador sai da organização, o aplicativo usando o token de atualização perde o acesso. Essa abordagem não é adequada para aplicações que são utilizadas pelas equipes para gerir os seus recursos do Azure.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Verifique se o utilizador pode atribuir acesso à subscrição
Agora o seu aplicativo tem um token de acesso do Azure Resource Manager em nome do utilizador. O passo seguinte é ligar a sua aplicação para a subscrição. Depois de ligar, a aplicação pode gerir essas subscrições, mesmo quando o utilizador não estiver presente (acesso offline de longo prazo).

Para cada subscrição ligar, chamar o [permissões do Gestor de recursos lista](https://docs.microsoft.com/rest/api/authorization/permissions) API para determinar se o utilizador tem direitos de gestão de acesso para a subscrição.

O [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) esta chamada implementa o método de aplicação de exemplo de ASP.NET MVC.

O exemplo seguinte mostra como pedir permissões de um utilizador numa subscrição. 83cfe939-2402-4581-b761-4f59b0a041e4 é o ID da subscrição.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Um exemplo da resposta ao obter as permissões de utilizador na subscrição é:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

As permissões de API devolve várias permissões. Cada permissão consiste em ações permitidas (**ações**) e não são permitidas ações (**notactions**). Se uma ação está presente nas ações permitidas de qualquer permissão e não está presente nas ações não permitidas dessa permissão, o utilizador tem permissão para efetuar essa ação. **Microsoft.Authorization/RoleAssignments/Write** é a ação que concede acesso a direitos de gestão. Seu aplicativo deve analisar o resultado de permissões para procurar uma correspondência de regex essa cadeia de caracteres de ação para o **ações** e **notactions** de cada permissão.

## <a name="get-app-only-access-token"></a>Obtenha o token de acesso só de aplicação
Agora, sabe se o utilizador pode atribuir acesso à subscrição do Azure. As próximas etapas são:

1. Atribua a função RBAC adequada a identidade do seu aplicativo na subscrição.
2. Valide a atribuição de acesso através da consulta de permissão da aplicação na subscrição ou ao aceder ao Gestor de recursos com o token de só de aplicação.
3. Registe a ligação na sua estrutura de dados de aplicativos "ligadas subscrições" - persistir o ID da subscrição.

Vamos ver com mais detalhes a primeira etapa. Para atribuir a função RBAC adequada a identidade da aplicação, tem de determinar:

* O ID de objeto da identidade da sua aplicação no Azure Active Directory do utilizador
* O identificador da função RBAC que seu aplicativo exigir na subscrição

Quando a sua aplicação autentica-se um utilizador a partir de um Azure AD, cria um objeto do principal de serviço para a sua aplicação em que o Azure AD. O Azure permite-funções RBAC a ser atribuída ao principais de serviço para conceder acesso direto a aplicações correspondentes em recursos do Azure. Esta ação é exatamente o que deseja fazer. Azure AD da consulta o Azure AD Graph API para determinar o identificador do principal de serviço da sua aplicação do utilizador com sessão iniciada.

Tem apenas um token de acesso para o Azure Resource Manager - precisa de um novo token de acesso para chamar a API do Azure AD Graph. Todas as aplicações no Azure AD tem permissão para consultar o seu próprio objeto principal de serviço, portanto, um token de acesso só de aplicação é suficiente.

<a id="app-azure-ad-graph" />

### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Obter token de acesso só de aplicação para o Azure AD Graph API

Para autenticar a sua aplicação e obter um token para o Azure AD Graph API, emita um pedido de token de fluxo de OAuth2.0 de concessão de credenciais de cliente ao ponto final de token do Azure AD (**https:\//login.microsoftonline.com/{directory_domain_name}/OAuth2/Token** ).

O [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) método do exemplo de aplicativo ASP.net MVC obtém um acesso só de aplicação token para a Graph API com o Active Directory Authentication Library para .NET.

Os parâmetros de cadeia de caracteres de consulta que estão disponíveis para este pedido descritos a [solicitar um Token de acesso](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#request-an-access-token) artigo.

Um exemplo de solicitação de token de concessão de credenciais de cliente:

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Uma resposta de exemplo para o token de concessão de credenciais de cliente:

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Obtenha o ObjectId do principal de serviço de aplicações no utilizador do Azure AD
Agora, utilize o token de acesso só de aplicação para consultar o [principais de serviço do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API para determinar o ID de objeto do principal de serviço do aplicativo no diretório.

O [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) esta chamada implementa o método do exemplo de aplicativo ASP.net MVC.

O exemplo seguinte mostra como pedir principal de serviço de um aplicativo. a0448380-c346-4f9f-b897-c18733de9394 é o ID de cliente da aplicação.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

O exemplo seguinte mostra uma resposta ao pedido de serviço de uma aplicação do principal

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Obter o identificador de função RBAC do Azure
Para atribuir a função RBAC adequada ao seu principal de serviço, tem de determinar o identificador da função RBAC do Azure.

A função RBAC certa para a sua aplicação:

* Se a aplicação monitoriza apenas a subscrição, sem fazer quaisquer alterações, requer permissões de leitura apenas na subscrição. Atribuir a **leitor** função.
* Se a subscrição, criar/modificar/eliminar entidades, gerir a sua aplicação Azure requer uma das permissões de contribuinte.
  * Para gerir um determinado tipo de recurso, atribua as funções de contribuinte de recursos específicos (contribuinte de Máquina Virtual, contribuinte de rede Virtual, contribuinte de conta de armazenamento, etc.)
  * Para gerir qualquer tipo de recurso, atribuir os **contribuinte** função.

A atribuição de função para a sua aplicação é visível para utilizadores, por isso, selecione o privilégio necessário de menos.

Chamar o [definição de função de Gestor de recursos API](https://docs.microsoft.com/rest/api/authorization/roledefinitions) listar todas as funções RBAC do Azure e pesquisa, em seguida, iterar sobre o resultado para encontrar a definição de função desejada por nome.

O [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) esta chamada implementa o método de aplicação de exemplo de ASP.net MVC.

O exemplo de pedido seguinte mostra como obter o identificador de função RBAC do Azure. 09cbd307-aa71-4aca-b346-5f253e6e3ebb é o ID da subscrição.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

A resposta está no seguinte formato:

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Não é necessário chamar esta API de forma contínua. Depois de determinar o GUID bem conhecido da definição de função, pode construir o ID de definição de função como:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Aqui estão os identificadores de funções incorporadas usadas:

| Função | GUID |
| --- | --- |
| Leitor |acdd72a7-3385-48ef-bd42-f606fba81ae7 |
| Contribuinte |b24988ac-6180-42a0-ab88-20f7382dd24c |
| Contribuinte de Máquina Virtual |d73bb868-a0df-4d4d-bd69-98a00b01fccb |
| Contribuinte de rede virtual |b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
| Contribuinte de Conta de Armazenamento |86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
| Contribuinte de Web site |de139f84-1756-47ae-9be6-808fbbe84772 |
| Contribuinte de Plano Web |2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
| Contribuinte do SQL Server |6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
| Contribuinte de Base de Dados SQL |9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |

### <a name="assign-rbac-role-to-application"></a>Atribuir função RBAC a aplicação
Tem tudo o que precisa para atribuir a função RBAC adequada ao seu principal de serviço, utilizando o [Resource Manager criar atribuição de função](https://docs.microsoft.com/rest/api/authorization/roleassignments) API.

O [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) esta chamada implementa o método de aplicação de exemplo de ASP.net MVC.

Um exemplo de solicitação para atribuir a função RBAC a aplicação:

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

Na solicitação, são utilizados os seguintes valores:

| Guid | Descrição |
| --- | --- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb |o ID da subscrição |
| c3097b31-7309-4c59-b4e3-770f8406bad2 |o ID de objeto do principal de serviço do aplicativo |
| acdd72a7-3385-48ef-bd42-f606fba81ae7 |o ID da função do leitor |
| 4f87261d-2816-465d-8311-70a27558df4c |um novo guid criado para a nova atribuição de função |

A resposta está no seguinte formato:

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Obter acesso só de aplicação token para o Azure Resource Manager
Para validar essa aplicação tem o desejado aceder na subscrição, executar uma tarefa de teste na subscrição com um token de só de aplicação.

Para obter um token de acesso só de aplicação, siga as instruções da secção [obter token de acesso só de aplicação para o Azure AD Graph API](#app-azure-ad-graph), com um valor diferente para o parâmetro de recurso:

    https://management.core.windows.net/

O [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) método do exemplo de aplicativo ASP.NET MVC obtém um acesso só de aplicação token para o Azure Resource Manager com o Active Directory Authentication Library para .net.

#### <a name="get-applications-permissions-on-subscription"></a>Obter as permissões da aplicação na subscrição
Para verificar se o seu aplicativo tem o acesso desejado numa subscrição do Azure, também pode chamar o [permissões do Gestor de recursos](https://docs.microsoft.com/rest/api/authorization/permissions) API. Essa abordagem é semelhante à forma como determinado se o utilizador tem direitos de gestão de acesso para a subscrição. No entanto, desta vez, chame as API de permissões com o token de acesso só de aplicação que recebeu no passo anterior.

O [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) esta chamada implementa o método de aplicação de exemplo de ASP.NET MVC.

## <a name="manage-connected-subscriptions"></a>Gerir subscrições ligadas
Quando lhe for atribuída a função RBAC adequada principal na subscrição de serviço da sua aplicação, seu aplicativo pode manter a monitorização/gerenciá-lo com os tokens de acesso só de aplicação para o Azure Resource Manager.

Se um proprietário da subscrição remover atribuição de função da sua aplicação com o portal ou ferramentas de linha de comandos, a aplicação já não é possível aceder a essa subscrição. Nesse caso, deve notificar o utilizador que a ligação com a subscrição foi severed a partir de fora da aplicação e dar-lhes uma opção para "reparar" a ligação. "Reparar" seria voltar a criar a atribuição de função que tenha sido eliminada offline.

Tal como ativou o utilizador para ligar a subscrições para a sua aplicação, tem de permitir ao utilizador desligar subscrições demasiado. A partir de um acesso gestão ponto de vista, desligue significa remover a atribuição de função principal de serviço do aplicativo tem na subscrição. Opcionalmente, qualquer Estado do aplicativo para a subscrição poderão ser removido demasiado.
Apenas os utilizadores com permissão de gestão de acesso da subscrição são possível desligar a subscrição.

O [RevokeRoleFromServicePrincipalOnSubscription método](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) do ASP.net MVC, aplicação de exemplo implementa esta chamada.

Isso é tudo - os utilizadores podem agora facilmente se conectar e gerem as suas subscrições do Azure com a sua aplicação.
