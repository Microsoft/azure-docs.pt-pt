---
title: Use o REST para gerir os recursos ML
titleSuffix: Azure Machine Learning
description: Como utilizar as APIs de REST para criar, executar e eliminar recursos de Aprendizagem automática Azure, como um espaço de trabalho, ou registar modelos.
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 01/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b61050db29ff960b7923f2211b2f09649608b37e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520324"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Criar, executar e eliminar recursos Azure ML usando REST



Existem várias formas de gerir os seus recursos Azure ML. Pode utilizar o [portal,](https://portal.azure.com/) [interface de linha de comando,](/cli/azure)ou Python [SDK](/python/api/overview/azure/ml/intro). Ou, pode escolher a API REST. A API REST utiliza verbos HTTP de forma padrão para criar, recuperar, atualizar e eliminar recursos. A API REST funciona com qualquer idioma ou ferramenta que possa fazer pedidos HTTP. A estrutura simples do REST muitas vezes faz com que seja uma boa escolha em ambientes de script e para a automação de MLOps. 

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Recuperar um sinal de autorização
> * Crie um pedido de REST devidamente formatado utilizando a autenticação principal do serviço
> * Utilize pedidos GET para obter informações sobre os recursos hierárquicos da Azure ML
> * Utilizar pedidos de PUT e POST para criar e modificar recursos
> * Utilize pedidos de DELETE para limpar recursos 
> * Utilize uma autorização baseada em chaves para marcar modelos implantados

## <a name="prerequisites"></a>Pré-requisitos

- Uma **assinatura Azure** para a qual tem direitos administrativos. Se não tiver tal subscrição, experimente a [subscrição pessoal gratuita ou paga](https://aka.ms/AMLFree)
- [Um espaço de trabalho de aprendizagem de máquinas Azure](./how-to-manage-workspace.md)
- Os pedidos de DESCANSO Administrativo utilizam a autenticação principal do serviço. Siga os passos na [Configuração de autenticação para recursos de aprendizagem automática Azure e fluxos de trabalho](./how-to-setup-authentication.md#service-principal-authentication) para criar um principal serviço no seu espaço de trabalho
- A **utilidade do caracol.** O programa **curl** está disponível no [Subsistema Windows para Linux](/windows/wsl/install-win10) ou qualquer distribuição UNIX. Em PowerShell, **curl** é um pseudónimo para **Invoke-WebRequest** e `curl -d "key=val" -X POST uri` torna-se `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` . 

## <a name="retrieve-a-service-principal-authentication-token"></a>Recuperar um sinal de autenticação principal de serviço

Os pedidos de REST Administrativos são autenticados com um fluxo implícito OAuth2. Este fluxo de autenticação utiliza um símbolo fornecido pelo principal de serviço da sua subscrição. Para recuperar este símbolo, vai precisar:

- O seu ID do seu inquilino (identificando a organização a que pertence a sua assinatura)
- ID do seu cliente (que será associado com o token criado)
- Segredo do seu cliente (que deve salvaguardar)

Deverá ter estes valores desde a resposta à criação do seu diretor de serviço. A obtenção destes valores é discutida na [Configuração de autenticação para recursos de aprendizagem automática Azure e fluxos de trabalho.](./how-to-setup-authentication.md#service-principal-authentication) Se estiver a utilizar a subscrição da empresa, poderá não ter permissão para criar um principal de serviço. Nesse caso, deve utilizar uma [subscrição pessoal gratuita ou paga.](https://aka.ms/AMLFree)

Para recuperar um símbolo:

1. Abra uma janela de terminal
1. Introduza o seguinte código na linha de comando
1. Substitua os seus próprios valores por `{your-tenant-id}` `{your-client-id}` , e `{your-client-secret}` . Ao longo deste artigo, as cordas rodeadas por suportes encaracolados são variáveis que terás de substituir pelos teus próprios valores apropriados.
1. Execute o comando

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

A resposta deve fornecer um sinal de acesso bom para uma hora:

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
curl -h "Authorization:Bearer {your-access-token}" ...more args...
```

Note que o valor começa com a cadeia "Bearer", incluindo um único espaço antes de adicionar o token.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Obtenha uma lista de grupos de recursos associados à sua subscrição

Para recuperar a lista de grupos de recursos associados à sua subscrição, corra:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

Através de Azure, muitas APIs rest são publicadas. Cada prestador de serviços atualiza a sua API na sua própria cadência, mas fá-lo sem quebrar os programas existentes. O prestador de serviços utiliza o `api-version` argumento para garantir a compatibilidade. O `api-version` argumento varia de serviço para serviço. Para o Serviço de Aprendizagem automática, por exemplo, a versão atual da API é `2019-11-01` . Para contas de armazenamento, `2019-06-01` é. Para cofres chave, `2019-09-01` é. Todas as chamadas REST devem definir o `api-version` argumento para o valor esperado. Pode confiar na sintaxe e na semântica da versão especificada, mesmo que a API continue a evoluir. Se enviar um pedido a um fornecedor sem o `api-version` argumento, a resposta conterá uma lista de valores suportados por humanos. 

A chamada acima resultará numa resposta compactada do formulário: 

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


## <a name="drill-down-into-workspaces-and-their-resources"></a>Aprofundar em espaços de trabalho e seus recursos

Para recuperar o conjunto de espaços de trabalho num grupo de recursos, executar o seguinte, `{your-subscription-id}` `{your-resource-group}` substituindo, e `{your-access-token}` : 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Mais uma vez receberá uma lista JSON, desta vez contendo uma lista, cada item dos quais detalha um espaço de trabalho:

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

Para trabalhar com recursos dentro de um espaço de trabalho, você mudará do servidor **geral management.azure.com** para um servidor API REST específico para a localização do espaço de trabalho. Note o valor da `discoveryUrl` chave na resposta JSON acima. Se conseguires essa URL, receberás uma resposta como:

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

O valor da `api` resposta é o URL do servidor que irá utilizar para pedidos adicionais. Para listar experiências, por exemplo, enviar o seguinte comando. `regional-api-server`Substitua-se pelo valor da `api` resposta (por exemplo, `centralus.api.azureml.ms` ). Substitua `your-subscription-id` `your-resource-group` também, `your-workspace-name` , e como de `your-access-token` costume:

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

Note que para listar experiências o caminho começa com `history/v1.0` enquanto lista modelos, o caminho começa com `modelmanagement/v1.0` . A API REST está dividida em vários grupos operacionais, cada um com um caminho distinto. 

|Área|Caminho|
|-|-|
|Artefactos|/rest/api/azureml|
|Arquivos de dados|/azul/machine-learning/como aceder aos dados|
|Otimização de hiperparâmetros|hiperdrive/v1.0/|
|Modelos|gestão modelo/v1.0/|
|Histórico de execuções|execução/v1.0/ e história/v1.0/|

Você pode explorar a API REST usando o padrão geral de:

|Componente URL|Exemplo|
|-|-|
| https://| |
| regional-api-servidor/ | centralus.api.azureml.ms/ |
| trajetória de operações/ | história/v1.0/ |
| subscrições/{your-subscription-id}/ | subscrições/abcde123-abab-abab-1234-0123456789abc/ |
| grupos de recursos/{o seu grupo de recursos}/ | grupos de recursos/MyResourceGroup/ |
| fornecedores/fornecedores de operação/ | fornecedores/Microsoft.MachineLearningServices/ |
| caminho de recurso/fornecedor/ | espaços de trabalho/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| ponto final de operações/ | artefactos/metadados/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Criar e modificar recursos utilizando pedidos DE EM e POST

Para além da recuperação de recursos com o verbo GET, a API REST suporta a criação de todos os recursos necessários para formar, implantar e monitorizar as soluções ML. 

Os modelos de formação e funcionamento da ML requerem recursos computacionais. Pode listar os recursos compute de um espaço de trabalho com: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Para criar ou substituir um recurso de computação nomeado, utilizará um pedido PUT. No seguinte, para além das substituições agora conhecidas `your-subscription-id` `your-resource-group` de, `your-workspace-name` `your-access-token` e, `your-compute-name` substitutos, e valores `location` `vmSize` para, `vmPriority` , , , `scaleSettings` e `adminUserName` `adminUserPassword` ... Conforme especificado na referência no [Machine Learning Compute - Create or Update SDK Reference](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate), o seguinte comando cria um Standard_D1 dedicado e único (um recurso computacional básico do CPU) que irá reduzir após 30 minutos:

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
> Nos terminais windows poderá ter de escapar aos símbolos de cotação dupla ao enviar dados JSON. Ou seja, texto como `"location"` se `\"location\"` torne. 

Um pedido bem sucedido obterá uma `201 Created` resposta, mas note que esta resposta significa simplesmente que o processo de provisionamento já começou. Terá de fazer sondagens (ou usar o portal) para confirmar o seu sucesso.

### <a name="create-an-experimental-run"></a>Criar uma execução experimental

Para iniciar uma execução dentro de uma experiência, precisa de uma pasta zip contendo o seu script de treino e ficheiros relacionados, e um ficheiro JSON de definição de execução. A pasta zip deve ter o ficheiro de entrada Python no seu diretório de raiz. Como exemplo, feche um programa trivial python, como o seguinte, numa pasta chamada **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Guarde este próximo corte enquanto **definition.js.** Confirme que o valor "Script" corresponde ao nome do ficheiro Python que acabou de fechar. Confirme que o valor "Target" corresponde ao nome de um recurso de computação disponível. 

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

Publique estes ficheiros no servidor utilizando o `multipart/form-data` conteúdo:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Um pedido de POST bem sucedido gerará um `200 OK` estado, com um organismo de resposta contendo o identificador da execução criada:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

Pode monitorizar uma execução utilizando o padrão REST-ful que agora deve ser familiar:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Eliminar recursos que já não precisa

Alguns, mas não todos, apoiam o verbo DELETE. Consulte a [Referência API](/rest/api/azureml/) antes de se comprometer com a API REST para obter casos de utilização de eliminação. Para eliminar um modelo, por exemplo, pode utilizar:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Use REST para marcar um modelo implantado

Embora seja possível implementar um modelo para que ele autentica com um principal serviço, a maioria das implementações voltadas para o cliente usam a autenticação baseada em chaves. Pode encontrar a chave apropriada na página da sua implementação no **separador Endpoints** do Studio. O mesmo local mostrará o URI de pontuação do seu ponto final. As entradas do seu modelo devem ser modeladas como uma matriz JSON chamada `data` :

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Criar um espaço de trabalho usando REST 

Cada espaço de trabalho Azure ML tem uma dependência de quatro outros recursos Azure: um registo de contentores com administração ativada, um cofre chave, um recurso Application Insights e uma conta de armazenamento. Não se pode criar um espaço de trabalho até que estes recursos existam. Consulte a referência REST API para obter os detalhes da criação de cada recurso.

Para criar um espaço de trabalho, coloque uma chamada semelhante à `management.azure.com` seguinte. Embora esta chamada exija que você estabeleça um grande número de variáveis, é estruturalmente idêntico a outras chamadas que este artigo discutiu. 

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

Deve receber uma `202 Accepted` resposta e, nos cabeçalhos devolvidos, um `Location` URI. Pode obter este URI para obter informações sobre a implantação, incluindo informações úteis de depuração se houver algum problema com um dos seus recursos dependentes (por exemplo, se se esqueceu de permitir o acesso administrativo ao seu registo de contentores). 

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="resource-provider-errors"></a>Erros do fornecedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Mover o espaço de trabalho

> [!WARNING]
> Mover o seu espaço de trabalho Azure Machine Learning para uma subscrição diferente, ou mover a subscrição proprietária para um novo inquilino, não é suportado. Fazê-lo pode causar erros.

### <a name="deleting-the-azure-container-registry"></a>Eliminação do Registo do Contentor de Azure

O espaço de trabalho Azure Machine Learning utiliza o Registo do Contentor Azure (ACR) para algumas operações. Criará automaticamente uma instância ACR quando precisa primeiro de uma.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Passos seguintes

- Explore a referência completa da [AzureML REST API](/rest/api/azureml/).
- Aprenda a usar o designer para prever o [preço do automóvel com o designer.](./tutorial-designer-automobile-price-train-score.md)
- Explore [a Azure Machine Learning com cadernos Jupyter.](..//machine-learning/samples-notebooks.md)
