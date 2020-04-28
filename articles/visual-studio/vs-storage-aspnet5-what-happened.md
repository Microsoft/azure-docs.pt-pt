---
title: O que aconteceu ao meu projeto ASP.NET 5 (serviços conectados ao Estúdio Visual)
description: Descreve o que acontece depois de se ligar a uma conta de armazenamento Azure num projeto do Estúdio Visual ASP.NET 5 utilizando serviços conectados ao Estúdio Visual
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72300008"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>O que aconteceu ao meu projeto ASP.NET 5 (Serviços ligados ao Armazenamento Visual Studio Azure)?
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

Além disso, o pacote NuGet **Microsoft.Framework.Configuration.Json** foi adicionado.

## <a name="connection-string-for-azure-storage-added"></a>Fio de ligação para armazenamento azure adicionado
No ficheiro config.json do seu projeto, foi criado um elemento com a cadeia e a chave de ligação da conta de armazenamento selecionada.

Para mais informações, consulte [ASP.NET 5](https://www.asp.net/vnext).

