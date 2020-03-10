---
title: Identidades geridas
description: Saiba como funcionam identidades geridas no Azure App Service e nas Funções Azure, como configurar uma identidade gerida e gerar um símbolo para um recurso de back-end.
author: mattchenderson
ms.topic: article
ms.date: 03/04/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 6e3169f2bfcba0a02af1490f875cbab8a14d02f6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365533"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure

> [!Important] 
> As identidades geridas para o Serviço de Aplicações e funções azure não se comportarão como esperado se a sua aplicação for migrada para subscrições/inquilinos. A aplicação terá de obter uma nova identidade, o que pode ser feito desativando e reativando a funcionalidade. Veja [a remoção de uma identidade](#remove) abaixo. Os recursos a jusante também terão de ter políticas de acesso atualizadas para utilizar a nova identidade.

Este tópico mostra como criar uma identidade gerida para aplicações do App Service e Funções Azure e como usá-lo para aceder a outros recursos. Uma identidade gerida do Azure Ative Directory (AAD) permite que a sua aplicação aceda facilmente a outros recursos protegidos pela AAD, como o Azure Key Vault. A identidade é gerida pela plataforma Azure e não o obriga a fornecer ou rodar quaisquer segredos. Para saber mais sobre identidades geridas em AAD, consulte [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md).

A sua aplicação pode ser concedida a dois tipos de identidades: 
- Uma **identidade atribuída** ao sistema está ligada à sua aplicação e é eliminada se a sua aplicação for eliminada. Uma aplicação só pode ter uma identidade atribuída ao sistema.
- Uma **identidade atribuída ao utilizador** é um recurso Azure autónomo que pode ser atribuído à sua aplicação. Uma aplicação pode ter várias identidades atribuídas ao utilizador.

## <a name="add-a-system-assigned-identity"></a>Adicione uma identidade atribuída ao sistema

A criação de uma aplicação com uma identidade atribuída ao sistema requer que seja definida uma propriedade adicional na aplicação.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para configurar uma identidade gerida no portal, primeiro criará uma aplicação normalmente e, em seguida, ativará a funcionalidade.

1. Crie uma aplicação no portal como normalmente faria. Navegue até ele no portal.

2. Se utilizar uma aplicação de função, navegue para **as funcionalidades da Plataforma**. Para outros tipos de aplicações, desloque-se para o grupo **Definições** na navegação à esquerda.

3. Selecione **Identidade**.

4. Dentro do separador designado pelo **Sistema,** mude o **Estado** para **o ligado**. Clique em **Guardar**.

    ![Identidade gerida no Serviço de Aplicações](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)

### <a name="using-the-azure-cli"></a>Com a CLI do Azure

Para configurar uma identidade gerida utilizando o Azure CLI, terá de utilizar o comando `az webapp identity assign` contra uma aplicação existente. Tem três opções para executar os exemplos nesta secção:

- Utilize a Casca de [Nuvem Azure](../cloud-shell/overview.md) do portal Azure.
- Utilize a casca de nuvem azure incorporada através do botão "Try It", localizado no canto superior direito de cada bloco de código abaixo.
- [Instale a versão mais recente do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 ou mais tarde) se preferir utilizar uma consola CLI local. 

Os seguintes passos irão acompanhá-lo através da criação de uma aplicação web e atribuindo-lhe uma identidade usando o CLI:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta associada à subscrição Azure sob a qual gostaria de implementar a aplicação:

    ```azurecli-interactive
    az login
    ```
2. Crie uma aplicação web utilizando o CLI. Para mais exemplos de como utilizar o CLI com o Serviço de Aplicações, consulte [as amostras CLI do Serviço de Aplicações:](../app-service/samples-cli.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Executar o comando `identity assign` para criar a identidade para esta aplicação:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os seguintes passos irão acompanhá-lo através da criação de uma aplicação web e atribuindo-lhe uma identidade usando o Azure PowerShell:

1. Se necessário, instale o Azure PowerShell utilizando as instruções encontradas no [guia Azure PowerShell](/powershell/azure/overview)e, em seguida, execute `Login-AzAccount` para criar uma ligação com o Azure.

2. Crie uma aplicação web utilizando o Azure PowerShell. Para mais exemplos de como utilizar o Azure PowerShell com o Serviço de Aplicações, consulte [as amostras do PowerShell do Serviço de Aplicações:](../app-service/samples-powershell.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Executar o comando `Set-AzWebApp -AssignIdentity` para criar a identidade para esta aplicação:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo de Gestor de Recursos Azure

Um modelo de Gestor de Recursos Azure pode ser usado para automatizar a implantação dos seus recursos Azure. Para saber mais sobre a implementação para o Serviço e Funções de Aplicações, consulte a [automatização](../app-service/deploy-complex-application-predictably.md) da implantação de recursos no Serviço de Aplicações e automatizando a implantação de [recursos em Funções Azure](../azure-functions/functions-infrastructure-as-code.md).

Qualquer recurso de tipo `Microsoft.Web/sites` pode ser criado com uma identidade, incluindo a seguinte propriedade na definição de recursos:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Uma aplicação pode ter identidades atribuídas ao sistema e atribuídas ao utilizador ao mesmo tempo. Neste caso, a propriedade `type` seria `SystemAssigned,UserAssigned`

Adicionar o tipo designado pelo sistema diz ao Azure para criar e gerir a identidade para a sua aplicação.

Por exemplo, uma aplicação web pode parecer a seguinte:
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

Quando o site é criado, tem as seguintes propriedades adicionais:
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

A propriedade tenantId identifica a que inquilino a AAD a identidade pertence. O principalid é um identificador único para a nova identidade da aplicação. Dentro da AAD, o diretor de serviço tem o mesmo nome que deu ao seu Serviço de Aplicações ou à instância De Funções Azure.


## <a name="add-a-user-assigned-identity"></a>Adicione uma identidade atribuída ao utilizador

Criar uma aplicação com uma identidade atribuída ao utilizador requer que crie a identidade e, em seguida, adicione o seu identificador de recursos à sua app config.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Primeiro, terá de criar um recurso de identidade atribuído ao utilizador.

1. Criar um recurso de identidade gerido atribuído ao utilizador de acordo com [estas instruções](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Crie uma aplicação no portal como normalmente faria. Navegue até ele no portal.

3. Se utilizar uma aplicação de função, navegue para **as funcionalidades da Plataforma**. Para outros tipos de aplicações, desloque-se para o grupo **Definições** na navegação à esquerda.

4. Selecione **Identidade**.

5. Dentro do separador designado pelo **Utilizador,** clique em **Adicionar**.

6. Procure a identidade que criou anteriormente e selecione-a. Clique em **Adicionar**.

    ![Identidade gerida no Serviço de Aplicações](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo de Gestor de Recursos Azure

Um modelo de Gestor de Recursos Azure pode ser usado para automatizar a implantação dos seus recursos Azure. Para saber mais sobre a implementação para o Serviço e Funções de Aplicações, consulte a [automatização](../app-service/deploy-complex-application-predictably.md) da implantação de recursos no Serviço de Aplicações e automatizando a implantação de [recursos em Funções Azure](../azure-functions/functions-infrastructure-as-code.md).

Qualquer recurso do tipo `Microsoft.Web/sites` pode ser criado com uma identidade, incluindo o seguinte bloco na definição de recursos, substituindo `<RESOURCEID>` pelo iD de recurso da identidade desejada:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Uma aplicação pode ter identidades atribuídas ao sistema e atribuídas ao utilizador ao mesmo tempo. Neste caso, a propriedade `type` seria `SystemAssigned,UserAssigned`

A adição do tipo designado pelo utilizador diz ao Azure para utilizar a identidade atribuída ao utilizador especificada para a sua aplicação.

Por exemplo, uma aplicação web pode parecer a seguinte:
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

Quando o site é criado, tem as seguintes propriedades adicionais:
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

O principalid é um identificador único para a identidade que é usada para a administração aAD. O clienteId é um identificador único para a nova identidade da aplicação que é usada para especificar que identidade usar durante as chamadas de tempo de execução.


## <a name="obtain-tokens-for-azure-resources"></a>Obtenha fichas para recursos Azure

Uma aplicação pode usar a sua identidade gerida para obter fichas para aceder a outros recursos protegidos pela AAD, como o Azure Key Vault. Estes tokens representam a aplicação de acesso ao recurso, e nenhum utilizador específico da aplicação. 

Poderá ser necessário configurar o recurso-alvo para permitir o acesso a partir da sua aplicação. Por exemplo, se solicitar um sinal para aceder ao Key Vault, tem de se certificar de que adicionou uma política de acesso que inclua a identidade da sua aplicação. Caso contrário, as suas chamadas para o Cofre Chave serão rejeitadas, mesmo que incluam o símbolo. Para saber mais sobre quais os recursos suportam os tokens do Azure Ative Directory, consulte [os serviços azure que suportam a autenticação Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

> [!IMPORTANT]
> Os serviços de back-end para identidades geridas mantêm uma cache por recurso URI por cerca de 8 horas. Se atualizar a política de acesso de um determinado recurso-alvo e recuperar imediatamente um símbolo desse recurso, poderá continuar a obter um símbolo em cache com permissões desatualizadas até que esse token expire. Não há como forçar uma atualização simbólica.

Existe um simples protocolo REST para obter um símbolo em Funções de Serviço de Aplicações e Azure. Isto pode ser usado para todas as aplicações e línguas. Para .NET e Java, o Azure SDK fornece uma abstração sobre este protocolo e facilita uma experiência de desenvolvimento local.

### <a name="using-the-rest-protocol"></a>Utilização do protocolo REST

Uma aplicação com uma identidade gerida tem duas variáveis ambientais definidas:

- MSI_ENDPOINT - o URL para o serviço de fichas local.
- MSI_SECRET - um cabeçalho usado para ajudar a mitigar os ataques de falsificação do lado do servidor (SSRF). O valor é rodado pela plataforma.

O **MSI_ENDPOINT** é um URL local a partir do qual a sua aplicação pode solicitar fichas. Para obter um símbolo para um recurso, faça um pedido HTTP GET para este ponto final, incluindo os seguintes parâmetros:

> |Nome do parâmetro|Em|Descrição|
> |-----|-----|-----|
> |resource|Consulta|O recurso AAD URI do recurso para o qual deve ser obtido um símbolo. Este pode ser um dos [serviços Azure que suportam a autenticação Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) ou qualquer outro recurso URI.|
> |api-version|Consulta|A versão da API simbólica a ser utilizada. "2017-09-01" é atualmente a única versão suportada.|
> |secret|Cabeçalho|O valor da variável ambiente MSI_SECRET. Este cabeçalho é usado para ajudar a mitigar os ataques de falsificação de pedido do lado do servidor (SSRF).|
> |clientid|Consulta|(Opcional, a menos que para o utilizador atribuído) A identificação da identidade atribuída ao utilizador a utilizar. Se omitida, a identidade atribuída ao sistema é utilizada.|

> [!IMPORTANT]
> Se estiver a tentar obter fichas para identidades atribuídas ao utilizador, deve incluir a propriedade `clientid`. Caso contrário, o serviço simbólico tentará obter um símbolo para uma identidade atribuída ao sistema, que pode ou não existir.

Uma resposta bem sucedida de 200 OK inclui um corpo JSON com as seguintes propriedades:

> |Nome da propriedade|Descrição|
> |-------------|----------|
> |access_token|O sinal de acesso solicitado. O serviço web de chamada pode usar este símbolo para autenticar o serviço web recetor.|
> |expires_on|O tempo em que o sinal de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até ao tempo de validade. Este valor é usado para determinar a vida útil de fichas em cache.|
> |resource|O App ID URI do serviço web recetor.|
> |token_type|Indica o valor do tipo simbólico. O único tipo que a Azure AD suporta é o Bearer. Para obter mais informações sobre fichas ao portador, consulte o Quadro de [Autorização OAuth 2.0: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt).|

Esta resposta é a mesma que a resposta para o pedido de acesso ao [serviço AAD.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md#get-a-token)

> [!NOTE]
> As variáveis ambientais são configuradas quando o processo começa pela primeira vez, por isso, depois de permitir uma identidade gerida para a sua aplicação, poderá ter de reiniciar a sua aplicação ou recolocar o seu código, antes de `MSI_ENDPOINT` e `MSI_SECRET` estarem disponíveis para o seu código.

### <a name="rest-protocol-examples"></a>Exemplos de protocolo REST

Um pedido de exemplo pode parecer o seguinte:

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

E uma resposta de amostra pode parecer a seguinte:

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

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> Para idiomas .NET, também pode utilizar [microsoft.Azure.Services.AppAuthentication](#asal) em vez de elaborar este pedido por si mesmo.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[python](#tab/python)

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=2017-09-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="asal"></a>Utilização da microsoft.Azure.Services.AppAuthentication library for .NET

Para aplicações e funções .NET, a forma mais simples de trabalhar com uma identidade gerida é através do pacote Microsoft.Azure.Services.Services.AppAuthentication. Esta biblioteca também lhe permitirá testar o seu código localmente na sua máquina de desenvolvimento, utilizando a sua conta de utilizador do Visual Studio, do [Azure CLI](/cli/azure)ou da Autenticação Integrada do Diretório Ativo. Para mais informações sobre as opções de desenvolvimento local com esta biblioteca, consulte a [Microsoft.Azure.Services.AppAuthentication reference]. Esta secção mostra-lhe como começar com a biblioteca no seu código.

1. Adicione referências ao [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e quaisquer outros pacotes NuGet necessários à sua aplicação. O exemplo abaixo também utiliza [microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Adicione o seguinte código à sua aplicação, modificando para direcionar o recurso correto. Este exemplo mostra duas maneiras de trabalhar com o Cofre chave Azure:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Para saber mais sobre microsoft.Azure.Services.AppAuthentication e as operações que expõe, consulte a [Microsoft.Azure.Services.AppAuthentication reference] e o Serviço de [Aplicações e KeyVault com amostra MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Utilização do SDK Azure para Java

Para aplicações e funções java, a forma mais simples de trabalhar com uma identidade gerida é através do [Azure SDK para Java.](https://github.com/Azure/azure-sdk-for-java) Esta secção mostra-lhe como começar com a biblioteca no seu código.

1. Adicione uma referência à [biblioteca Azure SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure). Para os projetos Maven, você pode adicionar este corte à `dependencies` secção do ficheiro POM do projeto:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Utilize o objeto `AppServiceMSICredentials` para autenticação. Este exemplo mostra como este mecanismo pode ser usado para trabalhar com o Cofre chave Azure:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove"></a>Remover uma identidade

Uma identidade atribuída ao sistema pode ser removida desativando a funcionalidade utilizando o portal, PowerShell ou CLI da mesma forma que foi criada. As identidades atribuídas ao utilizador podem ser removidas individualmente. Para remover todas as identidades, desloque o tipo para "Nenhuma" no [modelo ARM:](#using-an-azure-resource-manager-template)

```json
"identity": {
    "type": "None"
}
```

A remoção de uma identidade atribuída ao sistema desta forma também a eliminará da AAD. As identidades atribuídas ao sistema também são automaticamente removidas do AAD quando o recurso da aplicação é eliminado.

> [!NOTE]
> Há também uma definição de aplicação que pode ser definida, WEBSITE_DISABLE_MSI, que apenas desativa o serviço de fichas local. No entanto, deixa a identidade no lugar, e a ferramenta ainda mostrará a identidade gerida como "on" ou "habilitada". Como resultado, não é recomendada a utilização desta definição.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Access SQL Database de forma segura usando uma identidade gerida](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication reference]: https://go.microsoft.com/fwlink/p/?linkid=862452
