---
title: O que aconteceu com meu projeto do ASP.NET 5 (serviços conectados do Visual Studio)
description: Descreve o que acontece depois de se conectar a uma conta de armazenamento do Azure em um projeto do Visual Studio ASP.NET 5 usando os serviços conectados do Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be99465a48aaf680834f313e03384a9f0c211502
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300008"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>O que aconteceu com meu projeto do ASP.NET 5 (serviços conectados do armazenamento do Azure do Visual Studio)?
## <a name="references-added"></a>Referências adicionadas
O pacote NuGet do armazenamento do Azure foi adicionado ao seu projeto do Visual Studio.  
Este pacote adiciona as seguintes referências .NET:

* **Microsoft. Data. Edm**
* **Microsoft. Data. OData**
* **Microsoft. Data. Services. Client**
* **Microsoft. WindowsAzure. Configuration**
* **Microsoft. WindowsAzure. Storage**
* **Newtonsoft. JSON**
* **System. Data**
* **System. espacial**

Além disso, o pacote NuGet **Microsoft. Framework. Configuration. JSON** foi adicionado.

## <a name="connection-string-for-azure-storage-added"></a>Cadeia de conexão para o armazenamento do Azure adicionada
No arquivo config. JSON do seu projeto, um elemento foi criado com a cadeia de conexão e a chave da conta de armazenamento selecionada.

Para obter mais informações, consulte [ASP.NET 5](https://www.asp.net/vnext).

