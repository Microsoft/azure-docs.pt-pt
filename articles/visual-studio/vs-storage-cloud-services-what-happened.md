---
title: O que aconteceu com o meu projeto de serviço na nuvem?
description: Descreve o que acontece num projeto de serviços na nuvem depois de ligar a uma conta de armazenamento Azure usando serviços conectados do Estúdio Visual
services: storage
author: ghogen
manager: jillfra
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ac5a635b687e51ddd34899717497548296c2fc23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72298761"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>O que aconteceu ao meu projeto de serviços cloud (serviço ligado de Armazenamento Azure do Visual Studio)?
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
Foram criados elementos com a cadeia e a chave de ligação da conta de armazenamento selecionada. Foram feitas modificações nos seguintes ficheiros:

* **ServiceDefinition.csdef**
* **Configuração de serviço.Cloud.cscfg**
* **Configuração do serviço.local.cscfg**

