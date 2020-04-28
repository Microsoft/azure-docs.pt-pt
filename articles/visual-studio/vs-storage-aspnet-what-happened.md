---
title: O que aconteceu ao meu projeto ASP.NET?
description: Descreve o que acontece depois de adicionar o Azure Storage a um projeto ASP.NET utilizando serviços conectados ao Estúdio Visual
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
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72300048"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>O que aconteceu ao meu projeto ASP.NET (serviço conectado visual Studio Azure Storage)?
## <a name="references-added"></a>Referências adicionadas
O pacote Azure Storage NuGet foi adicionado ao seu projeto Visual Studio.  
Este pacote adiciona as seguintes referências .NET:

* **Microsoft.Data.Edm**
* **Microsoft.data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuração**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **Sistema.Dados**
* **Sistema.Espacial**

## <a name="connection-string-for-azure-storage-added"></a>Fio de ligação para armazenamento azure adicionado
No ficheiro web.config do seu projeto, foi criado um elemento com a cadeia e a chave de ligação da conta de armazenamento selecionada.

Para mais informações, consulte [ASP.NET](https://www.asp.net).

