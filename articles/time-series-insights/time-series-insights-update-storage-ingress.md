---
title: Armazenamento de dados e entrada em visualização-Azure Time Series Insights | Microsoft Docs
description: Saiba mais sobre o armazenamento de dados e a entrada no Azure Time Series Insights Preview.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/31/2019
ms.custom: seodec18
ms.openlocfilehash: f00529d00312fd6acb045de698590047f991bec7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714285"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Armazenamento de dados e entrada na visualização Azure Time Series Insights

Este artigo descreve as atualizações para o armazenamento de dados e entrada para Azure Time Series Insights versão prévia. Ele abrange a estrutura de armazenamento subjacente, o formato de arquivo e a propriedade ID da série temporal. Ele também aborda o processo de entrada subjacente, as práticas recomendadas e as limitações de visualização atuais.

## <a name="data-ingress"></a>Entrada de dados

Seu ambiente de Azure Time Series Insights contém um mecanismo de ingestão para coletar, processar e armazenar dados de série temporal. Ao planejar seu ambiente, há algumas considerações a serem levadas em conta para garantir que todos os dados de entrada sejam processados e para obter alta escala de entrada e minimizar a latência de ingestão (o tempo gasto pelo TSI para ler e processar dados do evento origem). 

Na visualização Time Series Insights, as políticas de entrada de dados determinam de onde os dados podem ser originados e qual formato os dados devem ter.

### <a name="ingress-policies"></a>Políticas de entrada

#### <a name="event-sources"></a>Fontes de eventos

Time Series Insights visualização dá suporte às seguintes origens de evento:

- [Hub IoT do Azure](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights visualização dá suporte a um máximo de duas origens de evento por instância.

> [!WARNING] 
> * Você pode experimentar alta latência inicial ao anexar uma origem do evento ao seu ambiente de visualização. 
> A latência de origem do evento depende do número de eventos atualmente no Hub IoT ou Hub de eventos.
> * A alta latência será sublado após os dados de origem do evento serem ingeridos pela primeira vez. Contacte-nos através da apresentação de um bilhete de apoio através do portal Azure se sentir uma latência contínua.

#### <a name="supported-data-format-and-types"></a>Formato e tipos de dados suportados

A Azure Time Series Insights suporta o UTF8 codificado JSON submetido através do Azure IoT Hub ou do Azure Event Hubs. 

Abaixo está a lista de tipos de dados suportados.

| Tipo de dados | Descrição |
|-----------|------------------|-------------|
| bool      |   Um tipo de dados com um de dois estados: verdadeiro ou falso.       |
| DateTime    |   Representa um instante no tempo, tipicamente expresso como uma data e hora do dia. O DateTimes deve estar no formato ISO 8601.      |
| double    |   Um ponto flutuante De precisão dupla 64 bits IEEE 754
| Cadeia de caracteres    |   Valores de texto, compostos por caracteres Unicode.          |

#### <a name="objects-and-arrays"></a>Objetos e matrizes

Pode enviar tipos complexos, como objetos e matrizes como parte da carga útil do seu evento, mas os seus dados serão submetidos a um processo de achatamento quando armazenados. Para obter mais informações sobre como moldar os seus eventos JSON, bem como detalhes sobre o tipo complexo e o achatamento de objetos aninhadas, consulte a página sobre [como moldar a JSON para ingresso e consulta.](./time-series-insights-update-how-to-shape-events.md)


### <a name="ingress-best-practices"></a>Práticas recomendadas de entrada

Recomendamos que você empregue as seguintes práticas recomendadas:

* Configure insights da Série de Tempo e o seu Hub IoT ou Hub de Eventos na mesma região, de forma a reduzir a latência de ingestão incorrida em rede.
* Planeje suas necessidades de dimensionamento calculando sua taxa de ingestão antecipada e verificando se ela está dentro da taxa com suporte listada abaixo
* Compreenda como otimizar e moldar os seus dados JSON, bem como as limitações atuais na pré-visualização, lendo [como moldar a JSON para ingresso e consulta](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-limitations-in-preview"></a>Escala e limitações de entrada na visualização

#### <a name="per-environment-limitations"></a>Por limitações ambientais

Em geral, as tarifas de entrada são exibidas como o fator do número de dispositivos que estão em sua organização, a frequência de emissão de eventos e o tamanho de cada evento:

*  **Número de dispositivos** × Frequência de **emissão** de evento × **Tamanho de cada evento**.

Por padrão, a pré-visualização time Series Insights pode ingerir dados de entrada a uma taxa de até 1 megabyte por segundo (MBps) **por ambiente TSI**. Contacte-nos se isso não cumprir os seus requisitos, podemos apoiar até 16 MBps para um ambiente, apresentando um bilhete de apoio no portal Azure.
 
Exemplo 1: O Transporte De Contoso tem 100.000 dispositivos que emitem um evento três vezes por minuto. O tamanho de um evento é de 200 bytes. Estão a usar um Centro de Eventos com 4 divisórias como fonte de eventos da TSI.
A taxa de ingestão para o seu ambiente DeT seria: 100.000 dispositivos * 200 bytes/evento * (3/60 evento/seg) = 1 MBps.
A taxa de ingestão por partição seria de 0,25 MBps.
A taxa de ingestão da Transportes de Contoso estaria dentro da limitação da escala de pré-visualização.
 
Exemplo 2: A Frota De Contoso Analytics tem 60.000 dispositivos que emitem um evento a cada segundo. Eles estão usando uma contagem de partição IoT Hub 24 de 4 como a fonte do evento TSI. O tamanho de um evento é de 200 bytes.
A taxa de ingestão ambiental seria: 20.000 dispositivos * 200 bytes/evento * 1 evento/seg = 4 MBps.
A taxa por divisória seria de 1 MBps.
A Frota De Contoso Analytics teria de apresentar um pedido à TSI através do portal Azure para um ambiente dedicado para atingir esta escala.

#### <a name="hub-partitions-and-per-partition-limits"></a>Divisórias do hub e limites por partição

Ao planear o seu ambiente TSI, é importante considerar a configuração da(s) fonte do evento que você estará conectando à TSI. Tanto o Azure IoT Hub como os Centros de Eventos utilizam divisórias para permitir a escala horizontal para o processamento de eventos.  Uma partição é uma sequência ordenada de eventos que é realizada num centro. A contagem de partição é definida durante a fase de criação do IoT ou do Event Hubs, e não é mutável. Para obter mais informações sobre a determinação da contagem de partições, consulte o FAQ dos Centros de Eventos de quantas divisórias preciso? Para ambientes TSI que usam o Hub IoT, geralmente a maioria dos Hubs IoT só precisa de 4 divisórias. Quer esteja ou não a criar um novo centro para o seu ambiente TSI, ou usando um existente, terá de calcular a sua taxa de ingestão por divisória para determinar se está dentro dos limites de pré-visualização. A pré-visualização da TSI tem atualmente um limite **por divisória** de 0,5 MB/s. Utilize os exemplos abaixo como referência e, por favor, note a seguinte consideração específica do IoT Hub se for um utilizador do IoT Hub.

#### <a name="iot-hub-specific-considerations"></a>Considerações específicas do Hub IoT

Quando um dispositivo é criado no IoT Hub é atribuído a uma partição, e a atribuição da partição não se altera. Ao fazê-lo, o IoT Hub é capaz de garantir a encomenda de eventos. No entanto, isto tem implicações para a TSI como leitor a jusante em certos cenários. Quando as mensagens de vários dispositivos são encaminhadas para o hub utilizando o mesmo ID do dispositivo gateway, chegarão na mesma divisória, ultrapassando assim a limitação da escala de divisórias. 

**Impacto**: Se uma única partição experimentar uma taxa sustentada de ingestão sobre a limitação de pré-visualização, existe o potencial que o leitor de TSI nunca recuperará antes de o período de retenção de dados do IoT Hub ter sido ultrapassado. Isto causaria uma perda de dados.

Recomendamos o seguinte: 

* Calcule o seu por ambiente e por taxa de ingestão de divisórias antes de implementar a sua solução
* Certifique-se de que os seus dispositivos IoT Hub (e, portanto, divisórias) são equilibrados em carga até ao prolongamento mais distante possível

> [!WARNING]
> Para ambientes que utilizam o IoT Hub como fonte de evento, calcule a taxa de ingestão utilizando o número de dispositivos hub utilizados para se certificar de que a taxa fica abaixo dos 0,5 MBps por limitação de divisória sintetizadora em pré-visualização.

  ![Diagrama de partição do hub iot](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Consulte os seguintes links para obter mais informações sobre unidades de entrada e divisórias:

* [Escala de hub iot](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Escala de Hub de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partições do Centro de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Armazenamento de dados

Ao criar uma Time Series Insights visualização do ambiente de SKU pago conforme o uso, você cria dois recursos do Azure:

* Um ambiente de visualização Time Series Insights que pode incluir, opcionalmente, recursos de armazenamento quentes.
* Uma conta de blob v1 de uso geral do armazenamento do Azure para armazenamento de dados frio.

Os dados na sua loja quente só estão disponíveis através da [Time Series Query](./time-series-insights-update-tsq.md) e do explorador de [pré-visualização](./time-series-insights-update-explorer.md)da Série De Tempo Azure Insights . 

Time Series Insights Preview guarda os seus dados da loja fria para o armazenamento De Blob Azure no formato de [ficheiro Parquet](#parquet-file-format-and-folder-structure). Time Series Insights visualização gerencia esses dados de armazenamento frio exclusivamente, mas está disponível para você ler diretamente como arquivos parquet padrão.

> [!WARNING]
> Como o proprietário da conta de armazenamento de BLOBs do Azure onde os dados de armazenamento frio residem, você tem acesso completo a todos os dados na conta. Esse acesso inclui permissões de gravação e exclusão. Não edite ou exclua os dados que Time Series Insights as gravações de visualização, pois isso pode causar perda de dados.

### <a name="data-availability"></a>Disponibilidade de dados

Time Series Insights Visualizar partições e dados de índices para obter um desempenho de consulta ideal. Os dados ficam disponíveis para consulta após sua indexação. A quantidade de dados que está sendo ingerida pode afetar essa disponibilidade.

> [!IMPORTANT]
> Durante a pré-visualização, poderá experimentar um período de até 60 segundos antes de os dados se tornarem disponíveis. Se você enfrentar uma latência significativa além de 60 segundos, envie um tíquete de suporte por meio do portal do Azure.

## <a name="azure-storage"></a>Storage do Azure

Esta seção descreve os detalhes do armazenamento do Azure relevantes para Azure Time Series Insights versão prévia.

Para obter uma descrição completa do armazenamento de Blob Azure, leia a introdução das bolhas de [armazenamento](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Sua conta de armazenamento

Quando você cria uma Time Series Insights visualização do ambiente pago conforme o uso, uma conta de blob v1 de uso geral do armazenamento do Azure é criada como sua loja fria de longo prazo.  

Time Series Insights visualização publica até duas cópias de cada evento em sua conta de armazenamento do Azure. A cópia inicial tem eventos ordenados pelo tempo de ingestão e é sempre preservada, para que você possa usar outros serviços para acessá-lo. Você pode usar o Spark, o Hadoop e outras ferramentas familiares para processar os arquivos brutos do parquet. 

Time Series Insights visualização reparticiona os arquivos parquet para otimizar para a consulta Time Series Insights. Essa cópia reparticionada dos dados também é salva.

Durante a visualização pública, os dados são armazenados indefinidamente em sua conta de armazenamento do Azure.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Gravando e editando blobs de Time Series Insights

Para garantir o desempenho da consulta e a disponibilidade de dados, não edite nem exclua nenhum blob que Time Series Insights versão prévia cria.

#### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Acessando e exportando dados do Time Series Insights Preview

Talvez você queira acessar os dados exibidos no Time Series Insights Explorer Preview para usar em conjunto com outros serviços. Por exemplo, você pode usar seus dados para criar um relatório no Power BI ou para treinar um modelo de aprendizado de máquina usando Azure Machine Learning Studio. Ou, você pode usar seus dados para transformar, Visualizar e modelar seus blocos de anotações do Jupyter.

Você pode acessar seus dados de três maneiras gerais:

* No Gerenciador do Time Series Insights Preview. Você pode exportar dados como um arquivo CSV do Explorer. Para mais informações, leia o explorador de [pré-visualização](./time-series-insights-update-explorer.md)da Time Series Insights .
* A partir da Time Series Insights Preview API usando Get Events Query. Para saber mais sobre esta API, leia A Pergunta da [Série Tempo.](./time-series-insights-update-tsq.md)
* Diretamente de uma conta de armazenamento do Azure. Você precisa de acesso de leitura para qualquer conta que esteja usando para acessar seus Time Series Insights dados de visualização. Para mais informações, leia Gerir o acesso aos recursos da sua conta de [armazenamento.](../storage/blobs/storage-manage-access-to-resources.md)

#### <a name="data-deletion"></a>Exclusão de dados

Não exclua seus arquivos de visualização Time Series Insights. Gerenciar dados relacionados somente no Time Series Insights Preview.

### <a name="parquet-file-format-and-folder-structure"></a>Formato de arquivo parquet e estrutura de pasta

Parquet é um formato de arquivo de coluna de código aberto que foi projetado para um armazenamento e desempenho eficientes. Time Series Insights visualização usa o parquet por esses motivos. Ele particiona os dados por ID de série temporal para desempenho de consulta em escala.  

Para mais informações sobre o tipo de ficheiro Parquet, leia a documentação do [Parquet.](https://parquet.apache.org/documentation/latest/)

Time Series Insights visualização armazena cópias de seus dados da seguinte maneira:

* A primeira, a cópia inicial é particionada pelo tempo de ingestão e armazena dados aproximadamente na ordem de chegada. Os dados residem na pasta `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A segunda cópia, reparticionada é dividida por um agrupamento de IDs da Série do Tempo e reside na pasta `PT=TsId`:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

Em ambos os casos, os valores de tempo correspondem ao tempo de criação do blob. Os dados da pasta `PT=Time` são preservados. Os dados da pasta `PT=TsId` serão otimizados para consulta ao longo do tempo e não permanecerão estáticos.

> [!NOTE]
> * `<YYYY>` mapas para uma representação de quatro dígitos.
> * `<MM>` mapas para uma representação de dois dígitos.
> * `<YYYYMMDDHHMMSSfff>` mapas para uma representação de carimbo de tempo com quatro dígitos ano (`YYYY`), mês de dois dígitos (`MM`), dia de dois dígitos (`DD`), hora de dois dígitos (`HH`), dois dígitos de minuto (`MM`), segundo de dois dígitos (`SS`) e milissegundo de três dígitos (`fff`).

Time Series Insights eventos de visualização são mapeados para o conteúdo do arquivo parquet da seguinte maneira:

* Cada evento é mapeado para uma única linha.
* Cada linha inclui a coluna de **carimbo** sinuoso com um carimbo de tempo de evento. A propriedade de carimbo de data/hora nunca é nula. Não se incorre no **tempo em que** o tempo não é especificado na fonte do evento. O carimbo de data/hora sempre está em UTC.
* Cada linha inclui a(s) coluna s id da Série de Tempo, tal como definida quando o ambiente Time Series Insights é criado. O nome da propriedade inclui o sufixo `_string`.
* Todas as outras propriedades enviadas como dados de telemetria são mapeadas para nomes de colunas que terminam com `_string` (corda), `_bool` (Boolean), `_datetime` (data), ou `_double` (duplo), dependendo do tipo de propriedade.
* Este esquema de mapeamento aplica-se à primeira versão do formato de ficheiro, referenciada como **V=1**. Conforme esse recurso evolui, o nome pode ser incrementado.

## <a name="next-steps"></a>Passos Seguintes

- Leia [como moldar a JSON para ingresso e consulta.](./time-series-insights-update-how-to-shape-events.md)

- Leia sobre a nova modelação de [dados.](./time-series-insights-update-tsm.md)
