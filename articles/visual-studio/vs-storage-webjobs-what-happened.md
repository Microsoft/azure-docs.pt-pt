---
title: O que aconteceu ao meu projeto WebJob (Visual Studio Azure Storage)?
description: Descreve o que aconteceu num projeto Azure WebJob depois de se ligar a uma conta de armazenamento usando serviços conectados do Estúdio Visual
services: storage
author: ghogen
manager: jillfra
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17861b7c25dfaf9bc9399e5261cdf2a5b43caf21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298738"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>O que aconteceu ao meu projeto WebJob (serviço conectado visual Studio Azure Storage)?
## <a name="references-added"></a>Referências adicionadas
O pacote Azure Storage NuGet foi adicionado ou atualizado no seu projeto Visual Studio.  
Este pacote adiciona as seguintes referências .NET:

* **Microsoft.Data.Edm**
* **Microsoft.data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **Sistema.Dados**
* **Sistema.Espacial**

## <a name="connection-string-for-azure-storage-added"></a>Fio de ligação para armazenamento azure adicionado
No ficheiro App.config do seu projeto, as entradas **AzureWebJobsStorage** e **AzureWebJobsDashboard** foram atualizadas com a cadeia e chave de ligação da conta de armazenamento selecionada.

Para mais informações, consulte os recursos de [documentação do Azure WebJobs.](https://go.microsoft.com/fwlink/?linkid=390226)

