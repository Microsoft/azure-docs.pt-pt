---
title: Compreenda as entradas para a Azure Stream Analytics
description: Este artigo descreve o conceito de entradas num trabalho do Azure Stream Analytics, comparando a entrada de streaming com a entrada de dados de referência.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 6b841d6b47e009c3b01d9925e11d352c00ed5c19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75426439"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Compreenda as entradas para a Azure Stream Analytics

Os trabalhos do Azure Stream Analytics ligam-se a uma ou mais entradas de dados. Cada entrada define uma ligação a uma fonte de dados existente. O Stream Analytics aceita a entrada de dados de vários tipos de fontes de eventos, incluindo Centros de Eventos, IoT Hub e armazenamento blob. As entradas são referenciadas pelo nome na consulta SQL de streaming que escreve para cada trabalho. Na consulta, pode juntar-se a múltiplas entradas para misturar dados ou comparar dados de streaming com uma procura de dados de referência e passar os resultados para as saídas. 

Stream Analytics tem integração de primeira classe com três tipos de recursos como inputs:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) 

Estes recursos de entrada podem viver na mesma subscrição Azure que o seu trabalho Stream Analytics, ou de uma subscrição diferente.

Pode utilizar o [portal Azure](stream-analytics-quick-create-portal.md#configure-job-input), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput), [.NET API,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions) [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)e [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) para criar, editar e testar entradas de trabalho stream Analytics.

## <a name="stream-and-reference-inputs"></a>Entradas de fluxo e referência
À medida que os dados são levados para uma fonte de dados, é consumido pelo trabalho stream Analytics e processado em tempo real. As entradas são divididas em dois tipos: entradas de fluxo de dados e entradas de dados de referência.

### <a name="data-stream-input"></a>Entrada de fluxo de dados
Um fluxo de dados é uma sequência ilimitada de eventos ao longo do tempo. Os trabalhos do Stream Analytics têm de incluir, pelo menos, uma entrada de fluxo de dados. Os Hubs de Eventos, o Hub IoT e o Armazenamento de blobs são suportados como origens de entrada de fluxo de dados. Os Centros de Eventos são utilizados para recolher streams de eventos de vários dispositivos e serviços. Estes streams podem incluir feeds de atividade das redes sociais, informações sobre comércio de ações ou dados de sensores. Os IoT Hubs estão otimizados para recolher dados de dispositivos conectados em cenários de Internet of Things (IoT).  O armazenamento de bolhas pode ser usado como uma fonte de entrada para ingerir dados a granel como um fluxo, como ficheiros de registo.  

Para obter mais informações sobre entradas de dados de streaming, consulte [os dados do Stream como entrada no Stream Analytics](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Entrada de dados de referência
O Stream Analytics também suporta a entrada conhecida como *dados de referência.* Os dados de referência são completamente estáticos ou mudam lentamente. É normalmente usado para executar correlação e procuras. Por exemplo, pode juntar dados na entrada de fluxo de dados aos dados dos dados de referência, tanto quanto você realizaria uma junção SQL para procurar valores estáticos. O armazenamento Azure Blob e a Base de Dados Azure SQL são atualmente suportados como fontes de entrada para dados de referência. As bolhas de origem de dados de referência têm um limite de tamanho até 300 MB, dependendo da complexidade da consulta e das Unidades de Streaming alocadas (consulte a secção de [limitação](stream-analytics-use-reference-data.md#size-limitation) de tamanho da documentação de dados de referência para mais detalhes).

Para obter mais informações sobre entradas de dados de referência, consulte [utilizando dados de referência para pesquisas no Stream Analytics](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Próximos passos
> [!div class="nextstepaction"]
> [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
