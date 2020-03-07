---
title: Configurar autenticação
titleSuffix: Azure Machine Learning
description: Saiba como configurar e configurar a autenticação para vários recursos e fluxos de trabalho em Azure Machine Learning. Existem múltiplas formas de configurar e utilizar a autenticação dentro do serviço, desde a simples uuth baseada em UI para fins de desenvolvimento ou teste, até à autenticação principal do serviço de Diretório Ativo Azure completo.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355313"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurar a autenticação para os recursos e fluxos de trabalho de Aprendizagem automática do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a configurar e configurar a autenticação para vários recursos e fluxos de trabalho em Azure Machine Learning. Existem múltiplas formas de autenticar o serviço, desde a simples uuth baseada em UI para fins de desenvolvimento ou teste até à autenticação principal do serviço de Diretório Ativo Azure completo. Este artigo também explica as diferenças na forma como funciona a autenticação do serviço web, bem como como autenticar a API de Aprendizagem automática Azure.

Este como-mostrar-lhe como fazer as seguintes tarefas:

* Utilize a autenticação ui interativa para testes/desenvolvimento
* Configurar a autenticação principal do serviço
* Autenticação ao seu espaço de trabalho
* Obtenha fichas tipo OAuth2.0 para API de aprendizagem automática Azure
* Compreender a autenticação do serviço web

Consulte o [artigo conceptual](concept-enterprise-security.md) para uma visão geral da segurança e autenticação no Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Crie um espaço de [trabalho azure machine learning.](how-to-manage-workspace.md)
* [Configure o seu ambiente](how-to-configure-environment.md) de desenvolvimento para instalar o Azure Machine Learning SDK ou utilize um VM de Bloco de [Aprendizagem automática Azure](concept-azure-machine-learning-architecture.md#compute-instance) com o SDK já instalado.

## <a name="interactive-authentication"></a>Autenticação interativa

A maioria dos exemplos na documentação para este serviço utilizam a autenticação interativa nos cadernos jupyter como um método simples para testar e demonstração. Esta é uma forma leve de testar o que estás a construir. Existem duas chamadas de função que o solicitarão automaticamente com um fluxo de autenticação baseado em UI.

Chamar a função `from_config()` emitirá o pedido.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

A função `from_config()` procura um ficheiro JSON que contenha as informações de ligação ao espaço de trabalho. Também pode especificar explicitamente os detalhes da ligação utilizando o construtor `Workspace`, o que também irá solicitar a autenticação interativa. Ambas as chamadas são equivalentes.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Se você tem acesso a vários inquilinos, você pode precisar importar a classe e definir explicitamente que inquilino você está alvo. Chamar o construtor para `InteractiveLoginAuthentication` também o levará a fazer login semelhante às chamadas acima.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Embora seja útil para testar e aprender, a autenticação interativa não o ajudará na construção de fluxos de trabalho automatizados ou sem cabeça. A instalação da autenticação principal do serviço é a melhor abordagem para processos automatizados que utilizam o SDK.

## <a name="set-up-service-principal-authentication"></a>Configurar a autenticação principal do serviço

Este processo é necessário para permitir a autenticação que é dissociada de um login específico do utilizador, que lhe permite autenticar o Azure Machine Learning Python SDK em fluxos de trabalho automatizados. A autenticação do principal de serviço também lhe permitirá [autenticar a API REST](#azure-machine-learning-rest-api-auth).

Para configurar a autenticação principal do serviço, cria primeiro um registo de aplicações no Azure Ative Directory e, em seguida, concede o acesso baseado na sua aplicação ao seu espaço de trabalho ML. A forma mais fácil de completar esta configuração é através da [Casca de Nuvem Azure](https://azure.microsoft.com/features/cloud-shell/) no portal Azure. Depois de iniciar sessão no portal, clique no ícone `>_` no lado superior direito da página perto do seu nome para abrir a concha.

Se ainda não usou a casca de nuvem na sua conta Azure, terá de criar um recurso de conta de armazenamento para armazenar quaisquer ficheiros que estejam escritos. Em geral, esta conta de armazenamento incorrerá num custo mensal insignificante. Além disso, instale a extensão de aprendizagem automática se não a tiver utilizado anteriormente com o seguinte comando.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Deve ser administrador na subscrição para realizar os seguintes passos.

Em seguida, executar o seguinte comando para criar o diretor de serviço. Dê-lhe um nome, neste caso **ml-auth**.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

A saída será um JSON semelhante ao seguinte. Tome nota dos campos de `clientId`, `clientSecret`e `tenantId`, pois vai precisar deles para outros passos neste artigo.

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

Em seguida, execute o seguinte comando para obter os detalhes sobre o diretor de serviço que acabou de criar, usando o valor `clientId` de cima como a entrada para o parâmetro `--id`.

```azurecli-interactive
az ad sp show --id your-client-id
```

Segue-se um exemplo simplificado da saída JSON a partir do comando. Tome nota do campo `objectId`, pois necessitará do seu valor para o próximo passo.

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

Em seguida, utilize o seguinte comando para atribuir o seu serviço principal de acesso ao seu espaço de trabalho de aprendizagem automática. Você precisará do seu nome de espaço de trabalho, e seu nome de grupo de recursos para os parâmetros `-w` e `-g`, respectivamente. Para o parâmetro `--user`, utilize o valor `objectId` do passo anterior. O parâmetro `--role` permite definir a função de acesso para o diretor de serviço, e em geral utilizará o **proprietário** ou **o colaborador.** Ambos têm acesso a recursos existentes, como clusters de computação e lojas de dados, mas só os **proprietários** podem fornecer esses recursos. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Esta chamada não produz qualquer saída, mas agora tem a autenticação principal do serviço configurada para o seu espaço de trabalho.

## <a name="authenticate-to-your-workspace"></a>Autenticar o seu espaço de trabalho

Agora que tem auth principal de serviço ativada, pode autenticar o seu espaço de trabalho no SDK sem iniciar sessão física como utilizador. Use o construtor de classe `ServicePrincipalAuthentication` e use os valores que obteve dos passos anteriores como os parâmetros. Os mapas dos parâmetros `tenant_id` para `tenantId` de cima, mapas `service_principal_id` para `clientId`, e mapas `service_principal_password` para `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

A variável `sp` agora detém um objeto de autenticação que utiliza diretamente no SDK. Em geral, é uma boa ideia armazenar os ids/segredos acima utilizados nas variáveis ambientais, como indicado no seguinte código.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Para fluxos de trabalho automatizados que funcionam em Python e utilizam o SDK principalmente, pode utilizar este objeto como é na maioria dos casos para a sua autenticação. O código que se segue autentica-se no seu espaço de trabalho utilizando o objeto auth que acabou de criar.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Auth auth de aprendizagem automática azure

O diretor de serviço criado nos degraus acima também pode ser usado para autenticar a [API de](https://docs.microsoft.com/rest/api/azureml/)Aprendizagem Automática Azure . Utiliza o fluxo de [concessão](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)de credenciais de clientes do Azure Ative Directory, que permite chamadas de serviço a serviço para autenticação sem cabeça em fluxos de trabalho automatizados. Os exemplos são implementados com a [biblioteca ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) tanto em Python como no Node.js, mas também pode usar qualquer biblioteca de código aberto que suporte o OpenID Connect 1.0. 

> [!NOTE]
> MSAL.js é uma biblioteca mais recente do que a ADAL, mas não pode fazer a autenticação serviço-a-serviço usando credenciais de cliente com MSAL.js, uma vez que é principalmente uma biblioteca do lado do cliente destinada à autenticação interativa/UI ligada a um utilizador específico. Recomendamos a utilização do ADAL, como mostrado abaixo, para construir fluxos de trabalho automatizados com a API REST.

### <a name="nodejs"></a>Node.js

Utilize os seguintes passos para gerar um símbolo de auth usando o Node.js. No seu ambiente, corra `npm install adal-node`. Em seguida, use o seu `tenantId`, `clientId`, e `clientSecret` do diretor de serviço que criou nos degraus acima como valores para as variáveis correspondentes no seguinte script.

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

A variável `tokenResponse` é um objeto que inclui o símbolo e metadados associados, tais como o tempo de validade. Os tokens são válidos por 1 hora, e podem ser refrescados executando a mesma chamada novamente para recuperar um novo token. Segue-se uma resposta da amostra.

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

Use a propriedade `accessToken` para buscar o símbolo auth. Consulte a [documentação rest API,](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) por exemplo, sobre como usar o símbolo para fazer chamadas API.

### <a name="python"></a>Python 

Use os seguintes passos para gerar um símbolo de auth usando Python. No seu ambiente, corra `pip install adal`. Em seguida, use o seu `tenantId`, `clientId`, e `clientSecret` do diretor de serviço que criou nos degraus acima como valores para as variáveis apropriadas no seguinte script.

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

A variável `token_response` é um dicionário que inclui o token e metadados associados, como o tempo de validade. Os tokens são válidos por 1 hora, e podem ser refrescados executando a mesma chamada novamente para recuperar um novo token. Segue-se uma resposta da amostra.

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

Use `token_response["accessToken"]` para pegar o símbolo do auth. Consulte a [documentação rest API,](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) por exemplo, sobre como usar o símbolo para fazer chamadas API.

## <a name="web-service-authentication"></a>Autenticação do serviço Web

Os serviços web em Azure Machine Learning utilizam um padrão de autenticação diferente do descrito acima. A forma mais fácil de autenticar os serviços web implantados é utilizar a **autenticação baseada em chaves,** que gera chaves de autenticação estáticas do tipo portador que não precisam de ser renovadas. Se necessitar apenas de autenticar um serviço web implantado, não precisa de configurar a autenticação do princípio do serviço, como mostrado acima.

Os serviços web implantados no Serviço Azure Kubernetes têm um auth baseado em chaves *ativado* por padrão. Os serviços implantados em casos de contentores Azure têm auth baseado em chaves *desativado* por padrão, mas pode capacitá-lo definindo `auth_enabled=True`ao criar o serviço web ACI. Segue-se um exemplo de criação de uma configuração de implementação ACI com auth baseado em chaves ativada.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Em seguida, pode utilizar a configuração ACI personalizada na implementação utilizando a classe `Model`.

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

Para ir buscar as teclas auth, use `aci_service.get_keys()`. Para regenerar uma tecla, utilize a função `regen_key()` e passe tanto **primária como** **secundária**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Os serviços web também suportam a autenticação baseada em token, mas apenas para implementações do Serviço Azure Kubernetes. Consulte o ["como consumir](how-to-consume-web-service.md) serviços web" para obter informações adicionais sobre a autenticação.

### <a name="token-based-web-service-authentication"></a>Autenticação do serviço web baseado em token

Quando ativar a autenticação simbólica para um serviço web, os utilizadores devem apresentar um Web Token Azure Machine Learning JSON ao serviço web para aceder ao mesmo. O símbolo expira após um prazo especificado e precisa de ser atualizado para continuar a fazer chamadas.

* A autenticação token é **desativada por padrão** quando se desloca para o Serviço Azure Kubernetes.
* A autenticação token **não é suportada** quando se implanta nas Instâncias de Contentores Azure.

Para controlar a autenticação simbólica, utilize o parâmetro `token_auth_enabled` quando criar ou atualizar uma implementação.

Se a autenticação simbólica estiver ativada, pode utilizar o método `get_token` para recuperar um Token Web JSON (JWT) e o tempo de validade do token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Terá de pedir um novo sinal depois da hora `refresh_by` do token. Se precisar de atualizar fichas fora do Python SDK, uma opção é utilizar a API REST com autenticação principal de serviço para fazer periodicamente a chamada `service.get_token()`, como discutido anteriormente.
>
> Recomendamos vivamente que crie o seu espaço de trabalho Azure Machine Learning na mesma região que o seu cluster de Serviço Azure Kubernetes. 
>
> Para autenticar com um símbolo, o serviço web fará uma chamada para a região em que o seu espaço de trabalho Azure Machine Learning é criado. Se a região do seu espaço de trabalho não estiver disponível, não poderá obter um símbolo para o seu serviço web, mesmo que o seu cluster esteja numa região diferente do seu espaço de trabalho. O resultado é que a Autenticação AD Azure não está disponível até que a região do seu espaço de trabalho esteja novamente disponível. 
>
> Além disso, quanto maior for a distância entre a região do seu cluster e a região do seu espaço de trabalho, mais tempo demorará a obter um símbolo.

## <a name="next-steps"></a>Passos seguintes

* [Treine e implante um modelo](tutorial-train-models-with-aml.md)de classificação de imagem.
* [Consumir um modelo de Aprendizagem automática Azure implementado como um serviço web.](how-to-consume-web-service.md)
