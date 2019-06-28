---
title: Descrição geral do Azure Stream Analytics
description: Saiba mais sobre o Stream Analytics, um serviço gerido que o ajuda a analisar os dados de transmissão a partir da Internet de Coisas (IoT) em tempo real.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: c50ad2b045f32daf53033318123b68e4b2d58db5
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329328"
---
# <a name="what-is-azure-stream-analytics"></a>O que é o Azure Stream Analytics?

O Azure Stream Analytics é uma análise em tempo real e o motor de processamento de eventos complexo que foi concebido para analisar e processar grandes volumes de dados de transmissão em fluxo rápido de várias fontes em simultâneo. Padrões e relações podem ser identificadas no informações extraídas de um número de origens de entrada, incluindo dispositivos, sensores, clickstreams, feeds de redes sociais e aplicativos. Estes padrões podem ser utilizados para acionar ações e iniciar fluxos de trabalho desse tipo criar alertas, informações para ferramentas de relatórios de manutenção ou armazenamento de dados transformados para utilização posterior. Além disso, o Stream Analytics está disponível no runtime do Azure IoT Edge e suporta a mesma linguagem exata ou sintaxe como na cloud. 

Os cenários seguintes são exemplos de quando pode utilizar o Azure Stream Analytics:

* Analisar fluxos de telemetria em tempo real de dispositivos IoT
* Análise de registos/clickstream da Web
* Análise geoespacial para gestão de frotas e veículos autónomos
* Monitorização remota e manutenção preditiva de ativos de valor elevado
* Análise em tempo real de dados de Pontos de Vendas para controlo de inventário e deteção de anomalias

## <a name="how-does-stream-analytics-work"></a>Como funciona o Stream Analytics?

Uma tarefa do Azure Stream Analytics é composta por uma entrada, consulta e uma saída. Stream Analytics ingere dados de Hubs de eventos do Azure, o IoT Hub do Azure ou o armazenamento de Blobs do Azure. A consulta, que é baseada na linguagem de consulta SQL, pode ser utilizado para facilmente filtrar, ordenar, Agregar e associar dados de transmissão ao longo de um período de tempo. Também pode expandir essa linguagem SQL com o JavaScript e C# (UDFs) de funções definidas pelo utilizador. Pode ajustar facilmente o evento de ordenação de opções e a duração das janelas de tempo quando preforming operações de agregação através de construções de linguagem simples e/ou configurações.

Cada tarefa tem uma saída para os dados transformados e pode controlar o que acontece em resposta às informações que analisou. Pode, por exemplo:

* Envie dados para serviços, como as funções do Azure, tópicos do Service Bus ou filas para acionar a comunicação ou downstream uma fluxos de trabalho personalizados.
* Envie dados para um dashboard do Power BI para vistas em tempo real.
* Store dados noutros serviços de armazenamento do Azure para preparar um modelo de machine learning com base nos dados históricos ou executar análises de lote.

A imagem seguinte mostra como os dados são enviados para o Stream Analytics, analisados e enviados para outras ações, como o armazenamento ou apresentação:

![Pipeline de introdução do Stream Analytics](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Principais capacidades e vantagens

O Azure Stream Analytics foi concebido para ser fácil de utilizar, flexível, fiável e dimensionável para qualquer tamanho de trabalho. Está disponível em várias regiões do Azure. A imagem seguinte ilustra as principais funcionalidades do Azure Stream Analytics:

![Capacidades principais do Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Facilidade para começar

O Azure Stream Analytics é fácil começar. Demora apenas alguns cliques para ligar a várias origens e sinks, criar um pipeline ponto a ponto. Stream Analytics consegue ligar à [os Hubs de eventos do Azure](/azure/event-hubs/) e [IoT Hub do Azure](/azure/iot-hub/) para ingestão de dados de transmissão em fluxo, bem como [armazenamento de Blobs do Azure](/azure/storage/storage-introduction) para ingerir dados históricos. Entrada da tarefa também pode incluir dados de referência estáticos ou com poucas alterações do armazenamento de Blobs do Azure ou [base de dados SQL](stream-analytics-use-reference-data.md#azure-sql-database) que pode associar a transmissão em fluxo de dados para efetuar operações de pesquisa.

Stream Analytics pode encaminhar a saída da tarefa para muitos sistemas de armazenamento, tal como [armazenamento de Blobs do Azure](/azure/storage/storage-introduction), [base de dados do Azure SQL](/azure/sql-database/), [Azure Data Lake Store](/azure/data-lake-store/), e [Azure CosmosDB](/azure/cosmos-db/introduction). Pode executar análises de lote de saída armazenados com o Azure HDInsight, ou pode enviar a saída para outro serviço, como os Hubs de eventos para consumo ou [Power BI](https://docs.microsoft.com/power-bi/) para visualização em tempo real.

Para obter a lista completa de saídas do Stream Analytics, consulte [compreender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Produtividade do Programador

O Azure Stream Analytics utiliza uma linguagem de consulta simples baseada em SQL que foi melhorada com limitações temporais poderosas para analisar dados em movimento. Para definir as transformações dos trabalhos, é utilizada uma [linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) simples e declarativa, a qual lhe permite criar consultas e análises temporais complexas através da utilização de construções SQL simples. Como linguagem de consulta do Stream Analytics é consistente para a linguagem SQL, familiaridade com o SQL é suficiente para começar a criar trabalhos. Também pode criar tarefas com as ferramentas de desenvolvedor, como o Azure PowerShell, [ferramentas do Stream Analytics para Visual Studio](stream-analytics-tools-for-visual-studio-install.md), o [extensão do Stream Analytics Visual Studio Code](quick-create-vs-code.md), ou modelos Azure Resource Manager . A utilização de ferramentas do programador permite-lhe desenvolver consultas de transformação offline e utilizar o [pipeline CI/CD](stream-analytics-tools-for-visual-studio-cicd.md) para submeter trabalhos para o Azure.

A linguagem de consulta do Stream Analytics oferece um vasto conjunto de funções para analisar e processar dados de transmissão em fluxo. Essa linguagem de consulta suporta manipulação de dados simples, funções de agregação e funções geoespaciais complexas. Pode editar as consultas no portal e testá-las com dados de exemplo que são extraídos de um fluxo em direto.

Pode definir e invocar funções adicionais para alargar as capacidades da linguagem de consultas. Pode definir chamadas de função no serviço do Azure Machine Learning para tirar partido das soluções do Azure Machine Learning e integrar o JavaScript ou C# funções definidas pelo utilizador (UDFs) ou agregações definidas pelo utilizador para realizar cálculos complexos como parte um Consulta do Stream Analytics.

## <a name="fully-managed"></a>Totalmente gerido

O Azure Stream Analytics é uma oferta sem servidor totalmente gerida (PaaS) no Azure. Não tem qualquer hardware de aprovisionar ou gerir clusters para executar suas tarefas. O Azure Stream Analytics gere totalmente o trabalho ao configurar o cluster de computação complexa na cloud e responsável pelo desempenho necessário para executar a tarefa de otimização. Integração com os Hubs de eventos do Azure e o IoT Hub do Azure permite que o seu trabalho ingerir milhões de eventos por segundo provenientes de várias origens, para incluir dispositivos ligados, clickstreams e ficheiros de registo. Utilizar a funcionalidade de criação de partições dos Hubs de eventos, pode particionar cálculos em passos lógicos, cada um com a capacidade para ter ainda mais partições para aumentar a escalabilidade.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Executar na cloud ou no edge inteligente

O Azure Stream Analytics pode executar na cloud, para análise em grande escala, ou executar no IoT Edge para análise de latência Ultra baixas. O Azure Stream Analytics utiliza a mesma linguagem de consulta em cloud e o edge, permitindo que os desenvolvedores para realmente criar arquiteturas híbridas para processamento de fluxo. 

## <a name="low-total-cost-of-ownership"></a>Baixo custo total de propriedade

Como serviço cloud, o Stream Analytics está otimizado para custo. Existem sem custos iniciais envolvidos – paga apenas o [consumir de unidades de transmissão em fluxo](stream-analytics-streaming-unit-consumption.md)e a quantidade de dados processados. Não existe nenhum compromisso ou o aprovisionamento do cluster necessário, e pode aumentar ou reduzir verticalmente com base nas suas necessidades empresariais.

## <a name="mission-critical-ready"></a>Pronto para de missão crítica

O Azure Stream Analytics está disponível em várias regiões em todo o mundo e foi concebido para executar cargas de trabalho de missão crítica ao suportar os requisitos de confiabilidade, segurança e conformidade.

### <a name="reliability"></a>Fiabilidade

O Azure Stream Analytics garante exatamente-depois do processamento de eventos e pelo-menos-uma vez entrega de eventos, por isso, eventos nunca sejam perdidos. Exatamente-processamento uma vez é garantida com a saída selecionada, conforme descrito em [garantias de entrega de eventos](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

O Azure Stream Analytics tem capacidades de recuperação incorporadas no caso de falha de entrega de um evento. Além disso, o Stream Analytics fornece pontos de verificação incorporados para manter o estado do seu trabalho e fornece resultados repetíveis.

Como um serviço gerido, Stream Analytics garante o processamento de eventos com uma disponibilidade de 99,9% num nível de minuto de granularidade. Para obter mais informações, consulte a [SLA para Stream Analytics](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) página. 

### <a name="security"></a>Segurança

Em termos de segurança, o Azure Stream Analytics encripta todas as comunicações de entrada e saídas e oferece suporte a TLS 1.2. Pontos de verificação incorporados também são encriptados. Stream Analytics não armazena os dados de entrada, uma vez que todo o processamento é feito na memória.

### <a name="compliance"></a>Conformidade

O Azure Stream Analytics segue várias certificações de conformidade, conforme descrito no [descrição geral de conformidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Desempenho

Stream Analytics consegue processar milhões de eventos por segundo e pode proporcionar resultados com latências baixas ultra. Permite-lhe aumentar verticalmente e aumentar horizontalmente para lidar com aplicações de processamento de grandes eventos em tempo real e complexos. Stream Analytics suporta um desempenho mais elevado ao particionamento, permitindo que consultas complexas ser paralelizadas e executadas em vários nós de transmissão em fluxo. O Azure Stream Analytics foi concebido sob [Trill](https://github.com/Microsoft/Trill), um motor de análise de transmissão em fluxo de dentro da memória do alto desempenho desenvolvido em colaboração com o Microsoft Research.

## <a name="next-steps"></a>Passos Seguintes

Viu agora uma descrição geral do Azure Stream Analytics. Em seguida, pode criar o seu primeiro trabalho do Stream Analytics e experimentá-lo na prática:

* [Create a Stream Analytics job by using the Azure portal](stream-analytics-quick-create-portal.md) (Criar um trabalho do Stream Analytics com o portal do Azure).
* [Create a Stream Analytics job by using Azure PowerShell](stream-analytics-quick-create-powershell.md) (Criar um trabalho do Stream Analytics com o Azure PowerShell).
* [Criar uma tarefa do Stream Analytics com o Visual Studio](stream-analytics-quick-create-vs.md).
* [Criar uma tarefa do Stream Analytics com o Visual Studio Code](quick-create-vs-code.md).
