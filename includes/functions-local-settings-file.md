---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205751"
---
## <a name="local-settings-file"></a>Ficheiro de definições locais

O ficheiro local.settings.json armazena definições de aplicativos, cordas de ligação e configurações usadas pelas ferramentas de desenvolvimento locais. As definições no ficheiro local.settings.json são usadas apenas quando estiver a executar projetos localmente. O ficheiro de configurações locais tem esta estrutura:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Estas configurações são suportadas quando executa projetos localmente:

| Definição      | Descrição                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Quando esta definição `true`estiver definida, todos os valores são encriptados com uma chave de máquina local. Usado `func settings` com comandos. O valor `false`predefinido é . |
| **`Values`** | Conjunto de definições de aplicação e cordas de ligação usadas quando um projeto está em execução local. Estes pares de valor-chave (string string) correspondem às definições [`AzureWebJobsStorage`]de aplicação na sua aplicação de função em Azure, como . Muitos gatilhos e encadernações têm uma propriedade que `Connection` se refere a uma definição de aplicação de cordas de ligação, como para o [gatilho de armazenamento Blob](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Para estas propriedades, necessita de uma `Values` definição de aplicação definida na matriz. <br/>[`AzureWebJobsStorage`]é uma definição de aplicação necessária para gatilhos que não o HTTP. <br/>A versão 2.x e superior do tempo`FUNCTIONS_WORKER_RUNTIME`de funcionamento das Funções requer a definição de [ ] ] que é gerada para o seu projeto pela Core Tools. <br/> Quando tiver o [emulador](../articles/storage/common/storage-use-emulator.md) de armazenamento Azure [`AzureWebJobsStorage`] `UseDevelopmentStorage=true`instalado localmente e definido para , core tools usa o emulador. O emulador é útil durante o desenvolvimento, mas deve testar com uma ligação de armazenamento real antes da implantação.<br/> Os valores devem ser cordas e não objetos Ou matrizes JSON. Definir nomes não pode`:`incluir um cólon`__`( ) ou um duplo sublinhado ( ). Estes caracteres são reservados pelo tempo de execução.  |
| **`Host`** | As definições nesta secção personalizam o processo de hospedar funções quando executa projetos localmente. Estas configurações são separadas das definições host.json, que também se aplicam quando executa projetos em Azure. |
| **`LocalHttpPort`** | Define a porta predefinida utilizada durante o`func host start` `func run`funcionamento do hospedeiro das Funções locais (e ). A `--port` opção de linha de comando tem precedência sobre esta definição. |
| **`CORS`** | Define as origens permitidas para [a partilha de recursos de origem cruzada (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). As origens são fornecidas como uma lista separada de vírem sem espaços. O valor\*wildcard é suportado, o que permite pedidos de qualquer origem. |
| **`CORSCredentials`** |  Quando definido `true`para, permite `withCredentials` pedidos. |
| **`ConnectionStrings`** | Uma coleção. Não utilize esta coleção para as cordas de ligação utilizadas pelas suas encadernações de função. Esta coleção é utilizada apenas por estruturas que `ConnectionStrings` normalmente obtêm cordas de ligação da secção de um ficheiro de configuração, como o Quadro de [Entidades](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). As cordas de ligação neste objeto são adicionadas ao ambiente com o tipo de fornecedor de [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Os itens desta coleção não são publicados no Azure com outras definições de aplicações. Deve adicionar explicitamente estes `Connection strings` valores à recolha das definições da sua aplicação de função. Se estiver a [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) criar um código de funcionamento, deverá armazenar o valor de cadeia de ligação com as suas outras ligações nas Definições de **Aplicação** no portal. |

['AzureWebJobsStorage']: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
