---
title: Monitor Azure Analysis Services servidor métricas | Microsoft Docs
description: Saiba como os Serviços de Análise utilizam o Azure Metrics Explorer, uma ferramenta gratuita no portal, para o ajudar a monitorizar o desempenho e a saúde dos seus servidores.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1cc517ac3c903930eddb95a4813a8146cae2ec2c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582673"
---
# <a name="monitor-server-metrics"></a>Monitorizar as métricas do servidor

Os Serviços de Análise fornecem métricas no Azure Metrics Explorer, uma ferramenta gratuita no portal, para o ajudar a monitorizar o desempenho e a saúde dos seus servidores. Por exemplo, monitorizar a memória e o uso do CPU, o número de ligações ao cliente e o consumo de recursos de consulta. Os Serviços de Análise utilizam o mesmo quadro de monitorização que a maioria dos outros serviços da Azure. Para saber mais, consulte [Começar com o Azure Metrics Explorer.](../azure-monitor/essentials/metrics-getting-started.md)

Para realizar diagnósticos mais aprofundados, rastrear o desempenho e identificar tendências através de múltiplos recursos de serviço num grupo de recursos ou subscrição, utilize [o Azure Monitor](../azure-monitor/overview.md). O Azure Monitor (serviço) pode resultar num serviço de faturação.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Para monitorizar métricas para um servidor de Serviços de Análise

1. No portal Azure, selecione **Métricas.**

    ![Monitorizar no portal do Azure](./media/analysis-services-monitor/aas-monitor-portal.png)

2. Em **Metric**, selecione as métricas para incluir na sua tabela. 

    ![Gráfico de monitor](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Métricas do servidor

Utilize esta tabela para determinar quais as métricas melhores para o seu cenário de monitorização. Apenas métricas da mesma unidade podem ser mostradas no mesmo gráfico.

|Metric|Nome de exibição métrica|Unidade|Tipo de Agregação|Description|
|---|---|---|---|---|
|ComandoPoolJobQueueLength|Comprimento da fila de trabalho da piscina de comando|de palavras|Média|Número de empregos na fila da piscina de fios de comando.|
|Correntes Deconhecões|Ligação: Ligações atuais|de palavras|Média|Número atual de ligações ao cliente estabelecidas.|
|Atuais Sesessãos de 2009|Sessões de Utilizador atuais|de palavras|Média|Número atual de sessões de utilizador estabelecidas.|
|mashup_engine_memory_metric|Memória do motor M|Bytes|Média|Utilização da memória por processos do motor de mashup|
|mashup_engine_qpu_metric|QPU do motor M|de palavras|Média|Utilização do QPU por processos de motor de mashup|
|memory_metric|Memória|Bytes|Média|Memória. Gama 0-25 GB para S1, 0-50 GB para S2 e 0-100 GB para S4|
|memory_thrashing_metric|Degradação de Memória|Percentagem|Média|Memória média a bater.|
|CleanerCurrentPrice|Memória: Preço corrente mais limpo|de palavras|Média|Preço atual da memória, $/byte/time, normalizado para 1000.|
|CleanerMemoryNonshrinkable|Memória: Memória limpa não é insuportável|Bytes|Média|Quantidade de memória, em bytes, não sujeita a purga pelo limpador de fundos.|
|LimpadorMesshrinkable|Memória: Memória mais limpa encolhível|Bytes|Média|Quantidade de memória, em bytes, sujeito a purga pelo limpador de fundos.|
|MemoryLimitHard|Memória: Limite de memória difícil|Bytes|Média|Limite de memória rígido, a partir do ficheiro de configuração.|
|MemóriaLimithigh|Memória: Limite de memória Alto|Bytes|Média|Limite de memória elevado, a partir do ficheiro de configuração.|
|MemoryLimitLow|Memória: Limite de memória baixo|Bytes|Média|Limite de memória baixo, a partir do ficheiro de configuração.|
|MemoryLimitVertiPaq|Memória: Limite de memória VertiPaq|Bytes|Média|Limite de memória, a partir do ficheiro de configuração.|
|MemóriaSage|Memória: Utilização da memória|Bytes|Média|Utilização da memória do processo do servidor como usado no cálculo do preço de memória mais limpo. Igual a contrariar o Processo\PrivateBytes mais o tamanho dos dados mapeados pela memória, ignorando qualquer memória, que foi mapeada ou atribuída pelo motor de análise em memória (VertiPaq) superior ao limite de memória do motor.|
|private_bytes_metric|Bytes privados |Bytes|Média|A quantidade total de memória que o processo do motor dos Serviços de Análise e os processos do contentor Mashup atribuíram, não incluindo a memória partilhada com outros processos.|
|virtual_bytes_metric|Bytes Virtuais |Bytes|Média|O tamanho atual do espaço de endereço virtual que o processo do motor dos Serviços de Análise e os processos de contentores Mashup estão a usar.|
|mashup_engine_private_bytes_metric|M Motor Private Bytes |Bytes|Média|A quantidade total de processos de mashup de memória atribuiu, não incluindo a memória partilhada com outros processos.|
|mashup_engine_virtual_bytes_metric|M Motor Virtual Bytes |Bytes|Média|O tamanho atual do espaço de endereço virtual Os processos do mashup estão a usar.|
|Quota|Memória: Quota|Bytes|Média|Quota de memória atual, em bytes. A quota de memória também é conhecida como um subsídio de memória ou reserva de memória.|
|Quota Bloqueada|Memória: Quota Bloqueada|de palavras|Média|Número atual de pedidos de quotas que são bloqueados até que outras quotas de memória sejam libertadas.|
|VertiPaqNonpaged|Memória: VertiPaq Não Pageed|Bytes|Média|Bytes de memória bloqueados no conjunto de trabalho para utilização pelo motor de memória.|
|VertiPaqPaged|Memória: VertiPaq Paged|Bytes|Média|Bytes de memória paged em uso para dados de memória.|
|ProcessamentoPoolJobQueueLength|Processamento de comprimento da fila de trabalho da piscina|de palavras|Média|Número de trabalhos não-I/O na fila da piscina de rosca de processamento.|
|RowsConvertedPerSec|Processamento: Linhas convertidas por seg|CondePerSecond|Média|Taxa de linhas convertidas durante o processamento.|
|RowsReadPerSec|Processamento: Linhas lidas por seg|CondePerSecond|Média|Taxa de linhas lidas de todas as bases de dados relacionais.|
|RowsWrittenPerSec|Processamento: Linhas escritas por seg|CondePerSecond|Média|Taxa de linhas escritas durante o processamento.|
|qpu_metric|QPU|de palavras|Média|QPU. Intervalo 0-100 para S1, 0-200 para S2 e 0-400 para S4|
|QueryPoolBusyThreads|Linhas ocupadas da piscina de consulta|de palavras|Média|Número de fios ocupados na piscina de rosca de consulta.|
|SuccessfullConnectionsPerSec|Conexões de sucesso por seg|CondePerSecond|Média|Taxa de conclusão de conexão bem sucedida.|
|CommandPoolBusyThreads|Threads: Linhas movimentadas da piscina de comando|de palavras|Média|Número de fios ocupados na piscina de fio de comando.|
|CommandPoolIdleThreads|Fios: Fios inativos da piscina de comando|de palavras|Média|Número de fios inativos na piscina do fio de comando.|
|LongParsingBusyThreads|Threads: Longas análises de fios ocupados|de palavras|Média|Número de fios ocupados na piscina de rosca de análise longa.|
|LongParsingIdleThreads|Threads: Fios longos de análise ocioso|de palavras|Média|Número de fios ociosos na piscina de rosca de análise longa.|
|LongParsingJobQueueLength|Threads: Comprimento da fila de trabalho de análise longa|de palavras|Média|Número de empregos na fila da piscina de rosca de longas análises.|
|ProcessamentoPoolIOJobQueueLength|Threads: Processamento de piscina I/O comprimento da fila de trabalho|de palavras|Média|Número de trabalhos de E/S na fila da piscina de rosca de processamento.|
|ProcessamentoPoolBusyIOJobThreads|Threads: Processamento de piscina ocupada linhas de trabalho de I/O|de palavras|Média|Número de fios a executar trabalhos de E/S na piscina de fios de processamento.|
|ProcessamentoPoolBusyNonIOThreads|Fios: Piscina de processamento ocupada fios não-I/O|de palavras|Média|Número de fios que executam trabalhos não-I/S na piscina de rosca de processamento.|
|ProcessamentoPoolIdleIOJobThreads|Fios: Processamento de linhas de trabalho I/O inativas da piscina|de palavras|Média|Número de fios inativos para trabalhos de E/S na piscina de rosca de processamento.|
|ProcessamentoPoolIdleNonIOThreads|Fios: Processamento de piscinas ociosas fios não-I/O|de palavras|Média|Número de fios inativos na piscina de fios de processamento dedicada a trabalhos não-I/S.|
|QueryPoolIdleThreads|Fios: Linhas ociosas da piscina de consulta|de palavras|Média|Número de fios inativos para trabalhos de E/S na piscina de rosca de processamento.|
|QueryPoolJobQueueLength|Threads: Comprimento da fila do trabalho da piscina de consulta|de palavras|Média|Número de empregos na fila da piscina de fios de consulta.|
|ShortParsingBusyThreads|Threads: Threads de análise curta|de palavras|Média|Número de fios ocupados na piscina de rosca de parsing curta.|
|ShortParsingIdleThreads|Fios: Fios curtos de parsing ocioso|de palavras|Média|Número de fios ociosos na piscina de rosca de parsing curta.|
|ShortParsingJobQueueLength|Threads: Comprimento da fila do trabalho de análise curta|de palavras|Média|Número de empregos na fila da piscina de rosca de pequena análise.|
|TotalConnectionFailures|Falhas totais de ligação|de palavras|Média|Tentativas de ligação falhadas totais.|
|TotalConnectionRequests|Total de pedidos de conexão|de palavras|Média|Pedidos de ligação total. |

## <a name="next-steps"></a>Passos seguintes
[Visão geral do Monitor Azure](../azure-monitor/overview.md)      
[Começando com a Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md)      
[Métricas em AZure Monitor REST API](/rest/api/monitor/metrics)
