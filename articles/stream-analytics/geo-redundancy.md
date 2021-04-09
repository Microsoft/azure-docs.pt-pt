---
title: Alcançar geo-redundância para empregos Azure Stream Analytics
description: Este artigo descreve como alcançar a geo-redundância dos empregos da Azure Stream Analytics em vez de geo-failover.
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/26/2020
ms.openlocfilehash: 86a90cbcbb3381e853dcea766364642a89dafd7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015526"
---
# <a name="achieve-geo-redundancy-for-azure-stream-analytics-jobs"></a>Alcançar geo-redundância para empregos Azure Stream Analytics

O Azure Stream Analytics não fornece geo-failover automático, mas pode alcançar a geoduferição através da implementação de trabalhos idênticos do Stream Analytics em várias regiões do Azure. Cada trabalho liga-se a uma entrada local e fontes de saída locais. É da responsabilidade da sua aplicação enviar dados de entrada para os dois inputs regionais e conciliar entre as duas saídas regionais. Os trabalhos da Stream Analytics são duas entidades distintas.

O diagrama seguinte retrata uma distribuição de trabalho de Stream Analytics geo-redundante com a entrada do Event Hub e a saída da Base de Dados Azure.

:::image type="content" source="media/geo-redundancy/geo-redundant-jobs.png" alt-text="diagrama de trabalhos de análise de fluxos geo redundantes":::

## <a name="primarysecondary-strategy"></a>Estratégia primária/secundária

A sua aplicação precisa de gerir qual a base de dados de saída da região considerada a principal e que é considerada secundária. Numa falha na região primária, a aplicação muda para a base de dados secundária e começa a ler atualizações a partir dessa base de dados. O mecanismo real que permite minimizar as leituras duplicadas depende da sua aplicação.Pode simplificar este processo escrevendo informações adicionais para a saída. Por exemplo, pode adicionar um timetamp ou um ID de sequência a cada saída para fazer do salto de linhas duplicadas uma operação trivial. Uma vez restaurada a região primária, alcança a base de dados secundária usando mecânicas semelhantes.

Embora diferentes tipos de entrada e saída permitam diferentes opções de geo-replicação, recomendamos a utilização do padrão descrito neste artigo para alcançar a geo-redundância, pois proporciona flexibilidade e controlo tanto para os produtores de eventos como para os consumidores de eventos.

## <a name="next-steps"></a>Passos seguintes

* [Monitorize e gera os trabalhos de Azure Stream Analytics com a PowerShell](stream-analytics-monitor-and-manage-jobs-use-powershell.md)
* [Depuração orientada por dados no Azure Stream Analytics](stream-analytics-job-diagram-with-metrics.md)