---
title: Visão geral do armazenamento - Azure Time Series Insights / Microsoft Docs
description: Saiba mais sobre o armazenamento de dados no Azure Time Series Insights.
author: esung22
ms.author: elsung
manager: diegoviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: fc6b6b42293b4f2028f1a12af950e96e28febc87
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085570"
---
# <a name="data-storage"></a>Armazenamento de Dados

Quando cria um ambiente SKU *pay-as-you-go* (PAYG) da Série Tempo, cria-se dois recursos Azure:

* Um ambiente Azure Time Series Insights que pode ser configurado para armazenamento de dados quente.
* Uma conta de armazenamento Azure para armazenamento de dados frios.

Os dados na sua loja quente só estão disponíveis através [da Consulta série de tempo](./time-series-insights-update-tsq.md) e do explorador [Azure Time Series Insights](./time-series-insights-update-explorer.md). A sua loja quente conterá dados recentes dentro do [período de retenção](./time-series-insights-update-plan.md#the-preview-environment) selecionado ao criar o ambiente Time Series Insights.

A azure Time Series Insights guarda os seus dados de cold store para o armazenamento Azure Blob no [formato de ficheiro Parquet](#parquet-file-format-and-folder-structure). O Time Series Insights gere estes dados do cold store exclusivamente, mas está disponível para que leia diretamente como ficheiros Parquet padrão.

> [!WARNING]
> Como proprietário da conta de armazenamento Azure Blob onde residem os dados do cold store, tem acesso total a todos os dados da conta. Este acesso inclui permissões de escrita e eliminação. Não edite nem elimine os dados que o Azure Time Series Insights escreve porque isso pode causar a perda de dados.

## <a name="data-availability"></a>Disponibilidade de dados

Azure Time Series Insights Insights partitions and indexes datas para o melhor desempenho da consulta. Os dados ficam disponíveis para consulta a partir de quente (se ativado) e de cold store após a sua indexação. A quantidade de dados que estão a ser ingeridos pode afetar esta disponibilidade.

> [!IMPORTANT]
> Pode experimentar um período de até 60 segundos antes de os dados se tornarem disponíveis. Se sentir uma latência significativa para além de 60 segundos, por favor envie um bilhete de apoio através do portal Azure.

## <a name="azure-storage"></a>Storage do Azure

Esta secção descreve detalhes do Azure Storage relevantes para a Azure Time Series Insights.

Para obter uma descrição completa do armazenamento do Azure Blob, leia a introdução das [bolhas de armazenamento](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>A sua conta de armazenamento

Quando cria um ambiente Azure Time Series Insights PAYG, uma conta Azure Storage é criada como o seu cold store de longo prazo.  

A Azure Time Series Insights retém até duas cópias de cada evento na sua conta de Armazenamento Azure. Uma cópia armazena eventos encomendados pelo tempo de ingestão, sempre permitindo o acesso a eventos numa sequência ordenada pelo tempo. Ao longo do tempo, o Time Series Insights também cria uma cópia repartida dos dados para otimizar para a consulta de Insights de Série sonora performante.

Todos os seus dados são armazenados indefinidamente na sua conta de Armazenamento Azure.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Escrever e editar séries de tempo Insights blobs

Para garantir o desempenho da consulta e a disponibilidade de dados, não edite ou elimine quaisquer bolhas que o Time Series Insights cria.

#### <a name="accessing-time-series-insights-cold-store-data"></a>Aceder a Time Series Insights insights dados de cold store

Além de aceder aos seus dados a partir do [explorador time series Insights](./time-series-insights-update-explorer.md) e da Consulta time [series,](./time-series-insights-update-tsq.md)também pode querer aceder aos seus dados diretamente a partir dos ficheiros Parquet armazenados na loja de frio. Por exemplo, pode ler, transformar e limpar dados num caderno Jupyter e depois usá-lo para treinar o seu modelo de Aprendizagem automática Azure no mesmo fluxo de trabalho spark.

Para aceder aos dados diretamente da sua conta de Armazenamento Azure, é necessário ler o acesso à conta utilizada para armazenar os seus dados de Pré-visualização de Insights de Séries Tempotadas. Pode então ler dados selecionados com base no tempo de criação do ficheiro Parquet localizado na `PT=Time` pasta descrita abaixo na secção [de formato de ficheiro Parquet.](#parquet-file-format-and-folder-structure)  Para obter mais informações sobre como permitir o acesso à sua conta de armazenamento, consulte [Gerir o acesso aos recursos da sua conta de armazenamento.](../storage/blobs/storage-manage-access-to-resources.md)

#### <a name="data-deletion"></a>Eliminação de dados

Não apague os seus ficheiros Azure Time Series Insights. Gerencie dados relacionados apenas dentro do Azure Time Series Insights.

### <a name="parquet-file-format-and-folder-structure"></a>Formato de ficheiro parquet e estrutura de pasta

Parquet é um formato de ficheiro colunar de código aberto projetado para um armazenamento e desempenho eficientes. Azure Time Series Insights utiliza o Parquet para ativar o desempenho da consulta baseada em Séries de Tempo à escala.  

Para mais informações sobre o tipo de ficheiro Parquet, leia a [documentação do Parquet.](https://parquet.apache.org/documentation/latest/)

A Azure Time Series Insights armazena cópias dos seus dados da seguinte forma:

* A primeira cópia inicial é dividida pelo tempo de ingestão e armazena os dados aproximadamente por ordem de chegada. Estes dados residem na `PT=Time` pasta:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A segunda cópia repartida é agrupada por IDs da Série De Tempo e reside na `PT=TsId` pasta:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

A datatampia nos nomes blob da `PT=Time` pasta corresponde à hora de chegada dos dados ao Azure Time Series Insights e não ao tempotad dos eventos.

Os dados na `PT=TsId` pasta serão otimizados para consulta ao longo do tempo e não estão estáticos. Durante a repartição, alguns eventos podem estar presentes em várias bolhas. O nome das bolhas nesta pasta não é garantido que permaneça o mesmo. 

Em geral, se precisar de aceder aos dados diretamente através dos ficheiros Parquet, utilize a `PT=Time` pasta.  A funcionalidade futura permitirá um acesso eficiente à `PT=TsId` pasta. 

> [!NOTE]
>
> * `<YYYY>`mapas para uma representação de quatro dígitos.
> * `<MM>`mapas para uma representação de dois dígitos meses.
> * `<YYYYMMDDHHMMSSfff>`mapas para uma representação de carimbo de tempo com quatro dígitos de ano `YYYY` (), mês de dois dígitos `MM` ( ), dia de dois dígitos `DD` (), duas horas `HH` de dois dígitos ( ), dois `MM` dígitos de segundo ( ) `SS` e milissegundo de três dígitos ( `fff` ).

Os eventos de visualização de séries de tempo são mapeados para o conteúdo do ficheiro Parquet da seguinte forma:

* Cada evento mapeia para uma única linha.
* Cada linha inclui a coluna **de relógios** com uma carimbo de tempo de evento. A propriedade do carimbo de tempo nunca é nula. O incumprimento do **evento é o tempo previsto** se a propriedade do carimbo de tempo não for especificada na fonte do evento. O carimbo de tempo armazenado está sempre na UTC.
* Todas as linhas incluem a coluna(s) ID (TSID) da Série De Tempo, tal como definida quando o ambiente de Insights de Séries De Tempo é criado. O nome da propriedade TSID inclui o `_string` sufixo.
* Todas as outras propriedades enviadas como dados de telemetria são mapeadas para nomes de colunas que terminam com `_bool` (boolean), `_datetime` (carimbo de `_long` tempo), `_double` (longo), `_string` (duplo), (cadeia) ou `dynamic` (dinâmico), dependendo do tipo de propriedade.  Para mais informações, leia sobre [os tipos de dados suportados.](./concepts-supported-data-types.md)
* Este esquema de mapeamento aplica-se à primeira versão do formato de ficheiro, referenciada como **V=1** e armazenada na pasta base com o mesmo nome. À medida que esta funcionalidade evolui, este esquema de mapeamento pode mudar e o nome de referência incrementado.

## <a name="next-steps"></a>Próximos passos

* Leia sobre [modelação de dados.](./time-series-insights-update-tsm.md)

* Planeie o seu [ambiente de visualização de insights da série de tempo Azure](./time-series-insights-update-plan.md).
