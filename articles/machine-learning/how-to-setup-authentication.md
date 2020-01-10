---
title: Configurar autenticação
titleSuffix: Azure Machine Learning
description: Saiba como configurar e configurar a autenticação para vários recursos e fluxos de trabalho no Azure Machine Learning. Há várias maneiras de configurar e usar a autenticação no serviço, desde a simples autenticação baseada em interface do usuário para fins de desenvolvimento ou teste até a autenticação completa da entidade de serviço Azure Active Directory.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: c3da9c6a49fd79946d62b0319bead1bd721f3aa6
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75536710"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Configurar a autenticação para Azure Machine Learning recursos e fluxos de trabalho
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a configurar e configurar a autenticação para vários recursos e fluxos de trabalho no Azure Machine Learning. Há várias maneiras de se autenticar no serviço, desde a autenticação simples baseada na interface do usuário para fins de desenvolvimento ou teste até a autenticação total Azure Active Directory entidade de serviço. Este artigo também explica as diferenças em como a autenticação de serviço Web funciona, bem como a autenticação para a API REST do Azure Machine Learning.

Este "como" mostra como realizar as seguintes tarefas:

* Usar autenticação interativa da interface do usuário para teste/desenvolvimento
* Configurar a autenticação da entidade de serviço
* Autenticando no seu espaço de trabalho
* Obter tokens de tipo de portador OAuth 2.0 para Azure Machine Learning API REST
* Entender a autenticação do serviço Web

Consulte o [artigo conceito](concept-enterprise-security.md) para obter uma visão geral da segurança e da autenticação no Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

* Crie um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).
* [Configure seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o sdk do Azure Machine Learning ou use uma [VM do Azure Machine Learning Notebook](concept-azure-machine-learning-architecture.md#compute-instance) com o SDK já instalado.

## <a name="interactive-authentication"></a>Autenticação interativa

A maioria dos exemplos na documentação para esse serviço usa a autenticação interativa em notebooks Jupyter como um método simples para teste e demonstração. Essa é uma maneira leve de testar o que você está criando. Há duas chamadas de função que irão solicitar automaticamente um fluxo de autenticação baseado em interface do usuário.

Chamar a função `from_config()` emitirá o prompt.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

A função `from_config()` procura um arquivo JSON que contém as informações de conexão do espaço de trabalho. Você também pode especificar os detalhes da conexão explicitamente usando o Construtor `Workspace`, que também solicitará a autenticação interativa. Ambas as chamadas são equivalentes.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Se você tiver acesso a vários locatários, talvez seja necessário importar a classe e definir explicitamente qual locatário você está direcionando. Chamar o construtor para `InteractiveLoginAuthentication` também solicitará que você faça logon semelhante às chamadas acima.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Embora seja útil para teste e aprendizado, a autenticação interativa não ajudará você a criar fluxos de trabalho automatizados ou sem periféricos. A configuração da autenticação da entidade de serviço é a melhor abordagem para processos automatizados que usam o SDK.

## <a name="set-up-service-principal-authentication"></a>Configurar a autenticação da entidade de serviço

Esse processo é necessário para habilitar a autenticação que é dissociada de um logon de usuário específico, que permite que você se autentique no SDK do Python Azure Machine Learning em fluxos de trabalho automatizados. A autenticação da entidade de serviço também permitirá que você se [autentique na API REST](#azure-machine-learning-rest-api-auth).

Para configurar a autenticação de entidade de serviço, primeiro crie um registro de aplicativo no Azure Active Directory e conceda ao seu aplicativo o acesso baseado em função ao seu espaço de trabalho am. A maneira mais fácil de concluir essa configuração é por meio do [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/) no portal do Azure. Depois de fazer logon no portal, clique no ícone de `>_` no canto superior direito da página perto do seu nome para abrir o Shell.

Se você não tiver usado o Cloud Shell antes em sua conta do Azure, será necessário criar um recurso de conta de armazenamento para armazenar todos os arquivos que forem gravados. Em geral, essa conta de armazenamento incorrerá em um custo mensal insignificante. Além disso, instale a extensão de Machine Learning se você não a tiver usado anteriormente com o comando a seguir.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Você deve ser um administrador na assinatura para executar as etapas a seguir.

Em seguida, execute o comando a seguir para criar a entidade de serviço. Dê um nome a ele, nesse caso, **ml-auth**.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

A saída será um JSON semelhante ao seguinte. Anote os campos `clientId`, `clientSecret`e `tenantId`, pois você precisará deles para outras etapas neste artigo.

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

Em seguida, execute o seguinte comando para obter os detalhes sobre a entidade de serviço que você acabou de criar, usando o valor `clientId` acima como a entrada para o parâmetro `--id`.

```azurecli-interactive
az ad sp show --id your-client-id
```

Veja a seguir um exemplo simplificado da saída JSON do comando. Anote o campo `objectId`, pois você precisará de seu valor para a próxima etapa.

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

Em seguida, use o comando a seguir para atribuir o acesso de sua entidade de serviço ao seu espaço de trabalho do Machine Learning. Você precisará do nome do seu espaço de trabalho e do nome do grupo de recursos para os parâmetros `-w` e `-g`, respectivamente. Para o parâmetro `--user`, use o valor `objectId` da etapa anterior. O parâmetro `--role` permite que você defina a função de acesso para a entidade de serviço e, em geral, você usará o **proprietário** ou o **colaborador**. Ambos têm acesso de gravação a recursos existentes, como clusters de computação e repositórios de armazenamento, mas somente o **proprietário** pode provisionar esses recursos. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Essa chamada não produz nenhuma saída, mas agora você tem a autenticação da entidade de serviço configurada para seu espaço de trabalho.

## <a name="authenticate-to-your-workspace"></a>Autenticar no seu espaço de trabalho

Agora que você tem a autenticação de entidade de serviço habilitada, é possível autenticar o seu espaço de trabalho no SDK sem fazer logon fisicamente como um usuário. Use o construtor da classe `ServicePrincipalAuthentication` e use os valores obtidos das etapas anteriores como os parâmetros. O parâmetro `tenant_id` é mapeado para `tenantId` acima, `service_principal_id` mapeia para `clientId`e `service_principal_password` mapeia para `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

A variável `sp` agora mantém um objeto de autenticação que você usa diretamente no SDK. Em geral, é uma boa ideia armazenar as IDs/segredos usados acima em variáveis de ambiente, conforme mostrado no código a seguir.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Para fluxos de trabalho automatizados executados no Python e usar o SDK principalmente, você pode usar esse objeto no estado em que se encontra na maioria dos casos para sua autenticação. O código a seguir é autenticado em seu espaço de trabalho usando o objeto de autenticação que você acabou de criar.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Autenticação da API REST do Azure Machine Learning

A entidade de serviço criada nas etapas acima também pode ser usada para autenticar para a [API REST](https://docs.microsoft.com/rest/api/azureml/)do Azure Machine Learning. Você usa o [fluxo de concessão de credenciais de cliente](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)Azure Active Directory, que permite chamadas de serviço a serviço para autenticação sem periféricos em fluxos de trabalho automatizados. Os exemplos são implementados com a [biblioteca Adal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) no Python e no node. js, mas você também pode usar qualquer biblioteca de software livre que ofereça suporte ao OpenID Connect 1,0. 

> ! ANOTAÇÕES MSAL. js é uma biblioteca mais recente do que a ADAL, mas você não pode fazer a autenticação serviço a serviço usando as credenciais do cliente com o MSAL. js, pois ela é basicamente uma biblioteca do lado do cliente destinada à autenticação interativa/de interface do usuário vinculada a um User específico. É recomendável usar a ADAL, conforme mostrado abaixo, para criar fluxos de trabalho automatizados com a API REST.

### <a name="nodejs"></a>Node.js

Use as etapas a seguir para gerar um token de autenticação usando o Node. js. Em seu ambiente, execute `npm install adal-node`. Em seguida, use o `tenantId`, `clientId`e `clientSecret` da entidade de serviço que você criou nas etapas acima como valores para as variáveis correspondentes no script a seguir.

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

A variável `tokenResponse` é um objeto que inclui o token e os metadados associados, como o tempo de expiração. Os tokens são válidos por 1 hora e podem ser atualizados executando a mesma chamada novamente para recuperar um novo token. Veja a seguir uma resposta de exemplo.

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

Use a propriedade `accessToken` para buscar o token de autenticação. Consulte a [documentação da API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) para obter exemplos de como usar o token para fazer chamadas à API.

### <a name="python"></a>Python 

Use as etapas a seguir para gerar um token de autenticação usando o Python. Em seu ambiente, execute `pip install adal`. Em seguida, use o `tenantId`, `clientId`e `clientSecret` da entidade de serviço que você criou nas etapas acima como valores para as variáveis apropriadas no script a seguir.

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

A variável `token_response` é um dicionário que inclui o token e os metadados associados, como o tempo de expiração. Os tokens são válidos por 1 hora e podem ser atualizados executando a mesma chamada novamente para recuperar um novo token. Veja a seguir uma resposta de exemplo.

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

Use `token_response["accessToken"]` para buscar o token de autenticação. Consulte a [documentação da API REST](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) para obter exemplos de como usar o token para fazer chamadas à API.

## <a name="web-service-authentication"></a>Autenticação de serviço Web

Os serviços Web no Azure Machine Learning usam um padrão de autenticação diferente do que é descrito acima. A maneira mais fácil de autenticar para os serviços Web implantados é usar a **autenticação baseada em chave**, que gera chaves de autenticação de tipo de portador estático que não precisam ser atualizadas. Se você só precisa se autenticar em um serviço Web implantado, não é necessário configurar a autenticação de entidade de serviço, conforme mostrado acima.

Os serviços Web implantados no serviço kubernetes do Azure têm a autenticação baseada em chave *habilitada* por padrão. Os serviços implantados das instâncias de contêiner do Azure têm a autenticação baseada em chave *desabilitada* por padrão, mas você pode habilitá-la configurando `auth_enabled=True`ao criar o serviço Web ACI. Veja a seguir um exemplo de como criar uma configuração de implantação ACI com a autenticação baseada em chave habilitada.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enable=True)
```

Em seguida, você pode usar a configuração de ACI personalizada na implantação usando a classe de `WebService` pai.

```python
from azureml.core.webservice import Webservice

aci_service = Webservice.deploy_from_image(deployment_config=aci_config,
                                           image=image,
                                           name="aci_service_sample",
                                           workspace=ws)
aci_service.wait_for_deployment(True)
```

Para buscar as chaves de autenticação, use `aci_service.get_keys()`. Para regenerar uma chave, use a função `regen_key()` e passe **principal** ou **secundária**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Os serviços Web também oferecem suporte à autenticação baseada em token, mas somente para implantações do serviço kubernetes do Azure. Confira o [instruções sobre como](how-to-consume-web-service.md) consumir os serviços da Web para obter informações adicionais sobre como autenticar.

### <a name="token-based-web-service-authentication"></a>Autenticação de serviço Web baseada em token

Quando você habilita a autenticação de token para um serviço Web, os usuários devem apresentar um Azure Machine Learning token Web JSON ao serviço Web para acessá-lo. O token expira após um período de tempo especificado e precisa ser atualizado para continuar fazendo chamadas.

* A autenticação de token é **desabilitada por padrão** quando você implanta no serviço kubernetes do Azure.
* **Não há suporte** para autenticação de token quando você implanta em instâncias de contêiner do Azure.

Para controlar a autenticação de tokens, use o parâmetro `token_auth_enabled` ao criar ou atualizar uma implantação.

Se a autenticação de token estiver habilitada, você poderá usar o método `get_token` para recuperar um token Web JSON (JWT) e o tempo de expiração desse token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Você precisará solicitar um novo token após o tempo de `refresh_by` do token. Se você precisar atualizar os tokens fora do SDK do Python, uma opção é usar a API REST com a autenticação de entidade de serviço para fazer a chamada de `service.get_token()` periodicamente, conforme discutido anteriormente.
>
> É altamente recomendável que você crie seu espaço de trabalho do Azure Machine Learning na mesma região que o cluster do serviço kubernetes do Azure. 
>
> Para autenticar com um token, o serviço Web fará uma chamada para a região em que seu espaço de trabalho Azure Machine Learning é criado. Se a região do seu espaço de trabalho estiver indisponível, você não poderá buscar um token para o serviço Web, mesmo que o cluster esteja em uma região diferente do seu espaço de trabalho. O resultado é que a autenticação do Azure AD não está disponível até que a região do seu espaço de trabalho esteja disponível novamente. 
>
> Além disso, quanto maior a distância entre a região do cluster e a região do seu espaço de trabalho, mais tempo será levado para buscar um token.

## <a name="next-steps"></a>Passos seguintes

* [Treine e implante um modelo de classificação de imagem](tutorial-train-models-with-aml.md).
* [Consumir um modelo de Azure Machine Learning implantado como um serviço Web](how-to-consume-web-service.md).