---
title: Identidades geridas
description: Saiba como as identidades geridas funcionam no Azure App Service e nas Funções Azure, como configurar uma identidade gerida e gerar um símbolo para um recurso de back-end.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: fa99920c8e9d8cd532bb6230d6a337a038ee3e31
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929332"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Como utilizar identidades geridas para o Serviço de Aplicações e Funções Azure

Este tópico mostra-lhe como criar uma identidade gerida para aplicações de App Service e Azure Functions e como usá-lo para aceder a outros recursos. 

> [!Important] 
> As identidades geridas para o Serviço de Aplicações e Funções Azure não se comportarão como esperado se a sua aplicação for migrada através de subscrições/inquilinos. A aplicação necessita de obter uma nova identidade, que é feita desativando e reativando a funcionalidade. Veja [a remoção de uma identidade](#remove) abaixo. Os recursos a jusante também precisam de ter políticas de acesso atualizadas para utilizar a nova identidade.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>Adicionar uma identidade atribuída ao sistema

A criação de uma app com uma identidade atribuída ao sistema requer uma propriedade adicional a ser definida na aplicação.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para configurar uma identidade gerida no portal, irá primeiro criar uma aplicação normal e depois ativar a funcionalidade.

1. Crie uma aplicação no portal como normalmente faria. Navegue até ele no portal.

2. Se utilizar uma aplicação de função, navegue para **as funcionalidades da Plataforma.** Para outros tipos de **aplicativos,** desloque-se até ao grupo Definições na navegação à esquerda.

3. Selecione **identidade**.

4. Dentro do separador **Designado sistema,** **altere o Estado** para **ligar**. Clique em **Guardar**.

    ![Screenshot que mostra onde mudar o Estado para ligar e, em seguida, selecionar Save.](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Para encontrar a identidade gerida para a sua aplicação web ou app slot no portal Azure, sob **aplicações da Enterprise,** procure na secção **de definições** do Utilizador. Normalmente, o nome da ranhura é semelhante a `<app name>/slots/<slot name>` .


### <a name="using-the-azure-cli"></a>Com a CLI do Azure

Para configurar uma identidade gerida utilizando o CLI Azure, terá de utilizar o `az webapp identity assign` comando contra uma aplicação existente. Tem três opções para executar os exemplos nesta secção:

- Utilize [a Azure Cloud Shell](../cloud-shell/overview.md) a partir do portal Azure.
- Utilize o Azure Cloud Shell incorporado através do botão "Try It", localizado no canto superior direito de cada bloco de código abaixo.
- [Instale a versão mais recente do Azure CLI](/cli/azure/install-azure-cli) (2.0.31 ou posterior) se preferir utilizar uma consola CLI local. 

Os seguintes passos irão acompanhá-lo através da criação de uma aplicação web e atribuindo-lhe uma identidade usando o CLI:

1. Se estiver a utilizar a CLI do Azure numa consola local, primeiro inicie sessão no Azure com [az login](/cli/azure/reference-index#az-login). Utilize uma conta associada à subscrição Azure sob a qual pretende implementar a aplicação:

    ```azurecli-interactive
    az login
    ```

2. Crie uma aplicação web utilizando o CLI. Para obter mais exemplos de como utilizar o CLI com o Serviço de Aplicações, consulte [as amostras do Serviço de Aplicações CLI](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Executar o `identity assign` comando para criar a identidade para esta aplicação:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os seguintes passos irão acompanhá-lo através da criação de uma app e atribuindo-lhe uma identidade usando a Azure PowerShell. As instruções para a criação de uma aplicação web e de uma aplicação de função são diferentes.

#### <a name="using-azure-powershell-for-a-web-app"></a>Usando a Azure PowerShell para uma aplicação web

1. Se necessário, instale o Azure PowerShell utilizando as instruções encontradas no [guia Azure PowerShell](/powershell/azure/)e, em seguida, corra `Login-AzAccount` para criar uma ligação com a Azure.

2. Crie uma aplicação web utilizando a Azure PowerShell. Para obter mais exemplos de como utilizar o Azure PowerShell com o Serviço de Aplicações, consulte [as amostras powerShell do Serviço de Aplicações:](../app-service/samples-powershell.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. Executar o `Set-AzWebApp -AssignIdentity` comando para criar a identidade para esta aplicação:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>Usando a Azure PowerShell para uma aplicação de função

1. Se necessário, instale o Azure PowerShell utilizando as instruções encontradas no [guia Azure PowerShell](/powershell/azure/)e, em seguida, corra `Login-AzAccount` para criar uma ligação com a Azure.

2. Crie uma aplicação de função utilizando o Azure PowerShell. Para obter mais exemplos de como utilizar a Azure PowerShell com Funções Azure, consulte a [referência Az.Functions](/powershell/module/az.functions/?view=azps-4.1.0#functions):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

Também pode atualizar uma aplicação de função existente `Update-AzFunctionApp` utilizando.

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo de gestor de recursos Azure

Um modelo de Gestor de Recursos Azure pode ser usado para automatizar a implementação dos seus recursos Azure. Para saber mais sobre a implementação do Serviço de Aplicações e Funções, consulte [automatizar a implementação de recursos no Serviço de Aplicações](../app-service/deploy-complex-application-predictably.md) e [Automatizar a implementação de recursos em Funções Azure](../azure-functions/functions-infrastructure-as-code.md).

Qualquer recurso de tipo `Microsoft.Web/sites` pode ser criado com uma identidade, incluindo a seguinte propriedade na definição de recurso:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Uma aplicação pode ter identidades atribuídas ao sistema e atribuídas ao mesmo tempo. Neste caso, a `type` propriedade seria `SystemAssigned,UserAssigned`

A adição do tipo atribuído ao sistema diz ao Azure para criar e gerir a identidade para a sua aplicação.

Por exemplo, uma aplicação web pode parecer o seguinte:

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

A propriedade do inquilinoId identifica a que Azure AD inquilino a identidade pertence. O directorid é um identificador único para a nova identidade da aplicação. Dentro da Azure AD, o responsável pelo serviço tem o mesmo nome que deu ao seu Serviço de Aplicações ou à instância Azure Functions.

Se precisar de fazer referência a estas propriedades numa fase posterior do modelo, pode fazê-lo através da [ `reference()` função do modelo](../azure-resource-manager/templates/template-functions-resource.md#reference) com a `'Full'` bandeira, como neste exemplo:

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>Adicionar uma identidade atribuída ao utilizador

A criação de uma aplicação com uma identidade atribuída ao utilizador requer que crie a identidade e adicione o seu identificador de recursos à sua app config.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Primeiro, terá de criar um recurso de identidade atribuído ao utilizador.

1. Criar um recurso de identidade gerido atribuído pelo utilizador de acordo com [estas instruções](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Crie uma aplicação no portal como normalmente faria. Navegue até ele no portal.

3. Se utilizar uma aplicação de função, navegue para **as funcionalidades da Plataforma.** Para outros tipos de **aplicativos,** desloque-se até ao grupo Definições na navegação à esquerda.

4. Selecione **identidade**.

5. Dentro do **separador Utilizador atribuído,** clique em **Adicionar**.

6. Procure a identidade que criou anteriormente e selecione-a. Clique em **Adicionar**.

    ![Identidade gerida no Serviço de Aplicações](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os seguintes passos irão acompanhá-lo através da criação de uma app e atribuindo-lhe uma identidade usando a Azure PowerShell.

> [!NOTE]
> A versão atual dos comandos Azure PowerShell para o Azure App Service não suporta identidades atribuídas ao utilizador. As instruções abaixo são para Funções Azure.

1. Se necessário, instale o Azure PowerShell utilizando as instruções encontradas no [guia Azure PowerShell](/powershell/azure/)e, em seguida, corra `Login-AzAccount` para criar uma ligação com a Azure.

2. Crie uma aplicação de função utilizando o Azure PowerShell. Para obter mais exemplos de como utilizar a Azure PowerShell com Funções Azure, consulte a [referência Az.Functions](/powershell/module/az.functions/?view=azps-4.1.0#functions). O script abaixo também utiliza `New-AzUserAssignedIdentity` o qual deve ser instalado separadamente de acordo com a [Criação, lista ou eliminação de uma identidade gerida atribuída pelo utilizador utilizando a Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create a function app with a user-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType UserAssigned -IdentityId $userAssignedIdentity.Id
    ```

Também pode atualizar uma aplicação de função existente `Update-AzFunctionApp` utilizando.

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo de gestor de recursos Azure

Um modelo de Gestor de Recursos Azure pode ser usado para automatizar a implementação dos seus recursos Azure. Para saber mais sobre a implementação do Serviço de Aplicações e Funções, consulte [automatizar a implementação de recursos no Serviço de Aplicações](../app-service/deploy-complex-application-predictably.md) e [Automatizar a implementação de recursos em Funções Azure](../azure-functions/functions-infrastructure-as-code.md).

Qualquer recurso do tipo `Microsoft.Web/sites` pode ser criado com uma identidade, incluindo o seguinte bloco na definição de recurso, substituindo-o pelo `<RESOURCEID>` ID de recurso da identidade pretendda:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Uma aplicação pode ter identidades atribuídas ao sistema e atribuídas ao mesmo tempo. Neste caso, a `type` propriedade seria `SystemAssigned,UserAssigned`

A adição do tipo atribuído ao utilizador diz ao Azure para utilizar a identidade atribuída ao utilizador especificada para a sua aplicação.

Por exemplo, uma aplicação web pode parecer o seguinte:

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

O directorid é um identificador único para a identidade que é usada para a administração da AD Azure. O clientId é um identificador único para a nova identidade da aplicação que é usada para especificar qual identidade usar durante chamadas de tempo de execução.

## <a name="obtain-tokens-for-azure-resources"></a>Obter fichas para recursos Azure

Uma aplicação pode usar a sua identidade gerida para obter fichas para aceder a outros recursos protegidos pela Azure AD, como o Azure Key Vault. Estes tokens representam a aplicação que acede ao recurso, e não qualquer utilizador específico da aplicação. 

Pode ser necessário configurar o recurso-alvo para permitir o acesso a partir da sua aplicação. Por exemplo, se você solicitar um token para aceder ao Key Vault, você precisa ter certeza de que adicionou uma política de acesso que inclui a identidade da sua aplicação. Caso contrário, as suas chamadas para Key Vault serão rejeitadas, mesmo que incluam o símbolo. Para saber mais sobre quais os recursos que suportam tokens do Azure Ative Directory, consulte [os serviços Azure que suportam a autenticação AD da Azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

> [!IMPORTANT]
> Os serviços back-end para identidades geridas mantêm uma cache por recurso URI durante cerca de 24 horas. Se atualizar a política de acesso de um determinado recurso alvo e recuperar imediatamente um símbolo para esse recurso, poderá continuar a obter um token em cache com permissões desatualizadas até que esse token expire. Não há como forçar uma atualização simbólica.

Existe um protocolo REST simples para obter um token no Serviço de Aplicações e Funções Azure. Isto pode ser usado para todas as aplicações e idiomas. Para .NET e Java, o Azure SDK fornece uma abstração sobre este protocolo e facilita uma experiência de desenvolvimento local.

### <a name="using-the-rest-protocol"></a>Utilização do protocolo REST

> [!NOTE]
> Uma versão mais antiga deste protocolo, utilizando a versão API "2017-09-01", utilizou o `secret` cabeçalho em vez de `X-IDENTITY-HEADER` e só aceitou a `clientid` propriedade para o utilizador atribuído. Também devolveu o `expires_on` em formato de timetamp. MSI_ENDPOINT pode ser usado como pseudónimo para IDENTITY_ENDPOINT, e MSI_SECRET pode ser usado como pseudónimo para IDENTITY_HEADER. Esta versão do protocolo é atualmente necessária para os planos de hospedagem do Linux Consumption.

Uma aplicação com uma identidade gerida tem duas variáveis ambientais definidas:

- IDENTITY_ENDPOINT - a URL para o serviço de fichas locais.
- IDENTITY_HEADER - um cabeçalho usado para ajudar a mitigar os ataques de falsificação de pedidos do lado do servidor (SSRF). O valor é rodado pela plataforma.

O **IDENTITY_ENDPOINT** é um URL local a partir do qual a sua aplicação pode solicitar tokens. Para obter um token para um recurso, faça um pedido HTTP GET para este ponto final, incluindo os seguintes parâmetros:

> | Nome do parâmetro    | Em     | Descrição                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | recurso          | Consulta  | O recurso AZURE AD URI do recurso para o qual deve ser obtido um símbolo. Este pode ser um dos [serviços Azure que suportam a autenticação AD AZure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) ou qualquer outro recurso URI.    |
> | api-version       | Consulta  | A versão da API simbólica a ser utilizada. Utilize "2019-08-01" ou mais tarde (a menos que utilize o Linux Consumption, que atualmente apenas oferece "2017-09-01" - ver nota acima).                                                                                                                                                                                                                                                                 |
> | CABEÇALHO DE IDENTIDADE X | Cabeçalho | O valor da variável ambiente IDENTITY_HEADER. Este cabeçalho é usado para ajudar a atenuar os ataques de falsificação de pedidos do servidor (SSRF).                                                                                                                                                                                                    |
> | client_id         | Consulta  | (Opcional) A identificação do cliente da identidade atribuída ao utilizador a ser usada. Não pode ser utilizado num pedido que `principal_id` `mi_res_id` inclua, ou `object_id` . Se todos os parâmetros de identificação `client_id` `principal_id` (, `object_id` e ) forem `mi_res_id` omitidos, a identidade atribuída ao sistema é utilizada.                                             |
> | principal_id      | Consulta  | (Opcional) A identificação principal da identidade atribuída ao utilizador a ser utilizada. `object_id` é um pseudónimo que pode ser usado em vez disso. Não pode ser usado num pedido que inclua client_id, mi_res_id ou object_id. Se todos os parâmetros de identificação `client_id` `principal_id` (, `object_id` e ) forem `mi_res_id` omitidos, a identidade atribuída ao sistema é utilizada. |
> | mi_res_id         | Consulta  | (Opcional) O ID de recurso Azure da identidade atribuída ao utilizador a ser utilizado. Não pode ser utilizado num pedido que `principal_id` `client_id` inclua, ou `object_id` . Se todos os parâmetros de identificação `client_id` `principal_id` (, `object_id` e ) forem `mi_res_id` omitidos, a identidade atribuída ao sistema é utilizada.                                      |

> [!IMPORTANT]
> Se estiver a tentar obter fichas para identidades atribuídas ao utilizador, deve incluir uma das propriedades opcionais. Caso contrário, o serviço de token tentará obter um símbolo para uma identidade atribuída ao sistema, que pode ou não existir.

Uma resposta bem sucedida de 200 OK inclui um corpo JSON com as seguintes propriedades:

> | Nome da propriedade | Descrição                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | O sinal de acesso solicitado. O serviço web de chamada pode usar este token para autenticar o serviço web recetor.                                                                                                                               |
> | client_id     | A identificação do cliente da identidade que foi usada.                                                                                                                                                                                                       |
> | expires_on    | O tempo de tempo quando a ficha de acesso expira. A data é representada como o número de segundos de "1970-01-01T0:0Z UTC" (corresponde à reivindicação do `exp` token).                                                                                |
> | not_before    | O período de tempo quando o token de acesso entra em vigor, e pode ser aceite. A data é representada como o número de segundos de "1970-01-01T0:0Z UTC" (corresponde à reivindicação do `nbf` token).                                                      |
> | recurso      | O recurso para o token de acesso foi solicitado, que corresponde ao `resource` parâmetro de cadeia de consulta do pedido.                                                                                                                               |
> | token_type    | Indica o valor do tipo símbolo. O único tipo que a Azure AD suporta é o Portador. Para obter mais informações sobre fichas ao portador, consulte o Quadro de [Autorização OAuth 2.0: Utilização do Token ao portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |

Esta resposta é a mesma que a [resposta para o pedido de acesso ao serviço da Azure AD.](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)

### <a name="rest-protocol-examples"></a>Exemplos de protocolo REST

Um pedido de exemplo pode parecer o seguinte:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

E uma resposta de amostra pode parecer o seguinte:

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
const rp = require('request-promise');
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

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Utilização da biblioteca Microsoft.Azure.Services.AppAuthentication para .NET

Para aplicações e funções .NET, a forma mais simples de trabalhar com uma identidade gerida é através do pacote Microsoft.Azure.Services.AppAuthentication. Esta biblioteca também lhe permitirá testar o seu código localmente na sua máquina de desenvolvimento, utilizando a sua conta de utilizador a partir do Visual Studio, do [Azure CLI](/cli/azure)ou da Autenticação Integrada do Diretório Ativo. Quando hospedado na nuvem, irá por defeito usar uma identidade atribuída ao sistema, mas pode personalizar este comportamento usando uma variável de ambiente de cadeia de ligação que faz referência ao ID do cliente de uma identidade atribuída ao utilizador. Para obter mais informações sobre as opções de desenvolvimento com esta biblioteca, consulte a [referência Microsoft.Azure.Services.AppAuthentication]. Esta secção mostra-lhe como começar com a biblioteca no seu código.

1. Adicione referências ao [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e quaisquer outros pacotes NuGet necessários à sua aplicação. O exemplo abaixo também utiliza [microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Adicione o seguinte código à sua aplicação, modificando para direcionar o recurso correto. Este exemplo mostra duas formas de trabalhar com o Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Se pretender utilizar uma identidade gerida atribuída pelo utilizador, pode definir a definição de `AzureServicesAuthConnectionString` aplicação para `RunAs=App;AppId=<clientId-guid>` . Substitua `<clientId-guid>` pela identificação do cliente da identidade que pretende utilizar. Pode definir várias cadeias de conexão, utilizando definições de aplicações personalizadas e passando os seus valores para o construtor AzureServiceTokenProvider.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

Para saber mais sobre a configuração do AzureServiceTokenProvider e as operações que expõe, consulte a [referência Microsoft.Azure.Services.AppAuthentication] e o [Serviço de Aplicações e KeyVault com a amostra MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Usando o Azure SDK para Java

Para aplicações e funções java, a forma mais simples de trabalhar com uma identidade gerida é através do [Azure SDK para Java.](https://github.com/Azure/azure-sdk-for-java) Esta secção mostra-lhe como começar com a biblioteca no seu código.

1. Adicione uma referência à [biblioteca Azure SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure). Para os projetos maven, você pode adicionar este corte à `dependencies` secção do arquivo POM do projeto:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Utilize o `AppServiceMSICredentials` objeto para autenticação. Este exemplo mostra como este mecanismo pode ser usado para trabalhar com o Cofre da Chave Azure:

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

Uma identidade atribuída ao sistema pode ser removida desativando a funcionalidade utilizando o portal, PowerShell ou CLI da mesma forma que foi criada. As identidades atribuídas ao utilizador podem ser removidas individualmente. Para remover todas as identidades, desa um tipo de identidade para "Nenhum".

A remoção de uma identidade atribuída ao sistema desta forma também a eliminará do Azure AD. As identidades atribuídas ao sistema também são automaticamente removidas do Azure AD quando o recurso da aplicação é eliminado.

Para remover todas as identidades num [modelo ARM:](#using-an-azure-resource-manager-template)

```json
"identity": {
    "type": "None"
}
```

Para remover todas as identidades em Azure PowerShell (apenas funções Azure):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> Há também uma configuração de aplicação que pode ser definida, WEBSITE_DISABLE_MSI, que apenas desativa o serviço de token local. No entanto, deixa a identidade no lugar, e a ferramenta ainda mostrará a identidade gerida como "on" ou "ativada". Como resultado, a utilização desta definição não é recomendada.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Access SQL Database de forma segura usando uma identidade gerida](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication referência]: ../key-vault/general/service-to-service-authentication.md
