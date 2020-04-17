---
title: Descrição geral do Azure Stream Analytics
description: Saiba mais sobre o Stream Analytics, um serviço gerido que o ajuda a analisar os dados de transmissão a partir da Internet de Coisas (IoT) em tempo real.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 673c6e2ed0aedcc186baf8c7e1884dd537f9567d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459853"
---
# <a name="what-is-azure-stream-analytics"></a>O que é Azure Stream Analytics?

O Azure Stream Analytics é um motor de análise em tempo real e complexo de processamento de eventos que é projetado para analisar e processar grandes volumes de dados de streaming rápido de várias fontes simultaneamente. Padrões e relações podem ser identificados em informações extraídas de várias fontes de entrada, incluindo dispositivos, sensores, clickstreams, feeds de redes sociais e aplicações. Estes padrões podem ser usados para desencadear ações e iniciar fluxos de trabalho tais criando alertas, alimentando informações a uma ferramenta de reporte, ou armazenando dados transformados para uso posterior. Além disso, o Stream Analytics está disponível no tempo de execução do Azure IoT Edge, e suporta a mesma linguagem ou sintaxe exata que a nuvem. 

Os seguintes cenários são exemplos de quando pode utilizar o Azure Stream Analytics:

* Analise os fluxos de telemetria em tempo real dos dispositivos IoT
* Análise de registos/clickstream da Web
* Análise geoespacial para gestão de frotas e veículos autónomos
* Monitorização remota e manutenção preditiva de ativos de alto valor
* Análise em tempo real de dados de Pontos de Vendas para controlo de inventário e deteção de anomalias

## <a name="how-does-stream-analytics-work"></a>Como funciona o Stream Analytics?

Um trabalho de Azure Stream Analytics consiste numa entrada, consulta e uma saída. Stream Analytics ingere dados de Hubs de Eventos Azure, Hub Azure IoT ou Armazenamento De Blob Azure. A consulta, que é baseada na linguagem de consulta SQL, pode ser usada para filtrar, classificar, agregar e juntar dados de streaming durante um período de tempo. Também pode estender este idioma SQL com funções definidas pelo utilizador JavaScript e C# (UDFs). Pode ajustar facilmente as opções de encomenda do evento e a duração das janelas de tempo quando pré-formar operações de agregação através de construções e/ou configurações simples de linguagem.

Cada trabalho tem uma saída para os dados transformados, e você pode controlar o que acontece em resposta à informação que analisou. Pode, por exemplo:

* Envie dados para serviços como Funções Azure, Tópicos de Autocarros de Serviço ou Filas para desencadear comunicações ou fluxos de trabalho personalizados a jusante.
* Envie dados para um dashboard Power BI para dashboardem em tempo real.
* Guarde dados em outros serviços de armazenamento do Azure para formar um modelo de aprendizagem automática com base em dados históricos ou realizar análises de lotes.

A imagem seguinte mostra como os dados são enviados para o Stream Analytics, analisados e enviados para outras ações como armazenamento ou apresentação:

![Gasoduto de introdução Stream Analytics](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Principais capacidades e vantagens

O Azure Stream Analytics foi concebido para ser fácil de utilizar, flexível, fiável e dimensionável para qualquer tamanho de trabalho. Está disponível em várias regiões azure. A imagem que se segue ilustra as principais capacidades do Azure Stream Analytics:

![Capacidades principais do Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Facilidade para começar

O Azure Stream Analytics é fácil de iniciar. São precisos apenas alguns cliques para se ligar a múltiplas fontes e pias, criando um gasoduto de ponta a ponta. O Stream Analytics pode ligar-se aos Hubs de [Eventos Azure](/azure/event-hubs/) e ao [Azure IoT Hub](/azure/iot-hub/) para o streaming de dados, bem como ao [armazenamento de Azure Blob](/azure/storage/common/storage-introduction) para ingerir dados históricos. A entrada de trabalho também pode incluir dados de referência estáticos ou em mudança lenta a partir do armazenamento Azure Blob ou da Base de [Dados SQL](stream-analytics-use-reference-data.md#azure-sql-database) que pode juntar-se a dados de streaming para realizar operações de pesquisa.

O Stream Analytics pode encaminhar a produção de trabalho para muitos sistemas de armazenamento, tais como [armazenamento Azure Blob,](/azure/storage/common/storage-introduction) [Base de Dados Azure SQL,](/azure/sql-database/) [Azure Data Lake Store](/azure/data-lake-store/)e [Azure CosmosDB.](/azure/cosmos-db/introduction) Pode executar análises de lotes na saída armazenada com o Azure HDInsight, ou pode enviar a saída para outro serviço, como Hubs de Eventos para consumo ou [Power BI](https://docs.microsoft.com/power-bi/) para visualização em tempo real.

Para toda a lista de saídas do Stream Analytics, consulte [as saídas de Compreender a partir do Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Produtividade dos programadores

O Azure Stream Analytics usa uma linguagem de consulta baseada em SQL simples que foi aumentada com fortes restrições temporais para analisar dados em movimento. Para definir as transformações dos trabalhos, é utilizada uma [linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) simples e declarativa, a qual lhe permite criar consultas e análises temporais complexas através da utilização de construções SQL simples. Como a linguagem de consulta stream analytics é consistente com a linguagem SQL, a familiaridade com o SQL é suficiente para começar a criar empregos. Também pode criar empregos utilizando ferramentas de desenvolvimento como o Azure PowerShell, [as ferramentas do Stream Analytics Visual Studio,](stream-analytics-tools-for-visual-studio-install.md)a [extensão do Código do Estúdio Visual Stream Analytics](quick-create-vs-code.md)ou os modelos do Gestor de Recursos Azure. A utilização de ferramentas do programador permite-lhe desenvolver consultas de transformação offline e utilizar o [pipeline CI/CD](stream-analytics-tools-for-visual-studio-cicd.md) para submeter trabalhos para o Azure.

A linguagem de consulta Stream Analytics oferece um vasto leque de funções para analisar e processar dados de streaming. Esta linguagem de consulta suporta simples manipulação de dados, funções de agregação e funções geoespaciais complexas. Pode editar consultas no portal e testá-las utilizando dados de amostra que são extraídos de um fluxo em direto.

Pode definir e invocar funções adicionais para alargar as capacidades da linguagem de consultas. Pode definir chamadas de função no Azure Machine Learning para tirar partido das soluções de Machine Learning Azure e integrar funções javaScript ou C# definidas pelo utilizador (UDFs) ou agregados definidos pelo utilizador para realizar cálculos complexos como parte de uma consulta de Stream Analytics.

## <a name="fully-managed"></a>Totalmente gerido

O Azure Stream Analytics é uma oferta sem servidor totalmente gerida (PaaS) no Azure. Não é preciso fornecer hardware ou gerir clusters para gerir os seus trabalhos. O Azure Stream Analytics gere totalmente o seu trabalho, criando complexos clusters de computação na nuvem e cuidando da afinação de desempenho necessária para executar o trabalho. A integração com o Azure Event Hubs e o Azure IoT Hub permite que o seu trabalho ingere milhões de eventos por segundo provenientes de várias fontes, para incluir dispositivos conectados, clickstreams e ficheiros de registo. Utilizando a característica de partição dos Centros de Eventos, pode dividir as computações em passos lógicos, cada um com a capacidade de ser mais dividido para aumentar a escalabilidade.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Corra na nuvem ou na borda inteligente

O Azure Stream Analytics pode correr na nuvem, para análises em larga escala, ou correr em IoT Edge para análises de latência ultra-baixa. O Azure Stream Analytics usa a mesma linguagem de consulta tanto na nuvem como na borda, permitindo aos desenvolvedores construir arquiteturas verdadeiramente híbridas para processamento de fluxo. 

## <a name="low-total-cost-of-ownership"></a>Baixo custo total de propriedade

Como serviço cloud, o Stream Analytics está otimizado para custo. Não existem custos iniciais envolvidos - só se paga pelas [unidades](stream-analytics-streaming-unit-consumption.md)de streaming que consome - e pela quantidade de dados processados. Não é necessário qualquer compromisso ou provisão de cluster, e pode escalar o trabalho para cima ou para baixo com base nas necessidades do seu negócio.

## <a name="mission-critical-ready"></a>Missão crítica pronta

O Azure Stream Analytics está disponível em várias regiões do mundo e foi concebido para executar cargas de trabalho críticas de missão, apoiando requisitos de fiabilidade, segurança e conformidade.

### <a name="reliability"></a>Fiabilidade

O Azure Stream Analytics garante o processamento de eventos e, pelo menos, uma vez entregues eventos, pelo que os eventos nunca se perdem. O processamento exatamente uma vez é garantido com a saída selecionada como descrito nas Garantias de Entrega de [Eventos](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

O Azure Stream Analytics tem capacidades de recuperação incorporadas caso a entrega de um evento falhe. O Stream Analytics também fornece pontos de verificação incorporados para manter o estado do seu trabalho e fornece resultados repetíveis.

Como serviço gerido, o Stream Analytics garante o processamento de eventos com uma disponibilidade de 99,9% a um nível de granularidade. Para mais informações, consulte a página [Stream Analytics SLA.](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) 

### <a name="security"></a>Segurança

Em termos de segurança, o Azure Stream Analytics encripta todas as comunicações recebidas e saídas e suporta o TLS 1.2. Os postos de controlo incorporados também estão encriptados. O Stream Analytics não armazena os dados de entrada, uma vez que todo o processamento é feito na memória.

### <a name="compliance"></a>Conformidade

O Azure Stream Analytics segue várias certificações de conformidade, conforme descrito na visão geral da conformidade com o [Azure.](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 

## <a name="performance"></a>Desempenho

O Stream Analytics pode processar milhões de eventos a cada segundo e pode apresentar resultados com tardios ultra baixos. Permite-lhe aumentar e escalar para lidar com grandes aplicações de processamento de eventos em tempo real e complexas. Stream Analytics suporta um desempenho mais elevado através da partição, permitindo que consultas complexas sejam paralelizadas e executadas em vários nós de streaming. O Azure Stream Analytics é construído em [Trill](https://github.com/Microsoft/Trill), um motor de análise de streaming de alta performance desenvolvido em colaboração com a Microsoft Research.

## <a name="next-steps"></a>Passos seguintes

Viu agora uma descrição geral do Azure Stream Analytics. Em seguida, pode criar o seu primeiro trabalho do Stream Analytics e experimentá-lo na prática:

* [Create a Stream Analytics job by using the Azure portal](stream-analytics-quick-create-portal.md) (Criar um trabalho do Stream Analytics com o portal do Azure).
* [Crie um trabalho de Stream Analytics utilizando o Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Crie um trabalho de Stream Analytics utilizando o Visual Studio](stream-analytics-quick-create-vs.md).
* [Crie um trabalho de Stream Analytics utilizando](quick-create-vs-code.md)o Código do Estúdio Visual .
