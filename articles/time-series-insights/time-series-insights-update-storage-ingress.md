---
title: Armazenamento de dados e entrada em Preview - Azure Time Series Insights / Microsoft Docs
description: Saiba mais sobre armazenamento de dados e ingresss in Azure Time Series Insights Preview.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: d3bfb589ec4c152b136e8e1f432864b719c97d58
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2020
ms.locfileid: "85509324"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Armazenamento de dados e entrada em Visualização de Insights da Série Temporal Azure

Este artigo descreve atualizações para armazenamento de dados e ingresss para Azure Time Series Insights Preview. Descreve a estrutura de armazenamento subjacente, o formato de ficheiro e a propriedade de ID da Série De Tempo. O processo de entrada subjacente, as boas práticas e as limitações atuais de pré-visualização também são descritos.

## <a name="data-ingress"></a>Entrada de dados

O seu ambiente Azure Time Series Insights contém um *motor de ingestão* para recolher, processar e armazenar dados da série de tempo.

Existem algumas considerações a ter em conta para garantir que todos os dados recebidos são tratados, para alcançar uma escala elevada de entrada, e minimizar a *latência de ingestão* (o tempo tomado pela Time Series Insights para ler e processar dados a partir da fonte do evento) ao planear o [seu ambiente](time-series-insights-update-plan.md).

Séries de tempo Insights As políticas de ingressão de dados determinam de onde os dados podem ser obtidos e de que formato os dados devem ter.

### <a name="ingress-policies"></a>Políticas ingresss

*A entrada de dados* envolve a forma como os dados são enviados para um ambiente de visualização de insights da Série de Tempo Azure.

A configuração da chave, a formatação e as melhores práticas são resumidas abaixo.

#### <a name="event-sources"></a>Origens de Eventos

A azure Time Series Insights Preview suporta as seguintes fontes de evento:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

A azure Time Series Insights Preview suporta um máximo de duas fontes de evento por exemplo. Quando ligar uma fonte de evento, o seu ambiente TSI irá ler todos os eventos atualmente armazenados no seu Iot ou Event Hub, começando com o evento mais antigo.

> [!IMPORTANT]
>
> * Pode experimentar uma elevada latência inicial ao anexar uma fonte de evento ao seu ambiente de pré-visualização.
> A latência da fonte do evento depende do número de eventos atualmente no seu Hub IoT ou Centro de Eventos.
> * A alta latência diminuirá após a primeira ingestão de dados de origem de eventos. Envie um bilhete de apoio através do portal Azure se sentir uma elevada latência.

#### <a name="supported-data-format-and-types"></a>Formato e tipos de dados suportados

Azure Time Series Insights suporta UTF-8 codificado JSON enviado a partir de Azure IoT Hub ou Azure Event Hubs. 

Os tipos de dados suportados são:

| Tipo de dados | Descrição |
|---|---|
| **bool** | Um tipo de dados com um de dois estados: `true` ou `false` . |
| **dataTime** | Representa um instante no tempo, tipicamente expresso como data e hora do dia. Expresso no formato [ISO 8601.](https://www.iso.org/iso-8601-date-and-time-format.html) |
| **long** | Um inteiro assinado de 64 bits  |
| **double** | Um ponto flutuante [IEEE 754](https://ieeexplore.ieee.org/document/8766229) de precisão dupla. |
| **cadeia** | Valores de texto, compostos por caracteres Unicode.          |

> [!IMPORTANT]
>
> * O seu ambiente de TSI é fortemente dactilografado. Se os dispositivos ou tags enviarem dados integrais e não-culturais, os valores de propriedade do dispositivo serão armazenados em duas colunas duplas e longas separadas e a [função coalesce()](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) deve ser utilizada ao fazer chamadas API e definir as suas expressões variáveis de modelo de séries de tempo.

#### <a name="objects-and-arrays"></a>Objetos e matrizes

Pode enviar tipos complexos, tais como objetos e matrizes, como parte da carga útil do evento, mas os seus dados serão submetidos a um processo de achatamento quando armazenados.

Informações detalhadas que descrevem como moldar os seus eventos JSON, enviar tipo complexo e achatamento de objetos aninhados estão disponíveis em [Como moldar jSON para ingressão e consulta](./time-series-insights-update-how-to-shape-events.md) para ajudar no planeamento e otimização.

### <a name="ingress-best-practices"></a>Ingress melhores práticas

Recomendamos que utilize as seguintes boas práticas:

* Configure Azure Time Series Insights e qualquer IoT Hub ou Event Hub na mesma região para reduzir a latência potencial.

* [Planeie as suas necessidades de escala](time-series-insights-update-plan.md) calculando a sua taxa de ingestão antecipada e verificando se se enquadra na taxa suportada listada abaixo.

* Compreenda como otimizar e moldar os seus dados JSON, bem como as limitações atuais na pré-visualização, lendo [como moldar o JSON para ingresss e consulta.](./time-series-insights-update-how-to-shape-events.md)

* Utilize a ingestão de streaming apenas em tempo real e dados recentes, o streaming de dados históricos não é suportado.

#### <a name="historical-data-ingestion"></a>Ingestão de dados históricos

A utilização do gasoduto de streaming para importar dados históricos não é atualmente suportada na Pré-visualização da Série temporal do Azure. Se precisar de importar dados passados para o seu ambiente, siga as orientações abaixo:

* Não transmita dados ao vivo e históricos em paralelo. Ingerir dados fora da ordem resultará num desempenho de consulta degradado.
* Ingerir dados históricos na moda ordenada pelo tempo para melhor desempenho.
* Mantenha-se dentro dos limites da taxa de produção abaixo.
* Desative a Warm Store se os dados forem mais antigos do que o período de retenção da Warm Store.

### <a name="ingress-scale-and-preview-limitations"></a>Escala de entrada e limitações de pré-visualização

Azure Time Series Insights As limitações de ingresss são descritas abaixo.

> [!TIP]
> Leia [O Plano do seu ambiente de pré-visualização](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) para uma lista completa de todos os limites de pré-visualização.

#### <a name="per-environment-limitations"></a>Por limitações ambientais

Em geral, as taxas de entrada são vistas como o fator do número de dispositivos que estão na sua organização, frequência de emissões de eventos e o tamanho de cada evento:

*  **Número de dispositivos** × **Frequência de emissão de eventos** × **Tamanho de cada evento**.

Por predefinição, a pré-visualização do Time Series Insights pode ingerir dados de entrada a uma taxa de **até 1 megabyte por segundo (MBps) por ambiente de Insights de Séries de Tempo**. Existem limitações adicionais [por partição do hub.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits)

> [!TIP]
>
> * O apoio ao ambiente para a ingestão de velocidades até 16 MBps pode ser fornecido a pedido.
> * Contacte-nos se necessitar de uma produção mais elevada, enviando um bilhete de apoio através do portal Azure.
 
* **Exemplo 1:**

    A Contoso Shipping tem 100.000 dispositivos que emitem um evento três vezes por minuto. O tamanho de um evento é de 200 bytes. Estão a usar um Hub IoT com quatro divisórias como fonte de evento da Time Series Insights.

    * A taxa de ingestão para o seu ambiente de Time Series Insights seria: **100.000 dispositivos * 200 bytes/event * (3/60 evento/seg) = 1 MBps**.
    * A taxa de ingestão por partição seria de 0,25 MBps.
    * A taxa de ingestão da Contoso Shipping estaria dentro da limitação da escala de pré-visualização.

* **Exemplo 2:**

    A Contoso Fleet Analytics tem 60.000 dispositivos que emitem um evento a cada segundo. Eles estão a usar um Centro de Eventos com uma contagem de partição de 4 como fonte de evento time series Insights. O tamanho de um evento é de 200 bytes.

    * A taxa de ingestão ambiental seria: **60.000 dispositivos * 200 bytes/event * 1 evento/seg = 12 MBps**.
    * A taxa por partição seria de 3 MBps.
    * A taxa de ingestão da Frota Contoso é sobre o ambiente e os limites de partição. Podem submeter um pedido ao Time Series Insights através do portal Azure para aumentar a taxa de ingestão para o seu ambiente e criar um Centro de Eventos com mais divisórias para estar dentro dos limites de Pré-visualização.

#### <a name="hub-partitions-and-per-partition-limits"></a>Divisórias centrais e por limite de partição

Ao planear o seu ambiente time series Insights, é importante considerar a configuração da(s) fonte(s) do evento que você estará conectando ao Time Series Insights. Tanto o Azure IoT Hub como o Event Hubs utilizam divisórias para permitir a escala horizontal para o processamento de eventos. 

Uma *partição* é uma sequência ordenada de eventos realizados num centro. A contagem de divisórias é definida durante a fase de criação do hub e não pode ser alterada.

Para o Event Hubs a dividir as melhores práticas, reveja [quantas divisórias preciso?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> A maioria dos hubs IoT usados com Azure Time Series Insights só precisam de quatro divisórias.

Quer esteja a criar um novo hub para o seu ambiente de Insights de Séries Tempotuais ou usando um existente, terá de calcular a sua taxa de ingestão por partição para determinar se está dentro dos limites de pré-visualização. 

A Azure Time Series Insights Preview tem atualmente um limite geral **por partição de 0,5 MBps**.

#### <a name="iot-hub-specific-considerations"></a>Considerações específicas do IoT Hub

Quando um dispositivo é criado no IoT Hub, é permanentemente atribuído a uma partição. Ao fazê-lo, o IoT Hub é capaz de garantir a encomenda de eventos (uma vez que a atribuição nunca muda).

Uma atribuição de partição fixa também impacta as instâncias time Series Insights que estão a ingerir dados enviados do IoT Hub a jusante. Quando as mensagens de vários dispositivos são reencaminhadas para o hub utilizando o mesmo ID do dispositivo de gateway, podem chegar à mesma partição ao mesmo tempo que podem exceder os limites de escala por partição.

**Impacto:**

* Se uma única partição experimentar uma taxa sustentada de ingestão acima do limite de visualização, é possível que a Time Series Insights não sincronize toda a telemetria do dispositivo antes de o período de retenção de dados do IoT Hub ter sido ultrapassado. Como resultado, os dados enviados podem ser perdidos se os limites de ingestão forem consistentemente ultrapassados.

Para mitigar esta circunstância, recomendamos as seguintes boas práticas:

* Calcule as suas taxas de ingestão por ambiente e por partição antes de implementar a sua solução.
* Certifique-se de que os seus dispositivos IoT Hub estão equilibrados na medida do possível.

> [!IMPORTANT]
> Para ambientes que utilizem o IoT Hub como fonte de evento, calcule a taxa de ingestão utilizando o número de dispositivos hub em uso para ter certeza de que a taxa fica abaixo dos 0,5 MBps por limitação de partição na pré-visualização.
>
> * Mesmo que vários eventos cheguem simultaneamente, o limite de pré-visualização não será ultrapassado.

  ![Diagrama de partição do hub IoT](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Consulte os seguintes recursos para saber mais sobre a otimização da produção e partições do hub:

* [Escala de hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Escala de hub de evento](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partições do Hub de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Armazenamento de dados

Quando cria um ambiente SKU de *pré-visualização* de séries de tempo,como-você-go (PAYG), cria dois recursos Azure:

* Um ambiente de pré-visualização de insights de séries de tempo Azure que pode ser configurado para armazenamento de dados quente.
* Uma blob V1 de armazenamento geral Azure para armazenamento de dados frios.

Os dados na sua loja quente só estão disponíveis através [da Consulta série de tempo](./time-series-insights-update-tsq.md) e do explorador de [pré-visualização da Série De Tempo Azure.](./time-series-insights-update-explorer.md) A sua loja quente conterá dados recentes dentro do [período de retenção](./time-series-insights-update-plan.md#the-preview-environment) selecionado ao criar o ambiente Time Series Insights.

Time Series Insights Preview guarda os dados do seu cold store para o armazenamento do Azure Blob no [formato de ficheiro Parquet](#parquet-file-format-and-folder-structure). A Time Series Insights Preview gere estes dados do cold store exclusivamente, mas está disponível para que leia diretamente como ficheiros Parquet padrão.

> [!WARNING]
> Como proprietário da conta de armazenamento Azure Blob onde residem os dados do cold store, tem acesso total a todos os dados da conta. Este acesso inclui permissões de escrita e eliminação. Não edite nem elimine os dados que a Time Series Insights Preview escreve, porque isso pode causar perda de dados.

### <a name="data-availability"></a>Disponibilidade de dados

Azure Time Series Insights Preview partições e indexações dados para o melhor desempenho da consulta. Os dados ficam disponíveis para consulta a partir de quente (se ativado) e de cold store após a sua indexação. A quantidade de dados que estão a ser ingeridos pode afetar esta disponibilidade.

> [!IMPORTANT]
> Durante a pré-visualização, poderá experimentar um período de até 60 segundos antes de os dados se tornarem disponíveis. Se sentir uma latência significativa para além de 60 segundos, por favor envie um bilhete de apoio através do portal Azure.

## <a name="azure-storage"></a>Storage do Azure

Esta secção descreve detalhes do Azure Storage relevantes para a visualização de Insights da Série De Tempo Azure.

Para obter uma descrição completa do armazenamento do Azure Blob, leia a introdução das [bolhas de armazenamento](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>A sua conta de armazenamento

Quando cria um ambiente PAYG de pré-visualização da Série De Tempo Azure, é criada uma conta V1 blob de uso geral do Azure Storage como a sua loja de frio de longo prazo.  

A Azure Time Series Insights Preview mantém até duas cópias de cada evento na sua conta de Armazenamento Azure. Uma cópia armazena eventos encomendados pelo tempo de ingestão, sempre permitindo o acesso a eventos numa sequência ordenada pelo tempo. Ao longo do tempo, a Time Series Insights Preview também cria uma cópia repartida dos dados para otimizar para a consulta de Insights de Série sonora performante.

Durante a visualização pública, os dados são armazenados indefinidamente na sua conta de Armazenamento Azure.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Escrever e editar séries de tempo Insights blobs

Para garantir o desempenho da consulta e a disponibilidade de dados, não edite ou elimine quaisquer bolhas que o Time Series Insights Preview cria.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Aceder a Time Series Insights Visualizar dados do cold store

Além de aceder aos seus dados a partir do explorador de [pré-visualização de Séries Tempotamos](./time-series-insights-update-explorer.md) e [da Consulta série de tempo,](./time-series-insights-update-tsq.md)também pode querer aceder aos seus dados diretamente a partir dos ficheiros Parquet armazenados na loja de frio. Por exemplo, pode ler, transformar e limpar dados num caderno Jupyter e depois usá-lo para treinar o seu modelo de Aprendizagem automática Azure no mesmo fluxo de trabalho spark.

Para aceder aos dados diretamente da sua conta de Armazenamento Azure, é necessário ler o acesso à conta utilizada para armazenar os seus dados de Pré-visualização de Insights de Séries Tempotadas. Pode então ler dados selecionados com base no tempo de criação do ficheiro Parquet localizado na `PT=Time` pasta descrita abaixo na secção [de formato de ficheiro Parquet.](#parquet-file-format-and-folder-structure)  Para obter mais informações sobre como permitir o acesso à sua conta de armazenamento, consulte [Gerir o acesso aos recursos da sua conta de armazenamento.](../storage/blobs/storage-manage-access-to-resources.md)

#### <a name="data-deletion"></a>Eliminação de dados

Não apague os seus ficheiros de visualização de insights de séries temporais. Gerir dados relacionados apenas a partir de in time Series Insights Preview.

### <a name="parquet-file-format-and-folder-structure"></a>Formato de ficheiro parquet e estrutura de pasta

Parquet é um formato de ficheiro colunar de código aberto projetado para um armazenamento e desempenho eficientes. A Preview Time Series Insights utiliza o Parquet para ativar o desempenho da consulta baseada em Séries de Tempo à escala.  

Para mais informações sobre o tipo de ficheiro Parquet, leia a [documentação do Parquet.](https://parquet.apache.org/documentation/latest/)

A Previsão de Insights de Séries De Tempo armazena cópias dos seus dados da seguinte forma:

* A primeira cópia inicial é dividida pelo tempo de ingestão e armazena os dados aproximadamente por ordem de chegada. Estes dados residem na `PT=Time` pasta:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A segunda cópia repartida é agrupada por IDs da Série De Tempo e reside na `PT=TsId` pasta:

  `V=1/PT=TsId/<TSI_INTERNAL_STRUCTURE>/<TSI_INTERNAL_NAME>.parquet`

O relógio de tempo nas manchas os nomes da `PT=Time` pasta corresponde à hora de chegada dos dados à TSI (não à datatamp dos eventos).

Os dados na `PT=TsId` pasta serão otimizados para consulta ao longo do tempo e não estão estáticos. Durante a repartição, os mesmos eventos podem estar presentes em várias bolhas. Além disso, o nome das bolhas pode mudar no futuro.

> [!NOTE]
>
> * `<YYYY>`mapas para uma representação de quatro dígitos.
> * `<MM>`mapas para uma representação de dois dígitos meses.
> * `<YYYYMMDDHHMMSSfff>`mapas para uma representação de carimbo de tempo com quatro dígitos de ano `YYYY` (), mês de dois dígitos `MM` ( ), dia de dois dígitos `DD` (), duas horas `HH` de dois dígitos ( ), dois `MM` dígitos de segundo ( ) `SS` e milissegundo de três dígitos ( `fff` ).

Os eventos de visualização de séries de tempo são mapeados para o conteúdo do ficheiro Parquet da seguinte forma:

* Cada evento mapeia para uma única linha.
* Cada linha inclui a coluna **de relógios** com uma carimbo de tempo de evento. A propriedade do carimbo de tempo nunca é nula. Não tem tempo de **mente** se a propriedade do carimbo de tempo não for especificada na fonte do evento. O carimbo de tempo armazenado está sempre na UTC.
* Todas as linhas incluem a coluna(s) ID (TSID) da Série De Tempo, tal como definida quando o ambiente de Insights de Séries De Tempo é criado. O nome da propriedade TSID inclui o `_string` sufixo.
* Todas as outras propriedades enviadas como dados de telemetria são mapeadas para nomes de colunas que terminam com `_string` (string), `_bool` (Boolean), `_datetime` (data, ou `_double` (duplo), dependendo do tipo de propriedade.
* Este esquema de mapeamento aplica-se à primeira versão do formato de ficheiro, referenciada como **V=1** e armazenada na pasta base com o mesmo nome. À medida que esta funcionalidade evolui, este esquema de mapeamento pode mudar e o nome de referência incrementado.

## <a name="next-steps"></a>Passos seguintes

- Leia [como moldar o JSON para ingresss e consulta](./time-series-insights-update-how-to-shape-events.md).

- Leia sobre a nova [modelação de dados.](./time-series-insights-update-tsm.md)
