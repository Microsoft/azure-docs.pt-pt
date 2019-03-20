---
title: Compreender as entradas para o Azure Stream Analytics
description: Este artigo descreve o conceito de entradas num trabalho do Azure Stream Analytics, comparando a entrada de transmissão em fluxo para entrada de dados de referência.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 408a77dd5409f8604a059d3bc7f37ffe1e3d6ba2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855372"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Compreender as entradas para o Azure Stream Analytics

Tarefas do Azure Stream Analytics ligar a uma ou mais entradas de dados. Cada entrada define uma ligação a uma origem de dados existente. Stream Analytics aceita a entrada de dados de vários tipos de origens de eventos, incluindo os Hubs de eventos, o IoT Hub e armazenamento de Blobs. As entradas são referenciadas por nome na consulta SQL transmissão em fluxo que escreve para cada tarefa. Na consulta, pode associar várias entradas para mesclar dados ou comparar dados de transmissão em fluxo com uma pesquisa a dados de referência e transmitir os resultados para saídas. 

Stream Analytics tem integração de primeira classe dentre três tipos de recursos como entradas:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) 
- [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) 

Estes recursos de entrada podem residir na mesma subscrição do Azure como a tarefa de Stream Analytics, ou de uma subscrição diferente.

Pode utilizar o [portal do Azure](stream-analytics-quick-create-portal.md#configure-job-input), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input), e [Visual Studio](stream-analytics-tools-for-visual-studio-install.md)para criar, editar e testar as entradas da tarefa de Stream Analytics.

## <a name="stream-and-reference-inputs"></a>Entradas de Stream e referência
Como os dados são emitidos via push para uma origem de dados, é consumido por tarefa do Stream Analytics e processados em tempo real. Entradas são divididas em dois tipos: dados transmitir entradas e entradas de dados de referência.

### <a name="data-stream-input"></a>Entrada de fluxo de dados
Um fluxo de dados é uma sequência não vinculada de eventos ao longo do tempo. Tarefas do Stream Analytics tem de incluir dados, pelo menos, uma entrada de fluxo. Os Hubs de eventos, o IoT Hub e o armazenamento de BLOBs são suportadas como origens de entrada de fluxo de dados. Os Hubs de eventos são utilizados para recolher fluxos de eventos de vários dispositivos e serviços. Estes fluxos podem incluir feeds de atividade de redes sociais, informações de mercado de ações ou dados de sensores. Os Hubs IoT são otimizados para recolher dados de dispositivos ligados em cenários Internet das coisas (IoT).  Armazenamento de BLOBs pode ser utilizado como uma origem de entrada para a ingestão de dados em massa como um fluxo, como ficheiros de registo.  

Para obter mais informações sobre entradas de dados de transmissão em fluxo, consulte [Stream dados como entrada para o Stream Analytics](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Entrada de dados de referência
Stream Analytics também suporta a entrada conhecida como *dados de referência*. Dados de referência estão completamente estáticos ou alterados lentamente. É normalmente utilizado para efetuar a correlação e pesquisas. Por exemplo, pode associar dados a entrada de fluxo de dados para dados nos dados de referência, como poderia executar uma associação SQL para pesquisar por valores estáticos. Armazenamento de Blobs do Azure está atualmente a única origem de entrada suportada para dados de referência. Blobs de origem de dados de referência têm um limite de 300 MB de tamanho, dependendo da complexidade da consulta e atribuído unidades transmissão em fluxo.

Para obter mais informações sobre as entradas de dados de referência, consulte [usando dados de referência para pesquisas no Stream Analytics](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Quickstart: Criar uma tarefa de Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
