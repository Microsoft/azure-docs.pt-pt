---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: cedfd719a5f0aeed6fc2e932d3aa5189b83c9796
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026206"
---
A [Biblioteca do Gestor de Configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) fornece uma classe para analisar uma cadeia de ligação a partir de um ficheiro de configuração. As definições de configuração da classe [CloudConfigurationManager.](/previous-versions/azure/reference/mt634650(v=azure.100)) Analisa as definições para aplicações de clientes que funcionam no ambiente de trabalho, num dispositivo móvel, numa máquina virtual Azure ou num serviço de nuvem Azure.

Para fazer referência à `CloudConfigurationManager` embalagem, adicione as `using` seguintes diretivas:

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

A utilização do Gestor de Configuração do Azure é opcional. Também pode utilizar uma API como a Classe Calibre de Configuração do Quadro [.NET.](/dotnet/api/system.configuration.configurationmanager)