---
title: Armazenamento de dados e entrada na visualização de Azure Time Series Insights | Microsoft Docs
description: Noções básicas sobre o armazenamento de dados e entrada no Azure Time Series Insights Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: 98baa8d3f951a8922bcd1f40449fa26840f3a3c4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051471"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Armazenamento de dados e entrada na visualização Azure Time Series Insights

Este artigo descreve as alterações no armazenamento de dados e entrada da versão prévia do Azure Time Series Insights. Ele abrange a estrutura de armazenamento subjacente, o formato de arquivo e a propriedade ID da série temporal. O artigo também aborda o processo de entrada, a taxa de transferência e as limitações subjacentes.

## <a name="data-ingress"></a>Entrada de dados

Azure Time Series Insights políticas de entrada de dados determinam de onde os dados podem ser originados e em qual formato.

[![Visão geral do modelo de série temporal](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>Políticas de entrada

A versão prévia do Time Series Insights dá suporte às mesmas fontes de eventos e tipos de arquivos aos quais Time Series Insights atualmente dá suporte:

- [Hub IoT do Azure](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)
  
O Azure Time Series Insights dá suporte a JSON enviado por meio do Hub IoT do Azure ou hubs de eventos do Azure. Para otimizar seus dados de JSON de IoT, saiba [como formatar JSON](./time-series-insights-send-events.md#json).

### <a name="data-storage"></a>Armazenamento de dados

Ao criar uma Time Series Insights visualização do ambiente de SKU pago conforme o uso, você cria dois recursos:

* Um ambiente Time Series Insights.
* Uma conta v1 de uso geral do armazenamento do Azure em que os dados serão armazenados.

A visualização Time Series Insights usa o armazenamento de BLOBs do Azure com o tipo de arquivo parquet. O Time Series Insights gerencia todas as operações de dados, incluindo a criação de BLOBs, a indexação e o particionamento dos dados na conta de armazenamento do Azure. Você cria esses BLOBs usando uma conta de armazenamento do Azure.

Assim como outros blobs de armazenamento do Azure, os BLOBs criados pelo Time Series Insights permitem que você leia e grave neles para dar suporte a vários cenários de integração.

### <a name="data-availability"></a>Disponibilidade de dados

A Time Series Insights visualização indexa dados usando uma estratégia de otimização de tamanho de BLOB. Os dados ficam disponíveis para consulta após sua indexação, que se baseia na quantidade de dados chegando e em qual velocidade.

> [!IMPORTANT]
> * A versão de GA (disponibilidade geral) Time Series Insights disponibilizará os dados em até 60 segundos depois de chegar a uma fonte de evento.
> * Durante a visualização, espere um período mais longo antes que os dados sejam disponibilizados.
> * Se você tiver qualquer latência significativa, entre em contato conosco.

### <a name="scale"></a>Escala

A visualização de Time Series Insights dá suporte a uma escala de entrada inicial de até 1 Mega byte por segundo (Mbps) por ambiente. O suporte ao dimensionamento avançado está em andamento. Planejamos atualizar nossa documentação para refletir essas melhorias.

## <a name="parquet-file-format"></a>Formato de arquivo parquet

Parquet é um formato de arquivo de dados orientado a coluna que foi projetado para:

* Interoperabilidade
* Eficiência de espaço
* Eficiência da consulta

Time Series Insights escolheu parquet porque fornece compactação de dados eficiente e esquemas de codificação, com desempenho aprimorado que pode lidar com dados complexos em massa.

Para obter mais informações sobre o tipo de arquivo parquet, consulte a [documentação do parquet](https://parquet.apache.org/documentation/latest/).

Para obter mais informações sobre o formato de arquivo parquet no Azure, consulte [tipos de arquivo com suporte no armazenamento do Azure](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#parquet-format).

### <a name="event-structure-in-parquet"></a>Estrutura de eventos em parquet

O Time Series Insights cria e armazena cópias de BLOBs nos dois formatos a seguir:

1. A primeira cópia inicial é particionada por hora de chegada:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Tempo de criação de BLOB para BLOBs particionados por hora de chegada.

1. A segunda cópia reparticionada é particionada por um agrupamento dinâmico da ID da série temporal:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Carimbo de data/hora mínimo de evento em um blob para BLOBs particionados por ID de série temporal.

> [!NOTE]
> * `<YYYY>`mapeia para uma representação de ano de 4 dígitos.
> * `<MM>`mapeia para uma representação de mês de 2 dígitos.
> * `<YYYYMMDDHHMMSSfff>`mapeia para uma representação de carimbo de data/hora com`YYYY`um ano de 4 dígitos ()`MM`, mês de 2 dígitos ()`DD`, dia de 2 dígitos ()`HH`, hora de 2 dígitos ()`MM`, minuto de 2 dígitos ()`SS`, segundo de 2 dígitos () e 3 dígitos milissegundo (`fff`).

Time Series Insights eventos são mapeados para o conteúdo do arquivo parquet da seguinte maneira:

* Cada evento é mapeado para uma única linha.
* Coluna de **carimbo de data/hora** interna com um carimbo de data/hora do evento. A propriedade Timestamp nunca é nula. O padrão será a **origem do evento na fila** quando a propriedade timestamp não for especificada na origem do evento. O carimbo de data/hora está em UTC. 
* Todas as outras propriedades que são mapeadas para colunas `_string` terminam com ( `_bool` String), ( `_datetime` booliano), ( `_double` DateTime) e (Double), dependendo do tipo de propriedade.
* Esse é o esquema de mapeamento para a primeira versão do formato de arquivo, à qual nos referimos como **V = 1**. À medida que esse recurso evoluir, o nome será incrementado para **V = 2**, **v = 3**e assim por diante.

## <a name="azure-storage"></a>Storage do Azure

Esta seção descreve os detalhes do armazenamento do Azure relevantes para Azure Time Series Insights.

Para obter uma descrição completa do serviço de armazenamento de BLOBs do Azure, leia a [introdução aos blobs de armazenamento](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Sua conta de armazenamento

Ao criar um Time Series Insights ambiente pago conforme o uso, você cria dois recursos: um ambiente de Time Series Insights e uma conta de uso geral v1 do armazenamento do Azure em que os dados serão armazenados. Optamos por tornar o armazenamento do Azure de uso geral v1 o recurso padrão devido à sua interoperabilidade, preço e desempenho.

Time Series Insights publica até duas cópias de cada evento em sua conta de armazenamento do Azure. A cópia inicial é sempre preservada para que você possa consultá-la rapidamente usando outros serviços. Você pode usar facilmente o Spark, o Hadoop e outras ferramentas familiares em IDs de série temporal sobre arquivos parquet brutos, pois esses mecanismos dão suporte à filtragem básica de nome de arquivo. O agrupamento de BLOBs por ano e mês é uma maneira útil de listar BLOBs em um intervalo de tempo específico para um trabalho personalizado.

Além disso, Time Series Insights reparticiona os arquivos parquet para otimizar para as APIs de Time Series Insights. O arquivo reparticionado mais recentemente também é salvo.

Durante a visualização pública, os dados são armazenados indefinidamente em sua conta de armazenamento do Azure.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Gravando e editando blobs de Time Series Insights

Para garantir o desempenho da consulta e a disponibilidade dos dados, não edite nem exclua nenhum blob criado por Time Series Insights.

> [!TIP]
> Time Series Insights desempenho poderá ser afetado negativamente se você ler ou gravar em seus BLOBs com muita frequência.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Acessando e exportando dados do Time Series Insights Preview

Talvez você queira acessar os dados armazenados no Time Series Insights Explorer Preview para usar em conjunto com outros serviços. Por exemplo, talvez você queira usar seus dados para relatar no Power BI, executar o Machine Learning usando Azure Machine Learning Studio ou usar em um aplicativo de bloco de anotações com notebooks Jupyter.

Você pode acessar seus dados de três maneiras gerais:

* No Gerenciador de visualização Time Series Insights: você pode exportar dados como um arquivo CSV do Gerenciador de visualização de Time Series Insights. Para obter mais informações, consulte [Time Series insights Preview Explorer](./time-series-insights-update-explorer.md).
* Na Time Series Insights APIs de visualização: o ponto de extremidade da API pode `/getRecorded`ser acessado em. Para saber mais sobre essa API, consulte [consulta de série temporal](./time-series-insights-update-tsq.md).
* Diretamente de uma conta de armazenamento do Azure (abaixo).

#### <a name="from-an-azure-storage-account"></a>De uma conta de armazenamento do Azure

* Você precisa de acesso de leitura para qualquer conta que esteja usando para acessar seus dados de Time Series Insights. Para obter mais informações, consulte [gerenciar o acesso aos recursos da sua conta de armazenamento](../storage/blobs/storage-manage-access-to-resources.md).
* Para obter mais informações sobre maneiras diretas de ler dados do armazenamento de BLOBs do Azure, consulte [escolher uma solução do Azure para transferência de dados](../storage/common/storage-choose-data-transfer-solution.md).
* Para exportar dados de uma conta de armazenamento do Azure:
    * Primeiro, verifique se sua conta atende aos requisitos necessários para exportar dados. Para obter mais informações, consulte [requisitos de importação e exportação de armazenamento](../storage/common/storage-import-export-requirements.md).
    * Para saber mais sobre outras maneiras de exportar dados de sua conta de armazenamento do Azure, consulte [importar e exportar dados de BLOBs](../storage/common/storage-import-export-data-from-blobs.md).

### <a name="data-deletion"></a>Exclusão de dados

Não exclua BLOBs. Eles não só são úteis para auditar e manter um registro de seus dados, a visualização Time Series Insights mantém os metadados de BLOB em cada blob.

## <a name="partitions"></a>Partições

Cada ambiente de visualização de Time Series Insights deve ter uma propriedade **ID de série temporal** e uma propriedade **timestamp** que a identificam de forma exclusiva. Sua ID de série temporal atua como uma partição lógica para seus dados e dá ao ambiente de visualização Time Series Insights um limite natural para distribuir dados entre partições físicas. As partições físicas são gerenciadas pela versão prévia do Time Series Insights em uma conta de armazenamento do Azure.

Time Series Insights usa o particionamento dinâmico para otimizar o desempenho de armazenamento e consulta descartando e recriando partições. O algoritmo de particionamento dinâmico do Time Series Insights Preview tenta impedir que uma única partição física tenha dados para várias partições lógicas distintas. Em outras palavras, o algoritmo de particionamento mantém todos os dados específicos para uma única ID de série temporal presente exclusivamente em arquivos parquet sem ser intercalado com outras IDs de série temporal. O algoritmo de particionamento dinâmico também tenta preservar a ordem original dos eventos em uma única ID de série temporal.

Inicialmente, no momento da entrada, os dados são particionados pelo carimbo de data/hora para que uma única partição lógica em um determinado intervalo de tempo possa ser distribuída entre várias partições físicas. Uma única partição física também pode conter muitas ou todas as partições lógicas. Devido às limitações de tamanho do blob, mesmo com o particionamento ideal, uma única partição lógica pode ocupar várias partições físicas.

> [!NOTE]
> Por padrão, o valor do carimbo de data/hora é o *tempo* enfileirado da mensagem em sua origem de evento configurada.

Se você estiver carregando dados históricos ou mensagens em lote, atribua o valor que deseja armazenar com seus dados à propriedade Timestamp que mapeia para o carimbo de data/hora apropriado. A propriedade Timestamp diferencia maiúsculas de minúsculas. Para obter mais informações, consulte [modelo de série temporal](./time-series-insights-update-tsm.md).

### <a name="physical-partitions"></a>Partições físicas

Uma partição física é um blob de blocos que é armazenado em sua conta de armazenamento. O tamanho real dos BLOBs pode variar porque o tamanho depende da taxa de push. No entanto, esperamos que os BLOBs tenham aproximadamente 20 MB a 50 MB de tamanho. Essa expectativa levou a equipe de Time Series Insights a selecionar 20 MB como o tamanho para otimizar o desempenho da consulta. Esse tamanho pode mudar ao longo do tempo, dependendo do tamanho do arquivo e da velocidade da entrada de dados.

> [!NOTE]
> * Os BLOBs são dimensionados a 20 MB.
> * Os BLOBs do Azure são ocasionalmente reparticionados para melhorar o desempenho, sendo retirados e recriados.
> * Além disso, os mesmos dados de Time Series Insights podem estar presentes em dois ou mais BLOBs.

### <a name="logical-partitions"></a>Partições lógicas

Uma partição lógica é uma partição em uma partição física que armazena todos os dados associados a um único valor de chave de partição. O Time Series Insights visualização logicamente particiona cada blob com base em duas propriedades:

* **ID da série temporal**: A chave de partição para todos os dados de Time Series Insights dentro do fluxo de eventos e o modelo.
* **Carimbo de data/hora**: A hora com base na entrada inicial.

A visualização de Time Series Insights fornece consultas de alto desempenho baseadas nessas duas propriedades. Essas duas propriedades também fornecem o método mais eficaz para fornecer dados Time Series Insights rapidamente.

É importante selecionar uma ID de série temporal apropriada, porque é uma propriedade imutável. Para obter mais informações, consulte [escolher IDs de série temporal](./time-series-insights-update-how-to-id.md).

## <a name="next-steps"></a>Passos seguintes

- Ler o [armazenamento e a entrada do Azure Time Series insights Preview](./time-series-insights-update-storage-ingress.md).

- Leia sobre a nova [modelagem de dados](./time-series-insights-update-tsm.md).
