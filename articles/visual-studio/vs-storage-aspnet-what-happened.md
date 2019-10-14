---
title: O que aconteceu ao meu projeto ASP.NET?
description: Descreve o que acontece depois de adicionar o armazenamento do Azure a um projeto ASP.NET usando os serviços conectados do Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4374955d6d51c1ae44c211aaa93d0b5e8930fe5b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300048"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>O que aconteceu com meu projeto do ASP.NET (serviço conectado do armazenamento do Azure do Visual Studio)?
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

## <a name="connection-string-for-azure-storage-added"></a>Cadeia de conexão para o armazenamento do Azure adicionada
No arquivo Web. config do seu projeto, um elemento foi criado com a cadeia de conexão e a chave da conta de armazenamento selecionada.

Para obter mais informações, consulte [ASP.net](https://www.asp.net).

