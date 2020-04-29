---
title: Identidades geridas
description: Saiba como funcionam identidades geridas no Azure App Service e nas Funções Azure, como configurar uma identidade gerida e gerar um símbolo para um recurso de back-end.
author: mattchenderson
ms.topic: article
ms.date: 04/14/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 875d2bbebdfa95c6d180979399d876eb2afc01b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81392531"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure

> [!Important] 
> As identidades geridas para o Serviço de Aplicações e funções azure não se comportarão como esperado se a sua aplicação for migrada para subscrições/inquilinos. A aplicação terá de obter uma nova identidade, o que pode ser feito desativando e reativando a funcionalidade. Veja [a remoção de uma identidade](#remove) abaixo. Os recursos a jusante também terão de ter políticas de acesso atualizadas para utilizar a nova identidade.

Este tópico mostra como criar uma identidade gerida para aplicações do App Service e Funções Azure e como usá-lo para aceder a outros recursos. Uma identidade gerida do Azure Ative Directory (Azure AD) permite que a sua aplicação aceda facilmente a outros recursos protegidos por AD Azure, como o Azure Key Vault. A identidade é gerida pela plataforma Azure e não o obriga a fornecer ou rodar quaisquer segredos. Para saber mais sobre identidades geridas em Azure AD, consulte [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md).

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

Para configurar uma identidade gerida utilizando o Azure CLI, terá de utilizar o `az webapp identity assign` comando contra uma aplicação existente. Tem três opções para executar os exemplos nesta secção:

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

3. Executar `identity assign` o comando para criar a identidade para esta aplicação:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os seguintes passos irão acompanhá-lo através da criação de uma aplicação web e atribuindo-lhe uma identidade usando o Azure PowerShell:

1. Se necessário, instale o Azure PowerShell utilizando as instruções encontradas `Login-AzAccount` no [guia Azure PowerShell](/powershell/azure/overview)e, em seguida, corra para criar uma ligação com o Azure.

2. Crie uma aplicação web utilizando o Azure PowerShell. Para mais exemplos de como utilizar o Azure PowerShell com o Serviço de Aplicações, consulte [as amostras do PowerShell do Serviço de Aplicações:](../app-service/samples-powershell.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Executar `Set-AzWebApp -AssignIdentity` o comando para criar a identidade para esta aplicação:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo de Gestor de Recursos Azure

Um modelo de Gestor de Recursos Azure pode ser usado para automatizar a implantação dos seus recursos Azure. Para saber mais sobre a implementação para o Serviço e Funções de Aplicações, consulte a [automatização](../app-service/deploy-complex-application-predictably.md) da implantação de recursos no Serviço de Aplicações e automatizando a implantação de [recursos em Funções Azure](../azure-functions/functions-infrastructure-as-code.md).

Qualquer recurso `Microsoft.Web/sites` de tipo pode ser criado com uma identidade, incluindo a seguinte propriedade na definição de recurso:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Uma aplicação pode ter identidades atribuídas ao sistema e atribuídas ao utilizador ao mesmo tempo. Neste caso, `type` a propriedade seria`SystemAssigned,UserAssigned`

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

A propriedade tenantId identifica a que inquilino azure a identidade pertence. O principalid é um identificador único para a nova identidade da aplicação. Dentro da AD Azure, o diretor de serviço tem o mesmo nome que deu ao seu Serviço de Aplicações ou à instância de Funções Azure.

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

Qualquer recurso `Microsoft.Web/sites` de tipo pode ser criado com uma identidade, incluindo o seguinte bloco na definição de recursos, substituindo `<RESOURCEID>` pela identificação do recurso da identidade desejada:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Uma aplicação pode ter identidades atribuídas ao sistema e atribuídas ao utilizador ao mesmo tempo. Neste caso, `type` a propriedade seria`SystemAssigned,UserAssigned`

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

O principalid é um identificador único para a identidade que é usada para a administração da AD Azure. O clienteId é um identificador único para a nova identidade da aplicação que é usada para especificar que identidade usar durante as chamadas de tempo de execução.

## <a name="obtain-tokens-for-azure-resources"></a>Obtenha fichas para recursos Azure

Uma aplicação pode usar a sua identidade gerida para obter fichas para aceder a outros recursos protegidos pela Azure AD, como o Azure Key Vault. Estes tokens representam a aplicação de acesso ao recurso, e nenhum utilizador específico da aplicação. 

Poderá ser necessário configurar o recurso-alvo para permitir o acesso a partir da sua aplicação. Por exemplo, se solicitar um sinal para aceder ao Key Vault, tem de se certificar de que adicionou uma política de acesso que inclua a identidade da sua aplicação. Caso contrário, as suas chamadas para o Cofre Chave serão rejeitadas, mesmo que incluam o símbolo. Para saber mais sobre quais os recursos suportam os tokens do Azure Ative Directory, consulte [os serviços azure que suportam a autenticação Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

> [!IMPORTANT]
> Os serviços de back-end para identidades geridas mantêm uma cache por recurso URI por cerca de 8 horas. Se atualizar a política de acesso de um determinado recurso-alvo e recuperar imediatamente um símbolo desse recurso, poderá continuar a obter um símbolo em cache com permissões desatualizadas até que esse token expire. Não há como forçar uma atualização simbólica.

Existe um simples protocolo REST para obter um símbolo em Funções de Serviço de Aplicações e Azure. Isto pode ser usado para todas as aplicações e línguas. Para .NET e Java, o Azure SDK fornece uma abstração sobre este protocolo e facilita uma experiência de desenvolvimento local.

### <a name="using-the-rest-protocol"></a>Utilização do protocolo REST

Uma aplicação com uma identidade gerida tem duas variáveis ambientais definidas:

- IDENTITY_ENDPOINT - o URL para o serviço de fichas local.
- IDENTITY_HEADER - um cabeçalho usado para ajudar a mitigar os ataques de falsificação do lado do servidor (SSRF). O valor é rodado pela plataforma.

O **IDENTITY_ENDPOINT** é um URL local a partir do qual a sua aplicação pode solicitar fichas. Para obter um símbolo para um recurso, faça um pedido HTTP GET para este ponto final, incluindo os seguintes parâmetros:

> | Nome do parâmetro    | Entrada     | Descrição                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | recurso          | Consulta  | O recurso Azure AD URI do recurso para o qual deve ser obtido um símbolo. Este pode ser um dos [serviços Azure que suportam a autenticação Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) ou qualquer outro recurso URI.    |
> | api-version       | Consulta  | A versão da API simbólica a ser utilizada. Por favor, use "2019-08-01" ou mais tarde.                                                                                                                                                                                                                                                                 |
> | Cabeçalho de X-IDENTIDADE | Cabeçalho | O valor da variável ambiente IDENTITY_HEADER. Este cabeçalho é usado para ajudar a mitigar os ataques de falsificação de pedido do lado do servidor (SSRF).                                                                                                                                                                                                    |
> | client_id         | Consulta  | (Opcional) A identificação do cliente da identidade atribuída ao utilizador a utilizar. Não pode ser utilizado `principal_id`num `mi_res_id`pedido `object_id`que inclua, ou . Se todos os`client_id`parâmetros de `mi_res_id`IDENTIFICAÇÃO forem `principal_id` `object_id`omitidos, a identidade atribuída ao sistema é utilizada.                                             |
> | principal_id      | Consulta  | (Opcional) A identificação principal da identidade atribuída ao utilizador a utilizar. `object_id`é um pseudónimo que pode ser usado em vez disso. Não pode ser utilizado num pedido que inclua client_id, mi_res_id ou object_id. Se todos os`client_id`parâmetros de `mi_res_id`IDENTIFICAÇÃO forem `principal_id` `object_id`omitidos, a identidade atribuída ao sistema é utilizada. |
> | mi_res_id         | Consulta  | (Opcional) A identificação de recurso Azure da identidade atribuída ao utilizador a utilizar. Não pode ser utilizado `principal_id`num `client_id`pedido `object_id`que inclua, ou . Se todos os`client_id`parâmetros de `mi_res_id`IDENTIFICAÇÃO forem `principal_id` `object_id`omitidos, a identidade atribuída ao sistema é utilizada.                                      |

> [!IMPORTANT]
> Se estiver a tentar obter fichas para identidades atribuídas ao utilizador, deve incluir uma das propriedades opcionais. Caso contrário, o serviço simbólico tentará obter um símbolo para uma identidade atribuída ao sistema, que pode ou não existir.

Uma resposta bem sucedida de 200 OK inclui um corpo JSON com as seguintes propriedades:

> | Nome da propriedade | Descrição                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | O sinal de acesso solicitado. O serviço web de chamada pode usar este símbolo para autenticar o serviço web recetor.                                                                                                                               |
> | client_id     | A identificação do cliente da identidade que foi usada.                                                                                                                                                                                                       |
> | expires_on    | O tempo de espera quando o sinal de acesso expira. A data é representada como o número de segundos de "1970-01-01T0:0:0Z UTC" (corresponde à reivindicação do `exp` token).                                                                                |
> | not_before    | O tempo de espera quando o token de acesso entra em vigor, e pode ser aceite. A data é representada como o número de segundos de "1970-01-01T0:0:0Z UTC" (corresponde à reivindicação do `nbf` token).                                                      |
> | recurso      | O recurso para o sinal de acesso `resource` foi solicitado, que corresponde ao parâmetro de corda de consulta do pedido.                                                                                                                               |
> | token_type    | Indica o valor do tipo simbólico. O único tipo que a Azure AD suporta é o FBearer. Para obter mais informações sobre fichas ao portador, consulte o Quadro de [Autorização OAuth 2.0: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |

Esta resposta é a mesma que a resposta para o pedido de acesso ao serviço da [AD Azure.](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)

> [!NOTE]
> Uma versão mais antiga deste protocolo, utilizando a versão API "2017-09-01", utilizou o `secret` cabeçalho em vez de `X-IDENTITY-HEADER` e só aceitou a `clientid` propriedade para a designada pelo utilizador. Também devolveu `expires_on` o formato timestamp. MSI_ENDPOINT pode ser usado como pseudónimo para IDENTITY_ENDPOINT, e MSI_SECRET pode ser usado como pseudónimo para IDENTITY_HEADER.

### <a name="rest-protocol-examples"></a>Exemplos de protocolo REST

Um pedido de exemplo pode parecer o seguinte:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

E uma resposta de amostra pode parecer a seguinte:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
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
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Utilização da microsoft.Azure.Services.AppAuthentication library for .NET

Para aplicações e funções .NET, a forma mais simples de trabalhar com uma identidade gerida é através do pacote Microsoft.Azure.Services.Services.AppAuthentication. Esta biblioteca também lhe permitirá testar o seu código localmente na sua máquina de desenvolvimento, utilizando a sua conta de utilizador do Visual Studio, do [Azure CLI](/cli/azure)ou da Autenticação Integrada do Diretório Ativo. Para mais informações sobre as opções de desenvolvimento local com esta biblioteca, consulte a [referência Microsoft.Azure.Services.AppAuthentication]. Esta secção mostra-lhe como começar com a biblioteca no seu código.

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

Para saber mais sobre microsoft.Azure.Services.AppAuthentication e as operações que expõe, consulte a [referência Microsoft.Azure.Services.AppAuthentication] e o Serviço de [Aplicações e KeyVault com amostra MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Utilização do SDK Azure para Java

Para aplicações e funções java, a forma mais simples de trabalhar com uma identidade gerida é através do [Azure SDK para Java.](https://github.com/Azure/azure-sdk-for-java) Esta secção mostra-lhe como começar com a biblioteca no seu código.

1. Adicione uma referência à [biblioteca Azure SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure). Para os projetos Maven, você pode `dependencies` adicionar este corte à secção do arquivo POM do projeto:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Utilize `AppServiceMSICredentials` o objeto para autenticação. Este exemplo mostra como este mecanismo pode ser usado para trabalhar com o Cofre chave Azure:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Remover uma identidade

Uma identidade atribuída ao sistema pode ser removida desativando a funcionalidade utilizando o portal, PowerShell ou CLI da mesma forma que foi criada. As identidades atribuídas ao utilizador podem ser removidas individualmente. Para remover todas as identidades, desloque o tipo para "Nenhuma" no [modelo ARM:](#using-an-azure-resource-manager-template)

```json
"identity": {
    "type": "None"
}
```

A remoção de uma identidade atribuída ao sistema desta forma também a eliminará da AD Azure. As identidades atribuídas ao sistema também são automaticamente removidas do Azure AD quando o recurso da aplicação é eliminado.

> [!NOTE]
> Há também uma definição de aplicação que pode ser definida, WEBSITE_DISABLE_MSI, que apenas desativa o serviço de fichas local. No entanto, deixa a identidade no lugar, e a ferramenta ainda mostrará a identidade gerida como "on" ou "habilitada". Como resultado, não é recomendada a utilização desta definição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Access SQL Database de forma segura usando uma identidade gerida](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication reference]: https://go.microsoft.com/fwlink/p/?linkid=862452
