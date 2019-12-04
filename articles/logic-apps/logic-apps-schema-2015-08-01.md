---
title: Atualizações de esquema para a versão prévia de agosto de 1-2015
description: Versão de esquema 2015-08-01-visualização atualizada para definições de aplicativo lógico em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: b6746baaede777eb8c2afcae9eb3fe80b669c468
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792840"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Atualizações de esquema para aplicativos lógicos do Azure – versão prévia de 1º de agosto de 2015

Essa versão de esquema e API para aplicativos lógicos do Azure inclui melhorias importantes que tornam os aplicativos lógicos mais confiáveis e mais fáceis de usar:

* O tipo de ação **APIApp** agora é chamado de [**APIConnection**](#api-connections).
* A ação **REPEAT** agora é chamada [**foreach**](#foreach).
* O [aplicativo de API de **ouvinte http** ](#http-listener) não é mais necessário.
* Chamar fluxos de trabalho filho usa um [novo esquema](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Mover para conexões de API

A maior alteração é que você não precisa mais implantar aplicativos de API em sua assinatura do Azure para que você possa usar APIs. Aqui estão as maneiras pelas quais você pode usar as APIs:

* APIs gerenciadas
* Suas APIs Web personalizadas

Cada uma delas é tratada de forma ligeiramente diferente porque seus modelos de gerenciamento e hospedagem são diferentes. Uma vantagem desse modelo é que você não está mais restrito a recursos implantados em seu grupo de recursos do Azure. 

### <a name="managed-apis"></a>APIs gerenciadas

A Microsoft gerencia algumas APIs em seu nome, como Office 365, Salesforce, Twitter e FTP. Você pode usar algumas APIs gerenciadas como estão, como o Bing translate, enquanto outras exigem a configuração, também chamada de *conexão*.

Por exemplo, ao usar o Office 365, você deve criar uma conexão que inclui o token de entrada do Office 365. O token é armazenado e atualizado com segurança para que seu aplicativo lógico sempre possa chamar a API do Office 365. Se você quiser se conectar ao servidor SQL ou FTP, deverá criar uma conexão que tenha a cadeia de conexão. 

Nessa definição, essas ações são chamadas de `APIConnection`. Aqui está um exemplo de uma conexão que chama o Office 365 para enviar um email:

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

O objeto `host` é uma parte das entradas que é exclusiva para conexões de API e contém estas partes: `api` e `connection`. O objeto `api` especifica a URL de tempo de execução para onde a API gerenciada está hospedada. Você pode ver todas as APIs gerenciadas disponíveis chamando este método:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

Quando você usa uma API, essa API pode ou não ter definido nenhum *parâmetro de conexão*. Portanto, se a API não definir esses parâmetros, nenhuma conexão será necessária. Se a API definir esses parâmetros, você deverá criar uma conexão com um nome especificado.  
Em seguida, você faz referência a esse nome no objeto `connection` dentro do objeto `host`. Para criar uma conexão em um grupo de recursos, chame este método:

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

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Implantar APIs gerenciadas em um modelo de Azure Resource Manager

Quando a entrada interativa não é necessária, você pode criar um aplicativo completo usando um modelo do Resource Manager.
Se a entrada for necessária, você ainda poderá usar um modelo do Resource Manager, mas precisará autorizar as conexões por meio do portal do Azure. 

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

Você pode ver neste exemplo que as conexões são apenas recursos que residem em seu grupo de recursos. Eles fazem referência às APIs gerenciadas disponíveis para você em sua assinatura.

### <a name="your-custom-web-apis"></a>Suas APIs Web personalizadas

Se você usar suas próprias APIs em vez de gerenciadas pela Microsoft, use a ação **http** interna para chamar suas APIs. O ideal é que você forneça um ponto de extremidade do Swagger para sua API. Esse ponto de extremidade ajuda o designer de aplicativos lógicos a mostrar as entradas e saídas da sua API. Sem um ponto de extremidade do Swagger, o designer só pode mostrar as entradas e saídas como objetos JSON opacos.

Veja um exemplo que mostra a nova propriedade `metadata.apiDefinitionUrl`:

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

Se você hospedar sua API Web no serviço Azure App, sua API Web aparecerá automaticamente na lista de ações disponíveis no designer. Caso contrário, você precisará colar a URL diretamente. O ponto de extremidade do Swagger deve ser não autenticado para ser utilizável no designer do aplicativo lógico, embora você possa proteger a API em si com quaisquer métodos aos quais o Swagger dá suporte.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Chamar aplicativos de API implantados com 2015-08-01-Preview

Se você tiver implantado anteriormente um aplicativo de API, poderá chamar esse aplicativo com a ação **http** .
Por exemplo, se você usar o Dropbox para listar arquivos, sua definição de versão de esquema **2014-12-01-Preview** poderá ter algo como:

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

Agora, agora você pode criar uma ação HTTP semelhante e deixar a seção `parameters` da definição do aplicativo lógico inalterada, por exemplo:

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

Percorrendo essas propriedades uma a uma:

| Propriedade de ação | Descrição |
| --- | --- |
| `type` | `Http` em vez de `APIapp` |
| `metadata.apiDefinitionUrl` | Para usar essa ação no designer de aplicativo lógico, inclua o ponto de extremidade de metadados, que é construído a partir de: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Construído a partir de: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Sempre `POST` |
| `inputs.body` | O mesmo que os parâmetros do aplicativo de API |
| `inputs.authentication` | O mesmo que a autenticação do aplicativo de API |

Essa abordagem deve funcionar para todas as ações de aplicativo de API. No entanto, lembre-se de que esses aplicativos de API anteriores não têm mais suporte. Portanto, você deve mudar para uma das duas outras opções anteriores, uma API gerenciada ou hospedando sua API Web personalizada.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>' Repetir ' renomeado para ' foreach '

Para a versão de esquema anterior, recebemos muitos comentários de clientes de que o nome da ação de **repetição** era confuso e não capturava corretamente que a **repetição** era realmente um loop for-each. Portanto, renomeamos `repeat` para `foreach`. Anteriormente, você escreveria essa ação como este exemplo:

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

Agora, você escreveria esta versão:

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

Além disso, a função `repeatItem()`, que referenciou o item que o loop está processando durante a iteração atual, agora é renomeada `item()`. 

### <a name="reference-outputs-from-foreach"></a>Saídas de referência de ' foreach '

Para simplificar, as saídas de `foreach` ações não são mais encapsuladas em um objeto chamado `repeatItems`. Além disso, com essas alterações, as funções `repeatItem()`, `repeatBody()`e `repeatOutputs()` são removidas.

Portanto, usando o exemplo de `repeat` anterior, você obtém estas saídas:

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

Agora, você obtém essas saídas:

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

Anteriormente, para obter a `body` da ação ao fazer referência a essas saídas:

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

Agora, você pode usar essa versão:

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

## <a name="native-http-listener"></a>Ouvinte HTTP nativo

Os recursos do ouvinte HTTP agora são internos, portanto, você não precisa implantar um aplicativo de API de ouvinte HTTP. Para obter mais informações, saiba como [tornar o ponto de extremidade do aplicativo lógico chamável](../logic-apps/logic-apps-http-endpoint.md). 

Com essas alterações, os aplicativos lógicos substituem a função `@accessKeys()` pela função `@listCallbackURL()`, que obtém o ponto de extremidade quando necessário. Além disso, agora você deve definir pelo menos um gatilho em seu aplicativo lógico. Se quiser `/run` fluxo de trabalho, você precisará usar um destes tipos de gatilho: `Manual`, `ApiConnectionWebhook`ou `HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Chamar fluxos de trabalho filho

Anteriormente, chamar os fluxos de trabalho filho exigia ir para o fluxo de trabalho, obter o token de acesso e colar o token na definição do aplicativo lógico em que você deseja chamar esse fluxo de trabalho filho. Com esse esquema, o mecanismo de aplicativos lógicos gera automaticamente uma SAS em tempo de execução para o fluxo de trabalho filho para que você não precise colar os segredos na definição. Segue-se um exemplo:

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

Além disso, os fluxos de trabalho filho obtêm acesso completo à solicitação de entrada. Portanto, você pode passar parâmetros na seção `queries` e no objeto `headers`. Você também pode definir totalmente a seção `body` inteira.

Por fim, os fluxos de trabalho filho têm essas alterações necessárias. Embora você possa chamar um fluxo de trabalho filho anteriormente e diretamente, agora você deve definir um ponto de extremidade de gatilho no fluxo de trabalho para o pai a ser chamado. Em geral, você adicionaria um gatilho que tem `Manual` tipo e, em seguida, usará esse gatilho na definição pai. A propriedade `host` especificamente tem um `triggerName` porque você sempre deve especificar o gatilho que está chamando.

## <a name="other-changes"></a>Outras alterações

### <a name="new-queries-property"></a>Nova propriedade ' queries '

Todos os tipos de ação agora dão suporte a uma nova entrada chamada `queries`. Essa entrada pode ser um objeto estruturado, em vez de você ter que montar a cadeia de caracteres manualmente.

### <a name="renamed-parse-function-to-json"></a>A função ' Parse () ' foi renomeada para ' JSON () '

A função `parse()` agora renomeou a função `json()` para futuros tipos de conteúdo.

## <a name="enterprise-integration-apis"></a>APIs de Enterprise Integration

Esse esquema ainda não dá suporte a versões gerenciadas para APIs Enterprise Integration, como AS2. No entanto, você pode usar as APIs do BizTalk implantadas existentes por meio da ação HTTP. Para obter mais informações, consulte "usando seus aplicativos de API já implantados" no [roteiro de integração](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
