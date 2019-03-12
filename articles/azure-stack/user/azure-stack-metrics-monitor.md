---
title: Consumir dados de monitorização do Azure Stack | Documentos da Microsoft
description: Saiba mais sobre as opções para o consumo de dados de monitorização do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2018
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 174038c611d9e804817d49478f99c643f5280f7b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773051"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Como consumir dados de monitorização do Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Pode encontrar dados de monitorização num único local com o pipeline do Azure Monitor, tal como o Azure Monitor no global Azure. Mas nem todos os dados de monitorização encontrados no global Azure está disponível no Azure Stack. Neste artigo, pode encontrar um resumo das várias formas, por meio de programação pode ingerir dados de monitorização do serviço.
 
## <a name="options-for-data-consumption"></a>Opções para consumo de dados

| Tipo de dados | Categoria | Serviços suportados | Métodos de acesso |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Métricas de nível de plataforma do Azure Monitor | Métricas | [Métricas suportadas com o Azure Monitor no Azure Stack](azure-stack-metrics-supported.md) | API REST |
| As métricas de SO convidado (por exemplo, a contagem de des) de computação | Métricas | Windows e máquinas virtuais do Linux | Tabela de armazenamento ou um blob:<br>Windows ou o diagnóstico do Linux do Azure <br>Hub de eventos:<br>Diagnóstico do Azure para Windows |
| Métricas de armazenamento | Métricas | Storage do Azure | Tabela de armazenamento:<br>Análise de Armazenamento |
| Registo de atividades | Eventos | Todos os Serviços do Azure | REST API:<br>Evento de Monitor do Azure API |
| Registos do SO convidado (por exemplo, o IIS, o ETW, syslogs) de computação | Eventos | Windows e máquinas virtuais do Linux | Tabela de armazenamento ou um blob:<br>Windows ou o diagnóstico do Linux do Azure <br>Hub de eventos:<br>Diagnóstico do Azure para Windows |
| Registos de armazenamento | Eventos | Storage do Azure | Tabela de armazenamento:<br>Análise de Armazenamento |

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [monitorizar o Azure no Azure Stack](azure-stack-metrics-azure-data.md).
