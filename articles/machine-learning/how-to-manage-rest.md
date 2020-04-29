---
title: Use o REST para gerir os recursos ml
titleSuffix: Azure Machine Learning
description: Como utilizar ASAP REST para criar, executar e eliminar recursos Do Azure ML
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 419dbd998abc5cbd2da64a990e13d46f3fb2efbe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77580633"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Criar, executar e eliminar recursos Do Azure ML utilizando o REST

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Existem várias formas de gerir os seus recursos Azure ML. Pode utilizar o [portal](https://portal.azure.com/), [interface de linha de comando,](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)ou Python [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Ou pode escolher a API rest. O REST API utiliza verbos HTTP de forma padrão para criar, recuperar, atualizar e eliminar recursos. A API REST trabalha com qualquer idioma ou ferramenta que possa fazer pedidos HTTP. A estrutura simples do REST muitas vezes faz com que seja uma boa escolha em ambientes de scripte e para automação mlops. 

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Recuperar um símbolo de autorização
> * Criar um pedido DE REST devidamente formatado utilizando a autenticação do principal serviço
> * Utilize pedidos get para recuperar informações sobre os recursos hierárquicos do Azure ML
> * Utilizar pedidos DE PUT e POST para criar e modificar recursos
> * Utilize pedidos DELETE para limpar recursos 
> * Utilize uma autorização baseada em chaves para pontuar modelos implantados

## <a name="prerequisites"></a>Pré-requisitos

- Uma **subscrição Azure** para a qual tem direitos administrativos. Se não tiver tal subscrição, experimente a [subscrição pessoal gratuita ou paga](https://aka.ms/AMLFree)
- Um espaço de trabalho de [aprendizagem automática azure](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- Pedidos de REPOUSO Administrativo utilizam autenticação principal do serviço. Siga os passos em [Configuração da autenticação para os recursos de Aprendizagem automática azure e fluxos de trabalho](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) para criar um diretor de serviço no seu espaço de trabalho
- A utilidade **do caracol.** O programa **curl** está disponível no [Subsistema Windows para Linux](https://aka.ms/wslinstall/) ou qualquer distribuição UNIX. No PowerShell, **o curl** é um pseudónimo `curl -d "key=val" -X POST uri` `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`para **Invoke-WebRequest e torna-se** . 

## <a name="retrieve-a-service-principal-authentication-token"></a>Recuperar um símbolo de autenticação principal de serviço

Os pedidos de REPOUSO Administrativo são autenticados com um fluxo implícito OAuth2. Este fluxo de autenticação utiliza um símbolo fornecido pelo diretor de serviço da sua subscrição. Para recuperar este símbolo, vai precisar de:

- Identificação do seu inquilino (identificando a organização a que pertence a sua subscrição)
- O ID do seu cliente (que será associado com o símbolo criado)
- O segredo do seu cliente (que deve salvaguardar)

Deverá ter estes valores desde a resposta à criação do seu diretor de serviço, tal como discutido na [autenticação de Dispositivos de Aprendizagem automática do Azure.](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) Se estiver a usar a subscrição da sua empresa, poderá não ter permissão para criar um diretor de serviço. Nesse caso, deve utilizar uma [subscrição pessoal gratuita ou paga.](https://aka.ms/AMLFree)

Para recuperar um símbolo:

1. Abra uma janela de terminal
1. Introduza o seguinte código na linha de comando
1. Substitua os `{your-tenant-id}`seus `{your-client-id}`próprios valores por, e `{your-client-secret}`. Ao longo deste artigo, cordas rodeadas por suportes encaracolados são variáveis que terá de substituir pelos seus próprios valores apropriados.
1. Execute o comando

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

A resposta deve fornecer um sinal de acesso bom durante uma hora:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Tome nota do símbolo, pois irá usá-lo para autenticar todos os pedidos administrativos subsequentes. Fá-lo-á definindo um cabeçalho de autorização em todos os pedidos:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Note que o valor começa com a corda "Bearer" incluindo um único espaço antes de adicionar o símbolo.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Obtenha uma lista de grupos de recursos associados à sua subscrição

Para recuperar a lista de grupos de recursos associados à sua subscrição, executar:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

Através do Azure, muitas APIs rest são publicadas. Cada prestador de serviços atualiza a sua API na sua própria cadência, mas fá-lo sem quebrar os programas existentes. O prestador de `api-version` serviços utiliza o argumento para garantir a compatibilidade. O `api-version` argumento varia de serviço para serviço. Para o Serviço de Aprendizagem automática, por `2019-11-01`exemplo, a versão Atual da API é . Para contas de `2019-06-01`armazenamento, é. Para cofres chave, `2019-09-01`é. Todas as chamadas `api-version` REST devem definir o argumento para o valor esperado. Pode contar com a sintaxe e a semântica da versão especificada, mesmo que a API continue a evoluir. Se enviar um pedido a `api-version` um fornecedor sem o argumento, a resposta conterá uma lista legível pelo homem de valores suportados. 

A chamada acima resultará numa resposta compacta da forma JSON: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Aperte em espaços de trabalho e seus recursos

Para recuperar o conjunto de espaços de trabalho num `{your-subscription-id}`grupo `{your-resource-group}`de `{your-access-token}`recursos, executar o seguinte, substituindo, e: 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Mais uma vez receberá uma lista JSON, desta vez contendo uma lista, cada item que detalha um espaço de trabalho:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Para trabalhar com recursos dentro de um espaço de trabalho, você mudará do servidor **geral management.azure.com** para um servidor REST API específico para a localização do espaço de trabalho. Note o valor `discoveryUrl` da chave na resposta json acima. Se receber estoque, receberá uma resposta como:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

O valor `api` da resposta é o URL do servidor que utilizará para pedidos adicionais. Para listar experiências, por exemplo, envie o seguinte comando. Substitua `regional-api-server` pelo valor `api` da resposta `centralus.api.azureml.ms`(por exemplo, ). Substitua `your-subscription-id` `your-resource-group`também, `your-workspace-name` `your-access-token` e como sempre:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Da mesma forma, para recuperar modelos registados no seu espaço de trabalho, envie:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Note que para listar `history/v1.0` experiências o caminho começa `modelmanagement/v1.0`com enquanto lista modelos, o caminho começa com . A API REST está dividida em vários grupos operacionais, cada um com um caminho distinto. Os docs de referência da API nos links abaixo listam as operações, parâmetros e códigos de resposta para as várias operações.

|Área|Caminho|Referência|
|-|-|-|
|Artefactos|artefacto/v2.0/|[Referência da API REST](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|Lojas de dados|datastore/v1.0/|[Referência da API REST](https://docs.microsoft.com/rest/api/azureml/datastores)|
|Otimização de hiperparâmetros|hiperdrive/v1.0/|[Referência da API REST](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|Modelos|modelge/v1.0/|[Referência da API REST](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|Histórico de execuções|execução/v1.0/ e história/v1.0/|[Referência da API REST](https://docs.microsoft.com/rest/api/azureml/runs)|

Você pode explorar a API REST usando o padrão geral de:

|Componente URL|Exemplo|
|-|-|
| https://| |
| regional-api-server/ | centralus.api.azureml.ms/ |
| caminho de operações/ | história/v1.0/ |
| subscrições/{your-subscrição-id}/ | subscrições/abcde123-abab-abab-1234-0123456789abc/ |
| recursosGroups/{your-resource-group}/ | recursosGroups/MyResourceGroup/ |
| fornecedores/fornecedores de operações/ | fornecedores/Microsoft.MachineLearningServices/ |
| caminho do fornecedor-recursos/ | espaços de trabalho/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| ponto final de operações/ | artefactos/metadados/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Criar e modificar recursos utilizando pedidos DE PUT e POST

Além da recuperação de recursos com o verbo GET, a API REST apoia a criação de todos os recursos necessários para treinar, implantar e monitorizar as soluções ML. 

Os modelos ML de formação e execução requerem recursos de computação. Pode listar os recursos computacionais de um espaço de trabalho com: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Para criar ou substituir um recurso computacional nomeado, utilizará um pedido DE PUT. Nas seguintes substituições agora `your-subscription-id`familiares de, `your-resource-group` `your-workspace-name` `your-access-token`e , `your-compute-name`substituto, e `location` `vmSize`valores `scaleSettings` `adminUserName`para, `adminUserPassword`, `vmPriority`, , , e . Conforme especificado na referência na [Machine Learning Compute - Create or Update SDK Reference](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate), o seguinte comando cria um Standard_D1 dedicado e de nó único (um recurso de computação cpu básico) que irá diminuir após 30 minutos:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> Nos terminais Windows poderá ter de escapar aos símbolos de dupla cotação ao enviar dados da JSON. Ou seja, texto `"location"` `\"location\"`como se torna . 

Um pedido bem `201 Created` sucedido receberá uma resposta, mas note que esta resposta significa simplesmente que o processo de provisionamento já começou. Terá de fazer sondagens (ou usar o portal) para confirmar a sua conclusão com sucesso.

### <a name="create-an-experimental-run"></a>Criar uma corrida experimental

Para iniciar uma execução dentro de uma experiência, precisa de uma pasta zip contendo o seu script de treino e ficheiros relacionados, e um ficheiro JSON de definição de execução. A pasta zip deve ter o ficheiro de entrada Python no seu diretório raiz. Como exemplo, zip um programa trivial Python, como o seguinte em uma pasta chamada **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Guarde este próximo corte como **definição.json**. Confirme o valor do "Script" corresponde ao nome do ficheiro Python que acabou de fechar. Confirme o valor "Target" corresponde ao nome de um recurso computacional disponível. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

Publique estes ficheiros `multipart/form-data` no servidor utilizando conteúdo:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Um pedido post bem `200 OK` sucedido gerará um estado, com um corpo de resposta contendo o identificador da execução criada:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

Pode monitorizar uma corrida utilizando o padrão REST-ful que deve agora ser familiar:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Eliminar recursos que já não precisa

Alguns, mas não todos, os recursos apoiam o verbo DELETE. Verifique a [Referência API](https://docs.microsoft.com/rest/api/azureml/) antes de se comprometer com a API REST para obter casos de utilização de eliminação. Para eliminar um modelo, por exemplo, pode utilizar:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Use o REST para marcar um modelo implantado

Embora seja possível implementar um modelo para que se autentique com um diretor de serviço, a maioria das implementações voltadas para o cliente utilizam a autenticação baseada na chave. Pode encontrar a chave adequada na página da sua implementação dentro do separador **Endpoints** do Studio. O mesmo local mostrará o seu ponto final a marcar URI. As inputs do seu modelo devem ser modeladas como uma matriz JSON chamada: `data`

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Criar um espaço de trabalho usando o REST 

Cada espaço de trabalho Azure ML tem uma dependência de quatro outros recursos Azure: um registo de contentores com administração habilitada, um cofre chave, um recurso Application Insights e uma conta de armazenamento. Não se pode criar um espaço de trabalho até que estes recursos existam. Consulte a referência rest API para obter os detalhes da criação de cada recurso.

Para criar um espaço de trabalho, COLOQUE `management.azure.com`uma chamada semelhante à seguinte a . Embora esta chamada exija que você detetete um grande número de variáveis, é estruturalmente idêntico a outras chamadas que este artigo tem discutido. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Deve receber `202 Accepted` uma resposta e, nos cabeçalhos devolvidos, um `Location` URI. Pode obter este URI para obter informações sobre a implementação, incluindo informações úteis de depuração se houver algum problema com um dos seus recursos dependentes (por exemplo, se se esqueceu de permitir o acesso da administração no seu registo de contentores). 

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="resource-provider-errors"></a>Erros do fornecedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Movendo o espaço de trabalho

> [!WARNING]
> A mudança do seu espaço de trabalho Azure Machine Learning para uma subscrição diferente, ou a mudança da subscrição própria para um novo inquilino, não é suportada. Fazê-lo pode causar erros.

### <a name="deleting-the-azure-container-registry"></a>Apagando o Registo de Contentores Azure

O espaço de trabalho Azure Machine Learning utiliza o Registo de Contentores Azure (ACR) para algumas operações. Criará automaticamente uma instância ACR quando precisar primeiro de uma.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Passos seguintes

- Explore a referência completa da [API Do REST AzureML](https://docs.microsoft.com/rest/api/azureml/).
- Aprenda a usar o Studio & Designer para [prever o preço do automóvel com o designer (pré-visualização)](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score).
- Explore o [Azure Machine Learning com os cadernos Jupyter.](https://docs.microsoft.com/azure//machine-learning/samples-notebooks)
