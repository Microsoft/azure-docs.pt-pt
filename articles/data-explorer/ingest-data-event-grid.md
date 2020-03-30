---
title: Ingest Azure Blobs no Azure Data Explorer
description: Neste artigo, aprende a enviar dados da conta de armazenamento para o Azure Data Explorer utilizando uma subscrição da Rede de Eventos.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ec218b1638183db463ff09488c988cad64d78c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370445"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Ingest blobs no Azure Data Explorer ao subscrever notificações da Rede de Eventos

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Pitão](data-connection-event-grid-python.md)
> * [Modelo Azure Resource Manager](data-connection-event-grid-resource-manager.md)

O Azure Data Explorer é um serviço de exploração rápida e escalável de dados para dados de registo e telemetria. Oferece ingestão contínua (carregamento de dados) de bolhas escritas a recipientes de bolhas. 

Neste artigo, você aprende como definir uma subscrição [azure Event Grid,](/azure/event-grid/overview) e route events to Azure Data Explorer através de um hub de eventos. Para começar, deverá ter uma conta de armazenamento com uma subscrição de rede de eventos que envia notificações para o Azure Event Hubs. Em seguida, criará uma ligação de dados da Rede de Eventos e verá o fluxo de dados em todo o sistema.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Crie uma [conta Azure gratuita.](https://azure.microsoft.com/free/)
* [Um cluster e base de dados.](create-cluster-database-portal.md)
* [Uma conta de armazenamento.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)
* [Um centro de eventos.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Crie uma subscrição da Grelha de Eventos na sua conta de armazenamento

1. No portal Azure, encontre a sua conta de armazenamento.
1.  > **Selecione Subscrição de** **Eventos**.

    ![Ligação da aplicação de consulta](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. Na janela **de subscrição** do Evento Criar dentro do separador **Basic,** forneça os seguintes valores:

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome | *ligação à grelha de ensaio* | O nome da grelha de eventos que quer criar.|
    | Esquema de Eventos | *Esquema do Event Grid* | O esquema que deve ser usado para a grelha do evento. |
    | Tipo de Tópico | *Conta de armazenamento* | O tipo de tópico da grelha de eventos. |
    | Recurso tópico | *gridteststorage* | O nome da sua conta de armazenamento. |
    | Subscreva todos os tipos de eventos | *claro* | Não seja notificado de todos os eventos. |
    | Tipos de eventos definidos | *Criado pelo Blob* | Que eventos específicos para ser notificado. |
    | Tipo endpoint | *Hubs de eventos* | O tipo de ponto final para o qual envia os eventos. |
    | Ponto Final | *test-hub* | O hub de eventos que criou. |
    | | |

1. Selecione o separador **Filtros** se pretender rastrear ficheiros a partir de um recipiente específico. Detete os filtros para as notificações da seguinte forma:
    * **Assunto Começa Com** o campo é o prefixo *literal* do recipiente de bolhas. À medida que o padrão aplicado *é iniciado,* pode abranger vários recipientes. Não são permitidos wildcards.
     Deve *must* ser definido da *`/blobServices/default/containers/`* seguinte forma: [prefixo do recipiente]
    * **O assunto termina com** o campo é o sufixo *literal* da bolha. Não são permitidos wildcards.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Criar uma tabela de destino no Azure Data Explorer

Crie uma tabela no Azure Data Explorer onde os Centros de Eventos enviarão dados. Crie a tabela no cluster e na base de dados preparada nos pré-requisitos.

1. No portal do Azure, no seu cluster, selecione **Consulta**.

    ![Ligação da aplicação de consulta](media/ingest-data-event-grid/query-explorer-link.png)

1. Copie o seguinte comando na janela e selecione **Executar** para criar a tabela (TestTable) que receberá os dados ingeridos.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Executar criação da consulta](media/ingest-data-event-grid/run-create-table.png)

1. Copie o seguinte comando na janela e selecione **Executar** para mapear os dados jSON de entrada para os nomes das colunas e tipos de dados da tabela (TestTable).

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Criar uma ligação de dados da Rede de Eventos no Explorador de Dados do Azure

Agora ligue-se à Grelha de Eventos do Azure Data Explorer, de modo a que os dados que fluem para o recipiente de bolha sejam transmitidos para a mesa de teste. 

1. Selecione **Notificações** na barra de ferramentas para verificar se a implementação do hub de eventos foi concluída com êxito.

1. Sob o cluster que criou, selecione **Databases** > **TestDatabase**.

    ![Selecionar a base de dados de teste](media/ingest-data-event-grid/select-test-database.png)

1.  >  **Selecione ingestão**de dados**Adicionar ligação de dados**.

    ![Ingestão de dados](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Selecione o tipo de ligação: **Depósito de Blob**.

1. Preencha o formulário com as seguintes informações e selecione **Criar**.

    ![Ligação ao hub de eventos](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Origem de dados:

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome da ligação de dados | *test-hub-connection* | O nome da ligação que pretende criar no Azure Data Explorer.|
    | Assinatura de conta de armazenamento | O seu ID de subscrição | O ID de subscrição onde reside a sua conta de armazenamento.|
    | Conta de armazenamento | *gridteststorage* | O nome da conta de armazenamento que criou anteriormente.|
    | Event Grid | *ligação à grelha de ensaio* | O nome da grelha de eventos que criaste. |
    | O nome do hub de eventos | *test-hub* | O centro de eventos que criou. Este campo é preenchido automaticamente quando escolhe uma grelha de eventos. |
    | Grupo de consumidores | *test-group* | O grupo de consumidores definiu no centro de eventos que criou. |
    | | |

    Tabela-alvo:

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Tabela | *TestTable* | A tabela que criou em **TestDatabase**. |
    | Formato de dados | *Rio JSON* | Os formatos suportados são Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV, RAW e TXT. Opções de compressão suportadas: Zip e GZip |
    | Mapeamento de colunas | *TestMapping* | O mapeamento que criou no **TestDatabase**, que mapeia os dados recebidos de JSON para os tipos de dados e os nomes de coluna de **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Gerar dados de exemplo

Agora que o Azure Data Explorer e a conta de armazenamento estão ligados, pode criar dados de amostra e carregá-lo para o armazenamento de blob.

Trabalharemos com um pequeno guião que emite alguns comandos básicos do Azure CLI para interagir com os recursos de Armazenamento Azure. Este script cria um novo recipiente na sua conta de armazenamento, envia um ficheiro existente (como uma bolha) para esse recipiente e, em seguida, lista as bolhas no recipiente. Pode utilizar a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) para executar o script diretamente no portal.

Guarde os dados num ficheiro e carregue-os com este script:

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> Para obter o melhor desempenho de ingestão, deve ser comunicada a dimensão *descomprimido* das bolhas comprimidos submetidas para ingestão. Dado que as notificações da Rede de Eventos contêm apenas detalhes básicos, as informações de tamanho devem ser explicitamente comunicadas. As informações de tamanho não comprimido `rawSizeBytes` podem ser fornecidas definindo a propriedade nos metadados blob com o tamanho de dados *não comprimido* em bytes.

### <a name="ingestion-properties"></a>Propriedades de ingestão

Pode especificar as propriedades de [Ingestion](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#ingestion-properties) da ingestão de bolhas através dos metadados blob.

Estas propriedades podem ser definidas:

|**Propriedade** | **Descrição do imóvel**|
|---|---|
| `rawSizeBytes` | Tamanho dos dados brutos (não comprimidos). Para a Avro/ORC/Parquet, este é o tamanho antes da aplicação da compressão específica do formato.|
| `kustoTable` |  Nome da tabela-alvo existente. Sobrepõe-se `Table` do `Data Connection` conjunto na lâmina. |
| `kustoDataFormat` |  Formato de dados. Sobrepõe-se `Data format` do `Data Connection` conjunto na lâmina. |
| `kustoIngestionMappingReference` |  Nome do mapeamento de ingestão existente a ser utilizado. Sobrepõe-se `Column mapping` do `Data Connection` conjunto na lâmina.|
| `kustoIgnoreFirstRecord` | Se estiver `true`programado, Kusto ignora a primeira linha da bolha. Utilize em dados de formato tabular (CSV, TSV ou similar) para ignorar os cabeçalhos. |
| `kustoExtentTags` | Etiquetas [representativas](/azure/kusto/management/extents-overview#extent-tagging) de cordas que serão anexadas à extensão resultante. |
| `kustoCreationTime` |  Substitui [$IngestionTime](/azure/kusto/query/ingestiontimefunction?pivots=azuredataexplorer) para a bolha, formatada como uma corda ISO 8601. Utilize para recheio. |

> [!NOTE]
> O Azure Data Explorer não apaga as bolhas pós ingestão.
> Mantenha as bolhas por cinco dias.
> Utilize o ciclo de vida de [armazenamento Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) para gerir a eliminação de blob. 

## <a name="review-the-data-flow"></a>Rever o fluxo de dados

> [!NOTE]
> O Azure Data Explorer tem uma política de agregação (loting) para a ingestão de dados concebida para otimizar o processo de ingestão.
Por predefinição, a apólice está configurada para 5 minutos.
Poderá alterar a apólice mais tarde, se necessário. Neste artigo pode esperar uma latência de alguns minutos.

1. No portal Azure, sob a sua grelha de eventos, você vê o pico de atividade enquanto a aplicação está em execução.

    ![Gráfico da grelha do evento](media/ingest-data-event-grid/event-grid-graph.png)

1. Para verificar quantas mensagens chegaram à base de dados até ao momento, execute a consulta seguintes na base de dados de teste.

    ```kusto
    TestTable
    | count
    ```

1. Para ver o conteúdo das mensagens, ecorra a seguinte consulta na sua base de dados de teste.

    ```kusto
    TestTable
    ```

    O conjunto de resultados deve ter um aspeto semelhante ao seguinte.

    ![Conjunto de resultados das mensagens](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender voltar a utilizar a sua grelha de eventos, limpe o **test-hub-rg,** para evitar custos incorridos.

1. No portal do Azure, selecione **Grupos de recursos** à esquerda e, em seguida, selecione o grupo de recursos que criou.  

    Se o menu à esquerda estiver fechado, selecione ![botão Expandir](media/ingest-data-event-grid/expand.png) para expandi-lo.

   ![Selecionar grupo de recursos a eliminar](media/ingest-data-event-grid/delete-resources-select.png)

1. Em **test-resource-group**, selecione **Eliminar grupo de recursos**.

1. Na nova janela, introduza o nome do grupo de recursos para eliminar *(test-hub-rg)* e, em seguida, **selecione Delete**.

## <a name="next-steps"></a>Passos seguintes

* [Dados de consulta no Explorador de Dados do Azure](web-query-data.md)
