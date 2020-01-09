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
ms.openlocfilehash: 3399cc47af20df05d6315a4bd77965f799eaf5f9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426176"
---
# <a name="what-is-azure-stream-analytics"></a>O que é Azure Stream Analytics?

Azure Stream Analytics é uma análise em tempo real e um mecanismo de processamento de eventos complexo projetado para analisar e processar grandes volumes de dados de transmissão rápida de várias fontes simultaneamente. Padrões e relações podem ser identificados nas informações extraídas de várias fontes de entrada, incluindo dispositivos, sensores, cliques, feeds de mídia social e aplicativos. Esses padrões podem ser usados para disparar ações e iniciar fluxos de trabalho, criando alertas, alimentando informações para uma ferramenta de relatório ou armazenando dados transformados para uso posterior. Além disso, Stream Analytics está disponível em Azure IoT Edge Runtime e dá suporte à mesma linguagem ou sintaxe exata que a nuvem. 

Os cenários a seguir são exemplos de quando você pode usar Azure Stream Analytics:

* Analisar fluxos de telemetria em tempo real de dispositivos IoT
* Análise de registos/clickstream da Web
* Análise geoespacial para gestão de frotas e veículos autónomos
* Monitoramento remoto e manutenção preditiva de ativos de alto valor
* Análise em tempo real de dados de Pontos de Vendas para controlo de inventário e deteção de anomalias

## <a name="how-does-stream-analytics-work"></a>Como funciona o Stream Analytics?

Um trabalho de Azure Stream Analytics consiste em uma entrada, consulta e uma saída. Stream Analytics ingeri dados dos hubs de eventos do Azure, do Hub IoT do Azure ou do armazenamento de BLOBs do Azure. A consulta, que é baseada na linguagem de consulta SQL, pode ser usada para filtrar, classificar, agregar e unir dados de streaming com facilidade ao longo de um período de tempo. Você também pode estender essa linguagem SQL com JavaScript e C# UDFs (funções definidas pelo usuário). Você pode ajustar facilmente as opções de ordenação de eventos e a duração das janelas de tempo ao formar operações de agregação por meio de construções e/ou configurações de linguagem simples.

Cada trabalho tem uma saída para os dados transformados e você pode controlar o que acontece em resposta às informações analisadas. Por exemplo, pode:

* Envie dados para serviços como Azure Functions, tópicos ou filas do barramento de serviço para disparar comunicações ou fluxos de trabalho personalizados downstream.
* Envie dados para um painel de Power BI para painel em tempo real.
* Armazene dados em outros serviços de armazenamento do Azure para treinar um modelo de aprendizado de máquina com base em dados históricos ou executar análise de lote.

A imagem a seguir mostra como os dados são enviados para Stream Analytics, analisados e enviados para outras ações, como armazenamento ou apresentação:

![Pipeline de introdução do Stream Analytics](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Principais capacidades e vantagens

O Azure Stream Analytics foi concebido para ser fácil de utilizar, flexível, fiável e dimensionável para qualquer tamanho de trabalho. Ele está disponível em várias regiões do Azure. A imagem a seguir ilustra os principais recursos do Azure Stream Analytics:

![Capacidades principais do Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Facilidade para começar

Azure Stream Analytics é fácil de iniciar. Basta alguns cliques para se conectar a várias fontes e coletores, criando um pipeline de ponta a ponta. Stream Analytics pode se conectar aos [hubs de eventos do](/azure/event-hubs/) Azure e ao [Hub IOT do Azure](/azure/iot-hub/) para a ingestão de dados de streaming, bem como o armazenamento de [BLOBs do Azure](/azure/storage/storage-introduction) para ingerir dados históricos. A entrada do trabalho também pode incluir dados de referência estáticos ou de alteração lenta do armazenamento de BLOBs do Azure ou do banco de dados [SQL](stream-analytics-use-reference-data.md#azure-sql-database) que você pode associar ao streaming de dados para executar operações de pesquisa.

Stream Analytics pode rotear a saída do trabalho para muitos sistemas de armazenamento, como o [armazenamento de BLOBs do Azure](/azure/storage/storage-introduction), [banco de dados SQL do Azure](/azure/sql-database/), [Azure data Lake Store](/azure/data-lake-store/)e [Azure CosmosDB](/azure/cosmos-db/introduction). Você pode executar a análise de lote na saída armazenada com o Azure HDInsight ou pode enviar a saída para outro serviço, como os hubs de eventos para consumo ou [Power bi](https://docs.microsoft.com/power-bi/) para visualização em tempo real.

Para obter a lista completa de saídas de Stream Analytics, consulte [entender as saídas de Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Produtividade do programador

O Azure Stream Analytics usa uma linguagem de consulta simples baseada em SQL que foi aumentada com restrições temporais poderosas para analisar dados em movimento. Para definir as transformações dos trabalhos, é utilizada uma [linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) simples e declarativa, a qual lhe permite criar consultas e análises temporais complexas através da utilização de construções SQL simples. Como Stream Analytics linguagem de consulta é consistente para a linguagem SQL, a familiaridade com o SQL é suficiente para começar a criar trabalhos. Você também pode criar trabalhos usando ferramentas de desenvolvedor, como Azure PowerShell [Stream Analytics, ferramentas do Visual Studio](stream-analytics-tools-for-visual-studio-install.md), a [extensão de Visual Studio Code do Stream Analytics](quick-create-vs-code.md)ou modelos de Azure Resource Manager. A utilização de ferramentas do programador permite-lhe desenvolver consultas de transformação offline e utilizar o [pipeline CI/CD](stream-analytics-tools-for-visual-studio-cicd.md) para submeter trabalhos para o Azure.

A linguagem de consulta Stream Analytics oferece uma ampla gama de funções para analisar e processar dados de streaming. Essa linguagem de consulta dá suporte à manipulação simples de dados, funções de agregação e funções geoespaciais complexas. Você pode editar consultas no portal e testá-las usando dados de exemplo que são extraídos de uma transmissão ao vivo.

Pode definir e invocar funções adicionais para alargar as capacidades da linguagem de consultas. Você pode definir chamadas de função no Azure Machine Learning para aproveitar as soluções de Azure Machine Learning e integrar o JavaScript ou C# as UDFs (funções definidas pelo usuário) ou as agregações definidas pelo usuário para executar cálculos complexos como parte de uma Stream Analytics consulta.

## <a name="fully-managed"></a>Totalmente gerida

O Azure Stream Analytics é uma oferta sem servidor totalmente gerida (PaaS) no Azure. Você não precisa provisionar nenhum hardware ou gerenciar clusters para executar seus trabalhos. Azure Stream Analytics gerencia totalmente seu trabalho configurando clusters de computação complexos na nuvem e tomando cuidado com o ajuste de desempenho necessário para executar o trabalho. A integração com os hubs de eventos do Azure e o Hub IoT do Azure permite que seu trabalho ingerir milhões de eventos por segundo, provenientes de várias fontes, para incluir dispositivos conectados, cliques e arquivos de log. Usando o recurso de particionamento dos hubs de eventos, você pode particionar cálculos em etapas lógicas, cada um com a capacidade de ser mais particionado para aumentar a escalabilidade.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Executar na nuvem ou na borda inteligente

Azure Stream Analytics pode ser executado na nuvem, para análise em larga escala ou executado em IoT Edge para análise de latência extremamente baixa. Azure Stream Analytics usa a mesma linguagem de consulta na nuvem e na borda, permitindo que os desenvolvedores criem arquiteturas realmente híbridas para processamento de fluxo. 

## <a name="low-total-cost-of-ownership"></a>Baixo custo total de propriedade

Como serviço cloud, o Stream Analytics está otimizado para custo. Não há custos antecipados envolvidos-você paga apenas pelas unidades de [streaming que consumir](stream-analytics-streaming-unit-consumption.md)e pela quantidade de dados processados. Não há nenhum compromisso ou provisionamento de cluster necessário, e você pode dimensionar ou reduzir o trabalho com base nas suas necessidades de negócios.

## <a name="mission-critical-ready"></a>Pronto para missão crítica

O Azure Stream Analytics está disponível em várias regiões em todo o mundo e é projetado para executar cargas de trabalho críticas ao dar suporte aos requisitos de confiabilidade, segurança e conformidade.

### <a name="reliability"></a>Fiabilidade

Azure Stream Analytics garante o processamento de eventos exatamente uma vez e a entrega de eventos pelo menos uma vez, de modo que os eventos nunca sejam perdidos. O processamento de exatamente uma vez é garantido com a saída selecionada, conforme descrito em [garantias de entrega de eventos](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

Azure Stream Analytics tem recursos de recuperação internos no caso de falha na entrega de um evento. O Stream Analytics também fornece pontos de verificação internos para manter o estado do seu trabalho e fornece resultados repetíveis.

Como um serviço gerenciado, o Stream Analytics garante o processamento de eventos com uma disponibilidade de 99,9% em um nível de granularidade de minutos. Para obter mais informações, consulte a página [SLA de Stream Analytics](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) . 

### <a name="security"></a>Segurança

Em termos de segurança, o Azure Stream Analytics criptografa todas as comunicações de entrada e saída e dá suporte a TLS 1,2. Os pontos de verificação internos também são criptografados. Stream Analytics não armazena os dados de entrada, já que todo o processamento é feito na memória.

### <a name="compliance"></a>Conformidade

Azure Stream Analytics segue várias certificações de conformidade, conforme descrito na [visão geral da conformidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Desempenho

Stream Analytics pode processar milhões de eventos a cada segundo e pode fornecer resultados com latências ultra baixas. Ele permite que você Escale verticalmente e escale horizontalmente para lidar com grandes aplicativos de processamento de eventos em tempo real e complexos. O Stream Analytics dá suporte a um melhor desempenho pelo particionamento, permitindo que consultas complexas sejam paralelizadas e executadas em vários nós de streaming. Azure Stream Analytics se baseia no [Trill](https://github.com/Microsoft/Trill), um mecanismo de análise de streaming na memória de alto desempenho desenvolvido em colaboração com o Microsoft Research.

## <a name="next-steps"></a>Passos seguintes

Viu agora uma descrição geral do Azure Stream Analytics. Em seguida, pode criar o seu primeiro trabalho do Stream Analytics e experimentá-lo na prática:

* [Create a Stream Analytics job by using the Azure portal](stream-analytics-quick-create-portal.md) (Criar um trabalho do Stream Analytics com o portal do Azure).
* [Create a Stream Analytics job by using Azure PowerShell](stream-analytics-quick-create-powershell.md) (Criar um trabalho do Stream Analytics com o Azure PowerShell).
* [Crie um trabalho de Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md).
* [Crie um trabalho de Stream Analytics usando Visual Studio Code](quick-create-vs-code.md).
