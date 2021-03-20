---
title: Introdução ao Azure Stream Analytics
description: Saiba mais sobre o Azure Stream Analytics, um serviço gerido que o ajuda a analisar dados de streaming a partir da Internet das Coisas (IoT) em tempo real.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc, contperf-fy21q2
ms.date: 11/12/2020
ms.openlocfilehash: 5aea6460f3a876d63544ce8422f9f205c22f2a0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015254"
---
# <a name="welcome-to-azure-stream-analytics"></a>Bem-vindo ao Azure Stream Analytics

Azure Stream Analytics é um motor de análise em tempo real e complexo de processamento de eventos que é projetado para analisar e processar grandes volumes de dados de streaming rápido de várias fontes simultaneamente. Padrões e relacionamentos podem ser identificados em informações extraídas de várias fontes de entrada, incluindo dispositivos, sensores, clickstreams, feeds de redes sociais e aplicações. Estes padrões podem ser usados para desencadear ações e iniciar fluxos de trabalho, tais como criar alertas, fornecer informações a uma ferramenta de reporte ou armazenar dados transformados para posterior utilização. Além disso, o Stream Analytics está disponível no tempo de execução do Azure IoT Edge, permitindo processar dados em dispositivos IoT.

Os seguintes cenários são exemplos de quando pode utilizar o Azure Stream Analytics:

* Analisar fluxos de telemetria em tempo real a partir de dispositivos IoT
* Análise de registos/clickstream da Web
* Análise geoespacial para gestão de frotas e veículos autónomos
* Monitorização remota e manutenção preditiva de ativos de alto valor
* Análise em tempo real de dados de Pontos de Vendas para controlo de inventário e deteção de anomalias

Pode experimentar a Azure Stream Analytics com uma subscrição gratuita do Azure.

> [!div class="nextstepaction"]
> [Experimente a Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="how-does-stream-analytics-work"></a>Como funciona o Stream Analytics?

Um trabalho do Azure Stream Analytics consiste numa entrada, consulta e saída. Stream Analytics ingere dados de Azure Event Hubs (incluindo Azure Event Hubs de Apache Kafka), Azure IoT Hub ou Azure Blob Storage. A consulta, que se baseia na linguagem de consulta SQL, pode ser usada para filtrar, classificar, agregar e juntar dados de streaming durante um período de tempo. Também pode estender este idioma SQL com funções definidas pelo utilizador JavaScript e C# (UDFs). Pode ajustar facilmente as opções de encomenda de eventos e a duração das janelas de tempo ao realizar operações de agregação através de simples construções e/ou configurações linguísticas.

Cada trabalho tem uma ou várias saídas para os dados transformados, e você pode controlar o que acontece em resposta à informação que analisou. Pode, por exemplo:

* Envie dados para serviços como Azure Functions, Service Bus Topics ou Queues para desencadear comunicações ou fluxos de trabalho personalizados a jusante.
* Envie dados para um dashboard Power BI para fazer dashboards em tempo real.
* Armazenar dados em outros serviços de armazenamento Azure (por exemplo, Azure Data Lake, Azure Synapse Analytics, etc.) para treinar um modelo de machine learning baseado em dados históricos ou realizar análises de lotes.

A imagem a seguir mostra como os dados são enviados para stream Analytics, analisados e enviados para outras ações como armazenamento ou apresentação:

![Pipeline de introdução stream Analytics](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Principais capacidades e vantagens

O Azure Stream Analytics foi concebido para ser fácil de utilizar, flexível, fiável e dimensionável para qualquer tamanho de trabalho. Está disponível em várias regiões do Azure, e funciona em IoT Edge ou Azure Stack.

## <a name="ease-of-getting-started"></a>Facilidade para começar

Azure Stream Analytics é fácil de iniciar. Bastam alguns cliques para se ligar a múltiplas fontes e pias, criando um gasoduto de ponta a ponta. O Stream Analytics pode ligar-se aos Azure Event Hubs e ao Azure IoT Hub para a ingestão de dados em streaming, bem como ao armazenamento de Azure Blob para ingerir dados históricos. A entrada de trabalho também pode incluir dados de referência estáticos ou de mudança lenta a partir do armazenamento de Azure Blob ou base de dados SQL que você pode juntar-se aos dados de streaming para realizar operações de procura.

O Stream Analytics pode encaminhar a produção de trabalho para muitos sistemas de armazenamento, tais como armazenamento Azure Blob, Base de Dados Azure SQL, Azure Data Lake Store e Azure CosmosDB. Também pode executar análises de lotes em saídas de stream com Azure Synapse Analytics ou HDInsight, ou pode enviar a saída para outro serviço, como Event Hubs para consumo ou Power BI para visualização em tempo real.

Para toda a lista de saídas stream Analytics, consulte [as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Produtividade do programador

Azure Stream Analytics usa uma linguagem de consulta SQL que foi aumentada com fortes restrições temporais para analisar dados em movimento. Também pode criar empregos utilizando ferramentas de desenvolvimento como Azure PowerShell, Azure CLI, Stream Analytics Visual Studio, a [extensão do Código do Estúdio Visual Stream Analytics](quick-create-visual-studio-code.md)ou os modelos Azure Resource Manager. A utilização de ferramentas de desenvolvimento permite-lhe desenvolver consultas de transformação offline e utilizar o pipeline CI/CD para submeter empregos ao Azure.

A linguagem de consulta Stream Analytics permite executar CEP (Complex Event Processing) oferecendo um vasto leque de funções para analisar dados de streaming. Esta linguagem de consulta suporta funções simples de manipulação de dados, agregação e análise, funções geoespaciais, correspondência de padrões e deteção de anomalias. Pode editar consultas no portal ou usar as nossas ferramentas de desenvolvimento e testá-las utilizando dados de amostras extraídos de um fluxo ao vivo.

Pode definir e invocar funções adicionais para alargar as capacidades da linguagem de consultas. Pode definir chamadas de função no Azure Machine Learning para tirar partido das soluções de Aprendizagem automática do Azure e integrar funções definidas pelo utilizador JavaScript ou C# (UDFs) ou agregados definidos pelo utilizador para realizar cálculos complexos como parte de uma consulta stream Analytics.

## <a name="fully-managed"></a>Totalmente gerido

Azure Stream Analytics é uma oferta totalmente gerida (PaaS) em Azure. Não tem de providenciar hardware ou infraestrutura, atualizar o SISTEMA ou o software. O Azure Stream Analytics gere totalmente o seu trabalho, para que possa focar-se na sua lógica de negócio e não na infraestrutura.


## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Corra na nuvem ou na borda inteligente

O Azure Stream Analytics pode correr na nuvem, para análises em larga escala, ou correr em IoT Edge ou Azure Stack para análises de latência ultra-baixas. O Azure Stream Analytics utiliza as mesmas ferramentas e linguagem de consulta tanto na nuvem como na borda, permitindo aos desenvolvedores construir arquiteturas verdadeiramente híbridas para o processamento de fluxos. 

## <a name="low-total-cost-of-ownership"></a>Baixo custo total de propriedade

Como serviço cloud, o Stream Analytics está otimizado para custo. Não há custos iniciais envolvidos - só se paga pelas [unidades de streaming que consome.](stream-analytics-streaming-unit-consumption.md) Não é necessário qualquer compromisso ou provisão de cluster, e pode escalar o trabalho para cima ou para baixo com base nas necessidades do seu negócio.

## <a name="mission-critical-ready"></a>Missão crítica pronta

O Azure Stream Analytics está disponível em várias regiões do mundo e foi projetado para executar cargas de trabalho críticas da missão, suportando requisitos de fiabilidade, segurança e conformidade.

### <a name="reliability"></a>Fiabilidade

O Azure Stream Analytics garante exatamente uma vez que o processamento do evento e pelo menos uma vez a entrega de eventos, para que os eventos nunca se percam. Exatamente uma vez que o processamento é garantido com saída selecionada, conforme descrito nas Garantias de Entrega de Eventos.

O Azure Stream Analytics tem capacidades de recuperação incorporadas no caso de a entrega de um evento falhar. Stream Analytics também fornece pontos de verificação incorporados para manter o estado do seu trabalho e fornece resultados repetíveis.

Como serviço gerido, o Stream Analytics garante o processamento de eventos com uma disponibilidade de 99,9% a um nível de granularidade. 

### <a name="security"></a>Segurança

Em termos de segurança, o Azure Stream Analytics encripta todas as comunicações recebidas e saídas e suporta o TLS 1.2. Os postos de controlo incorporados também são encriptados. O Stream Analytics não armazena os dados recebidos uma vez que todo o processamento é feito na memória. O Stream Analytics também suporta redes virtuais Azure (VNET) quando executa um trabalho num [Cluster de Análise de Fluxo.](./cluster-overview.md)

### <a name="compliance"></a>Conformidade

A Azure Stream Analytics segue várias certificações de conformidade como descrito na [visão geral da conformidade Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Desempenho

Stream Analytics pode processar milhões de eventos a cada segundo e pode entregar resultados com latências ultra baixas. Permite-lhe aumentar a escala e a escala para lidar com grandes aplicações de processamento de eventos em tempo real e complexas. Stream Analytics suporta um desempenho mais elevado através da partição, permitindo que consultas complexas sejam paralelas e executadas em múltiplos nós de streaming. O Azure Stream Analytics é construído em [Trill,](https://github.com/Microsoft/Trill)um motor de análise de streaming de alta memória desenvolvido em colaboração com a Microsoft Research.

## <a name="next-steps"></a>Passos seguintes

Viu agora uma descrição geral do Azure Stream Analytics. Em seguida, pode criar o seu primeiro trabalho do Stream Analytics e experimentá-lo na prática:

* [Crie um trabalho stream analytics utilizando o portal Azure](stream-analytics-quick-create-portal.md)
* [Criar um trabalho do Stream Analytics com o Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Crie um trabalho stream analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Crie um trabalho stream analytics usando o Código do Estúdio Visual](quick-create-visual-studio-code.md)
