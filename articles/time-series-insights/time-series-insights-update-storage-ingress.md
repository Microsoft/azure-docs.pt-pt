---
title: Armazenamento de dados e ingresso em Pré-visualização - Azure Time Series Insights  Microsoft Docs
description: Saiba mais sobre o armazenamento de dados e a entrada em Visualização de Insights da Série De Tempo Azure.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 2f12cf303c58f0fa614c59ffe643c6c2ee5d2415
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246191"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Armazenamento de dados e ingresso na Pré-visualização de Insights da Série De Tempo azure

Este artigo descreve atualizações ao armazenamento de dados e ingresso para a Pré-visualização de Insights da Série De Tempo Azure. Descreve a estrutura de armazenamento subjacente, o formato de ficheiro e a propriedade ID da Série Time. O processo de ingresso subjacente, as melhores práticas e as limitações de pré-visualização atuais também são descritos.

## <a name="data-ingress"></a>Ingressos de dados

O seu ambiente Azure Time Series Insights contém um motor de *ingestão* para recolher, processar e armazenar dados da série time. 

Existem algumas considerações a ter em conta para garantir que todos os dados que chegam são processados, para alcançar uma escala elevada de ingresso, e minimizar a *latência de ingestão* (o tempo detempo tomado pela Time Series Insights para ler e processar dados a partir da fonte do evento) ao [planear o seu ambiente](time-series-insights-update-plan.md).

Time Series Insights Preview Políticas de ingresso de dados determinam de onde os dados podem ser obtidos e de que formato os dados devem ter.

### <a name="ingress-policies"></a>Políticas de ingresso

*A entrada de dados* envolve como os dados são enviados para um ambiente de pré-visualização da Série De Tempo Azure Insights. 

A configuração das chaves, a formatação e as melhores práticas são resumidas abaixo.

#### <a name="event-sources"></a>Fontes de eventos

A Pré-visualização da Série de Tempo Azure suporta as seguintes fontes de evento:

- [Hub IoT do Azure](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

A Pré-visualização da Série de Tempo Azure suporta um máximo de duas fontes de eventopor exemplo.

> [!IMPORTANT] 
> * Pode sentir alta latência inicial ao ligar uma fonte de evento ao seu ambiente de pré-visualização. 
> A latência de fonte de eventos depende do número de eventos atualmente no seu Hub IoT ou Hub de Eventos.
> * A alta latência diminuirá após os dados de origem do evento serem ingeridos pela primeira vez. Envie um bilhete de apoio através do portal Azure se sentir alta latência.

#### <a name="supported-data-format-and-types"></a>Formato e tipos de dados suportados

A Azure Time Series Insights suporta o UTF-8 codificado JSON enviado do Azure IoT Hub ou do Azure Event Hubs. 

Os tipos de dados suportados são:

| Tipo de dados | Descrição |
|---|---|
| **bool** | Um tipo de dados com um de dois estados: `true` ou `false`. |
| **dataTempo** | Representa um instante no tempo, tipicamente expresso como uma data e hora do dia. Expresso no formato [ISO 8601.](https://www.iso.org/iso-8601-date-and-time-format.html) |
| **duplo** | Um ponto flutuante De precisão dupla 64 [bits IEEE 754.](https://ieeexplore.ieee.org/document/8766229) |
| **cadeia** | Valores de texto, compostos por caracteres Unicode.          |

#### <a name="objects-and-arrays"></a>Objetos e matrizes

Pode enviar tipos complexos, como objetos e matrizes como parte da carga útil do seu evento, mas os seus dados serão submetidos a um processo de achatamento quando armazenados. 

Informações detalhadas que descrevem como moldar os seus eventos JSON, enviar tipo complexo e achatamento de objetos aninhados estão disponíveis em [Como moldar a JSON para ingresso e consulta](./time-series-insights-update-how-to-shape-events.md) para ajudar no planeamento e otimização.

### <a name="ingress-best-practices"></a>Ingress as melhores práticas

Recomendamos que utilize as seguintes melhores práticas:

* Configure os Insights da Série de Tempo Azure e qualquer Hub IoT ou Hub de Eventos na mesma região para reduzir a latência potencial.

* [Planeie as suas necessidades](time-series-insights-update-plan.md) de escala calculando a sua taxa de ingestão antecipada e verificando se se enquadra na taxa suportada abaixo.

* Compreenda como otimizar e moldar os seus dados JSON, bem como as limitações atuais na pré-visualização, lendo [como moldar a JSON para ingresso e consulta](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-preview-limitations"></a>Escala de ingresso e limitações de pré-visualização 

As limitações de ingresso de pré-visualização da Série de Tempo Azure são descritas abaixo.

> [!TIP]
> Leia [Planifique o seu ambiente de pré-visualização](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) para uma lista completa de todos os limites de pré-visualização.

#### <a name="per-environment-limitations"></a>Por limitações ambientais

Em geral, as taxas de ingresso são vistas como o fator do número de dispositivos que estão na sua organização, frequência de emissões de eventos e o tamanho de cada evento:

*  **Número de dispositivos** × Frequência de **emissão** de evento × **Tamanho de cada evento**.

Por padrão, a pré-visualização time Series Insights pode ingerir dados de entrada a uma taxa de **até 1 megabyte por segundo (MBps) por ambiente Time Series Insights**.

> [!TIP] 
> * O apoio ambiental para ingerir velocidades até 16 MBps pode ser fornecido medendo a pedido.
> * Contacte-nos se necessitar de uma maior entrada, submetendo um bilhete de apoio através do portal Azure.
 
* **Exemplo 1:**

    A Transportes De Contoso tem 100.000 dispositivos que emitem um evento três vezes por minuto. O tamanho de um evento é de 200 bytes. Estão a usar um Event Hub com quatro divisórias como fonte do evento Time Series Insights.

    * A taxa de ingestão para o seu ambiente Time Series Insights seria: **100.000 dispositivos * 200 bytes/evento * (3/60 evento/seg) = 1 MBps**.
    * A taxa de ingestão por partição seria de 0,25 MBps.
    * A taxa de ingestão da Transportes de Contoso estaria dentro da limitação da escala de pré-visualização.

* **Exemplo 2:**

    Contoso Fleet Analytics tem 60.000 dispositivos que emitem um evento a cada segundo. Eles estão usando uma contagem de partição IoT Hub 24 de 4 como a fonte do evento Time Series Insights. O tamanho de um evento é de 200 bytes.

    * A taxa de ingestão ambiental seria: **20.000 dispositivos * 200 bytes/evento * 1 evento/seg = 4 MBps**.
    * A taxa por divisória seria de 1 MBps.
    * A Frota De Contoso Analytics pode submeter um pedido à Time Series Insights através do portal Azure para aumentar a taxa de ingestão para o seu ambiente.

#### <a name="hub-partitions-and-per-partition-limits"></a>Divisórias do hub e por limites de partição

Ao planear o seu ambiente Time Series Insights, é importante considerar a configuração da(s) fonte do evento que estará conectando a Time Series Insights. Tanto o Azure IoT Hub como os Centros de Eventos utilizam divisórias para permitir a escala horizontal para o processamento de eventos. 

Uma *partição* é uma sequência ordenada de eventos realizados num centro. A contagem de partição é definida durante a fase de criação do hub e não pode ser alterada. 

Para os Centros de Eventos que partem as melhores práticas, reveja [quantas divisórias preciso?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> A maioria dos Hubs IoT usados com Insights da Série Relógio Azure só precisam de quatro divisórias.

Quer esteja a criar um novo hub para o seu ambiente Time Series Insights ou a usar um existente, terá de calcular a sua taxa de ingestão por divisória para determinar se está dentro dos limites de pré-visualização. 

A Pré-visualização da Série de Tempo Azure tem atualmente um limite geral **por divisória de 0,5 MBps**.

#### <a name="iot-hub-specific-considerations"></a>Considerações específicas do Hub IoT

Quando um dispositivo é criado no IoT Hub, é permanentemente atribuído a uma partição. Ao fazê-lo, o IoT Hub é capaz de garantir a encomenda de eventos (uma vez que a atribuição nunca muda).

Uma atribuição de divisória fixa também impacta as instâncias da Time Series Insights que estão a ingerir dados enviados do IoT Hub a jusante. Quando as mensagens de vários dispositivos são encaminhadas para o hub utilizando o mesmo ID do dispositivo gateway, podem chegar à mesma divisória ao mesmo tempo potencialmente excedendo os limites de escala de divisórias por divisória. 

**Impacto:**

* Se uma única partição experimentar uma taxa sustentada de ingestão acima do limite de pré-visualização, é possível que a Time Series Insights não sincronize toda a telemetria do dispositivo antes de o período de retenção de dados do IoT Hub ter sido ultrapassado. Como resultado, os dados enviados podem ser perdidos se os limites de ingestão forem consistentemente ultrapassados.

Para mitigar esta circunstância, recomendamos as seguintes melhores práticas:

* Calcule o seu por ambiente e por taxas de ingestão de divisórias antes de implementar a sua solução.
* Certifique-se de que os seus dispositivos IoT Hub são equilibrados na medida mais possível.

> [!IMPORTANT]
> Para ambientes que utilizam o IoT Hub como fonte de evento, calcule a taxa de ingestão utilizando o número de dispositivos hub utilizados para se certificar de que a taxa fica abaixo dos 0,5 MBps por limitação de divisória sintetizadora em pré-visualização.
> * Mesmo que vários eventos cheguem simultaneamente, o limite de Pré-Visualização não será ultrapassado.

  ![Diagrama de partição do hub iot](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Consulte os seguintes recursos para saber mais sobre otimizar a entrada e as divisórias do hub:

* [Escala de hub iot](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Escala de Hub de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Partições do Centro de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Armazenamento de dados

Ao criar um ambiente SKU *pay-as-you-go* (PAYG) da Time Series Insights, cria dois recursos Azure:

* Um ambiente de pré-visualização da Série De Tempo Azure Insights que pode ser configurado para armazenamento de dados quentes.
* Uma conta V1 de uso geral de armazenamento azure para armazenamento de dados frios.

Os dados na sua loja quente só estão disponíveis através da [Time Series Query](./time-series-insights-update-tsq.md) e do explorador de [pré-visualização](./time-series-insights-update-explorer.md)da Série De Tempo Azure Insights . A sua loja quente conterá dados recentes dentro do período de [retenção](./time-series-insights-update-plan.md#the-preview-environment) selecionado ao criar o ambiente Time Series Insights.

Time Series Insights Preview guarda os seus dados da loja fria para o armazenamento De Blob Azure no formato de [ficheiro Parquet](#parquet-file-format-and-folder-structure). Time Series Insights Preview gere estes dados da loja de frio exclusivamente, mas está disponível para que você leia diretamente como ficheiros Parquet padrão.

> [!WARNING]
> Como proprietário da conta de armazenamento Azure Blob onde residem os dados da loja de frio, tem acesso total a todos os dados da conta. Este acesso inclui escrever e eliminar permissões. Não edite ou elimine os dados que a Time Series Insights Preview escreve, porque isso pode causar perda de dados.

### <a name="data-availability"></a>Disponibilidade de dados

Visualização de visualizações de visualizações da Série de Tempo Azure e indexa dados para um desempenho ótimo da consulta. Os dados ficam disponíveis para consulta tanto do calor (se ativado) como do cold store depois de indexado. A quantidade de dados que estão a ser ingeridos pode afetar esta disponibilidade.

> [!IMPORTANT]
> Durante a pré-visualização, poderá experimentar um período de até 60 segundos antes de os dados se tornarem disponíveis. Se sentir uma latência significativa para além dos 60 segundos, por favor envie um bilhete de apoio através do portal Azure.

## <a name="azure-storage"></a>Storage do Azure

Esta secção descreve detalhes do Armazenamento Azure relevantes para a Pré-visualização de Insights da Série De Tempo Azure.

Para obter uma descrição completa do armazenamento de Blob Azure, leia a introdução das bolhas de [armazenamento](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>A sua conta de armazenamento

Quando cria um ambiente payG de pré-visualização da Série De Tempo Azure, é criada uma conta V1 de uso geral de armazenamento azure como a sua loja de frio de longo prazo.  

A Pré-visualização da Série de Tempo Azure retém até duas cópias de cada evento na sua conta de Armazenamento Azure. Uma cópia armazena eventos encomendados pelo tempo de ingestão, permitindo sempre o acesso a eventos numa sequência ordenada pelo tempo. Com o tempo, a Time Series Insights Preview também cria uma cópia reparticionada dos dados para otimizar para a consulta performante time series Insights. 

Durante a Pré-visualização pública, os dados são armazenados indefinidamente na sua conta de Armazenamento Azure.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Escrever e editar as bolhas da Série de Tempo Insights

Para garantir a consulta de desempenho e disponibilidade de dados, não edite ou elimine quaisquer blobs que a Time Series Insights Preview cria.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Aceder a Time Series Insights Preview dados da loja de frio 

Além de aceder aos seus dados do explorador de [pré-visualização](./time-series-insights-update-explorer.md) da Time Series Insights e da Consulta da Série de [Tempo,](./time-series-insights-update-tsq.md)também poderá querer aceder aos seus dados diretamente a partir dos ficheiros Parquet armazenados na loja de frio. Por exemplo, pode ler, transformar e limpar dados num caderno jupyter, depois usá-lo para treinar o seu modelo de Aprendizagem automática Azure no mesmo fluxo de trabalho spark.

Para aceder aos dados diretamente da sua conta de Armazenamento Azure, precisa de ler o acesso à conta utilizada para armazenar os dados de Pré-visualização da Série Time Insights. Pode então ler dados selecionados com base no tempo de criação do ficheiro Parquet localizado na pasta `PT=Time` descrita abaixo na secção de formato de [ficheiroParquet.](#parquet-file-format-and-folder-structure)  Para obter mais informações sobre o acesso à sua conta de armazenamento, consulte [Gerir o acesso aos recursos da sua conta de armazenamento.](../storage/blobs/storage-manage-access-to-resources.md)

#### <a name="data-deletion"></a>Eliminação de dados

Não elimine os ficheiros de pré-visualização da Série De Tempo Insights. Gerencie dados relacionados a partir de dentro da Time Series Insights Preview apenas.

### <a name="parquet-file-format-and-folder-structure"></a>Formato de ficheiro parquet e estrutura de pastas

O Parquet é um formato de ficheiro colunar de código aberto concebido para um armazenamento e desempenho eficientes. Time Series Insights Preview utiliza o Parquet para ativar o desempenho da consulta baseada em ID da Série Time em escala.  

Para mais informações sobre o tipo de ficheiro Parquet, leia a documentação do [Parquet.](https://parquet.apache.org/documentation/latest/)

Time Series Insights Preview armazena cópias dos seus dados da seguinte forma:

* A primeira cópia inicial é dividida pelo tempo de ingestão e armazena os dados aproximadamente por ordem de chegada. Estes dados residem na pasta `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* A segunda cópia, reparticionada é agrupada por IDs da Série Do Tempo e reside na pasta `PT=TsId`:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

Em ambos os casos, a propriedade temporal do ficheiro Parquet corresponde ao tempo de criação de bolhas. Os dados da pasta `PT=Time` são preservados sem alterações uma vez que esteja escrito no ficheiro. Os dados da pasta `PT=TsId` serão otimizados para consulta ao longo do tempo e não estão estáticos.

> [!NOTE]
> * `<YYYY>` mapas para uma representação de quatro dígitos.
> * `<MM>` mapas para uma representação de dois dígitos.
> * `<YYYYMMDDHHMMSSfff>` mapas para uma representação de carimbo de tempo com quatro dígitos ano (`YYYY`), mês de dois dígitos (`MM`), dia de dois dígitos (`DD`), hora de dois dígitos (`HH`), dois dígitos de minuto (`MM`), segundo de dois dígitos (`SS`) e milissegundo de três dígitos (`fff`).

Os eventos de pré-visualização da Série Time Insights são mapeados para conteúdos de ficheiros Parquet da seguinte forma:

* Cada evento mapeia para uma única fila.
* Cada linha inclui a coluna de **carimbo** sinuoso com um carimbo de tempo de evento. A propriedade do carimbo do tempo nunca é nula. Não se incorre no **evento, o tempo que** apropriedade do carimbo de tempo não é especificado na fonte do evento. O carimbo de tempo armazenado está sempre na UTC.
* Todas as linhas incluem a(s) coluna(s) da Série de Tempo (TSID), tal como definida quando o ambiente Time Series Insights é criado. O nome da propriedade TSID inclui o sufixo `_string`.
* Todas as outras propriedades enviadas como dados de telemetria são mapeadas para nomes de colunas que terminam com `_string` (corda), `_bool` (Boolean), `_datetime` (data), ou `_double` (duplo), dependendo do tipo de propriedade.
* Este esquema de mapeamento aplica-se à primeira versão do formato de ficheiro, referenciada como **V=1** e armazenada na pasta base com o mesmo nome. À medida que esta funcionalidade evolui, este esquema de mapeamento pode mudar e o nome de referência incrementado.

## <a name="next-steps"></a>Passos seguintes

- Leia [como moldar a JSON para ingresso e consulta.](./time-series-insights-update-how-to-shape-events.md)

- Leia sobre a nova modelação de [dados.](./time-series-insights-update-tsm.md)
