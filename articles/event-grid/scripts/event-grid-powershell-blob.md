---
title: Azure PowerShell - subscreva a conta de armazenamento Blob
description: Este artigo fornece uma amostra do script Azure PowerShell que mostra como subscrever eventos de Grade de Eventos para uma conta blob Storage.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 15daa9843eb721ebf9caa2ae49e75fadbd0a4be8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87460801"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>Subscrever eventos para uma conta de armazenamento de Blobs com o PowerShell

Este script cria uma subscrição do Event Grid para os eventos para uma conta de armazenamento de Blobs.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Script de exemplo - estável

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza o seguinte comando para criar a subscrição de eventos. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [Nova-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Crie uma subscrição do Event Grid. |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral da Aplicação Gerida do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/get-started-azureps).
