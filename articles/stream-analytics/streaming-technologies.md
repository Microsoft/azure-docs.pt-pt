---
title: Escolha uma análise em tempo real e a tecnologia de processamento no Azure de transmissão em fluxo
description: Saiba mais sobre como escolher a análise à direita em tempo real e a tecnologia de processamento de transmissão em fluxo para criar a sua aplicação no Azure.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 85d6ed80da93f90e6dc0feaee7081ee3f36f1bf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242694"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Escolha uma análise em tempo real e a tecnologia de processamento no Azure de transmissão em fluxo

Vários serviços estão disponíveis para análise em tempo real e processamento de transmissão em fluxo no Azure. Este artigo fornece as informações necessárias decidir qual a tecnologia que é a melhor opção para a sua aplicação.

## <a name="when-to-use-azure-stream-analytics"></a>Quando utilizar o Azure Stream Analytics

O Azure Stream Analytics é o serviço recomendado para o stream analytics no Azure. Destina-se para uma vasta gama de cenários que incluem, mas não está limitado a:

* Dashboards para visualização de dados
* Em tempo real [alertas](stream-analytics-set-up-alerts.md) de padrões temporais e geográficos ou de anomalias
* Extrair, Transformar, Carregar (ETL)
* [Padrão de origem do evento](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

A adição de um Azure Stream Analytics tarefa ao seu aplicativo é a forma mais rápida de obter análise de transmissão a cópia de segurança e em execução no Azure, utilizando a linguagem SQL que já conhece. O Azure Stream Analytics é um serviço de tarefa, para que não temos de clusters de gestão do tempo, e não precisa se preocupar sobre o tempo de inatividade com um SLA de 99,9% no nível de tarefa. Faturação também é feita no nível tarefa tornando os custos com inicialização baixa (uma unidade transmissão em fluxo), mas escaláveis (até 192 unidades de transmissão em fluxo). É muito mais económica para executar algumas tarefas do Stream Analytics que executamos e mantemos um cluster.

O Azure Stream Analytics tem uma Rica experiência de out-of-the-box. Pode imediatamente tirar partido das seguintes funcionalidades sem qualquer configuração adicional:

* Operadores temporais incorporadas, tal como [agregados em janelas](stream-analytics-window-functions.md), associações temporais e funções de análise temporais.
* Nativa do Azure [entrada](stream-analytics-add-inputs.md) e [saída](stream-analytics-define-outputs.md) adaptadores
* Suporte para a alteração lenta [dados de referência](stream-analytics-use-reference-data.md) (também conhecido como um tabelas de pesquisa), incluindo a junção com dados de referência de geoespacial para barreira geográfica.
* Integrado a soluções, como [deteção de anomalias](stream-analytics-machine-learning-anomaly-detection.md)
* Várias janelas de tempo da mesma consulta
* Capacidade de criar vários operadores temporais nas sequências arbitrários.
* Em 100 ms a latência de ponto-a-ponto de entrada que chegam ao Hubs de eventos, para a saída de destino nos Hubs de eventos, incluindo o atraso de rede de e para os Hubs de eventos a alto débito a constante

## <a name="when-to-use-other-technologies"></a>Quando usar outras tecnologias

### <a name="you-need-to-input-from-or-output-to-kafka"></a>Terá de entrada do ou de saída ao Kafka

O Azure Stream Analytics não tem uma entrada do Apache Kafka ou adaptador de saída. Se tiver de eventos de destino numa ou precisar de enviar para o Kafka, e não tiver um requisito para executar seu próprio cluster do Kafka, pode continuar a utilizar o Stream Analytics através do envio de eventos para Hubs de eventos com a API de Kafka de Hubs de eventos sem alterar o remetente do evento. Se tiver de executar seu próprio cluster do Kafka, pode utilizar o Spark Structured Streaming, que é totalmente suportado em [Azure Databricks](../azure-databricks/index.yml), ou do Storm na [Azure HDInsight](../hdinsight/storm/apache-storm-tutorial-get-started-linux.md).

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Quer escrever UDFs, UDAs e desserializadores personalizados num idioma diferente do JavaScript ouC#

O Azure Stream Analytics suporta funções definidas pelo utilizador (UDF) ou agregações definidas pelo utilizador (UDA) em JavaScript para tarefas na cloud e C# para as tarefas do IoT Edge. C#definido pelo utilizador desserializadores também são suportados. Se pretender implementar um desserializador, uma UDF ou uma UDA em outras linguagens, como Java ou Python, pode utilizar a transmissão estruturada do Spark. Também pode executar os Hubs de eventos **EventProcessorHost** nas suas próprias máquinas virtuais para efetuar o processamento de transmissão em fluxo arbitrário.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>A sua solução é num ambiente de várias Clouds ou no local

O Azure Stream Analytics é a tecnologia proprietária da Microsoft e só está disponível no Azure. Se precisar de sua solução seja portátil entre nuvens ou no local, considere tecnologias de open source, como a transmissão estruturada do Spark ou Storm.

## <a name="next-steps"></a>Passos Seguintes

* [Criar uma tarefa de Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)
* [Criar uma tarefa do Stream Analytics com o Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Criar uma tarefa do Stream Analytics com o Visual Studio](stream-analytics-quick-create-vs.md)
* [Criar uma tarefa do Stream Analytics com o Visual Studio Code](quick-create-vs-code.md)