---
title: Descrição geral do Azure Stream Analytics
description: Saiba mais sobre o Stream Analytics, um serviço gerido que o ajuda a analisar os dados de transmissão a partir da Internet de Coisas (IoT) em tempo real.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 07/6/2020
ms.openlocfilehash: c6c878aa0e4a9d5caa22f7cd94c438f22a81d2ab
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90881885"
---
# <a name="what-is-azure-stream-analytics"></a>O que é Azure Stream Analytics?

Azure Stream Analytics é um motor de análise em tempo real e complexo de processamento de eventos que é projetado para analisar e processar grandes volumes de dados de streaming rápido de várias fontes simultaneamente. Padrões e relacionamentos podem ser identificados em informações extraídas de várias fontes de entrada, incluindo dispositivos, sensores, clickstreams, feeds de redes sociais e aplicações. Estes padrões podem ser usados para desencadear ações e iniciar fluxos de trabalho, tais como criar alertas, fornecer informações a uma ferramenta de reporte ou armazenar dados transformados para posterior utilização. Além disso, o Stream Analytics está disponível no tempo de execução do Azure IoT Edge, permitindo processar dados em dispositivos IoT. 

Os seguintes cenários são exemplos de quando pode utilizar o Azure Stream Analytics:

* Analisar fluxos de telemetria em tempo real a partir de dispositivos IoT
* Análise de registos/clickstream da Web
* Análise geoespacial para gestão de frotas e veículos autónomos
* Monitorização remota e manutenção preditiva de ativos de alto valor
* Análise em tempo real de dados de Pontos de Vendas para controlo de inventário e deteção de anomalias

## <a name="how-does-stream-analytics-work"></a>Como funciona o Stream Analytics?

Um trabalho do Azure Stream Analytics consiste numa entrada, consulta e saída. Stream Analytics ingere dados de Azure Event Hubs (incluindo Azure Event Hubs de Apache Kafka), Azure IoT Hub ou Azure Blob Storage. A consulta, que se baseia na linguagem de consulta SQL, pode ser usada para filtrar, classificar, agregar e juntar dados de streaming durante um período de tempo. Também pode estender este idioma SQL com funções definidas pelo utilizador JavaScript e C# (UDFs). Pode ajustar facilmente as opções de encomenda de eventos e a duração das janelas de tempo ao pré-formar operações de agregação através de simples construções e/ou configurações linguísticas.

Cada trabalho tem uma ou várias saídas para os dados transformados, e você pode controlar o que acontece em resposta à informação que analisou. Pode, por exemplo:

* Envie dados para serviços como Azure Functions, Service Bus Topics ou Queues para desencadear comunicações ou fluxos de trabalho personalizados a jusante.
* Envie dados para um dashboard Power BI para fazer dashboards em tempo real.
* Armazenar dados em outros serviços de armazenamento Azure (por exemplo, Azure Data Lake, Azure Synapse Analytics, etc.) para treinar um modelo de machine learning baseado em dados históricos ou realizar análises de lotes.

A imagem a seguir mostra como os dados são enviados para stream Analytics, analisados e enviados para outras ações como armazenamento ou apresentação:

![Pipeline de introdução stream Analytics](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Principais capacidades e vantagens

O Azure Stream Analytics foi concebido para ser fácil de utilizar, flexível, fiável e dimensionável para qualquer tamanho de trabalho. Está disponível em várias regiões do Azure. A imagem a seguir ilustra as principais capacidades do Azure Stream Analytics:

![Capacidades principais do Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Facilidade para começar

Azure Stream Analytics é fácil de iniciar. Bastam alguns cliques para se ligar a múltiplas fontes e pias, criando um gasoduto de ponta a ponta. O Stream Analytics pode ligar-se aos [Azure Event Hubs](/azure/event-hubs/) e [ao Azure IoT Hub](/azure/iot-hub/) para a ingestão de dados em streaming, bem como [ao armazenamento de Azure Blob](/azure/storage/common/storage-introduction) para ingerir dados históricos. A entrada de trabalho também pode incluir dados de referência estáticos ou de mudança lenta a partir do armazenamento de Azure Blob ou [base de dados SQL](stream-analytics-use-reference-data.md#azure-sql-database) que você pode juntar-se aos dados de streaming para realizar operações de procura.

O Stream Analytics pode encaminhar a produção de emprego para muitos sistemas de armazenamento, tais como [armazenamento Azure Blob](/azure/storage/common/storage-introduction), [Azure SQL Database,](/azure/sql-database/) [Azure Data Lake Store](/azure/data-lake-store/)e [Azure CosmosDB](/azure/cosmos-db/introduction). Pode executar análises de lotes na saída armazenada com Azure HDInsight, ou pode enviar a saída para outro serviço, como Os Centros de Eventos para consumo ou [Power BI](https://docs.microsoft.com/power-bi/) para visualização em tempo real.

Para toda a lista de saídas stream Analytics, consulte [as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Produtividade do programador

O Azure Stream Analytics usa uma linguagem de consulta baseada em SQL simples que foi aumentada com fortes restrições temporais para analisar dados em movimento. Para definir as transformações dos trabalhos, é utilizada uma [linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) simples e declarativa, a qual lhe permite criar consultas e análises temporais complexas através da utilização de construções SQL simples. Como a linguagem de consulta stream analytics é consistente com a linguagem SQL, a familiaridade com o SQL é suficiente para começar a criar empregos. Também pode criar empregos utilizando ferramentas de desenvolvimento como ferramentas de desenvolvimento como Azure PowerShell, [stream Analytics Visual Studio,](stream-analytics-tools-for-visual-studio-install.md)a [extensão do Código do Estúdio Visual Stream Analytics](quick-create-visual-studio-code.md)ou modelos Azure Resource Manager. A utilização de ferramentas do programador permite-lhe desenvolver consultas de transformação offline e utilizar o [pipeline CI/CD](stream-analytics-tools-for-visual-studio-cicd.md) para submeter trabalhos para o Azure.

O idioma de consulta Stream Analytics oferece uma grande variedade de funções para analisar e processar dados de streaming. Esta linguagem de consulta suporta funções simples de manipulação de dados, agregação e análise, [funções geoespaciais,](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-geospatial-functions) [correspondência de padrões](https://docs.microsoft.com/stream-analytics-query/match-recognize-stream-analytics) e [deteção de anomalias.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-machine-learning-anomaly-detection) Pode editar consultas no portal e testá-las utilizando dados de amostras extraídos de um fluxo ao vivo.

Pode definir e invocar funções adicionais para alargar as capacidades da linguagem de consultas. Pode definir chamadas de função no Azure Machine Learning para tirar partido das soluções de Aprendizagem automática do Azure e integrar funções definidas pelo utilizador JavaScript ou C# (UDFs) ou agregados definidos pelo utilizador para realizar cálculos complexos como parte de uma consulta stream Analytics.

## <a name="fully-managed"></a>Totalmente gerido

O Azure Stream Analytics é uma oferta sem servidor totalmente gerida (PaaS) no Azure. Não é preciso providenciar hardware, gerir clusters para executar os seus trabalhos, ou atualizar o SISTEMA ou software. O Azure Stream Analytics gere totalmente o seu trabalho, para que possa focar-se na sua lógica de negócio e não na infraestrutura.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Corra na nuvem ou na borda inteligente

O Azure Stream Analytics pode correr na nuvem, para análises em larga escala, ou correr em IoT Edge para análises de latência ultra-baixas. O Azure Stream Analytics utiliza as mesmas ferramentas e linguagem de consulta tanto na nuvem como na borda, permitindo aos desenvolvedores construir arquiteturas verdadeiramente híbridas para o processamento de fluxos. 

## <a name="low-total-cost-of-ownership"></a>Baixo custo total de propriedade

Como serviço cloud, o Stream Analytics está otimizado para custo. Não há custos iniciais envolvidos - só se paga pelas [unidades de streaming que consome.](stream-analytics-streaming-unit-consumption.md) Não é necessário qualquer compromisso ou provisão de cluster, e pode escalar o trabalho para cima ou para baixo com base nas necessidades do seu negócio.

## <a name="mission-critical-ready"></a>Missão crítica pronta

O Azure Stream Analytics está disponível em várias regiões do mundo e foi projetado para executar cargas de trabalho críticas da missão, suportando requisitos de fiabilidade, segurança e conformidade.

### <a name="reliability"></a>Fiabilidade

O Azure Stream Analytics garante o processamento de eventos exatamente uma vez e, pelo menos, uma vez que os eventos nunca se perdem. O processamento exatamente uma vez é garantido com uma saída selecionada, conforme descrito nas [Garantias de Entrega de Eventos.](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)

O Azure Stream Analytics tem capacidades de recuperação incorporadas no caso de a entrega de um evento falhar. Stream Analytics também fornece pontos de verificação incorporados para manter o estado do seu trabalho e fornece resultados repetíveis.

Como serviço gerido, o Stream Analytics garante o processamento de eventos com uma disponibilidade de 99,9% a um nível de granularidade. Para mais informações, consulte a página [Stream Analytics SLA.](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) 

### <a name="security"></a>Segurança

Em termos de segurança, o Azure Stream Analytics encripta todas as comunicações recebidas e saídas e suporta o TLS 1.2. Os postos de controlo incorporados também são encriptados. O Stream Analytics não armazena os dados recebidos uma vez que todo o processamento é feito na memória.

### <a name="compliance"></a>Conformidade

A Azure Stream Analytics segue várias certificações de conformidade como descrito na [visão geral da conformidade Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Desempenho

Stream Analytics pode processar milhões de eventos a cada segundo e pode entregar resultados com latências ultra baixas. Permite-lhe aumentar a escala e a escala para lidar com grandes aplicações de processamento de eventos em tempo real e complexas. Stream Analytics suporta um desempenho mais elevado através da partição, permitindo que consultas complexas sejam paralelas e executadas em múltiplos nós de streaming. O Azure Stream Analytics é construído em [Trill,](https://github.com/Microsoft/Trill)um motor de análise de streaming de alta memória desenvolvido em colaboração com a Microsoft Research.

## <a name="next-steps"></a>Passos seguintes

Viu agora uma descrição geral do Azure Stream Analytics. Em seguida, pode criar o seu primeiro trabalho do Stream Analytics e experimentá-lo na prática:

* [Create a Stream Analytics job by using the Azure portal](stream-analytics-quick-create-portal.md) (Criar um trabalho do Stream Analytics com o portal do Azure).
* [Crie um trabalho stream analytics utilizando a Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Crie um trabalho stream analytics utilizando o Visual Studio](stream-analytics-quick-create-vs.md).
* [Crie um trabalho stream analytics utilizando o Código de Estúdio Visual](quick-create-visual-studio-code.md).
