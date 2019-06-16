---
title: Implementar o CI/CD para o Azure Stream Analytics do IoT Edge através de REST APIs
description: Saiba como implementar um pipeline de implementação e integração contínua para o Azure Stream Analytics com REST APIs.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 40beb620e037061b189762a51e3c29d0fd251b27
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61362081"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>Implementar o CI/CD para o Stream Analytics do IoT Edge com APIs

Pode ativar a integração contínua e implementação para as tarefas de Azure Stream Analytics com REST APIs. Este artigo fornece exemplos sobre quais APIs a utilizar e como utilizá-los. REST APIs não são suportadas no Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Chamar as APIs de ambientes diferentes

REST APIs pode ser chamadas a partir do Linux e Windows. Os comandos seguintes demonstram a sintaxe correta para a chamada de API. Utilização da API específica irá ser descrita nas secções posteriores deste artigo.

### <a name="linux"></a>Linux

Para o Linux, pode utilizar `Curl` ou `Wget` comandos:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” { <url> }   
```

```bash
wget -q -O- --{ <method> }-data="<request body>”--header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Para Windows, utilize o Powershell: 

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
 
## <a name="create-an-asa-job-on-edge"></a>Criar uma tarefa ASA do Edge 
 
Para criar a tarefa do Stream Analytics, chame o método PUT com a API do Stream Analytics.

|Método|URL do Pedido|
|------|-----------|
|PUT|https://management.azure.com/subscriptions/{**id de subscrição**} /resourcegroups/ {**nome de grupo de recursos**} / providers/Microsoft.StreamAnalytics/streamingjobs/ {**nome da tarefa**}? api-version = 2017-04-01-pré-visualização|
 
Exemplo de como utilizar o comando **curl**:

```curl
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Exemplo do corpo do pedido em JSON:

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
 
## <a name="publish-edge-package"></a>Publicar o pacote do Edge 
 
Para publicar uma tarefa do Stream Analytics do IoT Edge, chame o método POST com a API de publicar pacote do Edge.

|Método|URL do Pedido|
|------|-----------|
|POST|https://management.azure.com/subscriptions/{**subscriptionid**} /resourceGroups/ {**resourcegroupname**} / providers/Microsoft.StreamAnalytics/streamingjobs/ {**jobname**} / publishedgepackage? api-version = 2017-04-01 - pré-visualização|

Esta operação assíncrona retorna um status de 202 até que a tarefa foi publicada com êxito. O cabeçalho de resposta de localização contém o URI usado para obter o estado do processo. Enquanto o processo está em execução, uma chamada para o URI no cabeçalho location retorna um status de 202. Quando o processo estar concluído, o URI no cabeçalho location retorna um status de 200. 

Exemplo de um pacote do Edge publicar através de chamada **curl**: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Depois de fazer a chamada POST, deve esperar uma resposta com um corpo vazio. Procure o localizados no cabeçalho da resposta de URL e registe-a para uso posterior.
 
Exemplo do URL do cabeçalho de resposta:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Uma espera para um ou dois minutos antes de executar o seguinte comando para efetuar uma chamada à API com o URL encontrado no cabeçalho da resposta. Repita o comando, se não obtém uma resposta 200.
 
Exemplo de efetuar a chamada de API com devolvido o URL com **curl**:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

A resposta inclui as informações que necessárias para adicionar para o script de implementação do Edge. Os exemplos abaixo mostram o que informações de que precisa para recolher e a localização para adicioná-lo na implementação do manifesto.
 
Corpo de resposta de exemplo após a publicação com êxito:

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

Após a configuração do manifesto de implantação, consulte [módulos de implementar o Azure IoT Edge com a CLI do Azure](../iot-edge/how-to-deploy-modules-cli.md) para implementação.


## <a name="next-steps"></a>Passos Seguintes 
 
* [O Azure Stream Analytics do IoT Edge](stream-analytics-edge.md)
* [ASA do tutorial do IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Desenvolver trabalhos do Edge do Stream Analytics com ferramentas do Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
