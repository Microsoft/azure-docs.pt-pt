---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72038168"
---
A [Biblioteca do Gestor de Configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) fornece uma classe para analisar uma cadeia de ligação a partir de um ficheiro de configuração. As definições de configuração da classe [CloudConfigurationManager.](https://msdn.microsoft.com/library/azure/mt634650.aspx) Analisa as configurações para aplicações de clientes que funcionam no ambiente de trabalho, num dispositivo móvel, numa máquina virtual Azure ou num serviço de nuvem Azure.

Para fazer `CloudConfigurationManager` referência ao `using` pacote, adicione as seguintes diretivas:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

Eis um exemplo que mostra como obter uma cadeia de ligação a partir de um ficheiro de configuração:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

A utilização do Gestor de Configuração do Azure é opcional. Também pode utilizar uma API como a classe .NET Framework's [ConfigurationManager](/dotnet/api/system.configuration.configurationmanager)Class .
