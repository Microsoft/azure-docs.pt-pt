---
title: Visão geral do armazenamento - Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre o armazenamento de dados na Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 748eaca93eaee5ec858ea43261995111cef8ceda
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676856"
---
# <a name="data-storage"></a>Armazenamento de Dados

Este artigo descreve o armazenamento de dados em Azure Time Series Insights Gen2. Abrange quente e frio, disponibilidade de dados e boas práticas.

## <a name="provisioning"></a>Aprovisionamento

Quando cria um ambiente Azure Time Series Insights Gen2, tem as seguintes opções:

* Armazenamento de dados frios:
  * Crie um novo recurso de Armazenamento Azure na subscrição e região que escolheu para o seu ambiente.
  * Anexar uma conta de Armazenamento Azure pré-existente. Esta opção só está disponível através de um [modelo](/azure/templates/microsoft.timeseriesinsights/allversions)de Gestor de Recursos Azure , e não está visível no portal Azure.
* Armazenamento de dados quente:
  * Uma loja quente é opcional e pode ser ativada ou desativada durante ou após o tempo de provisão. Se decidir ativar a loja quente mais tarde e já existirem dados na sua loja de colde, reveja [esta](concepts-storage.md#warm-store-behavior) secção abaixo para entender o comportamento esperado. O tempo de retenção de dados de armazenamento quente pode ser configurado durante 7 a 31 dias, o que também pode ser ajustado conforme necessário.

Quando um evento é ingerido, é indexado tanto em loja quente (se ativado) como no cold store.

[![Visão geral do armazenamento](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> Como proprietário da conta de armazenamento Azure Blob onde residem os dados do cold store, tem acesso total a todos os dados da conta. Este acesso inclui permissões de escrita e eliminação. Não edite nem elimine os dados que o Azure Time Series Insights Gen2 escreve porque isso pode causar perda de dados.

## <a name="data-availability"></a>Disponibilidade de dados

Azure Time Series Insights Gen2 partitions and indexes datas para o melhor desempenho da consulta. Os dados ficam disponíveis para consulta a partir de quente (se ativado) e de cold store após a sua indexação. A quantidade de dados que estão a ser ingeridos e a taxa de produção por partição podem afetar a disponibilidade. Reveja as limitações de produção de [eventos](./concepts-streaming-ingress-throughput-limits.md) e [as melhores práticas](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) para melhor desempenho. Também pode configurar um [alerta](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) de lag para ser notificado se o seu ambiente estiver a ter problemas de processamento de dados.

> [!IMPORTANT]
> Pode experimentar um período de até 60 segundos antes de os dados se tornarem disponíveis. Se sentir uma latência significativa para além de 60 segundos, por favor envie um bilhete de apoio através do portal Azure.

## <a name="warm-store"></a>Loja quente

Os dados na sua loja quente só estão disponíveis através das [APIs de Consulta de Séries Temporais,](./concepts-query-overview.md)do [Azure Time Series Insights TSI Explorer,](./concepts-ux-panels.md)ou do [Conector Power BI](./how-to-connect-power-bi.md). As consultas de loja quente são gratuitas e não há quota, mas há um [limite de 30](/rest/api/time-series-insights/reference-api-limits#query-apis---limits) pedidos simultâneos.

### <a name="warm-store-behavior"></a>Comportamento quente da loja

* Quando ativados, todos os dados transmitidos para o seu ambiente serão encaminhados para a sua loja quente, independentemente da hora do evento. Note que o gasoduto de ingestão de streaming é construído para o streaming em tempo quase real e ingerir eventos históricos não é [suportado](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion).
* O período de retenção é calculado com base no momento em que o evento foi indexado em loja quente, e não na marca de tempo do evento. Isto significa que os dados já não estão disponíveis em loja quente após o período de retenção ter decorrido, mesmo que o tempo de evento seja para o futuro.
  * Exemplo: um evento com previsões meteorológicas de 10 dias é ingerido e indexado num recipiente de armazenamento quente configurado com um período de retenção de 7 dias. Após 7 dias de tempo, a previsão já não é acessível em loja quente, mas pode ser questionada do frio.
* Se ativar a loja quente num ambiente existente que já tenha dados recentes indexados no armazenamento a frio, note que a sua loja quente não estará cheia de dados.
* Se tiver ativado a loja quente e estiver a ter problemas de visualização dos seus dados recentes no Explorer, pode temporariamente desviar as consultas de loja quentes:

   [![Desativar consultas quentes](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>Loja de frio

Esta secção descreve detalhes do Azure Storage relevantes para a Azure Time Series Insights Gen2.

Para obter uma descrição completa do armazenamento do Azure Blob, leia a introdução das [bolhas de armazenamento](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-cold-storage-account"></a>Sua conta de armazenamento frio

Azure Time Series Insights Gen2 retém até duas cópias de cada evento na sua conta de Armazenamento Azure. Uma cópia armazena eventos encomendados pelo tempo de ingestão, sempre permitindo o acesso a eventos numa sequência ordenada pelo tempo. Ao longo do tempo, a Azure Time Series Insights Gen2 também cria uma cópia repartida dos dados para otimizar para consultas performantes.

Todos os seus dados são armazenados indefinidamente na sua conta de Armazenamento Azure.

#### <a name="writing-and-editing-blobs"></a>Bolhas de escrita e edição

Para garantir o desempenho da consulta e a disponibilidade de dados, não edite ou elimine quaisquer bolhas que o Azure Time Series Insights Gen2 cria.

#### <a name="accessing-cold-store-data"></a>Acesso aos dados do cold store

Além de aceder aos seus dados a partir do [Azure Time Series Insights Explorer](./concepts-ux-panels.md) e da Time Series Consultas [APIs,](./concepts-query-overview.md)também pode querer aceder aos seus dados diretamente a partir dos ficheiros Parquet armazenados no cold store. Por exemplo, pode ler, transformar e limpar dados num caderno Jupyter e depois usá-lo para treinar o seu modelo de Aprendizagem automática Azure no mesmo fluxo de trabalho spark.

Para aceder aos dados diretamente da sua conta de Armazenamento Azure, precisa de ler o acesso à conta utilizada para armazenar os seus dados Azure Time Series Insights Gen2. Pode então ler dados selecionados com base no tempo de criação do ficheiro Parquet localizado na `PT=Time` pasta descrita abaixo na secção [de formato de ficheiro Parquet.](#parquet-file-format-and-folder-structure)  Para obter mais informações sobre como permitir o acesso à sua conta de armazenamento, consulte [Gerir o acesso aos recursos da sua conta de armazenamento.](../storage/blobs/anonymous-read-access-configure.md)

#### <a name="data-deletion"></a>Eliminação de dados

Não apague os ficheiros Azure Time Series Insights Gen2. Gerencie dados relacionados apenas dentro da Azure Time Series Insights Gen2.

### <a name="parquet-file-format-and-folder-structure"></a>Formato de ficheiro parquet e estrutura de pasta

Parquet é um formato de ficheiro colunar de código aberto projetado para um armazenamento e desempenho eficientes. Azure Time Series Insights Gen2 utiliza o Parquet para ativar o desempenho da consulta baseada em Séries de Tempo à escala.  

Para mais informações sobre o tipo de ficheiro Parquet, leia a [documentação do Parquet.](https://parquet.apache.org/documentation/latest/)

Azure Time Series Insights Gen2 armazena cópias dos seus dados da seguinte forma:

* A primeira cópia inicial é dividida pelo tempo de ingestão e armazena os dados aproximadamente por ordem de chegada. Estes dados residem na `PT=Time` pasta:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A segunda cópia repartida é agrupada por IDs da Série De Tempo e reside na `PT=TsId` pasta:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

O tempo de classificação nos nomes blob da `PT=Time` pasta corresponde à hora de chegada dos dados à Azure Time Series Insights Gen2 e não ao tempotad dos eventos.

Os dados na `PT=TsId` pasta serão otimizados para consulta ao longo do tempo e não estão estáticos. Durante a repartição, alguns eventos podem estar presentes em várias bolhas. O nome das bolhas nesta pasta não é garantido que permaneça o mesmo.

Em geral, se precisar de aceder aos dados diretamente através dos ficheiros Parquet, utilize a `PT=Time` pasta.  A funcionalidade futura permitirá um acesso eficiente à `PT=TsId` pasta.

> [!NOTE]
>
> * `<YYYY>` mapas para uma representação de quatro dígitos.
> * `<MM>` mapas para uma representação de dois dígitos meses.
> * `<YYYYMMDDHHMMSSfff>` mapas para uma representação de carimbo de tempo com quatro dígitos de ano `YYYY` (), mês de dois dígitos `MM` ( ), dia de dois dígitos `DD` (), duas horas `HH` de dois dígitos ( ), dois `MM` dígitos de segundo ( ) `SS` e milissegundo de três dígitos ( `fff` ).

Os eventos da Azure Time Series Insights Gen2 estão mapeados para os conteúdos de ficheiros Parquet da seguinte forma:

* Cada evento mapeia para uma única linha.
* Cada linha inclui a coluna **de relógios** com uma carimbo de tempo de evento. A propriedade do carimbo de tempo nunca é nula. O incumprimento do **evento é o tempo previsto** se a propriedade do carimbo de tempo não for especificada na fonte do evento. O carimbo de tempo armazenado está sempre na UTC.
* Todas as linhas incluem a coluna ID (TSID) da Série De Tempo(s) definida quando o ambiente Azure Time Series Insights Gen2 é criado. O nome da propriedade TSID inclui o `_string` sufixo.
* Todas as outras propriedades enviadas como dados de telemetria são mapeadas para nomes de colunas que terminam com `_bool` (boolean), `_datetime` (carimbo de `_long` tempo), `_double` (longo), `_string` (duplo), (cadeia) ou `dynamic` (dinâmico), dependendo do tipo de propriedade.  Para mais informações, leia sobre [os tipos de dados suportados.](./concepts-supported-data-types.md)
* Este esquema de mapeamento aplica-se à primeira versão do formato de ficheiro, referenciada como **V=1** e armazenada na pasta base com o mesmo nome. À medida que esta funcionalidade evolui, este esquema de mapeamento pode mudar e o nome de referência incrementado.

## <a name="next-steps"></a>Passos seguintes

* Leia sobre [modelação de dados.](./concepts-model-overview.md)

* Planeie o seu [ambiente Azure Time Series Insights Gen2](./how-to-plan-your-environment.md).
