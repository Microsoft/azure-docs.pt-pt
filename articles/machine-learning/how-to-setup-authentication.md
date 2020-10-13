---
title: Configurar autenticação
titleSuffix: Azure Machine Learning
description: Saiba como configurar e configurar a autenticação para vários recursos e fluxos de trabalho em Azure Machine Learning. Existem múltiplas formas de configurar e utilizar a autenticação dentro do serviço, desde simples auth baseado em UI para fins de desenvolvimento ou teste, até à autenticação principal do serviço Azure Ative.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js
ms.openlocfilehash: 486f026f0d9b325f8e17a040c69f9d3e1da9b359
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91729037"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurar a autenticação para recursos de aprendizagem automática Azure e fluxos de trabalho


Aprenda a autenticar o seu espaço de trabalho Azure Machine Learning e a modelos implementados como serviços web.

Em geral, existem dois tipos de autenticação que pode utilizar com a Azure Machine Learning:

* __Interativo__: Utilize a sua conta no Azure Ative Directory para autenticar diretamente ou para obter um token que seja utilizado para a autenticação. A autenticação interativa é utilizada durante a experimentação e o desenvolvimento iterativo. Ou onde pretende controlar o acesso a recursos (como um serviço web) por utilizador.
* __Principal de serviço__: Cria uma conta principal de serviço no Azure Ative Directory e utiliza-a para autenticar ou obter um token. Um principal de serviço é utilizado quando precisa de um processo automatizado para autenticar o serviço sem exigir interação do utilizador. Por exemplo, um script de integração e implantação contínua que treina e testa um modelo sempre que o código de formação muda. Também pode utilizar um principal de serviço para recuperar um token para autenticar num serviço web, caso não pretenda exigir que o utilizador final do serviço autente. Ou onde a autenticação do utilizador final não é realizada diretamente usando o Diretório Azure Ative.

Independentemente do tipo de autenticação utilizado, o controlo de acesso baseado em funções (RBAC) é utilizado para estender o nível de acesso permitido aos recursos. Por exemplo, uma conta que é usada para obter o token de acesso para um modelo implantado só precisa de ler o acesso ao espaço de trabalho. Para obter mais informações sobre o RBAC, consulte [Gerir o acesso ao espaço de trabalho Azure Machine Learning](how-to-assign-roles.md).

## <a name="prerequisites"></a>Pré-requisitos

* Criar um espaço de trabalho para [aprendizagem automática Azure.](how-to-manage-workspace.md)
* [Configure o seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o Azure Machine Learning SDK, ou use um [VM de Aprendizagem de Máquinas Azure](concept-azure-machine-learning-architecture.md#compute-instance) com o SDK já instalado.

## <a name="interactive-authentication"></a>Autenticação interativa

> [!IMPORTANT]
> A autenticação interativa utiliza o seu navegador e requer cookies (incluindo cookies de 3ª parte). Se tiver cookies desativados, poderá receber um erro como "não podemos inscrevê-lo". Este erro também pode ocorrer se tiver ativado [a autenticação de vários fatores Azure](/azure/active-directory/authentication/concept-mfa-howitworks).

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

## <a name="service-principal-authentication"></a>Autenticação do principal de serviço

Para utilizar a autenticação principal do serviço (SP), tem primeiro de criar o SP e conceder-lhe acesso ao seu espaço de trabalho. Como mencionado anteriormente, o controlo de acesso baseado em funções Azure (Azure RBAC) é usado para controlar o acesso, pelo que também deve decidir qual o acesso a conceder ao SP.

> [!IMPORTANT]
> Ao utilizar um comitado de serviço, conceda-lhe o __acesso mínimo necessário para a tarefa__ para a qual é utilizado. Por exemplo, não concederia a um proprietário principal de serviço ou ao contribuinte o acesso se tudo o que é utilizado for a leitura do token de acesso para uma implementação web.
>
> A razão para conceder o menor acesso é que um diretor de serviço usa uma senha para autenticar, e a palavra-passe pode ser armazenada como parte de um script de automação. Se a palavra-passe for vazada, ter o acesso mínimo necessário para uma tarefa específica minimiza a utilização maliciosa do SP.

A forma mais fácil de criar um SP e conceder acesso ao seu espaço de trabalho é utilizando o [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true). Para criar um principal serviço e conceder-lhe acesso ao seu espaço de trabalho, utilize os seguintes passos:

> [!NOTE]
> Você deve ser um administrador na subscrição para executar todos estes passos.

1. Autenticar para a sua subscrição Azure:

    ```azurecli-interactive
    az login
    ```

    Se a CLI conseguir abrir o seu browser predefinido, executa essa ação e carrega uma página de início de sessão. Caso contrário, é necessário abrir um browser e seguir as instruções na linha de comando. As instruções envolvem navegar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduzir um código de autorização.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

    Para outros métodos de autenticação, consulte [iniciar sing com Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true).

1. Instale a extensão Azure Machine Learning:

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

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
    > O acesso ao proprietário permite que o diretor de serviço faça praticamente qualquer operação no seu espaço de trabalho. É utilizado neste documento para demonstrar como conceder acesso; num ambiente de produção, a Microsoft recomenda conceder ao diretor de serviço o acesso mínimo necessário para desempenhar o papel que pretende. Para obter mais informações, consulte [Gerir o acesso ao espaço de trabalho Azure Machine Learning](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Esta chamada não produz qualquer produção sobre o sucesso.

### <a name="use-a-service-principal-from-the-sdk"></a>Utilize um principal de serviço do SDK

Para autenticar no seu espaço de trabalho a partir do SDK, utilizando o principal serviço, utilize o `ServicePrincipalAuthentication` construtor de classe. Utilize os valores que obteve ao criar o prestador de serviços como parâmetros. Os `tenant_id` mapas dos parâmetros para `tenantId` cima, `service_principal_id` mapas `clientId` para, e `service_principal_password` mapas para `clientSecret` .

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

Pode utilizar um rente-chefe de serviço para comandos Azure CLI. Para obter mais informações, consulte [Iniciar sina com um principal de serviço.](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest&preserve-view=true#sign-in-using-a-service-principal)

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Utilize um principal de serviço com a API REST (pré-visualização)

O principal de serviço também pode ser usado para autenticar a [API de](https://docs.microsoft.com/rest/api/azureml/) Aprendizagem automática Azure (pré-visualização). Utiliza o [fluxo de concessão](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)de credenciais de cliente do Azure Ative, que permite chamadas de serviço para autenticação sem cabeça em fluxos de trabalho automatizados. Os exemplos são implementados com a [biblioteca ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) em Python e Node.js, mas também pode usar qualquer biblioteca de código aberto que suporte OpenID Connect 1.0.

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

## <a name="web-service-authentication"></a>Autenticação de serviço web

As implementações de modelos criadas pela Azure Machine Learning fornecem dois métodos de autenticação:

* **baseado em teclas**: Uma tecla estática é usada para autenticar o serviço web.
* **Baseado em símbolos**: Um símbolo temporário deve ser obtido a partir do espaço de trabalho e utilizado para autenticar para o serviço web. Este token expira após um período de tempo, e deve ser atualizado para continuar a trabalhar com o serviço web.

    > [!NOTE]
    > A autenticação baseada em token só está disponível quando é implantada no Serviço Azure Kubernetes.

### <a name="key-based-web-service-authentication"></a>Autenticação de serviço web baseado em chaves

Os serviços web implantados no Serviço Azure Kubernetes (AKS) têm auth baseado em chaves *ativada* por padrão. Os serviços implantados em Azure Container Instances (ACI) têm auth baseado em chaves *desativados* por padrão, mas pode capacitá-lo definindo `auth_enabled=True` ao criar o serviço web ACI. O código a seguir é um exemplo de criação de uma configuração de implementação de ACI com auth baseado em chave ativada.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Em seguida, pode utilizar a configuração ACI personalizada na implementação utilizando a `Model` classe.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Para ir buscar as teclas auth, `aci_service.get_keys()` use. Para regenerar uma chave, utilize a `regen_key()` função e passe tanto **no Primário** como **no Secundário**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Para obter mais informações sobre a autenticação de um modelo implantado, consulte [Criar um cliente para um modelo implantado como serviço web](how-to-consume-web-service.md).

### <a name="token-based-web-service-authentication"></a>Autenticação de serviço web baseado em token

Quando ativa a autenticação simbólica de um serviço web, os utilizadores devem apresentar um Azure Machine Learning JSON Web Token ao serviço web para aceder ao mesmo. O token expira após um prazo especificado e precisa de ser atualizado para continuar a fazer chamadas.

* A autenticação token é **desativada por padrão** quando implementa para o Serviço Azure Kubernetes.
* A autenticação **token não é suportada** quando se implanta em Instâncias de Contentores Azure.
* A autenticação token **não pode ser utilizada ao mesmo tempo que a autenticação baseada em chaves.**

Para controlar a autenticação simbólica, utilize o `token_auth_enabled` parâmetro quando criar ou atualizar uma implementação:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Se a autenticação simbólica estiver ativada, pode utilizar o `get_token` método para recuperar um Token Web JSON (JWT) e o tempo de validade do token:

> [!TIP]
> Se utilizar um principal de serviço para obter o token, e quiser que tenha o acesso mínimo necessário para recuperar um token, atribua-o ao papel de **leitor** para o espaço de trabalho.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Precisará de pedir um novo token depois do prazo `refresh_by` do token. Se precisar de atualizar fichas fora do Python SDK, uma opção é utilizar a API REST com autenticação principal de serviço para fazer a `service.get_token()` chamada periodicamente, como discutido anteriormente.
>
> Recomendamos vivamente que crie o seu espaço de trabalho Azure Machine Learning na mesma região que o seu cluster de Serviço Azure Kubernetes.
>
> Para autenticar com um token, o serviço web fará uma chamada para a região em que o seu espaço de trabalho Azure Machine Learning é criado. Se a região do seu espaço de trabalho não estiver disponível, não poderá obter um símbolo para o seu serviço web, mesmo que o seu cluster esteja numa região diferente do seu espaço de trabalho. O resultado é que a autenticação AD AD Azure não está disponível até que a região do seu espaço de trabalho esteja novamente disponível.
>
> Além disso, quanto maior for a distância entre a região do seu aglomerado e a região do seu espaço de trabalho, mais tempo demorará a obter um símbolo.

## <a name="next-steps"></a>Passos seguintes

* [Como usar segredos no treino.](how-to-use-secrets-in-runs.md)
* [Treine e implemente um modelo de classificação de imagem.](tutorial-train-models-with-aml.md)
* [Consumir um modelo de Aprendizagem automática Azure implementado como serviço web](how-to-consume-web-service.md).
