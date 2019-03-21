---
title: Descrição geral do Azure Stream Analytics
description: Saiba mais sobre o Stream Analytics, um serviço gerido que o ajuda a analisar os dados de transmissão a partir da Internet de Coisas (IoT) em tempo real.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.workload: data-services
ms.custom: seodec18
ms.date: 12/07/2018
ms.openlocfilehash: f0104bdc4b5ea5c5c6333e12b95defafb34665b3
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295509"
---
# <a name="what-is-azure-stream-analytics"></a>O que é o Azure Stream Analytics?

O Azure Stream Analytics é um motor de processamento de eventos que lhe permite examinar elevados volumes de dados de transmissão em fluxo a partir de dispositivos. Os dados de entrada podem ser provenientes de dispositivos, sensores, Web sites, feeds de redes sociais, aplicações, entre outras origens. Também suporta a extração de informações de fluxos de dados e a identificação de padrões e relações. Em seguida, pode utilizar estes padrões para acionar outras ações a jusante, tais como criar alertas, informações de feeds para uma ferramenta de relatórios ou armazená-los para utilização posterior.

Seguem-se alguns exemplos de onde o Azure Stream Analytics pode ser utilizado: 

* Fusão de sensores de Internet das Coisas (IoT) e análise em tempo real da telemetria dos dispositivos
* Análise de registos/clickstream da Web
* Análise geoespacial para gestão de frotas e veículos autónomos
* Monitorização remota e a manutenção preditiva de recursos de elevado valor
* Análise em tempo real de dados de Pontos de Vendas para controlo de inventário e deteção de anomalias

## <a name="how-does-stream-analytics-work"></a>Como funciona o Stream Analytics?

O Azure Stream Analytics começa com uma origem de transmissão em fluxo de dados que é ingerida num hub do Hub de Eventos do Azure, no Hub IoT do Azure ou a partir de um arquivo de dados, como o Armazenamento de Blobs do Azure. Para examinar os fluxos, é criado um trabalho do Stream Analytics que especifica a origem de entrada que transmite os dados. O trabalho também especifica uma consulta de transformação que define como procurar dados, padrões ou relações. A consulta de transformação utiliza linguagem de consulta SQL para facilmente filtrar, ordenar, Agregar e associar dados de transmissão ao longo de um período de tempo. Quando executa o trabalho, pode ajustar as opções de ordem dos eventos e a duração das janelas de tempo quando são realizadas operações de agregação.

Depois de analisar os dados de entrada, é especificada uma saída para os dados transformados e pode controlar o que fazer em resposta às informações que analisou. Por exemplo, pode realizar ações como:

* Envie dados para uma fila monitorizada para acionar alertas ou downstream uma fluxos de trabalho personalizados.
* Enviar dados para o dashboard do Power BI, para visualização em tempo real.
* Store dados para outros serviços de armazenamento do Azure, pelo que pode formar um modelo de machine learning com base nos dados históricos ou realizar análises de lote.

A imagem seguinte ilustra o pipeline do Stream Analytics. O trabalho do Stream Analytics pode utilizar todos ou alguns conjuntos de entradas e saídas. Esta imagem mostra como é que os dados são enviados para o Stream Analytics, analisados e enviados para outras ações, como armazenamento ou apresentação:

![Pipeline de introdução do Stream Analytics](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Principais capacidades e vantagens

O Azure Stream Analytics foi concebido para ser fácil de utilizar, flexível, fiável e dimensionável para qualquer tamanho de trabalho. Está disponível em várias regiões do Azure. A imagem seguinte ilustra as principais funcionalidades do Azure Stream Analytics:

![Capacidades principais do Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Facilidade para começar

É fácil começar a utilizar o Azure Stream Analytics. Bastam apenas alguns cliques para ligar a várias origens e sinks e a criar um pipeline completo. O Stream Analytics pode ser ligado aos [Hubs de Eventos do Azure](https://docs.microsoft.com/azure/event-hubs/) e ao [Hub IoT do Azure](https://docs.microsoft.com/azure/iot-hub/) para ingestão de dados de transmissão em fluxo. Também pode ser ligado ao serviço [armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/storage-introduction) para ingerir dados históricos. Pode combinar dados de hubs de eventos com outras origens de dados e motores de processamento. A entrada dos trabalhos também pode incluir dados de referência que sejam estáticos ou dados com alterações pouco frequentes e pode associar dados de transmissão a esses dados de referência para realizar operações de pesquisa.

O Stream Analytics pode encaminhar a saída dos trabalhos para muitos sistemas de armazenamento como o [Blob do Azure](https://docs.microsoft.com/azure/storage/storage-introduction), a [Base de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/), o [Azure Data Lake Stores](https://docs.microsoft.com/azure/data-lake-store/) ou o [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction). Depois de armazenar, pode executar análises de lote com o Azure HDInsight ou enviar a saída para outro serviço, como os hubs de eventos para consumo, ou [Power BI](https://docs.microsoft.com/power-bi/) para visualização em tempo real, utilizando a API de transmissão em fluxo do Power BI.

## <a name="programmer-productivity"></a>Produtividade do programador

O Azure Stream Analytics utiliza uma linguagem de consulta simples baseada em SQL que foi melhorada com limitações temporais poderosas para analisar dados em movimento. Para definir as transformações dos trabalhos, é utilizada uma [linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) simples e declarativa, a qual lhe permite criar consultas e análises temporais complexas através da utilização de construções SQL simples. A linguagem de consulta do Stream Analytics é consistente com a linguagem do SQL, familiaridade essa que é suficiente para começar a criar trabalhos. Também pode utilizar ferramentas de programador como o Azure PowerShell, as [ferramentas do Stream Analytics para Visual Studio](stream-analytics-tools-for-visual-studio-install.md) ou modelos do Azure Resource Manager para criar trabalhos. A utilização de ferramentas do programador permite-lhe desenvolver consultas de transformação offline e utilizar o [pipeline CI/CD](stream-analytics-tools-for-visual-studio-cicd.md) para submeter trabalhos para o Azure. 

A linguagem de consulta do Stream Analytics oferece um vasto conjunto de funções para analisar e processar os dados das transmissões em fluxo. Esta linguagem de consulta suporta manipulação de dados simples, funções de agregação e funções geoespaciais complexas. Pode editar as consultas no portal e testá-las com dados de exemplo simples que são extraídos do fluxo em direto.

Pode definir e invocar funções adicionais para alargar as capacidades da linguagem de consultas. Pode definir chamadas de funções no serviço Azure Machine Learning para tirar partido das soluções do Azure Machine Learning e integrar funções definidas pelo utilizador (UDFs) em JavaScript ou agregações definidas pelo utilizador para realizar cálculos complexos como parte da consulta do Stream Analytics.

## <a name="fully-managed"></a>Totalmente gerido 

O Azure Stream Analytics é uma oferta sem servidor totalmente gerida (PaaS) no Azure. Isto significa que não tem de aprovisionar nenhum software nem de gerir clusters para executar os trabalhos. O Azure Stream Analytics gere totalmente o trabalho ao assumir a configuração de cluster de computação complexa na cloud e a otimização do desempenho necessário para executar esse trabalho. A integração com os Hubs de Eventos do Azure e com o Hub IoT do Azure permite que os trabalhos ingiram milhões de eventos por segundo provenientes de dispositivos ligados, clickstreams e ficheiros de registo, entre outros. Ao utilizar a funcionalidade de partições dos Hubs de Eventos, pode particionar os cálculos em passos lógicos, tendo a possibilidade de particioná-los ainda mais para aumentar a escalabilidade.

## <a name="run-in-the-cloud-on-in-the-intelligent-edge"></a>Executar na cloud num Edge inteligente

O Azure Stream Analytics pode executar na cloud, para análise em grande escala, ou executar num Edge inteligente para análise de latência Ultra baixas.
O Azure Stream Analytics utiliza a mesma linguagem de consulta em nuvem e de um Edge inteligente, permitindo que os desenvolvedores para realmente criar arquiteturas híbridas para processamento de fluxo.

## <a name="low-total-cost-of-ownership"></a>Custo total de posse baixo

Como serviço cloud, o Stream Analytics está otimizado para custo. Não tem custos à cabeça e só paga as [unidades de transmissão em fluxo que consumir](stream-analytics-streaming-unit-consumption.md) e a quantidade de dados processados. Não são necessários compromissos nem aprovisionamento de clusters. Pode aumentar ou reduzir verticalmente os trabalhos de transmissão em fluxo com base nas necessidades da sua empresa. 

## <a name="mission-critical-ready"></a>Pronto para de missão crítica
O Azure Stream Analytics está disponível em várias regiões em todo o mundo e foi concebido para executar cargas de trabalho de missão crítica ao suportar os requisitos de confiabilidade, segurança e conformidade.
### <a name="reliability"></a>Fiabilidade
O Azure Stream Analytics garante exatamente-depois do processamento de eventos e pelo-menos-uma vez entrega de eventos, por isso, eventos nunca sejam perdidos. Exatamente-processamento uma vez é garantida com a saída selecionada, conforme descrito em [garantias de entrega de eventos](https://docs.microsoft.com/en-us/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics). O Azure Stream Analytics tem capacidades de recuperação incorporadas no caso de falha de entrega de um evento. Além disso, o Stream Analytics fornece o ponto de verificação incorporado para manter o estado do seu trabalho e fornece resultados repetíveis.

Como um serviço gerido, Stream Analytics garante o processamento de eventos com uma disponibilidade de 99,9% no nível de minuto. Para obter mais informações, consulte a [SLA para Stream Analytics](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) página para obter mais detalhes. 

### <a name="security"></a>Segurança
Em termos de segurança, o Azure Stream Analytics encripta todas as comunicações de entrada e saídas e oferece suporte a TLS 1.2. Pontos de verificação incorporados também são encriptados. Stream Analytics não armazena os dados de entrada, uma vez que todo o processamento é feito na memória. 

### <a name="compliance"></a>Conformidade
O Azure Stream Analytics segue várias certificações de conformidade, conforme descrito no [descrição geral de conformidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Desempenho

O Stream Analytics consegue processar milhões de eventos por cada segundo e pode proporcionar resultados com baixa latência.
Permite-lhe aumentar verticalmente e aumentar horizontalmente para lidar com aplicações de processamento de grandes eventos em tempo real e complexos. Stream Analytics suporta o desempenho ao particionar, permitindo que consultas complexas ser paralelizadas e executadas em vários nós de transmissão em fluxo.
O Azure Stream Analytics foi concebido sob [Trill](https://github.com/Microsoft/Trill), um motor de análise de transmissão em fluxo de dentro da memória do alto desempenho desenvolvido em colaboração com o Microsoft Research. 

## <a name="next-steps"></a>Passos Seguintes

Viu agora uma descrição geral do Azure Stream Analytics. Em seguida, pode criar o seu primeiro trabalho do Stream Analytics e experimentá-lo na prática:

* [Create a Stream Analytics job by using the Azure portal](stream-analytics-quick-create-portal.md) (Criar um trabalho do Stream Analytics com o portal do Azure).
* [Create a Stream Analytics job by using Azure PowerShell](stream-analytics-quick-create-powershell.md) (Criar um trabalho do Stream Analytics com o Azure PowerShell).
* [Criar uma tarefa do Stream Analytics com o Visual Studio](stream-analytics-quick-create-vs.md).

