---
title: As atualizações do esquema de pré-visualização de 1 de Agosto 2015 - Azure Logic Apps | Documentos da Microsoft
description: Esquema atualizado versão 2015-08-01-pré-visualização para definições de aplicação lógica no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: 92f522c72f69218e55b1ee4cfff74511a30288b0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57904546"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Atualizações do esquema para o Azure Logic Apps - pré-visualização de 1 de Agosto de 2015

Este esquema e a API de versão para o Azure Logic Apps inclui os principais melhoramentos que tornam as aplicações lógicas mais confiável e fácil de utilizar:

* O **APIApp** tipo de ação tem agora o nome [ **APIConnection**](#api-connections).
* O **repetir** ação tem agora o nome [ **Foreach**](#foreach).
* O [ **serviço de escuta de HTTP** aplicação API](#http-listener) já não é necessário.
* Chamar fluxos de trabalho subordinado utiliza um [novo esquema](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Mover para ligações de API

A maior alteração é que já não tem de implementar aplicações de API na sua subscrição do Azure, para que pode usar APIs. Aqui estão as formas que pode usar APIs:

* APIs geridas
* Suas APIs Web personalizado

Cada forma é processada de forma um pouco diferente porque a gestão e modelos de hospedagem são diferentes. Uma vantagem desse modelo é que já não está restrito para recursos que são implementados no seu grupo de recursos do Azure. 

### <a name="managed-apis"></a>APIs geridas

Microsoft gerencia algumas APIs em seu nome, como o Office 365, Salesforce, Twitter e FTP. Pode usar algumas APIs gerenciadas como-, como o Bing traduzidas, enquanto outras precisam de configuração, também é denominado um *ligação*.

Por exemplo, quando utiliza o Office 365, tem de criar uma ligação que inclui o token de início de sessão do Office 365. O token em segurança é armazenado e atualizado para que a aplicação lógica pode sempre chamar a API do Office 365. Se pretender ligar ao seu servidor SQL ou FTP, tem de criar uma ligação com a cadeia de ligação. 

Esta definição, estas ações são chamadas `APIConnection`. Eis um exemplo de uma ligação que chama o Office 365 para enviar uma mensagem de e-mail:

``` json
{
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "host": {
               "api": {
                  "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
               },
               "connection": {
                  "name": "@parameters('$connections')['shared_office365']['connectionId']"
               }
            },
            "method": "POST",
            "body": {
               "Subject": "Reminder",
               "Body": "Don't forget!",
               "To": "me@contoso.com"
            },
            "path": "/Mail"
         }
      }
   }
}
```

O `host` objeto é uma parte das entradas que é exclusivo para ligações de API e contém essas partes: `api` e `connection`. O `api` objeto Especifica o tempo de execução do URL para onde o API que gerenciada está alojada. Pode ver todas as APIs gerenciadas disponíveis ao chamar este método:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

Quando utiliza uma API, essa API poderá ou poderá não definida qualquer *parâmetros de ligação*. Por isso, não se a API não definir estes parâmetros, é necessária nenhuma ligação. Se a API definir estes parâmetros, tem de criar uma ligação com um nome especificado.  
, Em seguida, fazer referência a esse nome na `connection` dentro de objeto a `host` objeto. Para criar uma ligação num grupo de recursos, chame este método:

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Com o corpo do seguinte:

``` json
{
   "properties": {
      "api": {
         "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/azureblob"
      },
      "parameterValues": {
         "accountName": "<Azure-storage-account-name-with-different-parameters-for-each-API>"
      }
   },
   "location": "<logic-app-location>"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Implementar APIs gerenciadas num modelo Azure Resource Manager

Quando o início de sessão interativo não é necessário, pode criar uma aplicação completa utilizando um modelo do Resource Manager.
Se o início de sessão é necessário, pode continuar a utilizar um modelo do Resource Manager, mas terá de autorizar as ligações através do portal do Azure. 

``` json
"resources": [ {
   "apiVersion": "2015-08-01-preview",
   "name": "azureblob",
   "type": "Microsoft.Web/connections",
   "location": "[resourceGroup().location]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
      },
      "parameterValues": {
         "accountName": "[parameters('storageAccountName')]",
         "accessKey": "[parameters('storageAccountKey')]"
      }
    },
},
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2015-08-01-preview",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"],
   "properties": {
      "sku": {
         "name": "[parameters('sku')]",
         "plan": {
            "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('svcPlanName'))]"
         }
      },
      "parameters": {
         "$connections": {
             "value": {
                  "azureblob": {
                     "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                     "connectionName": "azureblob",
                     "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                  }
             }
         }
      },
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "type": "Object",
            "$connections": {
               "defaultValue": {},
 
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Day",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Create_file": {
               "type": "ApiConnection",
               "inputs": {
                  "host": {
                     "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                     },
                     "connection": {
                       "name": "@parameters('$connections')['azureblob']['connectionId']"
                     }
                  },
                  "method": "POST",
                  "queries": {
                     "folderPath": "[concat('/', parameters('containerName'))]",
                     "name": "helloworld.txt"
                  },
                  "body": "@decodeDataUri('data:, Hello+world!')",
                  "path": "/datasets/default/files"
               },
               "conditions": []
            }
         },
         "outputs": {}
      }
   }
} ]
```

Pode ver neste exemplo, que as ligações são apenas recursos que residem no seu grupo de recursos. Fizerem referência a APIs gerenciadas disponíveis na sua subscrição.

### <a name="your-custom-web-apis"></a>Suas APIs Web personalizado

Se utilizar as suas próprias APIs em vez de aplicações geridas pela Microsoft, utilize o incorporado **HTTP** ação para chamar as suas APIs. Idealmente, deve fornecer um ponto de final de Swagger para a sua API. Este ponto final ajuda Estruturador da aplicação lógica Mostrar entradas e saídas da sua API. Sem um ponto final de Swagger, o designer pode mostrar apenas as entradas e saídas como objetos JSON opacos.

Eis um exemplo que mostra o novo `metadata.apiDefinitionUrl` propriedade:

``` json
"actions": {
   "mycustomAPI": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
      },
      "inputs": {
         "uri": "https://mysite.azurewebsites.net/api/getsomedata",
         "method": "GET"
      }
   }
}
```

Se alojar a API Web no App Service do Azure, a API Web é apresentada automaticamente na lista de ações disponíveis no designer. Caso contrário, terá de colar o URL diretamente. O ponto final de Swagger tem de ser não autenticado possam para ser usados no Estruturador da aplicação lógica, apesar de proteger as API com quaisquer métodos que suporta o Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Chamada implementou aplicações de API com 2015-08-01-pré-visualização

Se tiver implementado anteriormente uma aplicação API, pode chamar essa aplicação com o **HTTP** ação.
Por exemplo, se utilizar o Dropbox para listar ficheiros, sua **2014-12-01-pré-visualização** definição de versão do esquema pode ter algo como:

``` json
"definition": {
   "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
         "defaultValue": "eyJ0eX...wCn90",
         "type": "String",
         "metadata": {
            "token": {
               "name": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
            }
         }
      }
    },
    "actions": {
       "dropboxconnector": {
          "type": "ApiApp",
          "inputs": {
             "apiVersion": "2015-01-14",
             "host": {
                "id": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                "gateway": "https://avdemo.azurewebsites.net"
             },
             "operation": "ListFiles",
             "parameters": {
                "FolderPath": "/myfolder"
             },
             "authentication": {
                "type": "Raw",
                "scheme": "Zumo",
                "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
             }
          }
       }
    }
}
```

Agora, pode agora criar uma ação de HTTP semelhante e deixar a definição de aplicação de lógica `parameters` secção inalterado, por exemplo:

``` json
"actions": {
   "dropboxconnector": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
      },
      "inputs": {
         "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
         "method": "POST",
         "body": {
            "FolderPath": "/myfolder"
         },
         "authentication": {
            "type": "Raw",
            "scheme": "Zumo",
            "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
         }
      }
   }
}
```

Percorrendo estas propriedades de um a um:

| Propriedade de ação | Descrição |
| --- | --- |
| `type` | `Http` em vez de `APIapp` |
| `metadata.apiDefinitionUrl` | Para utilizar esta ação no Estruturador da aplicação lógica, incluem o ponto final de metadados, que é construído a partir de: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Construídos a partir de: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Sempre `POST` |
| `inputs.body` | Mesmo que os parâmetros de aplicação API |
| `inputs.authentication` | Mesmo que a autenticação da aplicação de API |

Esse método deve funcionar para todas as ações de aplicação API. No entanto, lembre-se de que estas aplicações de API anterior já não são suportadas. Desta forma, deve mover para uma das duas outras opções anteriores, uma API gerida ou a alojar a API Web personalizada.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Mudar o nome "Repetir" para "foreach"

Para a versão anterior do esquema, recebemos mais comentários dos clientes que o **repetir** nome da ação era confuso e não foi corretamente capturá-los **repetir** foi realmente um para cada ciclo. Então, renomeamos `repeat` para `foreach`. Anteriormente escreveria esta ação, como neste exemplo:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "repeat": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{repeatItem()}"
      }
   }
}
```

Agora deve escrever em vez disso, esta versão:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "foreach": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{item()}"
      }
   }
}
```

Além disso, o `repeatItem()` o nome da função, que é referenciado o item que está a processar o loop durante a iteração atual, é alterada agora `item()`. 

### <a name="reference-outputs-from-foreach"></a>Saídas de referência de "foreach"

Para simplificar, as saídas resultantes `foreach` ações já não são compactadas num objeto com o nome `repeatItems`. Além disso, com essas alterações, o `repeatItem()`, `repeatBody()`, e `repeatOutputs()` funções são removidas.

Assim, usando o anterior `repeat` exemplo, obtém essas saídas:

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

Agora tem essas saídas em vez disso:

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

Anteriormente, para obter o `body` da ação ao fazer referência essas saídas:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "repeat": "@outputs('pingBing').repeatItems",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@repeatItem().outputs.body"
      }
   }
}
```

Agora, pode utilizar em vez disso, esta versão:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "foreach": "@outputs('pingBing')",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Serviço de escuta HTTP nativo

Funcionalidades do serviço de escuta HTTP estão agora incorporadas, para que não tenha de implementar uma aplicação de API do serviço de escuta de HTTP. Para obter mais informações, saiba como [tornar o ponto de final de aplicação lógica pode ser chamada](../logic-apps/logic-apps-http-endpoint.md). 

Com essas alterações, o Logic Apps substitui o `@accessKeys()` funcionar com o `@listCallbackURL()` função, que obtém o ponto final quando for necessário. Além disso, agora tem de definir, pelo menos, um acionador na sua aplicação lógica. Se quiser `/run` fluxo de trabalho, precisa usar um dos seguintes tipos de Acionador: `Manual`, `ApiConnectionWebhook`, ou `HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Chamar fluxos de trabalho de subordinados

Anteriormente, chamar fluxos de trabalho subordinado necessário aceder ao fluxo de trabalho, obter o token de acesso e colar o token na definição da aplicação lógica em que deseja chamar esse fluxo de trabalho subordinado. Com esse esquema, o motor do Logic Apps gera automaticamente uma SAS em tempo de execução do fluxo de trabalho subordinado para que não tenha colar segredos na definição. Segue-se um exemplo:

``` json
"myNestedWorkflow": {
   "type": "Workflow",
   "inputs": {
      "host": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/myWorkflow001",
         "triggerName": "myEndpointTrigger"
      },
      "queries": {
         "extrafield": "specialValue"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "Content-type": "application/json"
      },
      "body": {
         "contentFieldOne": "value100",
         "anotherField": 10.001
      }
   },
   "conditions": []
}
```

Além disso, os fluxos de trabalho subordinado obtém acesso total à solicitação de entrada. Por isso, pode passar parâmetros nos `queries` secção e, no `headers` objeto. Também pode definir toda a `body` secção.

Por fim, os fluxos de trabalho subordinado terem essas alterações necessárias. Enquanto foi anteriormente e chama diretamente um fluxo de trabalho subordinado, agora tem de definir um ponto de extremidade do acionador do fluxo de trabalho para o elemento principal chamar. Em geral, adicionaria um acionador que tenha `Manual` escreva e, em seguida, utilizar esse acionador na definição do principal. O `host` propriedade especificamente tem um `triggerName` porque sempre tem de especificar o acionador estiver chamando.

## <a name="other-changes"></a>Outras alterações

### <a name="new-queries-property"></a>Nova propriedade de "consultas"

Todos os tipos de ação suportam agora uma nova entrada chamada `queries`. Esta entrada pode ser um objeto estruturado, em vez de ter de voltar a montar a cadeia de caracteres manualmente.

### <a name="renamed-parse-function-to-json"></a>Função de "parse()' nome mudado para 'json()'

O `parse()` função sido agora alterada o `json()` função para tipos de conteúdo futuros.

## <a name="enterprise-integration-apis"></a>APIs de integração do Enterprise

Esse esquema ainda não suporta versões gerenciadas para APIs de integração da empresa, como AS2. No entanto, pode utilizar APIs de BizTalk implementado existentes por meio da ação de HTTP. Para obter mais informações, consulte "Utilizar as aplicações API já implementadas" no [mapa de integração](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
