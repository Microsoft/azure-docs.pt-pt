---
title: Exemplo de script do Azure PowerShell - Criar um tópico personalizado | Microsoft Docs
description: Este artigo fornece uma amostra do script Azure PowerShell que mostra como criar um tópico personalizado da Grade de Eventos.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: tomfitz
ms.openlocfilehash: 915b8e13adaa440063f5db62b72fa39e1035d576
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87460784"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>Criar um tópico personalizado do Event Grid com o PowerShell

Este script cria um tópico personalizado do Event Grid.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>Explicação do script

Este script utiliza o seguinte comando para criar o tópico personalizado. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzEventGridTopic](/powershell/module/az.eventgrid/new-azeventgridtopic) | Crie um tópico personalizado do Event Grid. |

## <a name="next-steps"></a>Passos seguintes

* Para obter uma introdução às aplicações geridas, veja [Descrição geral da Aplicação Gerida do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/get-started-azureps).
