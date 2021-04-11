---
title: Configurar autenticação
titleSuffix: Azure Machine Learning
description: Saiba como configurar e configurar a autenticação para vários recursos e fluxos de trabalho em Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: ee767cbeafcce604ecb4a79ce0fdf21c70bbb47c
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219642"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurar a autenticação dos recursos e dos fluxos de trabalho do Azure Machine Learning


Saiba como configurar a autenticação no seu espaço de trabalho Azure Machine Learning. A autenticação no seu espaço de trabalho Azure Machine Learning baseia-se no __Azure Ative Directory__ (Azure AD) para a maioria das coisas. Em geral, existem três fluxos de trabalho de autenticação que pode utilizar ao ligar-se ao espaço de trabalho:

* __Interativo__: Utilize a sua conta no Azure Ative Directory para autenticar diretamente ou para obter um token que seja utilizado para a autenticação. A autenticação interativa é utilizada durante _a experimentação e o desenvolvimento iterativo._ A autenticação interativa permite controlar o acesso a recursos (como um serviço web) por utilizador.

* __Principal de serviço__: Cria uma conta principal de serviço no Azure Ative Directory e utiliza-a para autenticar ou obter um token. Um principal de serviço é utilizado quando precisa de um _processo automatizado para autenticar_ o serviço sem exigir interação do utilizador. Por exemplo, um script de integração e implantação contínua que treina e testa um modelo sempre que o código de formação muda.

* __Identidade gerida__: Ao utilizar o Azure Machine Learning SDK _numa Máquina Virtual Azure,_ pode utilizar uma identidade gerida para o Azure. Este fluxo de trabalho permite que o VM se conecte ao espaço de trabalho utilizando a identidade gerida, sem armazenar credenciais no código Python ou levando o utilizador a autenticar. Os clusters de cálculo Azure Machine Learning também podem ser configurados para usar uma identidade gerida para aceder ao espaço de trabalho quando _os modelos de treino._

> [!IMPORTANT]
> Independentemente do fluxo de trabalho de autenticação utilizado, o controlo de acesso baseado em funções (Azure RBAC) é utilizado para estender o nível de acesso (autorização) permitido aos recursos. Por exemplo, um processo de administração ou automação pode ter acesso a criar uma instância computacional, mas não usá-lo, enquanto um cientista de dados poderia usá-lo, mas não apagá-lo ou criá-lo. Para obter mais informações, consulte [Gerir o acesso ao espaço de trabalho Azure Machine Learning](how-to-assign-roles.md).

## <a name="prerequisites"></a>Pré-requisitos

* Criar um espaço de trabalho para [aprendizagem automática Azure.](how-to-manage-workspace.md)
* [Configure o seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o Azure Machine Learning SDK, ou use uma instância [computacional de aprendizagem automática Azure](concept-azure-machine-learning-architecture.md#compute-instance) com o SDK já instalado.

## <a name="azure-active-directory"></a>Azure Active Directory

Todos os fluxos de trabalho de autenticação do seu espaço de trabalho dependem do Azure Ative Directory. Se pretender que os utilizadores autentem usando contas individuais, devem ter contas no seu AD Azure. Se quiser utilizar os principais de serviço, devem existir no seu AD Azure. As identidades geridas são também uma característica do Azure AD. 

Para mais informações sobre a Azure AD, consulte [o que é a autenticação do Diretório Ativo Azure.](..//active-directory/authentication/overview-authentication.md)

Depois de criar as contas AD do Azure, consulte Gerir o [acesso ao espaço de trabalho Azure Machine Learning](how-to-assign-roles.md) para obter informações sobre como lhes dar acesso ao espaço de trabalho e outras operações em Azure Machine Learning.

## <a name="configure-a-service-principal"></a>Configurar um principal de serviço

Para utilizar um principal serviço (SP), tem primeiro de criar o SP e conceder-lhe acesso ao seu espaço de trabalho. Como mencionado anteriormente, o controlo de acesso baseado em funções Azure (Azure RBAC) é usado para controlar o acesso, pelo que também deve decidir qual o acesso a conceder ao SP.

> [!IMPORTANT]
> Ao utilizar um comitado de serviço, conceda-lhe o __acesso mínimo necessário para a tarefa__ para a qual é utilizado. Por exemplo, não concederia a um proprietário principal de serviço ou ao contribuinte o acesso se tudo o que é utilizado for a leitura do token de acesso para uma implementação web.
>
> A razão para conceder o menor acesso é que um diretor de serviço usa uma senha para autenticar, e a palavra-passe pode ser armazenada como parte de um script de automação. Se a palavra-passe for vazada, ter o acesso mínimo necessário para uma tarefa específica minimiza a utilização maliciosa do SP.

A forma mais fácil de criar um SP e conceder acesso ao seu espaço de trabalho é utilizando o [CLI Azure](/cli/azure/install-azure-cli). Para criar um principal serviço e conceder-lhe acesso ao seu espaço de trabalho, utilize os seguintes passos:

> [!NOTE]
> Você deve ser um administrador na subscrição para executar todos estes passos.

1. Autenticar para a sua subscrição Azure:

    ```azurecli-interactive
    az login
    ```

    Se a CLI conseguir abrir o seu browser predefinido, executa essa ação e carrega uma página de início de sessão. Caso contrário, é necessário abrir um browser e seguir as instruções na linha de comando. As instruções envolvem navegar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduzir um código de autorização.

    Se tiver várias subscrições do Azure, pode utilizar o `az account set -s <subscription name or ID>` comando para definir a subscrição. Para obter mais informações, consulte [utilizar várias subscrições do Azure](/cli/azure/manage-azure-subscriptions-azure-cli).

    Para outros métodos de autenticação, consulte [iniciar sing com Azure CLI](/cli/azure/authenticate-azure-cli).

1. Crie o diretor de serviço. No exemplo seguinte, é criado um SP chamado **ml-auth:**

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    A saída será um JSON semelhante ao seguinte. Tome nota do `clientId` `clientSecret` , e `tenantId` campos, como você vai precisar deles para outros passos neste artigo.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. Recupere os detalhes para o principal de serviço utilizando o `clientId` valor devolvido no passo anterior:

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    O JSON seguinte é um exemplo simplificado da saída do comando. Tome nota do `objectId` campo, pois vai precisar do seu valor para o próximo passo.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. Permitir ao SP aceder ao seu espaço de trabalho Azure Machine Learning. Você precisará do seu nome de espaço de trabalho, e seu nome de grupo de recursos para o `-w` e `-g` parâmetros, respectivamente. Para o `--user` parâmetro, utilize o `objectId` valor do passo anterior. O `--role` parâmetro permite definir a função de acesso para o principal serviço. No exemplo seguinte, o PS é atribuído à função **de proprietário.** 

    > [!IMPORTANT]
    > O acesso ao proprietário permite que o diretor de serviço faça praticamente qualquer operação no seu espaço de trabalho. É utilizado neste documento para demonstrar como conceder acesso; num ambiente de produção, a Microsoft recomenda conceder ao diretor de serviço o acesso mínimo necessário para desempenhar o papel que pretende. Para obter informações sobre a criação de um papel personalizado com o acesso necessário para o seu cenário, consulte [Gerir o acesso ao espaço de trabalho Azure Machine Learning](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Esta chamada não produz qualquer produção sobre o sucesso.

## <a name="configure-a-managed-identity"></a>Configurar uma identidade gerida

> [!IMPORTANT]
> A identidade gerida só é suportada quando se utiliza o SDK de aprendizagem de máquinas Azure a partir de uma máquina virtual Azure ou com um cluster de cálculo Azure Machine Learning. A utilização de uma identidade gerida com um cluster de cálculo está atualmente em pré-visualização.

### <a name="managed-identity-with-a-vm"></a>Identidade gerida com um VM

1. Permitir uma [identidade gerida atribuída pelo sistema para os recursos da Azure no VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

1. A partir do [portal Azure,](https://portal.azure.com)selecione o seu espaço de trabalho e, em seguida, selecione __Access Control (IAM)__, Adicione a Atribuição de __Funções__ e selecione __a Máquina Virtual__ a partir do Access Access __To__ dropdown. Finalmente, selecione a identidade do seu VM.

1. Selecione o papel a atribuir a esta identidade. Por exemplo, colaborador ou um papel personalizado. Para mais informações, consulte, [Controle o acesso aos recursos.](how-to-assign-roles.md)

### <a name="managed-identity-with-compute-cluster"></a>Identidade gerida com cluster de computação

Para obter mais informações, consulte [Configurar a identidade gerida para o cluster compute](how-to-create-attach-compute-cluster.md#managed-identity).

<a id="interactive-authentication"></a>

## <a name="use-interactive-authentication"></a>Utilize a autenticação interativa

> [!IMPORTANT]
> A autenticação interativa utiliza o seu navegador e requer cookies (incluindo cookies de 3ª parte). Se tiver cookies desativados, poderá receber um erro como "não podemos inscrevê-lo". Este erro também pode ocorrer se tiver ativado [a autenticação multi-factor Azure AD](../active-directory/authentication/concept-mfa-howitworks.md).

A maioria dos exemplos na documentação e amostras utilizam a autenticação interativa. Por exemplo, ao utilizar o SDK existem duas chamadas de função que irão automaticamente insusá-lo com um fluxo de autenticação baseado em UI:

* Chamar a `from_config()` função emitirá o pedido.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    A função `from_config()` procura um ficheiro JSON que contém as informações de ligação à área de trabalho.

* A utilização do `Workspace` construtor para fornecer informações de subscrição, grupo de recursos e espaço de trabalho, também solicitará a autenticação interativa.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Se tiver acesso a vários inquilinos, poderá ter de importar a classe e definir explicitamente qual o inquilino que está a ser alvo. Ligar para o construtor `InteractiveLoginAuthentication` também lhe pedirá para iniciar sessão semelhante às chamadas acima.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

Ao utilizar o Azure CLI, o `az login` comando é utilizado para autenticar a sessão CLI. Para mais informações, consulte [Começar com o Azure CLI.](/cli/azure/get-started-with-azure-cli)

> [!TIP]
> Se estiver a utilizar o SDK a partir de um ambiente onde já autenticou interativamente utilizando o Azure CLI, pode utilizar a `AzureCliAuthentication` classe para autenticar no espaço de trabalho utilizando as credenciais em cache pelo CLI:
>
> ```python
> from azureml.core.authentication import AzureCliAuthentication
> cli_auth = AzureCliAuthentication()
> ws = Workspace(subscription_id="your-sub-id",
>                resource_group="your-resource-group-id",
>                workspace_name="your-workspace-name",
>                auth=cli_auth
>                )
> ```

<a id="service-principal-authentication"></a>

## <a name="use-service-principal-authentication"></a>Utilizar a autenticação principal do serviço

Para autenticar no seu espaço de trabalho a partir do SDK, utilizando um diretor de serviço, utilize o `ServicePrincipalAuthentication` construtor de classe. Utilize os valores que obteve ao criar o prestador de serviços como parâmetros. Os `tenant_id` mapas dos parâmetros para `tenantId` cima, `service_principal_id` mapas `clientId` para, e `service_principal_password` mapas para `clientSecret` .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

A `sp` variável possui agora um objeto de autenticação que utiliza diretamente no SDK. Em geral, é uma boa ideia armazenar os ids/segredos usados acima em variáveis ambientais, como mostrado no seguinte código. Armazenar em variáveis ambientais impede que a informação seja acidentalmente verificada num repo GitHub.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Para fluxos de trabalho automatizados que funcionam em Python e utilizam o SDK principalmente, você pode usar este objeto como-é na maioria dos casos para a sua autenticação. O seguinte código autentica-se no seu espaço de trabalho utilizando o objeto auth que criou.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Utilize um diretor de serviço do Azure CLI

Pode utilizar um rente-chefe de serviço para comandos Azure CLI. Para obter mais informações, consulte [Iniciar sina com um principal de serviço.](/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal)

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Utilize um principal de serviço com a API REST (pré-visualização)

O principal de serviço também pode ser usado para autenticar a [API de](/rest/api/azureml/) Aprendizagem automática Azure (pré-visualização). Utiliza o [fluxo de concessão](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)de credenciais de cliente do Azure Ative, que permite chamadas de serviço para autenticação sem cabeça em fluxos de trabalho automatizados. Os exemplos são implementados com a [biblioteca ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md) em Python e Node.js, mas também pode usar qualquer biblioteca de código aberto que suporte OpenID Connect 1.0.

> [!NOTE]
> MSAL.js é uma biblioteca mais recente do que a ADAL, mas não é possível fazer autenticação de serviço-a-serviço utilizando credenciais de cliente com MSAL.js, uma vez que se trata principalmente de uma biblioteca do lado do cliente destinada à autenticação interativa/UI ligada a um utilizador específico. Recomendamos a utilização do ADAL como mostrado abaixo para construir fluxos de trabalho automatizados com a API REST.

#### <a name="nodejs"></a>Node.js

Utilize os seguintes passos para gerar um token de auth utilizando Node.js. No seu ambiente, `npm install adal-node` corra. Em seguida, use o seu `tenantId` , e do principal serviço que criou nos `clientId` `clientSecret` passos acima como valores para as variáveis correspondentes no seguinte script.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

A variável `tokenResponse` é um objeto que inclui os metadados simbólicos e associados, como o tempo de validade. Os tokens são válidos por 1 hora, e podem ser refrescados executando a mesma chamada novamente para recuperar um novo token. O seguinte corte é uma resposta de amostra.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

Use a `accessToken` propriedade para ir buscar o símbolo de auth. Consulte a documentação da [API REST,](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) por exemplo, sobre como usar o token para fazer chamadas API.

#### <a name="python"></a>Python

Utilize os seguintes passos para gerar um símbolo de auth utilizando Python. No seu ambiente, `pip install adal` corra. Em seguida, use o seu `tenantId` , e do principal serviço que criou nos `clientId` `clientSecret` passos acima como valores para as variáveis apropriadas no seguinte script.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

A variável `token_response` é um dicionário que inclui os metadados simbólicos e associados, como o tempo de validade. Os tokens são válidos por 1 hora, e podem ser refrescados executando a mesma chamada novamente para recuperar um novo token. O seguinte corte é uma resposta de amostra.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Use `token_response["accessToken"]` para ir buscar o símbolo auth. Consulte a documentação da [API REST,](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) por exemplo, sobre como usar o token para fazer chamadas API.

#### <a name="java"></a>Java

Em Java, recupere o símbolo do portador usando uma chamada padrão REST:

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

O código anterior teria de lidar com exceções e códigos de estado que `200 OK` não, mas mostra o padrão: 

- Use o ID do cliente e o segredo para validar que o seu programa deve ter acesso
- Use o seu ID do inquilino para especificar onde `login.microsoftonline.com` deve estar procurando
- Use o Gestor de Recursos Azure como fonte do token de autorização

## <a name="use-managed-identity-authentication"></a>Utilizar a autenticação de identidade gerida

Para autenticar no espaço de trabalho a partir de um cluster VM ou compute que esteja configurado com uma identidade gerida, utilize a `MsiAuthentication` classe. O exemplo a seguir demonstra como utilizar esta classe para autenticar num espaço de trabalho:

```python
from azureml.core.authentication import MsiAuthentication

msi_auth = MsiAuthentication()

ws = Workspace(subscription_id="your-sub-id",
                resource_group="your-resource-group-id",
                workspace_name="your-workspace-name",
                auth=msi_auth
                )
```

## <a name="next-steps"></a>Passos seguintes

* [Como usar segredos no treino.](how-to-use-secrets-in-runs.md)
* [Como configurar a autenticação para modelos implantados como serviço web](how-to-authenticate-web-service.md).
* [Consumir um modelo do Azure Machine Learning implementado como serviço Web](how-to-consume-web-service.md).
