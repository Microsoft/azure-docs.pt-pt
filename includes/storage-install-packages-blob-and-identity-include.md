---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 0adf1280fa50e9ee594f3025dff70786f5ba2199
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666172"
---
## <a name="install-client-library-packages"></a>Instalar pacotes de biblioteca de cliente

> [!NOTE]
> Os exemplos mostrados aqui usam a biblioteca de cliente de armazenamento do Azure versão 12. A biblioteca de cliente da versão 12 faz parte do SDK do Azure. Para obter mais informações sobre o SDK do Azure, consulte o repositório do SDK do Azure no [GitHub](https://github.com/Azure/azure-sdk).

Para instalar o pacote de armazenamento de BLOBs, execute o seguinte comando no console do Gerenciador de pacotes NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

Os exemplos mostrados aqui também usam a versão mais recente da [biblioteca de cliente de identidade do Azure para .net](https://www.nuget.org/packages/Azure.Identity/) para autenticar com as credenciais do Azure AD. Para instalar o pacote, execute o seguinte comando no console do Gerenciador de pacotes NuGet:

```powershell
Install-Package Azure.Identity
```

Para saber mais sobre como autenticar com a biblioteca de cliente de identidade do Azure do armazenamento do Azure, consulte a seção intitulada **autenticar com a biblioteca de identidades** do Azure em [autorizar o acesso a BLOBs e filas com Azure Active Directory e identidades gerenciadas para recursos do Azure](/azure/storage/common/storage-auth-aad-msi?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).