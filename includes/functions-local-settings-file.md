---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 190524251d139e1421c1aac93d5a4dd523068a7a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958328"
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
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
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

| Definições      | Descrição                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Quando esta definição está definida para `true` , todos os valores são encriptados com uma chave de máquina local. Usado com `func settings` comandos. O valor predefinido é `false`. É possível que pretenda encriptar as local.settings.jsno ficheiro do computador local quando este contiver segredos, tais como cadeias de ligação de serviço. O anfitrião desencripta automaticamente as definições quando funciona. Utilize o `func settings decrypt` comando antes de tentar ler as definições encriptadas localmente. |
| **`Values`** | Conjunto de configurações de aplicações e cordas de conexão usadas quando um projeto está em execução local. Estes pares de valor-chave (string-string) correspondem às definições de aplicação na sua aplicação de função em Azure, como [`AzureWebJobsStorage`] . Muitos gatilhos e encadernações têm uma propriedade que se refere a uma definição de aplicação de cadeia de ligação, como `Connection` para o gatilho de armazenamento [Blob](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Para estas propriedades, você precisa de uma definição de aplicação definida na `Values` matriz. Consulte a tabela seguinte para obter uma lista de configurações comumente utilizadas. <br/>Os valores devem ser cordas e não objetos ou matrizes JSON. Definir nomes não pode incluir um cólon `:` () ou um duplo sublinhado `__` (). Os caracteres de duplo sublinhado são reservados pelo tempo de execução, e o cólon é reservado para apoiar a [injeção de dependência](../articles/azure-functions/functions-dotnet-dependency-injection.md#working-with-options-and-settings). |
| **`Host`** | As definições nesta secção personalizam o processo de anfitrião de Funções quando executar projetos localmente. Estas definições são separadas do host.jsnas definições, que também se aplicam quando executam projetos em Azure. |
| **`LocalHttpPort`** | Define a porta predefinida utilizada quando executa o hospedeiro local de funções `func host start` (e `func run` . A `--port` opção de linha de comando tem precedência sobre esta definição. Por exemplo, ao executar no Visual Studio IDE, pode alterar o número de porta navegando para a janela "Project Properties -> Debug" e especificando explicitamente o número da porta num `host start --port <your-port-number>` comando que pode ser fornecido no campo "Argumentos de Aplicação". |
| **`CORS`** | Define as origens permitidas para [a partilha de recursos de origem cruzada (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). As origens são fornecidas como uma lista separada por vírgula sem espaços. O valor wildcard é \* suportado, o que permite pedidos de qualquer origem. |
| **`CORSCredentials`** |  Quando definido `true` para, permite `withCredentials` pedidos. |
| **`ConnectionStrings`** | Uma coleção. Não utilize esta coleção para as cordas de ligação utilizadas pelas ligações da sua função. Esta coleção é utilizada apenas por quadros que normalmente obtêm cordas de ligação a partir `ConnectionStrings` da secção de um ficheiro de configuração, como o [Entity Framework](/ef/ef6/). As cadeias de ligação neste objeto são adicionadas ao ambiente com o tipo de fornecedor de [System.Data.SqlClient](/dotnet/api/system.data.sqlclient). Os itens desta coleção não são publicados no Azure com outras configurações de aplicações. Deve adicionar explicitamente estes valores à `Connection strings` recolha das definições da aplicação de função. Se estiver a criar um [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection) código de função, deverá armazenar o valor da cadeia de ligação com as outras ligações nas **Definições de Aplicação** no portal. |

As seguintes definições de aplicação podem ser incluídas na **`Values`** matriz quando correrem localmente:

| Definição | Valores | Descrição |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| Cadeia de ligação de conta de armazenamento, ou<br/>`UseDevelopmentStorage=true`| Contém o fio de ligação para uma conta de armazenamento Azure. Necessário ao utilizar gatilhos diferentes do HTTP. Para mais informações, consulte a [`AzureWebJobsStorage`] referência.<br/>Quando tiver o [Emulador de Armazenamento Azure](../articles/storage/common/storage-use-emulator.md) instalado localmente e definido [`AzureWebJobsStorage`] `UseDevelopmentStorage=true` para, a Core Tools utiliza o emulador. O emulador é útil durante o desenvolvimento, mas deve testar com uma ligação de armazenamento real antes de ser implantado.| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | Para desativar uma função quando estiver a funcionar localmente, adicione `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` à coleção, onde `<FUNCTION_NAME>` está o nome da função. Para saber mais, consulte [Como desativar funções em Funções Azure](../articles/azure-functions/disable-function.md#localsettingsjson) |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| Indica a linguagem direcionada do tempo de funcionamento das Funções. Necessário para a versão 2.x e superior do tempo de funcionamento das Funções. Esta definição é gerada para o seu projeto pela Core Tools. Para saber mais, consulte a [`FUNCTIONS_WORKER_RUNTIME`](../articles/azure-functions/functions-app-settings.md#functions_worker_runtime) referência.|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |Indica que o PowerShell 7 deve ser utilizado quando funciona localmente. Se não estiver definido, então o PowerShell Core 6 é utilizado. Esta definição só é utilizada quando funciona localmente. Ao correr em Azure, a versão de tempo de execução PowerShell é determinada pela definição de configuração do `powerShellVersion` site, que pode ser [definida no portal](../articles/azure-functions/functions-reference-powershell.md#changing-the-powershell-version). | 

['AzureWebJobsStorage']: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
