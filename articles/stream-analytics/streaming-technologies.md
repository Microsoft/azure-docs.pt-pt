---
title: Escolha uma solução de processamento em tempo real e stream no Azure
description: Saiba como escolher a tecnologia de análise e processamento de streaming em tempo real para construir a sua aplicação no Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 2146b1bd782aba5d98729a2d37d956744e469ba1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75860253"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Escolha uma tecnologia de análise e processamento de streaming em tempo real no Azure

Existem vários serviços disponíveis para análise em tempo real e processamento de streaming no Azure. Este artigo fornece a informação que precisa para decidir qual a tecnologia mais adequada para a sua aplicação.

## <a name="when-to-use-azure-stream-analytics"></a>Quando utilizar o Azure Stream Analytics

O Azure Stream Analytics é o serviço recomendado para análise de streaming no Azure. Destina-se a uma ampla gama de cenários que incluem, mas não se limitam a:

* Dashboards para visualização de dados
* [Alertas](stream-analytics-set-up-alerts.md) em tempo real de padrões ou anomalias temporais e espaciais
* Extrair, Transformar, Carregar (ETL)
* [Padrão de sourcing de eventos](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

Adicionar um trabalho azure Stream Analytics à sua aplicação é a forma mais rápida de fazer streaming de análise saem e funcionando em Azure, utilizando o idioma SQL que já conhece. O Azure Stream Analytics é um serviço de trabalho, por isso não tens de gastar tempo a gerir clusters, e não tens de te preocupar com o tempo de inatividade com um SLA de 99,9% a nível de trabalho. A faturação também é feita a nível de trabalho, tornando os custos de arranque baixos (uma Unidade de Streaming), mas escaláveis (até 192 Unidades de Streaming). É muito mais rentável gerir alguns trabalhos da Stream Analytics do que gerir e manter um cluster.

O Azure Stream Analytics tem uma experiência rica fora da caixa. Pode aproveitar imediatamente as seguintes funcionalidades sem qualquer configuração adicional:

* Operadores temporais incorporados, tais como [agregados com janelas,](stream-analytics-window-functions.md)juntas temporais e funções analíticas temporais.
* Adaptadores de [entrada](stream-analytics-add-inputs.md) e [saída](stream-analytics-define-outputs.md) Native Azure
* Suporte para dados de [referência](stream-analytics-use-reference-data.md) em mudança lenta (também conhecidos como mesas de lookup), incluindo a junção com dados de referência geoespacial para geofencing.
* Soluções integradas, como [a Deteção de Anomalias](stream-analytics-machine-learning-anomaly-detection.md)
* Janelas de tempo súltiplo na mesma consulta
* Capacidade de compor múltiplos operadores temporais em sequências arbitrárias.
* Menos de 100 ms de latência de ponta a ponta desde a entrada que chega ao Event Hubs, até à aterragem de saída em Event Hubs, incluindo o atraso da rede de e para os Centros de Eventos, em alta potência sustentada

## <a name="when-to-use-other-technologies"></a>Quando usar outras tecnologias

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Você quer escrever UDFs, UDAs e desserializers personalizados em uma língua diferente de JavaScript ou C #

O Azure Stream Analytics suporta funções definidas pelo utilizador (UDF) ou agregados definidos pelo utilizador (UDA) no JavaScript para trabalhos na nuvem e C# para trabalhos ioT Edge. C# os desserializers definidos pelo utilizador também são suportados. Se quiser implementar um desserializer, um UDF ou um UDA noutras línguas, como Java ou Python, pode utilizar o Spark Structured Streaming. Também pode executar o Event Hubs **EventProcessorHost** nas suas próprias máquinas virtuais para fazer o processamento de streaming arbitrário.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>A sua solução está num ambiente multi-nuvem ou no local

O Azure Stream Analytics é a tecnologia proprietária da Microsoft e só está disponível no Azure. Se precisar da sua solução para ser portátil através de Clouds ou no local, considere tecnologias de código aberto como Spark Structured Streaming ou Storm.

## <a name="next-steps"></a>Passos seguintes

* [Crie um trabalho de Stream Analytics utilizando o portal Azure](stream-analytics-quick-create-portal.md)
* [Criar um trabalho do Stream Analytics com o Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Crie um trabalho de Stream Analytics usando o Estúdio Visual](stream-analytics-quick-create-vs.md)
* [Crie um trabalho de Stream Analytics usando o Código de Estúdio Visual](quick-create-vs-code.md)
