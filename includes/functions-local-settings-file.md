---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77205751"
---
## <a name="local-settings-file"></a>Ficheiro de definições locais

O local.settings.jsnas configurações de aplicações de lojas de ficheiros, cordas de ligação e configurações utilizadas pelas ferramentas de desenvolvimento local. As definições no local.settings.jsno ficheiro são usadas apenas quando se está a executar projetos localmente. O ficheiro de configurações locais tem esta estrutura:

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

Estas definições são suportadas quando executam projetos localmente:

| Definição      | Descrição                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Quando esta definição está definida para `true` , todos os valores são encriptados com uma chave de máquina local. Usado com `func settings` comandos. O valor predefinido é `false` . |
| **`Values`** | Conjunto de configurações de aplicações e cordas de conexão usadas quando um projeto está em execução local. Estes pares de valor-chave (string-string) correspondem às definições de aplicação na sua aplicação de função em Azure, como [`AzureWebJobsStorage`] . Muitos gatilhos e encadernações têm uma propriedade que se refere a uma definição de aplicação de cadeia de ligação, como `Connection` para o gatilho de armazenamento [Blob](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Para estas propriedades, você precisa de uma definição de aplicação definida na `Values` matriz. <br/>[`AzureWebJobsStorage`]é uma definição de aplicação necessária para gatilhos diferentes de HTTP. <br/>A versão 2.x e superior do tempo de funcionamento das Funções requer a `FUNCTIONS_WORKER_RUNTIME` definição [ ] que é gerada para o seu projeto por Core Tools. <br/> Quando tiver o [emulador de armazenamento Azure](../articles/storage/common/storage-use-emulator.md) instalado localmente e definido [`AzureWebJobsStorage`] `UseDevelopmentStorage=true` para, a Core Tools utiliza o emulador. O emulador é útil durante o desenvolvimento, mas deve testar com uma ligação de armazenamento real antes de ser implantado.<br/> Os valores devem ser cordas e não objetos ou matrizes JSON. Definir nomes não pode incluir um cólon `:` () ou um duplo sublinhado `__` (). Estes caracteres são reservados pelo tempo de execução.  |
| **`Host`** | As definições nesta secção personalizam o processo de anfitrião de Funções quando executar projetos localmente. Estas definições são separadas do host.jsnas definições, que também se aplicam quando executam projetos em Azure. |
| **`LocalHttpPort`** | Define a porta predefinida utilizada quando executa o hospedeiro local de funções `func host start` (e `func run` . A `--port` opção de linha de comando tem precedência sobre esta definição. |
| **`CORS`** | Define as origens permitidas para [a partilha de recursos de origem cruzada (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). As origens são fornecidas como uma lista separada por vírgula sem espaços. O valor wildcard é \* suportado, o que permite pedidos de qualquer origem. |
| **`CORSCredentials`** |  Quando definido `true` para, permite `withCredentials` pedidos. |
| **`ConnectionStrings`** | Uma coleção. Não utilize esta coleção para as cordas de ligação utilizadas pelas ligações da sua função. Esta coleção é utilizada apenas por quadros que normalmente obtêm cordas de ligação a partir `ConnectionStrings` da secção de um ficheiro de configuração, como o [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). As cadeias de ligação neste objeto são adicionadas ao ambiente com o tipo de fornecedor de [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Os itens desta coleção não são publicados no Azure com outras configurações de aplicações. Deve adicionar explicitamente estes valores à `Connection strings` recolha das definições da aplicação de função. Se estiver a criar um [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) código de função, deverá armazenar o valor da cadeia de ligação com as outras ligações nas **Definições de Aplicação** no portal. |

['AzureWebJobsStorage']: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
