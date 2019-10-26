---
title: Usar as APIs REST para fazer o CI/CD para Azure Stream Analytics no IoT Edge
description: Saiba como implementar um pipeline de implantação e integração contínua para Azure Stream Analytics usando APIs REST.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 94f124a1b2571a685de9da7f37b2ed50a2860da7
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72925656"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>Implementar CI/CD para Stream Analytics em IoT Edge usando APIs

Você pode habilitar a integração e a implantação contínuas para trabalhos de Azure Stream Analytics usando APIs REST. Este artigo fornece exemplos sobre quais APIs usar e como usá-las. Não há suporte para APIs REST no Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Chamar APIs de ambientes diferentes

As APIs REST podem ser chamadas tanto do Linux quanto do Windows. Os comandos a seguir demonstram a sintaxe correta para a chamada à API. O uso específico da API será descrito nas seções posteriores deste artigo.

### <a name="linux"></a>Linux

Para o Linux, você pode usar os comandos `Curl` ou `Wget`:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” { <url> }   
```

```bash
wget -q -O- --{ <method> }-data="<request body>”--header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Para o Windows, use o PowerShell: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url>-Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Criar um trabalho do ASA no Edge 
 
Para criar Stream Analytics trabalho, chame o método PUT usando a API Stream Analytics.

|Método|URL do Pedido|
|------|-----------|
|Posicione|https://management.azure.com/subscriptions/{**Subscription-ID**}/resourcegroups/{**Resource-Group-Name**}/Providers/Microsoft.StreamAnalytics/streamingjobs/{**nome-** do-trabalho}? API-Version = 2017-04-01-Preview|
 
Exemplo de comando usando **ondulação**:

```curl
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Exemplo de corpo da solicitação em JSON:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
Para obter mais informações, consulte a [documentação da API](/rest/api/streamanalytics/stream-analytics-job).  
 
## <a name="publish-edge-package"></a>Publicar pacote do Edge 
 
Para publicar um trabalho de Stream Analytics no IoT Edge, chame o método POST usando a API de publicação do pacote de borda.

|Método|URL do Pedido|
|------|-----------|
|Postar|https://management.azure.com/subscriptions/{**SubscriptionId**}/resourceGroups/{**resourcegroupname**}/Providers/Microsoft.StreamAnalytics/streamingjobs/{**JobName**}/publishedgepackage? API-Version = 2017-04-01-Preview|

Essa operação assíncrona retorna um status de 202 até que o trabalho tenha sido publicado com êxito. O cabeçalho de resposta de local contém o URI usado para obter o status do processo. Enquanto o processo está em execução, uma chamada para o URI no cabeçalho de local retorna um status de 202. Quando o processo é concluído, o URI no cabeçalho de local retorna um status de 200. 

Exemplo de uma chamada de publicação de pacote de borda usando **ondulação**: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Depois de fazer a chamada POST, você deve esperar uma resposta com um corpo vazio. Procure a URL localizada no cabeçalho da resposta e registre-a para uso posterior.
 
Exemplo da URL do início da resposta:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Uma espera por um a dois minutos antes de executar o comando a seguir para fazer uma chamada à API com a URL encontrada no cabeçalho da resposta. Tente o comando novamente se você não receber uma resposta 200.
 
Exemplo de fazer chamada à API com a URL retornada com **ondulação**:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

A resposta inclui as informações que você precisa para adicionar ao script de implantação do Edge. Os exemplos a seguir mostram quais informações você precisa coletar e onde adicioná-las ao manifesto de implantação.
 
Corpo de resposta de exemplo após a publicação bem-sucedida:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}",”PublishTimeStamp”:”{publishtimestamp}”}}}}" 
  status : "Succeeded" 
} 
```

Exemplo de manifesto de implantação: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

Após a configuração do manifesto de implantação, consulte [implantar módulos de Azure IOT Edge com CLI do Azure](../iot-edge/how-to-deploy-modules-cli.md) para implantação.


## <a name="next-steps"></a>Passos seguintes 
 
* [Azure Stream Analytics no IoT Edge](stream-analytics-edge.md)
* [Tutorial do ASA no IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Desenvolver Stream Analytics trabalhos do Edge usando as ferramentas do Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
