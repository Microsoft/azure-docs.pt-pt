---
title: Armazenamento de dados e entrada em visualização-Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre o armazenamento de dados e a entrada no Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 62ee248c06d2b26b935f72b3bb73cf708f949c72
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014715"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Armazenamento de dados e entrada na visualização Azure Time Series Insights

Este artigo descreve as atualizações para o armazenamento de dados e entrada para Azure Time Series Insights versão prévia. Ele abrange a estrutura de armazenamento subjacente, o formato de arquivo e a propriedade ID da série temporal. Ele também aborda o processo de entrada subjacente, as práticas recomendadas e as limitações de visualização atuais.

## <a name="data-ingress"></a>Entrada de dados

Seu ambiente de Azure Time Series Insights contém um mecanismo de ingestão para coletar, processar e armazenar dados de série temporal. Ao planejar seu ambiente, há algumas considerações a serem levadas em conta para garantir que todos os dados de entrada sejam processados e para obter alta escala de entrada e minimizar a latência de ingestão (o tempo gasto pelo TSI para ler e processar dados do evento origem). Na visualização Time Series Insights, as políticas de entrada de dados determinam de onde os dados podem ser originados e qual formato os dados devem ter.

### <a name="ingress-policies"></a>Políticas de entrada

Time Series Insights visualização dá suporte às seguintes origens de evento:

- [Hub IoT do Azure](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights visualização dá suporte a um máximo de duas origens de evento por instância.
  
O Azure Time Series Insights dá suporte a JSON enviado por meio do Hub IoT do Azure ou hubs de eventos do Azure.

> [!WARNING] 
> Ao anexar uma nova origem do evento ao seu ambiente de Time Series Insights versão prévia, dependendo do número de eventos atualmente no Hub IoT ou Hub de eventos, você pode enfrentar alta latência inicial de ingestão. À medida que os dados são ingeridos, você deve esperar essa alta latência para o sublado, mas se sua experiência indicar outra hora, entre em contato conosco enviando um tíquete de suporte por meio do portal do Azure.

## <a name="ingress-best-practices"></a>Práticas recomendadas de entrada

Recomendamos que você empregue as seguintes práticas recomendadas:

* Configure Time Series Insights e um hub IoT ou Hub de eventos na mesma região. Isso reduzirá a latência de ingestão incorrida devido à rede.
* Planeje suas necessidades de dimensionamento calculando sua taxa de ingestão antecipada e verificando se ela está dentro da taxa com suporte listada abaixo
* Entenda como otimizar e formatar seus dados JSON, bem como as limitações atuais na visualização, lendo [como formatar JSON para entrada e consulta](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-limitations-in-preview"></a>Escala e limitações de entrada na visualização

Por padrão, Time Series Insights visualização dá suporte a uma escala de entrada inicial de até 1 megabyte por segundo (MB/s) por ambiente. A taxa de transferência de até 16 MB/s está disponível, se necessário, entre em contato conosco enviando um tíquete de suporte no portal do Azure se isso for necessário. Além disso, há um limite por partição de 0,5 MB/s. Isso tem implicações para clientes que usam o Hub IoT especificamente, considerando a afinidade entre um dispositivo do Hub IoT uma partição. Em cenários em que um dispositivo de gateway está encaminhando mensagens para o Hub usando sua própria ID de dispositivo e cadeia de conexão, há o risco de atingir o limite de 0,5 MB/s, Considerando que as mensagens chegarão em uma única partição, mesmo que o conteúdo do evento especifique diferentes TS Identidade. Em geral, a taxa de entrada é exibida como um fator do número de dispositivos que estão em sua organização, a frequência de emissão de eventos e o tamanho de um evento. Ao calcular a taxa de ingestão, os usuários do Hub IoT devem usar o número de conexões de Hub em uso, em vez do total de dispositivos na organização. O suporte ao dimensionamento avançado está em andamento. Esta documentação será atualizada para refletir essas melhorias. 

> [!WARNING]
> Para ambientes que usam o Hub IoT como uma origem de evento, calcule a taxa de ingestão usando o número de dispositivos de Hub em uso.

Consulte os links a seguir para obter mais informações sobre unidades e partições de produtividade:

* [Escala do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Escala do hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partições do hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

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
> A próxima versão de GA (disponibilidade geral) do Time Series Insights disponibilizará os dados em 60 segundos depois que eles forem lidos da origem do evento. Durante a visualização, você pode experimentar um período mais longo antes que os dados se tornem disponíveis. Se você enfrentar uma latência significativa além de 60 segundos, envie um tíquete de suporte por meio do portal do Azure.

## <a name="azure-storage"></a>Storage do Azure

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
