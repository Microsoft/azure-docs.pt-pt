---
title: Armazenamento de dados e entrada na visualização de Azure Time Series Insights | Microsoft Docs
description: Noções básicas sobre o armazenamento de dados e entrada no Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: 0b61e194bdea5fd8272ffc0fc9e16a2d80d3cf60
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989696"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Armazenamento de dados e entrada na visualização Azure Time Series Insights

Este artigo descreve as atualizações para o armazenamento de dados e entrada para Azure Time Series Insights versão prévia. Ele abrange a estrutura de armazenamento subjacente, o formato de arquivo e a propriedade ID da série temporal. Ele também aborda o processo de entrada, a taxa de transferência e as limitações subjacentes.

## <a name="data-ingress"></a>Entrada de dados

Na visualização Time Series Insights, as políticas de entrada de dados determinam de onde os dados podem ser originados e qual formato os dados devem ter.

[Visão geral do modelo de série temporal![](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>Políticas de entrada

O Time Series Insights Preview dá suporte às mesmas fontes de eventos que Time Series Insights atualmente dá suporte a:

- [Hub IoT do Azure](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights visualização dá suporte a um máximo de duas origens de evento por instância.
  
O Azure Time Series Insights dá suporte a JSON enviado por meio do Hub IoT do Azure ou hubs de eventos do Azure. Para otimizar seus dados de JSON de IoT, saiba [como formatar JSON](./time-series-insights-send-events.md#supported-json-shapes).

### <a name="data-storage"></a>Armazenamento de dados

Ao criar uma Time Series Insights visualização do ambiente de SKU pago conforme o uso, você cria dois recursos do Azure:

* Um ambiente de visualização Time Series Insights que pode incluir, opcionalmente, recursos de armazenamento quentes.
* Uma conta de blob v1 de uso geral do armazenamento do Azure para armazenamento de dados frio.

Os dados em sua loja a quente estão disponíveis apenas por meio da [consulta de série temporal](./time-series-insights-update-tsq.md) e do [Azure Time Series insights Explorer Preview](./time-series-insights-update-explorer.md). 

Time Series Insights visualização salva os dados de armazenamento frio no armazenamento de BLOBs do Azure no [formato de arquivo parquet](#parquet-file-format-and-folder-structure). Time Series Insights visualização gerencia esses dados de armazenamento frio exclusivamente, mas está disponível para você ler diretamente como arquivos parquet padrão.

> [!WARNING]
> Como o proprietário da conta de armazenamento de BLOBs do Azure onde os dados de armazenamento frio residem, você tem acesso completo a todos os dados na conta. Esse acesso inclui permissões de gravação e exclusão. Não edite ou exclua os dados que Time Series Insights as gravações de visualização, pois isso pode causar perda de dados.

### <a name="data-availability"></a>Disponibilidade de dados
Time Series Insights Visualizar partições e dados de índices para obter um desempenho de consulta ideal. Os dados ficam disponíveis para consulta após sua indexação. A quantidade de dados que está sendo ingerida pode afetar essa disponibilidade.

> [!IMPORTANT]
> A versão de GA (disponibilidade geral) do Time Series Insights disponibilizará os dados em 60 segundos após a leitura da origem do evento. Durante a visualização, você pode experimentar um período mais longo antes que os dados se tornem disponíveis. Se você enfrentar uma latência significativa além de 60 segundos, entre em contato conosco.

### <a name="scale"></a>Escalabilidade

Por padrão, Time Series Insights visualização dá suporte a uma escala de entrada inicial de até 1 megabyte por segundo (MB/s) por ambiente. Uma taxa de transferência de até 16 MB/s estará disponível se você precisar dela. Se precisar de suporte de dimensionamento avançado, entre em contato conosco.

Você pode obter recursos adicionais de entrada e colocação em escala para a origem do evento:

* [Hub IoT](../iot-hub/iot-hub-scaling.md)
* [Hubs de Eventos](../event-hubs/event-hubs-scalability.md)

## <a name="azure-storage"></a>Armazenamento do Azure

Esta seção descreve os detalhes do armazenamento do Azure relevantes para Azure Time Series Insights versão prévia.

Para obter uma descrição completa do armazenamento de BLOBs do Azure, leia a [introdução aos blobs de armazenamento](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Sua conta de armazenamento

Quando você cria uma Time Series Insights visualização do ambiente pago conforme o uso, uma conta de blob v1 de uso geral do armazenamento do Azure é criada como sua loja fria de longo prazo.  

Time Series Insights visualização publica até duas cópias de cada evento em sua conta de armazenamento do Azure. A cópia inicial tem eventos ordenados pelo tempo de ingestão e é sempre preservada, para que você possa usar outros serviços para acessá-lo. Você pode usar o Spark, o Hadoop e outras ferramentas familiares para processar os arquivos brutos do parquet. 

Time Series Insights visualização reparticiona os arquivos parquet para otimizar para a consulta Time Series Insights. Essa cópia reparticionada dos dados também é salva.

Durante a visualização pública, os dados são armazenados indefinidamente em sua conta de armazenamento do Azure.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Gravando e editando blobs de Time Series Insights

Para garantir o desempenho da consulta e a disponibilidade de dados, não edite nem exclua nenhum blob que Time Series Insights versão prévia cria.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Acessando e exportando dados do Time Series Insights Preview

Talvez você queira acessar os dados exibidos no Time Series Insights Explorer Preview para usar em conjunto com outros serviços. Por exemplo, você pode usar seus dados para criar um relatório no Power BI ou para treinar um modelo de aprendizado de máquina usando Azure Machine Learning Studio. Ou, você pode usar seus dados para transformar, Visualizar e modelar seus blocos de anotações do Jupyter.

Você pode acessar seus dados de três maneiras gerais:

* No Gerenciador do Time Series Insights Preview. Você pode exportar dados como um arquivo CSV do Explorer. Para obter mais informações, consulte [Time Series insights Preview Explorer](./time-series-insights-update-explorer.md).
* Na API de visualização do Time Series Insights. Você pode acessar o ponto de extremidade da API em `/getRecorded`. Para saber mais sobre essa API, consulte [consulta de série temporal](./time-series-insights-update-tsq.md).
* Diretamente de uma conta de armazenamento do Azure. Você precisa de acesso de leitura para qualquer conta que esteja usando para acessar seus Time Series Insights dados de visualização. Para obter mais informações, consulte [gerenciar o acesso aos recursos da sua conta de armazenamento](../storage/blobs/storage-manage-access-to-resources.md).

### <a name="data-deletion"></a>Exclusão de dados

Não exclua seus arquivos de visualização Time Series Insights. Você deve gerenciar dados relacionados somente no Time Series Insights Preview.

## <a name="parquet-file-format-and-folder-structure"></a>Formato de arquivo parquet e estrutura de pasta

Parquet é um formato de arquivo de coluna de código aberto que foi projetado para um armazenamento e desempenho eficientes. Time Series Insights visualização usa o parquet por esses motivos. Ele particiona os dados por ID de série temporal para desempenho de consulta em escala.  

Para obter mais informações sobre o tipo de arquivo parquet, consulte a [documentação do parquet](https://parquet.apache.org/documentation/latest/).

Time Series Insights visualização armazena cópias de seus dados da seguinte maneira:

* A primeira, a cópia inicial é particionada pelo tempo de ingestão e armazena dados aproximadamente na ordem de chegada. Os dados residem na pasta `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A segunda cópia reparticionada é particionada por um agrupamento de IDs de série temporal e reside na pasta `PT=TsId`:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

Em ambos os casos, os valores de tempo correspondem ao tempo de criação do blob. Os dados na pasta `PT=Time` são preservados. Os dados na pasta `PT=TsId` serão otimizados para consulta ao longo do tempo e não permanecerão estáticos.

> [!NOTE]
> * `<YYYY>` mapeia para uma representação de ano de quatro dígitos.
> * `<MM>` mapeia para uma representação de mês de dois dígitos.
> * `<YYYYMMDDHHMMSSfff>` mapeia para uma representação de carimbo de data/hora com ano de quatro dígitos (`YYYY`), mês de dois dígitos (`MM`), dia de dois dígitos (`DD`), hora de dois dígitos (`HH`), minuto de dois dígitos (`MM`), segundo de dois dígitos (`SS`) e milissegundos de três dígitos (`fff`).

Time Series Insights eventos de visualização são mapeados para o conteúdo do arquivo parquet da seguinte maneira:

* Cada evento é mapeado para uma única linha.
* Cada linha inclui a coluna timestamp com um carimbo de data **/** hora de evento. A propriedade de carimbo de data/hora nunca é nula. O padrão será o **tempo de enfileiramento do evento** se a propriedade de carimbo de data/hora não for especificada na origem do evento. O carimbo de data/hora sempre está em UTC.
* Cada linha inclui a coluna ID da série temporal, conforme definido quando o ambiente de Time Series Insights é criado. O nome da propriedade inclui o sufixo `_string`.
* Todas as outras propriedades enviadas como dados de telemetria são mapeadas para nomes de coluna que terminam com `_string` (cadeia de caracteres), `_bool` (booliano), `_datetime` (DateTime) ou `_double` (duplo), dependendo do tipo de propriedade.
* Este esquema de mapeamento se aplica à primeira versão do formato de arquivo, referenciado como **V = 1**. Conforme esse recurso evolui, o nome pode ser incrementado.

## <a name="next-steps"></a>Passos seguintes

- Leia [Azure Time Series insights visualização de armazenamento e entrada](./time-series-insights-update-storage-ingress.md).

- Leia sobre a nova [modelagem de dados](./time-series-insights-update-tsm.md).
