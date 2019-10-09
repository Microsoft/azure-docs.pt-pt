---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038168"
---
A [Biblioteca do Gestor de Configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) fornece uma classe para analisar uma cadeia de ligação a partir de um ficheiro de configuração. A classe [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analisa as definições de configuração. Ele analisa as configurações para aplicativos cliente que são executados na área de trabalho, em um dispositivo móvel, em uma máquina virtual do Azure ou em um serviço de nuvem do Azure.

Para fazer referência ao pacote `CloudConfigurationManager`, adicione as seguintes diretivas `using`:

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

A utilização do Gestor de Configuração do Azure é opcional. Você também pode usar uma API como a [classe ConfigurationManager](/dotnet/api/system.configuration.configurationmanager)do .NET Framework.
