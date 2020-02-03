---
title: Azure PowerShell - subscreva a conta de armazenamento Blob
description: Este artigo fornece uma amostra do script Azure PowerShell que mostra como subscrever eventos da Rede de Eventos para uma conta de Armazenamento Blob.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: a8a0982ca118663cbf0f7e4d72412ce8feda3c4b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721443"
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
| [Subscrição new-AzEventGrid](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Crie uma subscrição do Event Grid. |

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral da Aplicação Gerida do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
