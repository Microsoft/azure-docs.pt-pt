---
title: O que aconteceu ao meu projeto ASP.NET? | Microsoft Docs
description: Descreve o que acontece depois de adicionar o armazenamento do Azure para um projeto do ASP.NET usando o Visual Studio serviços ligados
services: storage
author: ghogen
manager: douge
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: e0e065b23581f297ee4ae2288a6e437da461a19f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998364"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>O que aconteceu ao meu projeto ASP.NET (serviço ligado do armazenamento do Azure do Visual Studio)?
## <a name="references-added"></a>Referências adicionadas
O pacote NuGet de armazenamento do Azure foi adicionado ao seu projeto do Visual Studio.  
Esse pacote adiciona as seguintes referências de .NET:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Cadeia de ligação do armazenamento do Azure adicionado
No ficheiro Web. config do seu projeto, um elemento foi criado com a cadeia de ligação e a chave da conta de armazenamento selecionada.

Para obter mais informações, consulte [ASP.NET](https://www.asp.net).

