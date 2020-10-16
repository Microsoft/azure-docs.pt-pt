---
title: Escolha uma solução de processamento em tempo real e stream no Azure
description: Saiba como escolher a tecnologia de análise e processamento de streaming em tempo real para construir a sua aplicação no Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: a204aacc0061091e0a273581e766bb669d191f0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903710"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Escolha uma tecnologia de processamento de análise e streaming em tempo real no Azure

Existem vários serviços disponíveis para análise em tempo real e processamento de streaming no Azure. Este artigo fornece as informações necessárias para decidir qual a tecnologia mais adequada para a sua aplicação.

## <a name="when-to-use-azure-stream-analytics"></a>Quando usar a Azure Stream Analytics

Azure Stream Analytics é o serviço recomendado para análise de streaming no Azure. Destina-se a uma vasta gama de cenários que incluem, mas não se limitam a:

* Dashboards para visualização de dados
* [Alertas](stream-analytics-set-up-alerts.md) em tempo real de padrões temporais e espaciais ou anomalias
* Extrair, Transformar, Carregar (ETL)
* [Padrão de sourcing de evento](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Adicionar um trabalho Azure Stream Analytics à sua aplicação é a forma mais rápida de obter análises de streaming em funcionamento em Azure, utilizando o idioma SQL que já conhece. O Azure Stream Analytics é um serviço de trabalho, por isso não tens de passar tempo a gerir clusters, e não tens de te preocupar com o tempo de inatividade com um SLA de 99,9% a nível de emprego. A faturação também é feita a nível de trabalho, tornando os custos de arranque baixos (uma Unidade de Streaming), mas escaláveis (até 192 Unidades de Streaming). É muito mais rentável executar alguns trabalhos stream Analytics do que para executar e manter um cluster.

A Azure Stream Analytics tem uma experiência rica fora da caixa. Pode tirar imediatamente partido das seguintes funcionalidades sem qualquer configuração adicional:

* Operadores temporais incorporados, tais como [agregados janelados,](stream-analytics-window-functions.md)juntas temporais e funções analíticas temporais.
* Adaptadores de [entrada](stream-analytics-add-inputs.md) e [saída](stream-analytics-define-outputs.md) nativos do Azure
* Suporte para [alterações lentas de dados de referência](stream-analytics-use-reference-data.md) (também conhecidos como tabelas de procura), incluindo a junção com dados geoespaciais de referência para geofencing.
* Soluções integradas, como [deteção de anomalias](stream-analytics-machine-learning-anomaly-detection.md)
* Janelas de tempo múltiplas na mesma consulta
* Capacidade de compor múltiplos operadores temporais em sequências arbitrárias.
* Menores de 100 ms de latência de ponta a ponta desde a entrada que chega aos Centros de Eventos, até à aterragem de produção em Event Hubs, incluindo o atraso da rede de e para os Centros de Eventos, a uma produção elevada sustentada

## <a name="when-to-use-other-technologies"></a>Quando usar outras tecnologias

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Você quer escrever UDFs, UDAs e deserializantes personalizados em uma língua diferente de JavaScript ou C #

O Azure Stream Analytics suporta funções definidas pelo utilizador (UDF) ou agregados definidos pelo utilizador (UDA) em JavaScript para trabalhos na nuvem e C# para trabalhos IoT Edge. Os deserializadores definidos pelo utilizador também são suportados. Se quiser implementar um deserializador, um UDF ou um UDA em outras línguas, como Java ou Python, pode utilizar o Spark Structured Streaming. Também pode executar o **EventProcessorHost** dos Seus próprios dispositivos virtuais para fazer o processamento de streaming arbitrário.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>A sua solução está num ambiente multi-nuvem ou no local

O Azure Stream Analytics é a tecnologia proprietária da Microsoft e só está disponível no Azure. Se precisar da sua solução para ser portátil através de Nuvens ou no local, considere tecnologias de código aberto como o Spark Structured Streaming ou Storm.

## <a name="next-steps"></a>Passos seguintes

* [Crie um trabalho stream analytics utilizando o portal Azure](stream-analytics-quick-create-portal.md)
* [Criar um trabalho do Stream Analytics com o Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Crie um trabalho stream analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Crie um trabalho stream analytics usando o Código do Estúdio Visual](quick-create-visual-studio-code.md)
