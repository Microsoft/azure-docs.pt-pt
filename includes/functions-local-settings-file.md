---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: e319356d555f26354ea29dc7be068bf6168abb17
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455154"
---
## <a name="local-settings-file"></a>Ficheiro de definições locais

O ficheiro Settings armazena as definições da aplicação, as cadeias de ligação e as definições utilizadas pelas ferramentas de desenvolvimento local. Definições no arquivo Settings só são utilizadas ao executar localmente. O arquivo de configurações local tem a seguinte estrutura:

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

As seguintes definições são suportadas ao executar localmente:

| Definição      | Descrição                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Quando definido como `true`, todos os valores são criptografados usando uma chave de computador local. Utilizado com `func settings` comandos. Valor predefinido é `false`. |
| **`Values`** | Matriz de definições da aplicação e as cadeias de ligação utilizadas ao executar localmente. Estes pares chave-valor (cadeia-cadeia) correspondem às definições da aplicação na sua aplicação de função no Azure, tal como [ `AzureWebJobsStorage` ]. Muitos acionadores e enlaces de ter uma propriedade que se refere a uma definição de aplicação de cadeia de ligação, tal como `Connection` para o [acionador do armazenamento de BLOBs](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration). Para essas propriedades, precisa de uma definição da aplicação definida no `Values` matriz. <br/>[`AzureWebJobsStorage`] é uma aplicação necessária a configuração para acionadores que não seja o HTTP. <br/>Versão 2.x do runtime de funções requer o [`FUNCTIONS_WORKER_RUNTIME`] definição, que é gerada para o seu projeto por ferramentas de núcleo. <br/> Quando tem o [emulador de armazenamento do Azure](../articles/storage/common/storage-use-emulator.md) instalados localmente, pode definir [ `AzureWebJobsStorage` ] para `UseDevelopmentStorage=true` e ferramentas de núcleo utiliza o emulador. Isto é útil durante o desenvolvimento, mas deve testar com uma ligação de armazenamento real antes da implantação.<br/> Valores devem ser cadeias de caracteres e não os objetos JSON ou matrizes. A definição de nomes não pode incluir uma vírgula (`:`) ou um sublinhado duplo (`__`); estes estão reservados pelo tempo de execução.  |
| **`Host`** | As definições nesta secção personalizar o processo de host de funções ao executar localmente. Estes são separadas das definições do Host. JSON, que também são aplicadas quando em execução no Azure. |
| **`LocalHttpPort`** | Define a porta predefinida utilizada ao executar o anfitrião local de funções (`func host start` e `func run`). O `--port` opção da linha de comandos tem precedência sobre este valor. |
| **`CORS`** | Define as origens permitidas para [recursos de várias origens (CORS) de partilha](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Origens são fornecidas como uma lista separada por vírgulas, sem espaços. O valor de caráter universal (\*) é suportado, que permite que os pedidos a partir de qualquer origem. |
| **`CORSCredentials`** |  Defina como verdadeiro para permitir `withCredentials` pedidos. |
| **`ConnectionStrings`** | Não utilize esta coleção para as cadeias de ligação utilizadas pelo seu enlaces de funções. Esta coleção só é utilizada por estruturas que normalmente obtém cadeias de ligação do `ConnectionStrings` secção de uma configuração de ficheiros, tais como [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Cadeias de ligação desse objeto são adicionadas ao ambiente com o tipo de fornecedor de [SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Itens dessa coleção não são publicadas no Azure com outras definições de aplicação. Tem de adicionar explicitamente esses valores para o `Connection strings` coleção das definições de aplicação de função. Se estiver a criar uma [ `SqlConnection` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) no código da função, deve armazenar o valor da cadeia de ligação **configurações de aplicativo** no portal com as outras ligações. |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
