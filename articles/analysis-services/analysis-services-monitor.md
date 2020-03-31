---
title: Monitor Azure Analysis Services métricas de servidores [ Monitor Azure Analysis Services server metrics ] Microsoft Docs
description: Saiba como os Serviços de Análise utilizam o Azure Metrics Explorer, uma ferramenta gratuita no portal, para o ajudar a monitorizar o desempenho e a saúde dos seus servidores.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aaa3a6d128fe7dd466f6f60ab515f05fa38ba63b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252808"
---
# <a name="monitor-server-metrics"></a>Monitorizar as métricas do servidor

Os Serviços de Análise fornecem métricas no Azure Metrics Explorer, uma ferramenta gratuita no portal, para ajudá-lo a monitorizar o desempenho e a saúde dos seus servidores. Por exemplo, monitorizar a memória e o uso do CPU, o número de ligações ao cliente e o consumo de recursos de consulta. Os Serviços de Análise utilizam o mesmo quadro de monitorização que a maioria dos outros serviços Azure. Para saber mais, veja [Getting started with Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md).

Para realizar diagnósticos mais aprofundados, acompanhar o desempenho e identificar tendências em vários recursos de serviço num grupo de recursos ou subscrição, utilize [o Monitor Azure.](../azure-monitor/overview.md) O Azure Monitor (serviço) pode resultar num serviço de faturação.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Para monitorizar as métricas de um servidor de Serviços de Análise

1. No portal Azure, selecione **Métricas**.

    ![Monitorizar no portal do Azure](./media/analysis-services-monitor/aas-monitor-portal.png)

2. Em **Métrica,** selecione as métricas a incluir na sua ficha. 

    ![Gráfico de monitor](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Métricas do servidor

Utilize esta tabela para determinar quais as métricas melhores para o seu cenário de monitorização. Apenas as métricas da mesma unidade podem ser mostradas no mesmo gráfico.

|Métrica|Nome de exibição métrica|Unidade|Tipo de Agregação|Descrição|
|---|---|---|---|---|
|Comprimento de fila de trabalho de commandpool|Comprimento da fila do trabalho da piscina de comando|Contagem|Média|Número de empregos na fila da piscina de linhas de comando.|
|CurrentConnections|Ligação: Ligações atuais|Contagem|Média|Número atual de ligações ao cliente estabelecidas.|
|Sessões de Utilizadores Atuais|Sessões de utilizador atuais|Contagem|Média|Número atual de sessões de utilizador estabelecidas.|
|mashup_engine_memory_metric|Memória do Motor M|Bytes|Média|Utilização da memória por processos do motor mashup|
|mashup_engine_qpu_metric|M Motor QPU|Contagem|Média|Utilização de QPU por processos de monomotor de mashup|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo 0-25 GB para S1, 0-50 GB para S2 e 0-100 GB para S4|
|memory_thrashing_metric|Degradação de Memória|Percentagem|Média|A memória média a bater.|
|Preço Corrente mais limpo|Memória: Preço Atual Mais Limpo|Contagem|Média|Preço atual da memória, $/byte/tempo, normalizado a 1000.|
|Memória Mais LimpaNão Encolhe|Memória: Memória mais limpa não encolhe|Bytes|Média|Quantidade de memória, em bytes, não sujeito a purga pelo limpador de fundo.|
|Memória Mais LimpaEncolhe|Memória: Memória mais limpa encolhe|Bytes|Média|Quantidade de memória, em bytes, sujeito a purga pelo limpador de fundo.|
|MemoryLimithard|Memória: Limite de memória difícil|Bytes|Média|Limite de memória dura, a partir do ficheiro de configuração.|
|MemoryLimitHigh|Memória: Limite de memória alto|Bytes|Média|Limite de memória elevado, a partir do ficheiro de configuração.|
|Limite de memória|Memória: Limite de memória baixo|Bytes|Média|Baixo limite de memória, a partir do ficheiro de configuração.|
|MemoryLimitVertiPaq|Memória: Limite de memória VertiPaq|Bytes|Média|Limite de memória, a partir do ficheiro de configuração.|
|Uso da Memória|Memória: Utilização da memória|Bytes|Média|Utilização da memória do processo do servidor como utilizado no cálculo do preço de memória mais limpo. Igual a contra processo\PrivateBytes mais o tamanho dos dados mapeados pela memória, ignorando qualquer memória, que foi mapeada ou atribuída pelo motor de análise em memória (VertiPaq) em excesso do limite de memória do motor.|
|private_bytes_metric|Bytes Privados |Bytes|Média|A quantidade total de memória que o processo do motor dos Serviços de Análise e os processos de contentores Mashup atribuíram, sem incluir a memória partilhada com outros processos.|
|virtual_bytes_metric|Bytes Virtuais |Bytes|Média|O tamanho atual do espaço de endereço virtual que o processo do motor dos Serviços de Análise e os processos de contentores Mashup estão a utilizar.|
|mashup_engine_private_bytes_metric|M Motor Private Bytes |Bytes|Média|A quantidade total de processos de contentores Mashup de memória alocadas, sem incluir a memória partilhada com outros processos.|
|mashup_engine_virtual_bytes_metric|Bytes virtuais do motor M |Bytes|Média|O tamanho atual dos processos de contentores mashup do espaço de endereço virtual estão a ser utilizados.|
|Quota|Memória: Quota|Bytes|Média|Quota de memória atual, em bytes. A quota de memória também é conhecida como subsídio de memória ou reserva de memória.|
|Bloco de Quotas|Memória: Quota bloqueada|Contagem|Média|Número atual de pedidos de quota que são bloqueados até que outras quotas de memória sejam libertadas.|
|VertiPaqNonpaged|Memória: VertiPaq Nonpaged|Bytes|Média|Bytes de memória bloqueados no conjunto de trabalho para utilização pelo motor de memória.|
|VertiPaqPaged|Memória: VertiPaq Paged|Bytes|Média|Bytes de memória páginada em uso para dados na memória.|
|ProcessingPoolJobQueueLength|Processamento de trabalho de piscina comprimento de fila|Contagem|Média|Número de empregos não-I/O na fila da piscina de fios de processamento.|
|RowsConvertedPerSec|Processamento: Linhas convertidas por seg|CondeEmSegundo|Média|Taxa de linhas convertidas durante o processamento.|
|LinhasReadPerSec|Processamento: Linhas lidas por segundo|CondeEmSegundo|Média|Taxa de linhas lidas a partir de todas as bases de dados relacionais.|
|LinhasWrittenPerSec|Processamento: Linhas escritas por seg|CondeEmSegundo|Média|Taxa de linhas escritas durante o processamento.|
|qpu_metric|QPU|Contagem|Média|O QPU. Intervalo 0-100 para S1, 0-200 para S2 e 0-400 para S4|
|ConsultaPoolBusyThreads|Filas movimentadas da piscina de consulta|Contagem|Média|Número de fios ocupados na piscina de fios de consulta.|
|SucessoConexsPerSec|Conexões bem sucedidas por Sec|CondeEmSegundo|Média|Taxa de conclusão de ligação bem sucedida.|
|CommandPoolBusyThreads|Threads: Comando piscina fios ocupados|Contagem|Média|Número de fios ocupados na piscina de fios de comando.|
|CommandPoolIdleThreads|Threads: Linhas ociosas da piscina de comando|Contagem|Média|Número de fios ociosos na piscina de fios de comando.|
|LongParsingBusyThreads|Threads: Longo parsing fios ocupados|Contagem|Média|Número de fios ocupados na longa piscina de fios de análise.|
|LongParsingIdleThreads|Threads: Longos fios ociosos de análise|Contagem|Média|Número de fios ociosos na longa piscina de fios de análise.|
|LongParsingJobQueueLength|Threads: Longa análise do comprimento da fila do trabalho|Contagem|Média|Número de empregos na fila da longa piscina de fios de análise.|
|ProcessingPoolioJobQueueLength|Threads: Processamento de piscina I/O comprimento da fila de trabalho|Contagem|Média|Número de empregos em I/S na fila da piscina de fios de processamento.|
|ProcessingPoolBusyIOJobThreads|Threads: Processing pool busy I/O job threads|Contagem|Média|Número de fios em funcionamento de empregos em I/S na piscina de fios de processamento.|
|ProcessingPoolBusyNonIOThreads|Threads: Piscina de processamento movimentada fios não-I/O|Contagem|Média|Número de fios que executam trabalhos não-I/O na piscina de fios de processamento.|
|ProcessingPoolIdleIOJobThreads|Threads: Processing pool idle I/O job threads|Contagem|Média|Número de fios ociosos para trabalhos de I/S na piscina de fios de processamento.|
|ProcessingPoolIdleNonIOThreads|Threads: Processamento de fios não-I/O da piscina|Contagem|Média|Número de fios ociosos na piscina de fios de processamento dedicados a empregos não-I/O.|
|ConsultaPoolIdleThreads|Threads: Filas ociosas da piscina de consulta|Contagem|Média|Número de fios ociosos para trabalhos de I/S na piscina de fios de processamento.|
|ConsultaPoolJobQueueLength|Threads: Consulta de trabalho na piscina comprimento da fila|Contagem|Média|Número de empregos na fila da piscina de fios de consulta.|
|ShortParsingBusyThreads|Threads: Curto parsing fios ocupados|Contagem|Média|Número de fios ocupados na piscina de fios de análise curta.|
|ShortParsingIdleThreads|Threads: Fios ociosos de análise curta|Contagem|Média|Número de fios ociosos na piscina de fios de análise curta.|
|ShortParsingJobQueueLength|Threads: Curto período de fila de trabalho de análise|Contagem|Média|Número de empregos na fila da piscina de fios de análise curta.|
|TotalDefalhas de ligação|Falhas totais de ligação|Contagem|Média|Total tentativas de ligação falhadas.|
|TotalDePedidos de Conexão|Total de pedidos de ligação|Contagem|Média|Pedidos de ligação total. |

## <a name="next-steps"></a>Passos seguintes
[Visão geral do Monitor Azure](../azure-monitor/overview.md)      
[Começar com o Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md)      
[Métricas em API REST Monitor Azure](/rest/api/monitor/metrics)
