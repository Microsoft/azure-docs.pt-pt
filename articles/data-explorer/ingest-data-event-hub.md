---
title: Ingerir dados do Event Hub no Azure Data Explorer
description: Neste artigo, aprende-se a ingerir (carregar) dados no Azure Data Explorer a partir do Event Hub.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: bce92eeed669628fa1b6318abd6b0c13f7e84848
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411208"
---
# <a name="ingest-data-from-event-hub-into-azure-data-explorer"></a>Ingerir dados do Event Hub no Azure Data Explorer

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C #](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Modelo Azure Resource Manager](data-connection-event-hub-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Azure Data Explorer fornece ingestão (carregamento de dados) a partir dos Hubs de Eventos, uma plataforma de transmissão de macrodados e um serviço de ingestão de eventos. Os Centros de Eventos podem processar milhões de [eventos](/azure/event-hubs/event-hubs-about) por segundo em quase tempo real. Neste artigo, cria-se um hub de eventos, conecta-se a ele a partir do Azure Data Explorer e vê-se o fluxo de dados através do sistema.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster de teste e base de dados.](create-cluster-database-portal.md)
* [Uma aplicação de amostra](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) que gera dados e envia para um centro de eventos. Descarregue a aplicação de amostra para o seu sistema.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) para executar a aplicação de amostras.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-an-event-hub"></a>Criar um hub de eventos

Neste artigo, gera dados de amostra e envia-os para um centro de eventos. O primeiro passo é criar um hub de eventos. Pode fazê-lo através de um modelo do Azure Resource Manager no portal do Azure.

1. Para criar um centro de eventos, utilize o seguinte botão para iniciar a implementação. Clique à direita e selecione **Abra em nova janela,** para que possa seguir o resto dos passos neste artigo.

    [![Implementar no Azure](media/ingest-data-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    O botão **Implementar no Azure** leva-o para o portal do Azure para preencher um formulário de implementação.

    ![Implementar no Azure](media/ingest-data-event-hub/deploy-to-azure.png)

1. Selecione a subscrição em que quer criar o hub de eventos e crie um grupo de recursos chamado *test-hub-rg*.

    ![Criar um grupo de recursos](media/ingest-data-event-hub/create-resource-group.png)

1. Preencha o formulário com as seguintes informações.

    ![Formulário de implementação](media/ingest-data-event-hub/deployment-form.png)

    Utilize as predefinições para todas as definições não listadas na tabela seguinte.

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Subscrição | A sua subscrição | Selecione a subscrição do Azure que quer utilizar para o hub de eventos.|
    | Grupo de recursos | *test-hub-rg* | Crie um novo grupo de recursos. |
    | Localização | *E.U.A. Oeste* | Selecione *West US* para este artigo. Para um sistema de produção, selecione a região que melhor se adequa às suas necessidades. Crie o espaço de nome do centro de eventos no mesmo local que o cluster Kusto para melhor desempenho (o mais importante para espaços de nome solo de eventocom alta produção).
    | Nome do espaço de nomes | Um nome de espaço de nomes exclusivo | Escolha um nome exclusivo que identifique o seu espaço de nomes. Por exemplo, *mytestnamespace*. O nome de domínio *servicebus.windows.net* é anexado ao nome que indicar. O nome só pode conter letras, números e hífenes. O nome tem de começar com uma letra e terminar com uma letra ou número. O valor deve ter entre 6 e 50 carateres.
    | Nome do hub de eventos | *test-hub* | O hub de eventos encontra-se no espaço de nomes, que fornece um contentor de âmbito exclusivo. O nome do hub de eventos tem de ser exclusivo no espaço de nomes. |
    | Nome do grupo de consumidores | *test-group* | Os grupos de consumidores permitem que cada aplicação de consumo tenha uma vista separada do fluxo de eventos. |
    | | |

1. Selecione **Comprar**, que reconhece que está a criar recursos na sua subscrição.

1. Selecione **Notificações** na barra de ferramentas para monitorizar o processo de aprovisionamento. Poderá demorar alguns minutos até que a implementação seja bem-sucedida, mas pode avançar para o passo seguinte agora.

    ![Notificações](media/ingest-data-event-hub/notifications.png)

## <a name="create-a-target-table-in-azure-data-explorer"></a>Criar uma tabela de destino no Azure Data Explorer

Agora, vai criar uma tabela no Azure Data Explorer, para onde os Hubs de Eventos vão enviar os dados. Cria a tabela no cluster e na base de dados aprovisionada em **Pré-requisitos**.

1. No portal do Azure, navegue para o cluster e, em seguida, selecione **Consulta**.

    ![Ligação da aplicação de consulta](media/ingest-data-event-hub/query-explorer-link.png)

1. Copie o seguinte comando na janela e selecione **Executar** para criar a tabela (TestTable) que receberá os dados ingeridos.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Executar criação da consulta](media/ingest-data-event-hub/run-create-query.png)

1. Copie o seguinte comando na janela e selecione **Executar** para mapear os dados jSON de entrada para os nomes das colunas e tipos de dados da tabela (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp", "Properties": {"Path": "$.timeStamp"}},{"column":"Name", "Properties": {"Path":"$.name"}} ,{"column":"Metric", "Properties": {"Path":"$.metric"}}, {"column":"Source", "Properties": {"Path":"$.source"}}]'
    ```

## <a name="connect-to-the-event-hub"></a>Ligar ao hub de eventos

Agora ligue ao hub de eventos do Azure Data Explorer. Quando esta ligação está no lugar, os dados que fluem para o hub de eventos são transmitidos para a tabela de teste que criou anteriormente neste artigo.

1. Selecione **Notificações** na barra de ferramentas para verificar se a implementação do hub de eventos foi concluída com êxito.

1. No cluster que criou, selecione **Bases de dados** e, em seguida, **TestDatabase**.

    ![Selecionar a base de dados de teste](media/ingest-data-event-hub/select-test-database.png)

1. **Selecione a ingestão** de dados e **adicione a ligação de dados**. Em seguida, preencha o formulário com as seguintes informações. Selecione **Criar** quando terminar.

    ![Ligação ao hub de eventos](media/ingest-data-event-hub/event-hub-connection.png)

    **Fonte de Dados:**

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome da ligação de dados | *test-hub-connection* | O nome da ligação que quer criar no Azure Data Explorer.|
    | Espaço de nomes do hub de eventos | Um nome de espaço de nomes exclusivo | O nome que escolheu anteriormente que identifica o seu espaço de nomes. |
    | Hub de eventos | *test-hub* | O hub de eventos que criou. |
    | Grupo de consumidores | *test-group* | O grupo de consumidores definido no hub de eventos que criou. |
    | Propriedades do sistema de eventos | Selecione propriedades relevantes | As propriedades do [sistema Event Hub.](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations) Se houver vários registos por mensagem de evento, as propriedades do sistema serão adicionadas à primeira. Ao adicionar propriedades do sistema, [crie](/azure/kusto/management/create-table-command) ou [atualize](/azure/kusto/management/alter-table-command) o esquema de tabela e [mapeie](/azure/kusto/management/mappings) para incluir as propriedades selecionadas. |
    | Compressão | *Nenhum* | O tipo de compressão das mensagens Do Event Hub. Tipos de compressão suportados: *Nenhum, GZip*.|
    | | |

    **Tabela-alvo:**

    Existem duas opções para encaminhamento dos dados ingeridos: *estática* e *dinâmica.* 
    Para este artigo, utiliza o encaminhamento estático, onde especifica o nome da tabela, o formato de dados e o mapeamento. Portanto, deixe **os meus dados incluem informações de encaminhamento** não selecionadas.

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Tabela | *TestTable* | A tabela que criou em **TestDatabase**. |
    | Formato de dados | *JSON* | Os formatos suportados são Avro, CSV, JSON, MULTILINE JSON, PSV, SOHSV, SCSV, TSV, TSVE, TXT, ORC e PARQUET. |
    | Mapeamento de colunas | *TestMapping* | O [mapeamento](/azure/kusto/management/mappings) que criou no **TestDatabase,** que mapeia os dados jSON que chegam aos nomes das colunas e tipos de dados do **TestTable**. Necessário para JSON ou MultiLINE JSON, e opcional para outros formatos.|
    | | |

    > [!NOTE]
    > * Selecione **Os meus dados incluem informações de encaminhamento** dinâmico para utilizar o encaminhamento dinâmico, onde os seus dados incluem as informações de encaminhamento necessárias, como se pode ver nos comentários da aplicação da [amostra.](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) Se as propriedades estáticas e dinâmicas forem definidas, as propriedades dinâmicas sobrepõem-se às estáticas. 
    > * Só os eventos gravados depois de criar a ligação de dados são ingeridos.
    > * Também pode definir o tipo de compressão através de propriedades dinâmicas, como visto na [aplicação de amostras.](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)
    > * Os formatos Avro, ORC e PARQUET, bem como as propriedades do sistema de eventos, não são suportados na carga útil da compressão GZip.

[!INCLUDE [data-explorer-container-system-properties](../../includes/data-explorer-container-system-properties.md)]

## <a name="copy-the-connection-string"></a>Copiar a cadeia de ligação

Quando executa a [aplicação de exemplo](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) listada nos Pré-requisitos, precisa da cadeia de ligação para o espaço de nomes do hub de eventos.

1. No espaço de nomes do hub de eventos que criou, selecione **Políticas de acesso partilhado** e, em seguida, **RootManageSharedAccessKey**.

    ![Políticas de acesso partilhado](media/ingest-data-event-hub/shared-access-policies.png)

1. Fio de **ligação**de cópia - chave primária . Cole na próxima secção.

    ![Cadeia de ligação](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Gerar dados de exemplo

Utilize a [aplicação de amostra](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) que descarregou para gerar dados.

1. Abra a solução de aplicação de exemplo no Visual Studio.

1. No ficheiro *program.cs*, atualize a constante `connectionString` para a cadeia de ligação que copiou do espaço de nomes do hub de eventos.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Compile e execute a aplicação. A aplicação envia mensagens para o hub de eventos e apresenta o estado a cada dez segundos.

1. Depois de a aplicação enviar algumas mensagens, avance para o próximo passo: rever o fluxo de dados no hub de eventos e testar a tabela.

## <a name="review-the-data-flow"></a>Rever o fluxo de dados

Com a aplicação de geração de dados, agora pode ver o fluxo dos dados do hub de eventos para a tabela no seu cluster.

1. No portal do Azure, no seu hub de eventos, verá o pico de atividade enquanto a aplicação está em execução.

    ![Gráfico do hub de eventos](media/ingest-data-event-hub/event-hub-graph.png)

1. Para verificar quantas mensagens chegaram à base de dados até ao momento, execute a consulta seguintes na base de dados de teste.

    ```Kusto
    TestTable
    | count
    ```

1. Para ver o conteúdo das mensagens, execute a seguinte consulta:

    ```Kusto
    TestTable
    ```

    O conjunto de resultados deve parecer o seguinte:

    ![Conjunto de resultados das mensagens](media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    > * O Azure Data Explorer tem uma política de agregação (loting) para a ingestão de dados, concebida para otimizar o processo de ingestão. A política está configurada a 5 minutos ou 500 MB de dados, por padrão, para que possa experimentar uma latência. Consulte [a política de lotação](/azure/kusto/concepts/batchingpolicy) para opções de agregação. 
    > * A ingestão do Hub de Eventos inclui o tempo de resposta do Event Hub de 10 segundos ou 1 MB. 
    > * Configure a sua tabela para suportar o streaming e remova o atraso no tempo de resposta. Consulte [a política de streaming.](/azure/kusto/concepts/streamingingestionpolicy) 

## <a name="clean-up-resources"></a>Limpar recursos

Se não planear voltar a utilizar o hub de eventos, limpe **test-hub-rg**, para evitar incorrer em custos.

1. No portal do Azure, selecione **Grupos de recursos** à esquerda e, em seguida, selecione o grupo de recursos que criou.  

    Se o menu à esquerda estiver fechado, selecione ![botão Expandir](media/ingest-data-event-hub/expand.png) para expandi-lo.

   ![Selecionar grupo de recursos a eliminar](media/ingest-data-event-hub/delete-resources-select.png)

1. Em **test-resource-group**, selecione **Eliminar grupo de recursos**.

1. Na nova janela, escreva o nome do grupo de recursos a eliminar (*test-hub-rg*) e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

* [Dados de consulta no Explorador de Dados do Azure](web-query-data.md)
