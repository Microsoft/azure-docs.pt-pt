---
title: Configurar autenticação
titleSuffix: Azure Machine Learning
description: Saiba como configurar e configurar a autenticação para vários recursos e fluxos de trabalho em Azure Machine Learning. Existem múltiplas formas de configurar e utilizar a autenticação dentro do serviço, desde simples auth baseado em UI para fins de desenvolvimento ou teste, até à autenticação principal do serviço Azure Ative.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 12/17/2019
ms.custom: has-adal-ref
ms.openlocfilehash: 57160088c283b1f2c686429168cc858fee58324a
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433116"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurar a autenticação para recursos de aprendizagem automática Azure e fluxos de trabalho
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a configurar e configurar a autenticação para vários recursos e fluxos de trabalho no Azure Machine Learning. Existem múltiplas formas de autenticar o serviço, desde a auth simples baseada em UI para fins de desenvolvimento ou teste até à autenticação principal do serviço Azure Ative. Este artigo também explica as diferenças no funcionamento da autenticação do serviço web, bem como como autenticar para a API de Aprendizagem automática Azure.

Este como mostrar-lhe como fazer as seguintes tarefas:

* Utilizar a autenticação interativa de UI para testes/desenvolvimento
* Configurar a autenticação principal do serviço
* Autenticação no seu espaço de trabalho
* Obtenha fichas do tipo portador OAuth2.0 para Azure Machine Learning REST API
* Compreender a autenticação do serviço web

Consulte o [artigo do conceito](concept-enterprise-security.md) para uma visão geral da segurança e da autenticação no âmbito do Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Criar um espaço de trabalho para [aprendizagem automática Azure.](how-to-manage-workspace.md)
* [Configure o seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o Azure Machine Learning SDK, ou use um [VM de Aprendizagem de Máquinas Azure](concept-azure-machine-learning-architecture.md#compute-instance) com o SDK já instalado.

## <a name="interactive-authentication"></a>Autenticação interativa

A maioria dos exemplos na documentação deste serviço usam a autenticação interativa em cadernos Jupyter como um método simples para testar e demonstração. Esta é uma forma leve de testar o que estás a construir. Existem duas chamadas de função que irão automaticamente aussentar-lhe um fluxo de autenticação baseado em UI.

Chamar a `from_config()` função emitirá o pedido.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

A `from_config()` função procura um ficheiro JSON que contenha informações de ligação do seu espaço de trabalho. Também pode especificar explicitamente os detalhes da ligação utilizando o `Workspace` construtor, o que também irá solicitar a autenticação interativa. Ambas as chamadas são equivalentes.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Se tiver acesso a vários inquilinos, poderá ter de importar a classe e definir explicitamente qual o inquilino que está a ser alvo. Ligar para o construtor `InteractiveLoginAuthentication` também lhe pedirá para iniciar sessão semelhante às chamadas acima.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Embora útil para testar e aprender, a autenticação interativa não o ajudará na construção de fluxos de trabalho automatizados ou sem cabeça. A criação da autenticação principal do serviço é a melhor abordagem para processos automatizados que utilizam o SDK.

## <a name="set-up-service-principal-authentication"></a>Configurar a autenticação principal do serviço

Este processo é necessário para permitir a autenticação dissociada a partir de um login específico do utilizador, que lhe permite autenticar para o Azure Machine Learning Python SDK em fluxos de trabalho automatizados. A autenticação principal do serviço também lhe permitirá [autenticar para a API REST.](#azure-machine-learning-rest-api-auth)

Para configurar a autenticação principal do serviço, primeiro cria um registo de aplicações no Azure Ative Directory e, em seguida, concede à sua aplicação acesso baseado em funções ao seu espaço de trabalho ML. A forma mais fácil de completar esta configuração é através da [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) no portal Azure. Depois de iniciar sessão no portal, clique `>_` no ícone no topo direito da página perto do seu nome para abrir a concha.

Se ainda não ter usado a casca de nuvem na sua conta Azure, terá de criar um recurso de conta de armazenamento para armazenar quaisquer ficheiros que estejam escritos. Em geral, esta conta de armazenamento incorrerá num custo mensal insignificante. Além disso, instale a extensão de aprendizagem automática se não a tiver utilizado previamente com o seguinte comando.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Deve ser administrador da subscrição para executar os seguintes passos.

Em seguida, executar o seguinte comando para criar o principal de serviço. Dê-lhe um nome, neste caso **ml-auth**.

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

Em seguida, executar o seguinte comando para obter os detalhes sobre o principal de serviço que acabou de criar, usando o `clientId` valor de cima como entrada para o `--id` parâmetro.

```azurecli-interactive
az ad sp show --id your-client-id
```

Segue-se um exemplo simplificado da saída JSON do comando. Tome nota do `objectId` campo, pois vai precisar do seu valor para o próximo passo.

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

Em seguida, utilize o seguinte comando para atribuir o seu acesso principal de serviço ao seu espaço de trabalho de aprendizagem automática. Você precisará do seu nome de espaço de trabalho, e seu nome de grupo de recursos para o `-w` e `-g` parâmetros, respectivamente. Para o `--user` parâmetro, utilize o `objectId` valor do passo anterior. O `--role` parâmetro permite-lhe definir a função de acesso para o principal de serviço, e em geral utilizará o **proprietário** ou **o contribuinte.** Ambos têm acesso a recursos existentes, como clusters de computação e datastores, mas apenas **o proprietário** pode providenciar esses recursos.

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Esta chamada não produz qualquer saída, mas tem agora a autenticação principal do serviço configurada para o seu espaço de trabalho.

## <a name="authenticate-to-your-workspace"></a>Autenticar para o seu espaço de trabalho

Agora que tem o serviço principal ativado, pode autenticar-se no seu espaço de trabalho no SDK sem fazer login fisicamente como utilizador. Use o `ServicePrincipalAuthentication` construtor de classe e use os valores que obteve dos passos anteriores como parâmetros. Os `tenant_id` mapas dos parâmetros para `tenantId` cima, `service_principal_id` mapas `clientId` para, e `service_principal_password` mapas para `clientSecret` .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

A `sp` variável possui agora um objeto de autenticação que utiliza diretamente no SDK. Em geral, é uma boa ideia armazenar os ids/segredos usados acima em variáveis ambientais, como mostrado no seguinte código.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Para fluxos de trabalho automatizados que funcionam em Python e utilizam o SDK principalmente, você pode usar este objeto como-é na maioria dos casos para a sua autenticação. O código a seguir autentica-se no seu espaço de trabalho utilizando o objeto auth que acabou de criar.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure Machine Learning REST AUTH

O principal serviço criado nos passos acima também pode ser usado para autenticar a [API API](https://docs.microsoft.com/rest/api/azureml/)de Aprendizagem automática Azure. Utiliza o [fluxo de concessão](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)de credenciais de cliente do Azure Ative, que permite chamadas de serviço para autenticação sem cabeça em fluxos de trabalho automatizados. Os exemplos são implementados com a [biblioteca ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) em Python e Node.js, mas também pode usar qualquer biblioteca de código aberto que suporte OpenID Connect 1.0.

> [!NOTE]
> A MSAL.js é uma biblioteca mais recente do que a ADAL, mas não é possível fazer autenticação de serviço-a-serviço utilizando credenciais de cliente com MSAL.js, uma vez que se trata principalmente de uma biblioteca do lado do cliente destinada à autenticação interativa/UI ligada a um utilizador específico. Recomendamos a utilização do ADAL como mostrado abaixo para construir fluxos de trabalho automatizados com a API REST.

### <a name="nodejs"></a>Node.js

Utilize os seguintes passos para gerar um token de auth utilizando node.js. No seu ambiente, `npm install adal-node` corra. Em seguida, use o seu `tenantId` , e do principal serviço que criou nos `clientId` `clientSecret` passos acima como valores para as variáveis correspondentes no seguinte script.

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

A variável `tokenResponse` é um objeto que inclui os metadados simbólicos e associados, como o tempo de validade. Os tokens são válidos por 1 hora, e podem ser refrescados executando a mesma chamada novamente para recuperar um novo token. Segue-se uma resposta de amostra.

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

### <a name="python"></a>Python

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

A variável `token_response` é um dicionário que inclui os metadados simbólicos e associados, como o tempo de validade. Os tokens são válidos por 1 hora, e podem ser refrescados executando a mesma chamada novamente para recuperar um novo token. Segue-se uma resposta de amostra.

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

Os serviços web em Azure Machine Learning utilizam um padrão de autenticação diferente do descrito acima. A forma mais fácil de autenticar os serviços web implantados é utilizar a **autenticação baseada em chaves**, que gera chaves de autenticação estáticas do tipo portador que não precisam de ser atualizadas. Se apenas precisar de autenticar para um serviço web implantado, não precisa de configurar a autenticação do princípio de serviço como mostrado acima.

Os serviços web implantados no Serviço Azure Kubernetes têm auth baseado em chaves *ativada* por padrão. Os serviços implantados em Azure Container Instances têm auth baseado em chaves *desativados* por padrão, mas pode capacitá-lo definindo `auth_enabled=True` ao criar o serviço web ACI. Segue-se um exemplo de criação de uma configuração de implementação de ACI com auth baseado em chaves ativada.

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

Os serviços web também suportam a autenticação baseada em fichas, mas apenas para implementações do Serviço Azure Kubernetes. Consulte o [como](how-to-consume-web-service.md) consumir serviços web para obter informações adicionais sobre a autenticação.

### <a name="token-based-web-service-authentication"></a>Autenticação de serviço web baseado em token

Quando ativa a autenticação simbólica de um serviço web, os utilizadores devem apresentar um Azure Machine Learning JSON Web Token ao serviço web para aceder ao mesmo. O token expira após um período de tempo especificado e precisa de ser atualizado para continuar a fazer chamadas.

* A autenticação token é **desativada por padrão** quando implementa para o Serviço Azure Kubernetes.
* A autenticação **token não é suportada** quando se implanta em Instâncias de Contentores Azure.

Para controlar a autenticação simbólica, utilize o `token_auth_enabled` parâmetro quando criar ou atualizar uma implementação.

Se a autenticação simbólica estiver ativada, pode utilizar o `get_token` método para recuperar um Token Web JSON (JWT) e o tempo de validade do token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Terá de pedir um novo símbolo depois da hora do `refresh_by` símbolo. Se precisar de atualizar fichas fora do Python SDK, uma opção é utilizar a API REST com autenticação principal de serviço para fazer a `service.get_token()` chamada periodicamente, como discutido anteriormente.
>
> Recomendamos vivamente que crie o seu espaço de trabalho Azure Machine Learning na mesma região que o seu cluster de Serviço Azure Kubernetes.
>
> Para autenticar com um token, o serviço web fará uma chamada para a região em que o seu espaço de trabalho Azure Machine Learning é criado. Se a região do seu espaço de trabalho não estiver disponível, não poderá obter um símbolo para o seu serviço web, mesmo que o seu cluster esteja numa região diferente do seu espaço de trabalho. O resultado é que a autenticação AD AD Azure não está disponível até que a região do seu espaço de trabalho esteja novamente disponível.
>
> Além disso, quanto maior for a distância entre a região do seu aglomerado e a região do seu espaço de trabalho, mais tempo demorará a obter um símbolo.

## <a name="next-steps"></a>Passos seguintes

* [Treine e implemente um modelo de classificação de imagem.](tutorial-train-models-with-aml.md)
* [Consumir um modelo de Aprendizagem automática Azure implementado como serviço web](how-to-consume-web-service.md).
