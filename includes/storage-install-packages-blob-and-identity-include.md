---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74806592"
---
## <a name="install-client-library-packages"></a>Instalar pacotes de bibliotecas de clientes

> [!NOTE]
> Os exemplos aqui mostrados utilizam a versão 12 da biblioteca do cliente Azure Storage. A biblioteca de clientes da versão 12 faz parte do Azure SDK. Para obter mais informações sobre o Azure SDK, consulte o repositório Azure SDK no [GitHub.](https://github.com/Azure/azure-sdk)

Para instalar o pacote de armazenamento Blob, executar o seguinte comando a partir da consola do gestor de pacotes NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

Os exemplos aqui apresentados também utilizam a versão mais recente da biblioteca de [clientes Azure Identity para .NET](https://www.nuget.org/packages/Azure.Identity/) para autenticar com credenciais AD AD Azure. Para instalar o pacote, executar o seguinte comando a partir da consola do gestor de pacotes NuGet:

```powershell
Install-Package Azure.Identity
```
