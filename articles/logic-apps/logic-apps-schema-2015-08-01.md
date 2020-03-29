---
title: Atualizações de Schema para a pré-visualização de agosto-1-2015
description: Versão de esquema atualizada 2015-08-01-pré-visualização para definições de aplicações lógicas em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: b6746baaede777eb8c2afcae9eb3fe80b669c468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792840"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Atualizações de Schema para Apps Lógicas Azure - Pré-visualização de 1 de agosto de 2015

Esta versão schema e API para Apps Lógicas Azure inclui melhorias fundamentais que tornam as aplicações lógicas mais fiáveis e fáceis de usar:

* O tipo de ação **APIApp** passa a [**chamar-se APIConnection**](#api-connections).
* A ação **Repeat** chama-se [**Foreach.**](#foreach)
* A App [ **Http Listener** API](#http-listener) já não é necessária.
* Chamar fluxos de trabalho para crianças usa um [novo esquema.](#child-workflows)

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Mude para ligações API

A maior mudança é que já não precisa de implementar Aplicações API na sua subscrição Azure para que possa utilizar APIs. Aqui estão as formas de usar APIs:

* APIs geridos
* As suas APIs web personalizadas

Cada forma é tratada de forma ligeiramente diferente porque os seus modelos de gestão e hospedagem são diferentes. Uma das vantagens deste modelo é que já não está limitado a recursos que são implantados no seu grupo de recursos Azure. 

### <a name="managed-apis"></a>APIs geridos

A Microsoft gere algumas APIs em seu nome, tais como Office 365, Salesforce, Twitter e FTP. Você pode usar algumas APIs geridas como é, como Bing Translate, enquanto outros requerem configuração, também chamado de *ligação*.

Por exemplo, quando utilizar o Office 365, deve criar uma ligação que inclua o seu sinal de inscrição no Office 365. A sua ficha é armazenada e atualizada de forma segura para que a sua aplicação lógica possa sempre ligar para a API do Office 365. Se pretender ligar-se ao seu servidor SQL ou FTP, tem de criar uma ligação que tenha a cadeia de ligação. 

Nesta definição, estas `APIConnection`ações são chamadas . Aqui está um exemplo de uma ligação que liga para o Office 365 para enviar um e-mail:

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

O `host` objeto é uma parte das inputs que é única para `api` as `connection`ligações API, e contém estas partes: e . O `api` objeto especifica o URL de tempo de execução para onde a API gerida está hospedada. Você pode ver todas as APIs geridas disponíveis chamando este método:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

Quando utilizar uma API, essa API pode ou não ter definido quaisquer parâmetros de *ligação*. Então, se a API não definir estes parâmetros, não é necessária nenhuma ligação. Se a API definir estes parâmetros, deve criar uma ligação com um nome especificado.  
Em seguida, refere `connection` esse nome `host` no objeto dentro do objeto. Para criar uma ligação num grupo de recursos, chame este método:

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Com o seguinte corpo:

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

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Implementar APIs geridos num modelo de Gestor de Recursos Azure

Quando não é necessário iniciar sessão interativa, pode criar uma aplicação completa utilizando um modelo de Gestor de Recursos.
Se for necessário iniciar o sessão, ainda pode utilizar um modelo de Gestor de Recursos, mas tem de autorizar as ligações através do portal Azure. 

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

Pode ver neste exemplo que as ligações são apenas recursos que vivem no seu grupo de recursos. Eles referem as APIs geridas disponíveis na sua subscrição.

### <a name="your-custom-web-apis"></a>As suas APIs web personalizadas

Se utilizar as suas próprias APIs em vez das geridas pela Microsoft, utilize a ação **HTTP** incorporada para ligar para as suas APIs. Idealmente, você deve fornecer um ponto final swagger para a sua API. Este ponto final ajuda o Logic App Designer a mostrar as inputs e saídas da sua API. Sem um ponto final swagger, o designer só pode mostrar as inputs e saídas como objetos Opacos JSON.

Aqui está um exemplo `metadata.apiDefinitionUrl` mostrando a nova propriedade:

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

Se hospedar a sua Web API no Serviço de Aplicações Azure, a sua Web API aparece automaticamente na lista de ações disponíveis no designer. Caso contrário, tem de colar diretamente no URL. O ponto final da Swagger deve ser inautenticado para ser utilizável no Logic App Designer, embora possa garantir a própria API com quaisquer métodos que a Swagger suporte.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Call implementado aplicativos API com pré-visualização 2015-08-01

Se já implementou uma App API, pode ligar para essa aplicação com a ação **HTTP.**
Por exemplo, se utilizar o Dropbox para listar ficheiros, a definição de versão de esquema de **pré-visualização 2014-12-01** pode ter algo como:

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

Agora, pode agora construir uma ação http semelhante e `parameters` deixar a secção da definição de aplicações lógica inalterada, por exemplo:

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

Andando por estas propriedades um a um:

| Propriedade de ação | Descrição |
| --- | --- |
| `type` | `Http`Em vez de`APIapp` |
| `metadata.apiDefinitionUrl` | Para utilizar esta ação no Logic App Designer, inclua o ponto final dos metadados, que é construído a partir de:`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Construído a partir de:`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Sempre`POST` |
| `inputs.body` | O mesmo que os parâmetros da App API |
| `inputs.authentication` | O mesmo que a autenticação da App API |

Esta abordagem deve funcionar para todas as ações da API App. No entanto, lembre-se que estas aplicações API anteriores já não são suportadas. Por isso, deve mudar-se para uma das duas outras opções anteriores, uma API gerida ou hospedar a sua API Web personalizada.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Renomeado 'repetir' para 'foreach'

Para a versão anterior do esquema, recebemos muito feedback do cliente de que o nome de ação **Repeat** era confuso e não capturamos corretamente que **Repeat** era realmente um loop para cada loop. Então, nós `repeat` rebatizamos para. `foreach` Anteriormente escreveria esta ação como este exemplo:

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

Agora escreveria esta versão:

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

Além `repeatItem()` disso, a função, que referiu o item que o `item()`laço está a processar durante a iteração atual, é agora renomeada . 

### <a name="reference-outputs-from-foreach"></a>Saídas de referência de 'foreach'

Para simplificar, as saídas de `foreach` ações deixaram de `repeatItems`ser embrulhadas num objeto denominado . Além disso, com `repeatItem()` `repeatBody()`estas `repeatOutputs()` alterações, as funções e funções são removidas.

Assim, usando o `repeat` exemplo anterior, obtém-se estas saídas:

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

Agora obtém estas saídas:

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

Anteriormente, para `body` obter o da ação ao fazer referência a estas saídas:

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

Agora pode usar esta versão:

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

## <a name="native-http-listener"></a>Ouvinte nativo http

As funcionalidades de escuta HTTP estão agora incorporadas, pelo que não é preciso implementar uma App Http Listener API. Para mais informações, saiba como tornar a [sua aplicação lógica callable](../logic-apps/logic-apps-http-endpoint.md). 

Com estas alterações, as `@accessKeys()` Aplicações `@listCallbackURL()` Lógicas substituem a função pela função, que obtém o ponto final quando necessário. Além disso, agora deve definir pelo menos um gatilho na sua aplicação lógica. Se quiser `/run` o fluxo de trabalho, tem de usar `Manual` `ApiConnectionWebhook`um destes tipos de gatilho: , ou`HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Call child workflows

Anteriormente, chamar fluxos de trabalho para crianças exigia ir ao fluxo de trabalho, obter o token de acesso, e colar o símbolo na definição de aplicação lógica onde você quer chamar esse fluxo de trabalho infantil. Com este esquema, o motor Logic Apps gera automaticamente um SAS no tempo de funcionamento para o fluxo de trabalho infantil para que não tenha de colar quaisquer segredos na definição. Segue-se um exemplo:

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

Além disso, os fluxos de trabalho para crianças têm acesso total ao pedido de entrada. Então, pode passar parâmetros `queries` na secção `headers` e no objeto. Também pode definir totalmente `body` toda a secção.

Finalmente, os fluxos de trabalho para crianças têm estas alterações necessárias. Embora possa ligar previamente e diretamente para um fluxo de trabalho infantil, deve agora definir um ponto final no fluxo de trabalho para o progenitor ligar. Geralmente, adicionaria um `Manual` gatilho que tem tipo e, em seguida, usaria esse gatilho na definição dos pais. A `host` propriedade tem `triggerName` um porque você sempre deve especificar o gatilho que você está ligando.

## <a name="other-changes"></a>Outras alterações

### <a name="new-queries-property"></a>Nova propriedade 'consultas'

Todos os tipos de ação `queries`suportam agora uma nova entrada chamada . Esta entrada pode ser um objeto estruturado, em vez de ter que montar a corda à mão.

### <a name="renamed-parse-function-to-json"></a>Função "parse)" para «json()».

A `parse()` função é `json()` agora renomeada a função para futuros tipos de conteúdo.

## <a name="enterprise-integration-apis"></a>APIs de Integração Empresarial

Este esquema ainda não suporta versões geridas para APIs de Integração Empresarial, como as AS2. No entanto, pode utilizar APIs bizTalk implantados existentes através da ação HTTP. Para mais informações, consulte "Utilizar as suas aplicações API já implantadas" no roteiro de [integração](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
