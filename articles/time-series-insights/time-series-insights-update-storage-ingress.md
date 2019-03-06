---
title: Armazenamento de dados e de entrada na pré-visualização do Azure Time Series Insights | Documentos da Microsoft
description: Noções básicas sobre o armazenamento de dados e de entrada na pré-visualização do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: eba4c70a25cba2e456ed418a98b938f2029c4c1f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438368"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Armazenamento de dados e de entrada na pré-visualização do Azure Time Series Insights

Este artigo descreve as alterações para o armazenamento de dados e de entrada de pré-visualização do Azure Time Series Insights. Ele aborda a estrutura de armazenamento subjacente, o formato de ficheiro e a propriedade de ID de série de tempo. Ele também discute o processo de entrada subjacente, débito e as limitações.

## <a name="data-storage"></a>Armazenamento de dados

Quando cria um ambiente de SKU de pay as you go de pré-visualização do Time Series Insights, que está a criar dois recursos:

* Um ambiente do Time Series Insights.
* Uma armazenamento do Azure para fins gerais V1 conta onde serão armazenados os dados.

Pré-visualização do Time Series Insights utiliza o armazenamento de Blobs do Azure com o tipo de ficheiro Parquet. O Time Series Insights gerencia todas as operações de dados incluindo a criação de blobs, indexação e criação de partições de dados na conta de armazenamento do Azure. Criar nestes blobs, utilizando uma conta de armazenamento do Azure.

Como outros blobs de armazenamento do Azure, o Time Series Insights-criado blobs permitem-lhe ler e escrever nas mesmas para oferecer suporte a vários cenários de integração.

> [!TIP]
> Desempenho do Time Series Insights pode ser afetado negativamente se ler ou escrever os blobs com demasiada frequência.

Para uma descrição geral do armazenamento de Blobs do Azure, consulte [introdução de blobs de armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

Para mais informações sobre o tipo de ficheiro Parquet, consulte [tipos de ficheiro suportados no armazenamento do Azure](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#Parquet-format).

## <a name="parquet-file-format"></a>Formato de ficheiro parquet

Parquet é orientado por colunas, dados formato de ficheiro que foi projetado para:

* Interoperabilidade
* Eficiência de espaço
* Eficiência de consulta

O Time Series Insights escolheu Parquet, porque fornece a compactação de dados eficiente e a codificação de esquemas, com desempenho que pode manipular dados complexos em massa.

Para uma melhor compreensão sobre o formato de ficheiro Parquet, consulte [Parquet documentação](https://parquet.apache.org/documentation/latest/).

## <a name="event-structure-in-parquet"></a>Estrutura de eventos no Parquet

O Time Series Insights cria e armazena cópias de blobs nos dois formatos seguintes:

1. Hora da chegada particionada a cópia em primeiro lugar, inicial:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Hora de criação de BLOBs para os blobs particionados por tempo de chegada.

1. A cópia de segunda, repartitioned particionada um agrupamento dinâmico do ID de série de tempo:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Timestamp de mínima do evento num blob para blobs particionados por ID de série de tempo.

> [!NOTE]
> * `<YYYY>` é mapeado para uma representação do ano de 4 dígitos.
> * `<MM>` é mapeado para uma representação do mês de 2 dígitos.
> * `<YYYYMMDDHHMMSSfff>` mapeia para uma representação de timestamp com o ano de 4 dígitos (`YYYY`), 2 dígitos mês (`MM`), dígitos de 2 dias (`DD`), hora de 2 dígitos (`HH`), dígitos de 2 minutos (`MM`), segundo de 2 dígitos (`SS`) e 3 dígitos milissegundo (`fff`).

Eventos do Time Series Insights são mapeados para o conteúdo do ficheiro Parquet, da seguinte forma:

* Cada evento é mapeado para uma única linha.
* Incorporado **Timestamp** coluna com um timestamp de evento. A propriedade Timestamp nunca é nula. Assume como predefinição **hora de colocados em fila de origem do evento** se a propriedade Timestamp não for especificada de eventos de origem. É o carimbo de hora em UTC. 
* Todas as outras propriedades que são mapeadas para colunas de terminar com `_string` (cadeia), `_bool` (booleano), `_datetime` (datetime), e `_double` (double), dependendo do tipo de propriedade.
* Que é o esquema de mapeamento para a primeira versão do formato de arquivo, que denominamos **V = 1**. À medida que esta funcionalidade evolui, o nome será incrementado para **V = 2**, **V = 3**e assim por diante.

## <a name="partitions"></a>Partições

Cada ambiente de pré-visualização do Time Series Insights tem de ter uma propriedade de ID de série de tempo e uma propriedade Timestamp que identificam exclusivamente. O ID de série de tempo age como uma partição lógica para os seus dados e fornece o ambiente de pré-visualização do Time Series Insights um limite de natural para a distribuição de dados pelas partições físicas. Gestão de partição física é gerido pelo pré-visualização do Time Series Insights numa conta de armazenamento do Azure.

O Time Series Insights utiliza o particionamento dinâmico para otimizar o desempenho de armazenamento e a consulta ao remover e voltar a criar partições. A pré-visualização de informações de série de tempo algoritmo de particionamento dinâmico tenta para impedir que uma única partição física ter dados para vários partições distintas, lógicas. Em outras palavras, o algoritmo de criação de partições mantém todos os dados específicos a um ID de série de tempo única, exclusivamente presente nos arquivos de Parquet sem a ser intercalado com outros IDs de série de tempo. O algoritmo de particionamento dinâmico também tenta preservar a ordem original dos eventos dentro de um único tempo série ID.

Inicialmente, ao tempo de entrada, dados são particionados pelo carimbo de hora para que uma única partição lógica dentro de um intervalo de tempo especificado possam ser distribuída em várias partições físicas. Uma única partição física também pode conter muitos ou todos os lógicas partições. Devido às limitações de tamanho do blob, mesmo com a criação de partições ideal, uma única partição lógica pode ocupar várias partições físicas.

> [!NOTE]
> Por predefinição, o valor de Timestamp é a mensagem *colocados em fila tempo* na origem de evento configurado. 

Se estiver a carregar os dados históricos ou mensagens de lote, atribua o valor que pretende armazenar com os seus dados para a propriedade Timestamp, que mapeia para o período de tempo adequado. A propriedade Timestamp diferencia maiúsculas de minúsculas. Para obter mais informações, consulte [modelo de série de tempo](./time-series-insights-update-tsm.md).

## <a name="physical-partitions"></a>Partições físicas

Uma partição física é um blob de blocos que é armazenado na conta de armazenamento. O tamanho real dos blobs pode variar porque o tamanho depende da taxa de push. No entanto, podemos esperar blobs para ser cerca de 20 MB para a 50 MB de tamanho. Essa expectativa levou a equipe do Time Series Insights para selecionar 20 MB como o tamanho para otimizar o desempenho de consulta. Este tamanho pode alterar ao longo do tempo, dependendo do tamanho do ficheiro e a velocidade da entrada de dados.

> [!NOTE]
> * BLOBs são redimensionados em 20 MB.
> * Blobs do Azure são ocasionalmente dividida novamente em partições para melhorar o desempenho ao que está a ser removido e recriado.
> * Além disso, os mesmos dados de Time Series Insights podem estar presentes em duas ou mais blobs.

## <a name="logical-partitions"></a>Partições lógicas

Uma partição lógica é uma partição dentro de uma partição física que armazena todos os dados associados a um valor de chave de partição única. Pré-visualização do Time Series Insights partições, logicamente, cada blob com base em duas propriedades:

* **ID de série de tempo**: A chave de partição para todos os dados de Time Series Insights dentro do fluxo de eventos e o modelo.
* **Timestamp**: A hora com base na entrada de inicial.

Pré-visualização do Time Series Insights fornece consultas de elevado desempenho que se baseiam nessas duas propriedades. Essas duas propriedades também fornecem o método mais eficaz para fornecer dados de Time Series Insights rapidamente.

É importante selecionar um ID de série de tempo adequado, porque se trata de uma propriedade imutável. Para obter mais informações, consulte [escolha IDs de série de tempo](./time-series-insights-update-how-to-id.md).

## <a name="your-azure-storage-account"></a>Sua conta de armazenamento do Azure

### <a name="storage"></a>Armazenamento

Quando cria um ambiente de pay as you go do Time Series Insights, crie dois recursos: um ambiente de Time Series Insights e uma conta de fins gerais de armazenamento do Azure da V1 onde serão armazenados os dados. Podemos optar por criar o recurso de padrão de armazenamento do Azure para fins gerais V1 devido à sua interoperabilidade, preço e desempenho. 

O Time Series Insights publica até duas cópias de cada evento na sua conta de armazenamento do Azure. A cópia inicial sempre é preservada para que rapidamente pode consultá-lo ao utilizar outros serviços. Que pode facilmente utilizar Spark, Hadoop e outras ferramentas familiares em IDs de série de tempo com ficheiros Parquet não processados, porque estes mecanismos suportam a filtragem básica de nome de ficheiro. Blobs de agrupamento por ano e mês é uma forma útil de listar os blobs dentro de um intervalo de tempo específico para uma tarefa personalizada. 

Além disso, o Time Series Insights reparticiona os ficheiros Parquet para otimizar as APIs de informações de série de tempo. O ficheiro mais recentemente repartitioned também é guardado.

Durante a pré-visualização pública, os dados são armazenados indefinidamente na sua conta de armazenamento do Azure.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Escrever e edição de blobs do Time Series Insights

Para garantir o desempenho de consulta e a disponibilidade de dados, não edite ou elimine quaisquer blobs criados por Time Series Insights.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Aceder e exportar dados de pré-visualização do Time Series Insights

Pode querer aceder a dados armazenados no Explorador do Time Series Insights pré-visualização para utilizar em conjunto com outros serviços. Por exemplo, pode querer utilizar os seus dados ao relatório no Power BI, para fazer o machine learning com o Azure Machine Learning Studio ou para usar num aplicativo de bloco de notas com blocos de notas do Jupyter.

Pode aceder aos dados de três formas gerais:

* No Explorador do Time Series Insights pré-visualização.
* Das APIs de pré-visualização de informações de série de tempo.
* Diretamente a partir de uma conta de armazenamento do Azure.

#### <a name="from-the-time-series-insights-preview-explorer"></a>No Explorador do Time Series Insights pré-visualização

Pode exportar dados como um ficheiro CSV a partir do Explorador de pré-visualização do Time Series Insights. Para obter mais informações, consulte [Explorador de pré-visualização do Time Series Insights](./time-series-insights-update-explorer.md).

#### <a name="from-the-time-series-insights-preview-apis"></a>Das APIs de pré-visualização de informações de série de tempo

O ponto final de API pode ser contatado pelo `/getRecorded`. Para saber mais sobre esta API, veja [consultas de séries de tempo](./time-series-insights-update-tsq.md).

#### <a name="from-an-azure-storage-account"></a>De uma conta de armazenamento do Azure

* Precisa de acesso de leitura para qualquer conta estiver a utilizar para aceder aos seus dados do Time Series Insights. Para obter mais informações, consulte [gerir o acesso aos recursos da conta de armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).

* Para obter mais informações sobre formas diretas de ler dados a partir do armazenamento de Blobs do Azure, consulte [mover dados de e para sua conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

* Para exportar dados a partir de uma conta de armazenamento do Azure:

    * Primeiro, certifique-se de que sua conta cumpre os requisitos necessários para exportação de dados. Para obter mais informações, consulte [armazenamento importar e exportar requisitos](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).

    * Para saber mais sobre outras formas de exportar os dados da sua conta de armazenamento do Azure, veja [importação e exportação de dados de blobs](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs).

### <a name="data-deletion"></a>Eliminação de dados

Não elimine os blobs, como pré-visualização do Time Series Insights mantém metadados sobre os blobs dentro da mesma.

## <a name="ingress"></a>Entrada

### <a name="time-series-insights-ingress-policies"></a>Políticas de entrada do Time Series Insights

Pré-visualização do Time Series Insights suporta as mesmas origens de eventos e tipos de ficheiro que suporta atualmente o Time Series Insights.

Origens de eventos suportados incluem:

- Azure IoT Hub
- Azure Event Hubs
  
  > [!NOTE]
  > Instâncias de Hub de eventos do Azure suportam Kafka.

Tipos de ficheiro suportados incluem:

* JSON: Para obter mais informações sobre as formas JSON suportadas, podemos manipular, consulte [como a forma JSON](./time-series-insights-send-events.md#json).

### <a name="data-availability"></a>Disponibilidade de dados

Pré-visualização do Time Series Insights indexa dados através de uma estratégia de otimização de tamanho do blob. Dados ficam disponíveis para consulta depois é indexada, o que é baseada na quantidade de dados está a chegar em e com que velocidade.

> [!IMPORTANT]
> * A versão de disponibilidade geral (GA) do Time Series Insights vai disponibilizar dados em 60 segundos de atingir uma origem de evento. 
> * Durante a pré-visualização, espere um período mais longo antes dos dados ficam disponíveis. 
> * Se tiver qualquer uma latência significativa, certifique-se de que contacte-nos.

### <a name="scale"></a>Escala

Pré-visualização do Time Series Insights suporta um dimensionamento de entrada inicial de até 6 de megabytes de Bytes por segundo (Mbps) por ambiente. Suporte avançado de dimensionamento está em curso. Estamos a planear atualizar a nossa documentação para refletir esses avanços

## <a name="next-steps"></a>Passos Seguintes

Leitura a [armazenamento e de entrada de pré-visualização do Azure Time Series Insights](./time-series-insights-update-storage-ingress.md).

Saiba mais sobre a nova [modelação de dados](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png
