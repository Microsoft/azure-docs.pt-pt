---
title: Azure PowerShell-assinar a assinatura do Azure
description: Azure PowerShell & amostra de script da grade de eventos do Azure – assinar assinatura do Azure
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: 8ab0d61e2c7ec23b7c3ed82d460cbb53711d5c7f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790505"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-powershell"></a>Subscrever eventos para uma subscrição do Azure com o PowerShell

Este script cria uma subscrição do Event Grid para os eventos para uma subscrição do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

O script de exemplo de visualização requer o módulo de grade de eventos. Para instalar, execute `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Script de exemplo - estável

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.ps1 "Subscribe to Azure subscription")]

## <a name="sample-script---preview-module"></a>Script de exemplo – módulo de visualização

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.ps1 "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza o seguinte comando para criar a subscrição de eventos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Crie uma subscrição do Event Grid. |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral da Aplicação Gerida do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
