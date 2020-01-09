---
title: Entender as entradas para Azure Stream Analytics
description: Este artigo descreve o conceito de entradas em um trabalho Azure Stream Analytics, comparando a entrada de streaming para entrada de dados de referência.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 6b841d6b47e009c3b01d9925e11d352c00ed5c19
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426439"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Entender as entradas para Azure Stream Analytics

Azure Stream Analytics trabalhos se conectam a uma ou mais entradas de dados. Cada entrada define uma conexão com uma fonte de dados existente. Stream Analytics aceita dados recebidos de vários tipos de fontes de eventos, incluindo hubs de eventos, Hub IoT e armazenamento de BLOBs. As entradas são referenciadas pelo nome na consulta SQL de streaming que você escreve para cada trabalho. Na consulta, você pode unir várias entradas para misturar dados ou comparar dados de streaming com uma pesquisa para dados de referência e passar os resultados para saídas. 

Stream Analytics tem integração de primeira classe com três tipos de recursos como entradas:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) 
- [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) 

Esses recursos de entrada podem residir na mesma assinatura do Azure que seu trabalho de Stream Analytics ou em uma assinatura diferente.

Você pode usar o [portal do Azure](stream-analytics-quick-create-portal.md#configure-job-input), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), [API .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [API REST](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)e o [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) para criar, editar e testar entradas de trabalho Stream Analytics.

## <a name="stream-and-reference-inputs"></a>Entradas de fluxo e referência
Como os dados são enviados por push para uma fonte de dados, eles são consumidos pelo trabalho de Stream Analytics e processados em tempo real. As entradas são divididas em dois tipos: entradas de fluxo de dados e entradas de dados de referência.

### <a name="data-stream-input"></a>Entrada de fluxo de dados
Um fluxo de dados é uma sequência não vinculada de eventos ao longo do tempo. Os trabalhos do Stream Analytics têm de incluir, pelo menos, uma entrada de fluxo de dados. Os Hubs de Eventos, o Hub IoT e o Armazenamento de blobs são suportados como origens de entrada de fluxo de dados. Os hubs de eventos são usados para coletar fluxos de eventos de vários dispositivos e serviços. Esses fluxos podem incluir feeds de atividades de mídia social, informações comerciais de ações ou dados de sensores. Os hubs IoT são otimizados para coletar dados de dispositivos conectados em cenários de Internet das Coisas (IoT).  O armazenamento de BLOBs pode ser usado como uma fonte de entrada para ingerir dados em massa como um fluxo, como arquivos de log.  

Para obter mais informações sobre streaming de entradas de dados, consulte [transmitir dados como entrada no Stream Analytics](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Entrada de dados de referência
Stream Analytics também dá suporte à entrada conhecida como *dados de referência*. Os dados de referência são completamente estáticos ou são alterados lentamente. Normalmente, ele é usado para executar correlação e pesquisas. Por exemplo, você pode unir dados na entrada de fluxo de dados aos dados nos dados de referência, da mesma forma que faria com uma junção SQL para pesquisar valores estáticos. O armazenamento de BLOBs do Azure e o banco de dados SQL do Azure são atualmente compatíveis como fontes de entrada para os mesmos. Os blobs de fonte de dados de referência têm um limite de até 300 MB de tamanho, dependendo da complexidade da consulta e das unidades de streaming alocadas (consulte a seção [limitação de tamanho](stream-analytics-use-reference-data.md#size-limitation) da documentação de dados de referência para obter mais detalhes).

Para obter mais informações sobre as entradas de dados de referência, consulte [usando dados de referência para pesquisas no Stream Analytics](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Início rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
