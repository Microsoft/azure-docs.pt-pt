---
title: Ingerir BLOBs do Azure no Azure Data Explorer
description: Neste artigo, você aprenderá a enviar dados da conta de armazenamento para o Azure Data Explorer usando uma assinatura da grade de eventos.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: da701dc91781ef72c29e6454e79523073810dbe4
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667487"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Ingerir BLOBs no Azure Data Explorer assinando notificações de grade de eventos

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Modelo do Azure Resource Manager](data-connection-event-grid-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e escalonável para dados de log e telemetria. Ele oferece ingestão contínua (carregamento de dados) de Blobs gravados em contêineres de BLOB. 

Neste artigo, você aprenderá a definir uma assinatura da [grade de eventos do Azure](/azure/event-grid/overview) e a rotear eventos para o Azure data Explorer por meio de um hub de eventos. Para começar, você deve ter uma conta de armazenamento com uma assinatura de grade de eventos que envia notificações para os hubs de eventos do Azure. Em seguida, você criará uma conexão de dados de grade de eventos e verá o fluxo de dados em todo o sistema.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/).
* [Um cluster e um banco de dados](create-cluster-database-portal.md).
* [Uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Um hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Criar uma assinatura de grade de eventos em sua conta de armazenamento

1. Na portal do Azure, localize sua conta de armazenamento.
1. Selecione **eventos** > **assinatura de evento**.

    ![Ligação da aplicação de consulta](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. Na janela **criar assinatura de evento** dentro da guia **básico** , forneça os seguintes valores:

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome | *Test-Grid-Connection* | O nome da grade de eventos que você deseja criar.|
    | Esquema de evento | *Esquema de grade de eventos* | O esquema que deve ser usado para a grade de eventos. |
    | Tipo de tópico | *Conta de armazenamento* | O tipo de tópico da grade de eventos. |
    | Recurso de tópico | *gridteststorage* | O nome da sua conta de armazenamento. |
    | Assinar todos os tipos de evento | *formatação* | Não seja notificado sobre todos os eventos. |
    | Tipos de evento definidos | *Blob criado* | A quais eventos específicos serão notificados. |
    | Tipo de ponto de extremidade | *Hubs de eventos* | O tipo de ponto de extremidade para o qual você envia os eventos. |
    | Ponto Final | *test-hub* | O hub de eventos que criou. |
    | | |

1. Selecione a guia **filtros** se desejar rastrear arquivos de um contêiner específico. Defina os filtros para as notificações da seguinte maneira:
    * O **assunto começa com** o campo é o prefixo *literal* do contêiner de BLOB. Como o padrão aplicado é *StartsWith*, ele pode abranger vários contêineres. Nenhum caractere curinga é permitido.
     Ele *deve* ser definido da seguinte maneira: *`/blobServices/default/containers/`* [prefixo do contêiner]
    * A **entidade termina com** o campo é o sufixo *literal* do blob. Nenhum caractere curinga é permitido.

## <a name="create-a-target-table-in-azure-data-explorer"></a>Criar uma tabela de destino no Azure Data Explorer

Crie uma tabela no Azure Data Explorer em que os hubs de eventos enviarão dados. Crie a tabela no cluster e no banco de dados preparado nos pré-requisitos.

1. No portal do Azure, no seu cluster, selecione **Consulta**.

    ![Ligação da aplicação de consulta](media/ingest-data-event-grid/query-explorer-link.png)

1. Copie o seguinte comando na janela e selecione **executar** para criar a tabela (TestTable) que receberá os dados ingeridos.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Executar criação da consulta](media/ingest-data-event-grid/run-create-table.png)

1. Copie o seguinte comando na janela e selecione **executar** para mapear os dados JSON de entrada para os nomes de coluna e tipos de dados da tabela (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Criar uma conexão de dados de grade de eventos no Azure Data Explorer

Agora, conecte-se à grade de eventos do Data Explorer do Azure, para que os dados que fluem para o contêiner de blob sejam transmitidos para a tabela de teste. 

1. Selecione **Notificações** na barra de ferramentas para verificar se a implementação do hub de eventos foi concluída com êxito.

1. No cluster que você criou, selecione **bancos de dados** > **TestDatabase**.

    ![Selecionar a base de dados de teste](media/ingest-data-event-grid/select-test-database.png)

1. Selecione **ingestão de dados** > **Adicionar conexão de dados**.

    ![Ingestão de dados](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Selecione o tipo de conexão: **armazenamento de BLOBs**.

1. Preencha o formulário com as informações a seguir e selecione **criar**.

    ![Ligação ao hub de eventos](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Fonte de dados:

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome da ligação de dados | *test-hub-connection* | O nome da conexão que você deseja criar no Azure Data Explorer.|
    | Assinatura da conta de armazenamento | Sua ID de assinatura | A ID da assinatura na qual sua conta de armazenamento reside.|
    | Conta de armazenamento | *gridteststorage* | O nome da conta de armazenamento que você criou anteriormente.|
    | Event Grid | *Test-Grid-Connection* | O nome da grade de eventos que você criou. |
    | O nome do hub de eventos | *test-hub* | O Hub de eventos que você criou. Esse campo é preenchido automaticamente quando você escolhe uma grade de eventos. |
    | Grupo de consumidores | *test-group* | O grupo de consumidores definido no Hub de eventos que você criou. |
    | | |

    Tabela de destino:

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Tabelas | *TestTable* | A tabela que criou em **TestDatabase**. |
    | Formato de dados | *JSON* | Os formatos com suporte são Avro, CSV, JSON, JSON MULTILINHA, PSV, SOH, SCSV, TSV e TXT. Opções de compactação com suporte: zip e GZip |
    | Mapeamento de colunas | *TestMapping* | O mapeamento que criou no **TestDatabase**, que mapeia os dados recebidos de JSON para os tipos de dados e os nomes de coluna de **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Gerar dados de exemplo

Agora que o Azure Data Explorer e a conta de armazenamento estão conectados, você pode criar dados de exemplo e carregá-los no armazenamento de BLOBs.

Trabalharemos com um pequeno script de shell que emite alguns comandos básicos de CLI do Azure para interagir com os recursos de armazenamento do Azure. Esse script cria um novo contêiner em sua conta de armazenamento, carrega um arquivo existente (como um blob) para esse contêiner e, em seguida, lista os BLOBs no contêiner. Você pode usar [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) para executar o script diretamente no Portal.

Salve os dados em um arquivo e carregue-os com este script:

```Json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```bash
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
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

## <a name="review-the-data-flow"></a>Rever o fluxo de dados

> [!NOTE]
> O Azure Data Explorer tem uma política de agregação (em lote) para ingestão de dados projetada para otimizar o processo de ingestão.
Por padrão, a política é configurada para 5 minutos.
Você poderá alterar a política posteriormente, se necessário. Neste artigo, você pode esperar uma latência de alguns minutos.

1. Na portal do Azure, na sua grade de eventos, você verá o pico na atividade enquanto o aplicativo estiver em execução.

    ![Grafo de grade de eventos](media/ingest-data-event-grid/event-grid-graph.png)

1. Para verificar quantas mensagens chegaram à base de dados até ao momento, execute a consulta seguintes na base de dados de teste.

    ```Kusto
    TestTable
    | count
    ```

1. Para ver o conteúdo das mensagens, execute a consulta a seguir no banco de dados de teste.

    ```Kusto
    TestTable
    ```

    O conjunto de resultados deve ter um aspeto semelhante ao seguinte.

    ![Conjunto de resultados das mensagens](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você não planeja usar sua grade de eventos novamente, limpe **Test-Hub-RG**para evitar incorrer em custos.

1. No portal do Azure, selecione **Grupos de recursos** à esquerda e, em seguida, selecione o grupo de recursos que criou.  

    Se o menu à esquerda estiver fechado, selecione ![botão Expandir](media/ingest-data-event-grid/expand.png) para expandi-lo.

   ![Selecionar grupo de recursos a eliminar](media/ingest-data-event-grid/delete-resources-select.png)

1. Em **test-resource-group**, selecione **Eliminar grupo de recursos**.

1. Na nova janela, insira o nome do grupo de recursos a ser excluído (*Test-Hub-RG*) e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

* [Consultar dados no Azure Data Explorer](web-query-data.md)
