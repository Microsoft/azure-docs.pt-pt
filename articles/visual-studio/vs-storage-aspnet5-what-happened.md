---
title: O que aconteceu ao meu projeto ASP.NET 5 (serviços ligados da Visual Studio) | Documentos da Microsoft
description: Descreve o que acontece depois de ligar a uma conta de armazenamento do Azure num projeto do Visual Studio ASP.NET 5 com o Visual Studio serviços ligados
services: storage
author: ghogen
manager: douge
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 71a95e1974cbcec9afcc3337eb37275532e1b527
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999720"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>O que aconteceu ao meu projeto ASP.NET 5 (serviços ligados armazenamento do Azure do Visual Studio)?
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

Além disso, o pacote NuGet **Microsoft.Framework.Configuration.Json** foi adicionado.

## <a name="connection-string-for-azure-storage-added"></a>Cadeia de ligação do armazenamento do Azure adicionado
No arquivo config do seu projeto, um elemento foi criado com a cadeia de ligação e a chave da conta de armazenamento selecionada.

Para obter mais informações, consulte [ASP.NET 5](https://www.asp.net/vnext).

