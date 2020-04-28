---
title: Compreender as inputs para azure stream analytics
description: Este artigo descreve o conceito de inputs num trabalho do Azure Stream Analytics, comparando a entrada de streaming com a entrada de dados de referência.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 6b841d6b47e009c3b01d9925e11d352c00ed5c19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75426439"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>Compreender as inputs para azure stream analytics

Os trabalhos da Azure Stream Analytics ligam-se a uma ou mais informações de dados. Cada entrada define uma ligação a uma fonte de dados existente. Stream Analytics aceita entrada de dados de vários tipos de fontes de eventos, incluindo Hubs de Eventos, Hub IoT e armazenamento blob. As inputs são referenciadas pelo nome na consulta SQL de streaming que escreve para cada trabalho. Na consulta, pode juntar várias inputs para misturar dados ou comparar dados de streaming com uma procura de dados de referência e passar os resultados para as saídas. 

O Stream Analytics tem integração de primeira classe com três tipos de recursos como inputs:
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) 

Estes recursos de entrada podem viver na mesma subscrição do Azure que o seu trabalho stream analytics, ou a partir de uma subscrição diferente.

Pode utilizar o [portal Azure,](stream-analytics-quick-create-portal.md#configure-job-input) [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput) [.NET API,](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions) [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)e [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) para criar, editar e testar as inputs de trabalho do Stream Analytics.

## <a name="stream-and-reference-inputs"></a>Entradas de fluxo e referência
À medida que os dados são empurrados para uma fonte de dados, é consumido pelo trabalho do Stream Analytics e processado em tempo real. As entradas são divididas em dois tipos: entradas de fluxo de dados e entradas de dados de referência.

### <a name="data-stream-input"></a>Entrada de fluxo de dados
Um fluxo de dados é uma sequência ilimitada de eventos ao longo do tempo. Os trabalhos do Stream Analytics têm de incluir, pelo menos, uma entrada de fluxo de dados. Os Hubs de Eventos, o Hub IoT e o Armazenamento de blobs são suportados como origens de entrada de fluxo de dados. Os Hubs de Eventos são usados para recolher fluxos de eventos de vários dispositivos e serviços. Estes fluxos podem incluir feeds de atividade seleções de redes sociais, informações de stock trade ou dados de sensores. Os Hubs IoT são otimizados para recolher dados de dispositivos conectados em cenários de Internet das Coisas (IoT).  O armazenamento de blob pode ser usado como uma fonte de entrada para ingerir dados a granel como um fluxo, como ficheiros de registo.  

Para obter mais informações sobre as inputs de dados de streaming, consulte [os dados do Stream como entrada no Stream Analytics](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>Entrada de dados de referência
O Stream Analytics também suporta a entrada conhecida como dados de *referência*. Os dados de referência são completamente estáticos ou mudam lentamente. É normalmente usado para realizar correlação e procurações. Por exemplo, pode juntar dados na entrada de dados para dados nos dados de referência, por muito que realize uma adesão sQL para procurar valores estáticos. O armazenamento azure Blob e a Base de Dados Azure SQL são atualmente suportados como fontes de entrada para dados de referência. As bolhas de origem de dados de referência têm um limite de até 300 MB de tamanho, dependendo da complexidade da consulta e das unidades de streaming atribuídas (ver a secção de [limitação](stream-analytics-use-reference-data.md#size-limitation) de tamanho da documentação de dados de referência para mais detalhes).

Para obter mais informações sobre as inputs de dados de referência, consulte A utilização de dados de [referência para procura supérfluas no Stream Analytics](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
