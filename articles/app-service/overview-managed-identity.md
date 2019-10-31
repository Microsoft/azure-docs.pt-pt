---
title: Visão geral de identidades gerenciadas-serviço de Azure App | Microsoft Docs
description: Guia de referência conceitual e de configuração para identidades gerenciadas no serviço Azure App e Azure Functions
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/30/2019
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: a5176f74964e0809cea39aa160943cc6f3451237
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176518"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Como usar identidades gerenciadas para o serviço de aplicativo e Azure Functions

> [!Important] 
> As identidades gerenciadas para o serviço de aplicativo e o Azure Functions não se comportarão conforme o esperado se seu aplicativo for migrado entre assinaturas/locatários. O aplicativo precisará obter uma nova identidade, o que pode ser feito desabilitando e reabilitando o recurso. Consulte [removendo uma identidade](#remove) abaixo. Os recursos downstream também precisarão ter políticas de acesso atualizadas para usar a nova identidade.

Este tópico mostra como criar uma identidade gerenciada para aplicativos de Azure Functions e serviço de aplicativo e como usá-lo para acessar outros recursos. As identidades geridas do Azure Active Directory permitem que a sua aplicação aceda facilmente a outros recursos protegidos pelo AAD, como o Azure Key Vault. A identidade é gerenciada pela plataforma do Azure e não exige que você provisione ou gire segredos. Para obter mais informações sobre identidades gerenciadas no AAD, consulte [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

Seu aplicativo pode receber dois tipos de identidades: 
- Uma **identidade atribuída pelo sistema** é vinculada ao seu aplicativo e é excluída se seu aplicativo for excluído. Um aplicativo pode ter apenas uma identidade atribuída pelo sistema.
- Uma **identidade atribuída pelo usuário** é um recurso autônomo do Azure que pode ser atribuído ao seu aplicativo. Um aplicativo pode ter várias identidades atribuídas pelo usuário.

## <a name="adding-a-system-assigned-identity"></a>Adicionando uma identidade atribuída pelo sistema

A criação de um aplicativo com uma identidade atribuída pelo sistema requer que uma propriedade adicional seja definida no aplicativo.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para configurar uma identidade gerenciada no portal, primeiro você criará um aplicativo como normal e, em seguida, habilitará o recurso.

1. Crie um aplicativo no portal como faria normalmente. Navegue até ele no Portal.

2. Se estiver usando um aplicativo de funções, navegue até **recursos da plataforma**. Para outros tipos de aplicativos, role para baixo até o grupo **configurações** no painel de navegação esquerdo.

3. Selecione **identidade gerenciada**.

4. Na guia **atribuído pelo sistema** , alterne o **status** para **ativado**. Clique em **Guardar**.

    ![Identidade gerenciada no serviço de aplicativo](media/app-service-managed-service-identity/msi-blade-system.png)

### <a name="using-the-azure-cli"></a>Com a CLI do Azure

Para configurar uma identidade gerenciada usando o CLI do Azure, você precisará usar o comando `az webapp identity assign` em um aplicativo existente. Você tem três opções para executar os exemplos nesta seção:

- Use [Azure cloud Shell](../cloud-shell/overview.md) do portal do Azure.
- Use o Azure Cloud Shell inserido por meio do botão "experimentar", localizado no canto superior direito de cada bloco de código abaixo.
- [Instale a versão mais recente do CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 ou posterior) se preferir usar um console local da CLI. 

As etapas a seguir mostrarão como criar um aplicativo Web e atribuir a ele uma identidade usando a CLI:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Use uma conta que esteja associada à assinatura do Azure sob a qual você deseja implantar o aplicativo:

    ```azurecli-interactive
    az login
    ```
2. Crie um aplicativo Web usando a CLI. Para obter mais exemplos de como usar a CLI com o serviço de aplicativo, consulte [exemplos da CLI do serviço de aplicativo](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Execute o comando `identity assign` para criar a identidade para este aplicativo:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

As etapas a seguir mostrarão como criar um aplicativo Web e atribuir a ele uma identidade usando Azure PowerShell:

1. Se for preciso, instale o Azure PowerShell com a instrução que se encontra no [Guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Login-AzAccount` para criar uma ligação ao Azure.

2. Crie um aplicativo Web usando Azure PowerShell. Para obter mais exemplos de como usar Azure PowerShell com o serviço de aplicativo, consulte [exemplos do PowerShell do serviço de aplicativo](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Execute o comando `Set-AzWebApp -AssignIdentity` para criar a identidade para este aplicativo:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo de Azure Resource Manager

Um modelo de Azure Resource Manager pode ser usado para automatizar a implantação de seus recursos do Azure. Para saber mais sobre como implantar o serviço de aplicativo e as funções, consulte [automatizando a implantação de recursos no serviço de aplicativo](../app-service/deploy-complex-application-predictably.md) e [automatizando a implantação de recursos no Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Qualquer recurso do tipo `Microsoft.Web/sites` pode ser criado com uma identidade, incluindo a seguinte propriedade na definição de recurso:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Um aplicativo pode ter identidades atribuídas pelo sistema e atribuídas pelo usuário ao mesmo tempo. Nesse caso, a propriedade `type` seria `SystemAssigned,UserAssigned`

Adicionar o tipo atribuído pelo sistema informa ao Azure para criar e gerenciar a identidade do seu aplicativo.

Por exemplo, um aplicativo Web pode ser semelhante ao seguinte:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Quando o site é criado, ele tem as seguintes propriedades adicionais:
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Onde `<TENANTID>` e `<PRINCIPALID>` são substituídos por GUIDs. A propriedade tenantid identifica a qual locatário do AAD a identidade pertence. O PrincipalId é um identificador exclusivo para a nova identidade do aplicativo. No AAD, a entidade de serviço tem o mesmo nome que você atribuiu ao serviço de aplicativo ou à instância de Azure Functions.


## <a name="adding-a-user-assigned-identity"></a>Adicionando uma identidade atribuída pelo usuário

A criação de um aplicativo com uma identidade atribuída pelo usuário requer que você crie a identidade e, em seguida, adicione seu identificador de recurso à configuração do aplicativo.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Primeiro, você precisará criar um recurso de identidade atribuído pelo usuário.

1. Crie um recurso de identidade gerenciada atribuído pelo usuário de acordo com [estas instruções](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Crie um aplicativo no portal como faria normalmente. Navegue até ele no Portal.

3. Se estiver usando um aplicativo de funções, navegue até **recursos da plataforma**. Para outros tipos de aplicativos, role para baixo até o grupo **configurações** no painel de navegação esquerdo.

4. Selecione **identidade gerenciada**.

5. Na guia **atribuído pelo usuário** , clique em **Adicionar**.

6. Pesquise a identidade que você criou anteriormente e selecione-a. Clique em **Adicionar**.

    ![Identidade gerenciada no serviço de aplicativo](media/app-service-managed-service-identity/msi-blade-user.png)

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo de Azure Resource Manager

Um modelo de Azure Resource Manager pode ser usado para automatizar a implantação de seus recursos do Azure. Para saber mais sobre como implantar o serviço de aplicativo e as funções, consulte [automatizando a implantação de recursos no serviço de aplicativo](../app-service/deploy-complex-application-predictably.md) e [automatizando a implantação de recursos no Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Qualquer recurso do tipo `Microsoft.Web/sites` pode ser criado com uma identidade, incluindo o bloco a seguir na definição de recurso, substituindo `<RESOURCEID>` pela ID de recurso da identidade desejada:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Um aplicativo pode ter identidades atribuídas pelo sistema e atribuídas pelo usuário ao mesmo tempo. Nesse caso, a propriedade `type` seria `SystemAssigned,UserAssigned`

Adicionar o tipo atribuído pelo usuário e um o informa ao Azure para criar e gerenciar a identidade do seu aplicativo.

Por exemplo, um aplicativo Web pode ser semelhante ao seguinte:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Quando o site é criado, ele tem as seguintes propriedades adicionais:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

Onde `<PRINCIPALID>` e `<CLIENTID>` são substituídos por GUIDs. O PrincipalId é um identificador exclusivo para a identidade que é usada para a administração do AAD. O clientId é um identificador exclusivo para a nova identidade do aplicativo que é usado para especificar qual identidade usar durante chamadas de tempo de execução.


## <a name="obtaining-tokens-for-azure-resources"></a>Obtendo tokens para recursos do Azure

Um aplicativo pode usar sua identidade para obter tokens para outros recursos protegidos pelo AAD, como Azure Key Vault. Esses tokens representam o aplicativo que está acessando o recurso e não qualquer usuário específico do aplicativo. 

> [!IMPORTANT]
> Talvez seja necessário configurar o recurso de destino para permitir o acesso do seu aplicativo. Por exemplo, se você solicitar um token para Key Vault, precisará certificar-se de ter adicionado uma política de acesso que inclui a identidade do aplicativo. Caso contrário, suas chamadas para Key Vault serão rejeitadas, mesmo que incluam o token. Para saber mais sobre quais recursos dão suporte a tokens de Azure Active Directory, consulte [Serviços do Azure que dão suporte à autenticação do Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Há um protocolo REST simples para obter um token no serviço de aplicativo e Azure Functions. Isso pode ser usado para todos os aplicativos e linguagens. Para alguns .NET e Java, o SDK do Azure fornece uma abstração sobre esse protocolo e facilita uma experiência de desenvolvimento local.

### <a name="using-the-rest-protocol"></a>Usando o protocolo REST

Um aplicativo com uma identidade gerenciada tem duas variáveis de ambiente definidas:

- MSI_ENDPOINT-a URL para o serviço de token local.
- MSI_SECRET-um cabeçalho usado para ajudar a mitigar ataques de SSRF (falsificação de solicitação no lado do servidor). O valor é girado pela plataforma.

O **MSI_ENDPOINT** é uma URL local da qual seu aplicativo pode solicitar tokens. Para obter um token para um recurso, faça uma solicitação HTTP GET para esse ponto de extremidade, incluindo os seguintes parâmetros:

> |Nome do parâmetro|No|Descrição|
> |-----|-----|-----|
> |Kit|Consulta|O URI de recurso do AAD do recurso para o qual um token deve ser obtido. Pode ser um dos serviços do [Azure que dão suporte à autenticação do Azure ad ou a](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) qualquer outro URI de recurso.|
> |versão da API|Consulta|A versão da API do token a ser usada. "2017-09-01" é a única versão com suporte no momento.|
> |segredo|Cabeçalho|O valor da variável de ambiente MSI_SECRET. Esse cabeçalho é usado para ajudar a mitigar ataques SSRF (falsificação de solicitação no lado do servidor).|
> |ClientID|Consulta|(Opcional, a menos que seja atribuído pelo usuário) A ID da identidade atribuída pelo usuário a ser usada. Se omitido, a identidade atribuída pelo sistema será usada.|

> [!IMPORTANT]
> Se você estiver tentando obter tokens para identidades atribuídas pelo usuário, deverá incluir a propriedade `clientid`. Caso contrário, o serviço de token tentará obter um token para uma identidade atribuída pelo sistema, que pode ou não existir.

Uma resposta bem-sucedida de 200 OK inclui um corpo JSON com as seguintes propriedades:

> |Nome da propriedade|Descrição|
> |-------------|----------|
> |access_token|O token de acesso solicitado. O serviço Web de chamada pode usar esse token para se autenticar no serviço Web de recebimento.|
> |expires_on|A hora em que o token de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0: 0Z UTC até a hora de expiração. Esse valor é usado para determinar o tempo de vida dos tokens armazenados em cache.|
> |Kit|O URI da ID do aplicativo do serviço Web de recebimento.|
> |token_type|Indica o valor do tipo de token. O único tipo ao qual o Azure AD dá suporte é portador. Para obter mais informações sobre tokens de portador, consulte [a estrutura de autorização do OAuth 2,0: uso de token de portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt).|

Essa resposta é a mesma que a [resposta para a solicitação de token de acesso de serviço a serviço do AAD](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE]
> As variáveis de ambiente são configuradas quando o processo é iniciado pela primeira vez, portanto, depois de habilitar uma identidade gerenciada para seu aplicativo, talvez seja necessário reiniciar o aplicativo ou reimplantar seu código, antes que `MSI_ENDPOINT` e `MSI_SECRET` estejam disponíveis para seu código.

### <a name="rest-protocol-examples"></a>Exemplos de protocolo REST

Uma solicitação de exemplo pode ser semelhante ao seguinte:

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

E uma resposta de exemplo pode ser semelhante ao seguinte:

```
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Exemplos de código

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!TIP]
> Para linguagens .NET, você também pode usar [Microsoft. Azure. Services. AppAuthentication](#asal) em vez de criar essa solicitação por conta própria.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2017-09-01", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource));
    request.Headers.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await _client.SendAsync(request);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=2017-09-01`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import os
import requests

msi_endpoint = os.environ["MSI_ENDPOINT"]
msi_secret = os.environ["MSI_SECRET"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{msi_endpoint}?resource={resource_uri}&api-version=2017-09-01"
    head_msi = {'Secret':msi_secret}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=2017-09-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="asal"></a>Usando a biblioteca Microsoft. Azure. Services. AppAuthentication para .NET

Para aplicativos e funções .NET, a maneira mais simples de trabalhar com uma identidade gerenciada é por meio do pacote Microsoft. Azure. Services. AppAuthentication. Essa biblioteca também permitirá que você teste seu código localmente em seu computador de desenvolvimento, usando sua conta de usuário do Visual Studio, o [CLI do Azure](/cli/azure)ou Active Directory autenticação integrada. Para obter mais informações sobre opções de desenvolvimento local com essa biblioteca, consulte a [Referência de Microsoft. Azure. Services. AppAuthentication]. Esta seção mostra como começar a usar a biblioteca em seu código.

1. Adicione referências ao [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e quaisquer outros pacotes NuGet necessários ao seu aplicativo. O exemplo abaixo também usa [Microsoft. Azure. keyvault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Adicione o código a seguir ao seu aplicativo, modificando para direcionar o recurso correto. Este exemplo mostra duas maneiras de trabalhar com Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Para saber mais sobre o Microsoft. Azure. Services. AppAuthentication e as operações que ele expõe, consulte a [Referência de Microsoft. Azure. Services. AppAuthentication] e o [serviço de aplicativo e o keyvault com o exemplo do MSI .net](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Usando o SDK do Azure para Java

Para aplicativos e funções Java, a maneira mais simples de trabalhar com uma identidade gerenciada é por meio do [SDK do Azure para Java](https://github.com/Azure/azure-sdk-for-java). Esta seção mostra como começar a usar a biblioteca em seu código.

1. Adicione uma referência à [biblioteca do SDK do Azure](https://mvnrepository.com/artifact/com.microsoft.azure/azure). Para projetos Maven, você pode adicionar esse trecho à seção `dependencies` do arquivo POM do projeto:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Use o objeto `AppServiceMSICredentials` para autenticação. Este exemplo mostra como esse mecanismo pode ser usado para trabalhar com Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove"></a>Removendo uma identidade

Uma identidade atribuída pelo sistema pode ser removida desabilitando o recurso usando o portal, o PowerShell ou a CLI da mesma maneira que foi criado. As identidades atribuídas pelo usuário podem ser removidas individualmente. Para remover todas as identidades, no protocolo de modelo REST/ARM, isso é feito definindo o tipo como "None":

```json
"identity": {
    "type": "None"
}
```

A remoção de uma identidade atribuída pelo sistema dessa maneira também a excluirá do AAD. As identidades atribuídas pelo sistema também são removidas automaticamente do AAD quando o recurso de aplicativo é excluído.

> [!NOTE]
> Também há uma configuração de aplicativo que pode ser definida, WEBSITE_DISABLE_MSI, que apenas desabilita o serviço de token local. No entanto, ele deixa a identidade em vigor e as ferramentas ainda mostrarão a identidade gerenciada como "ativada" ou "habilitada". Como resultado, o uso dessa configuração não é recomendado.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Acessar o banco de dados SQL com segurança usando uma identidade gerenciada](app-service-web-tutorial-connect-msi.md)

[Referência de Microsoft. Azure. Services. AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
