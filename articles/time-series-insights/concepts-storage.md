---
title: Visão geral do armazenamento - Azure Time Series Insights Gen2 / Microsoft Docs
description: Saiba mais sobre o armazenamento de dados na Azure Time Series Insights Gen2.
author: esung22
ms.author: elsung
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/31/2020
ms.custom: seodec18
ms.openlocfilehash: c05de0462dde2b09e0e01919dfc691a85df153fa
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89483274"
---
# <a name="data-storage"></a>Armazenamento de Dados

Quando cria um ambiente Azure Time Series Insights Gen2, cria-se dois recursos Azure:

* Um ambiente Azure Time Series Insights Gen2 que pode ser configurado para armazenamento de dados quente.
* Uma conta de armazenamento Azure para armazenamento de dados frios.

Os dados na sua loja quente só estão disponíveis através de [APIs de Consulta de Séries temporizadas](./time-series-insights-update-tsq.md) e do [Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md). A sua loja quente conterá dados recentes dentro do [período de retenção](./time-series-insights-update-plan.md#the-preview-environment) selecionado ao criar o ambiente Azure Time Series Insights Gen2.

Azure Time Series Insights Gen2 guarda os seus dados de cold store para o armazenamento Azure Blob no [formato de ficheiro Parquet](#parquet-file-format-and-folder-structure). Azure Time Series Insights Gen2 gere estes dados do cold store exclusivamente, mas está disponível para você ler diretamente como ficheiros parquet padrão.

> [!WARNING]
> Como proprietário da conta de armazenamento Azure Blob onde residem os dados do cold store, tem acesso total a todos os dados da conta. Este acesso inclui permissões de escrita e eliminação. Não edite nem elimine os dados que o Azure Time Series Insights Gen2 escreve porque isso pode causar perda de dados.

## <a name="data-availability"></a>Disponibilidade de dados

Azure Time Series Insights Gen2 partitions and indexes datas para o melhor desempenho da consulta. Os dados ficam disponíveis para consulta a partir de quente (se ativado) e de cold store após a sua indexação. A quantidade de dados que estão a ser ingeridos pode afetar esta disponibilidade.

> [!IMPORTANT]
> Pode experimentar um período de até 60 segundos antes de os dados se tornarem disponíveis. Se sentir uma latência significativa para além de 60 segundos, por favor envie um bilhete de apoio através do portal Azure.

## <a name="azure-storage"></a>Storage do Azure

Esta secção descreve detalhes do Azure Storage relevantes para a Azure Time Series Insights Gen2.

Para obter uma descrição completa do armazenamento do Azure Blob, leia a introdução das [bolhas de armazenamento](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>A sua conta de armazenamento

Quando cria um ambiente Azure Time Series Insights Gen2, uma conta Azure Storage é criada como a sua loja de frio a longo prazo.  

Azure Time Series Insights Gen2 retém até duas cópias de cada evento na sua conta de Armazenamento Azure. Uma cópia armazena eventos encomendados pelo tempo de ingestão, sempre permitindo o acesso a eventos numa sequência ordenada pelo tempo. Ao longo do tempo, a Azure Time Series Insights Gen2 também cria uma cópia repartida dos dados para otimizar para consultas performantes.

Todos os seus dados são armazenados indefinidamente na sua conta de Armazenamento Azure.

#### <a name="writing-and-editing-blobs"></a>Bolhas de escrita e edição

Para garantir o desempenho da consulta e a disponibilidade de dados, não edite ou elimine quaisquer bolhas que o Azure Time Series Insights Gen2 cria.

#### <a name="accessing-cold-store-data"></a>Acesso aos dados do cold store

Além de aceder aos seus dados a partir do [Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md) e da Time Series Consultas [APIs,](./time-series-insights-update-tsq.md)também pode querer aceder aos seus dados diretamente a partir dos ficheiros Parquet armazenados no cold store. Por exemplo, pode ler, transformar e limpar dados num caderno Jupyter e depois usá-lo para treinar o seu modelo de Aprendizagem automática Azure no mesmo fluxo de trabalho spark.

Para aceder aos dados diretamente da sua conta de Armazenamento Azure, precisa de ler o acesso à conta utilizada para armazenar os seus dados Azure Time Series Insights Gen2. Pode então ler dados selecionados com base no tempo de criação do ficheiro Parquet localizado na `PT=Time` pasta descrita abaixo na secção [de formato de ficheiro Parquet.](#parquet-file-format-and-folder-structure)  Para obter mais informações sobre como permitir o acesso à sua conta de armazenamento, consulte [Gerir o acesso aos recursos da sua conta de armazenamento.](../storage/blobs/storage-manage-access-to-resources.md)

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

## <a name="next-steps"></a>Próximos passos

* Leia sobre [modelação de dados.](./time-series-insights-update-tsm.md)

* Planeie o seu [ambiente Azure Time Series Insights Gen2](./time-series-insights-update-plan.md).
