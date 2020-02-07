---
title: Ingerir dados do Hub IoT para o Azure Data Explorer
description: Neste artigo, você aprenderá a ingerir (carregar) dados no Azure Data Explorer do Hub IoT.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 93c0b3e27156655acee24d424cd890b48bb8e4e7
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064993"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>Ingerir dados do Hub IoT para o Azure Data Explorer 

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [python](data-connection-iot-hub-python.md)
> * [Modelo do Azure Resource Manager](data-connection-iot-hub-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Azure Data Explorer oferece ingestão (carregamento de dados) do Hub IoT, uma plataforma Big Data streaming e serviço de ingestão de IoT.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Criar um cluster de [teste e uma base de dados](create-cluster-database-portal.md) com *testdb*de nome de base de dados .
* [Uma aplicação de amostra](https://github.com/Azure-Samples/azure-iot-samples-csharp) e documentação para simular um dispositivo.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) para executar a aplicação de amostras.

## <a name="create-an-iot-hub"></a>Criar um hub IOT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Registrar um dispositivo no Hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Criar uma tabela de destino no Azure Data Explorer

Agora, você cria uma tabela no Azure Data Explorer à qual os hubs IoT enviarão dados. Cria a tabela no cluster e na base de dados aprovisionada em [**Pré-requisitos.** ](#prerequisites)

1. No portal Azure, navegue para o seu cluster e selecione **Consulta**.

    ![Consulta do ADX no portal](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Copie o seguinte comando na janela e selecione **Executar** para criar a tabela (TestTable) que receberá os dados ingeridos.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Executar criação da consulta](media/ingest-data-iot-hub/run-create-query.png)

1. Copie o seguinte comando na janela e selecione **Executar** para mapear os dados jSON de entrada para os nomes das colunas e tipos de dados da tabela (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Conectar a tabela de Data Explorer do Azure ao Hub IoT

Agora você se conecta ao Hub IoT do Azure Data Explorer. Quando esta ligação estiver completa, os dados que fluem para o centro iot streams para a [tabela-alvo que criou](#create-a-target-table-in-azure-data-explorer).

1. Selecione **Notificações** na barra de ferramentas para verificar se a implementação do IoT Hub foi bem sucedida.

1. Sob o cluster que criou, selecione Bases de **Dados** e, em seguida, selecione a base de dados que criou **testdb**.
    
    ![Selecionar a base de dados de teste](media/ingest-data-iot-hub/select-database.png)

1. **Selecione a ingestão** de dados e **adicione a ligação de dados**. Em seguida, preencha o formulário com as informações a seguir. Selecione **Criar** quando terminar.

    ![Conexão do Hub IoT](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Fonte de Dados**:

    **Definição** | **Descrição do campo**
    |---|---|
    | Nome da ligação de dados | O nome da conexão que você deseja criar no Azure Data Explorer
    | IoT Hub | Nome do Hub IoT |
    | Política de acesso compartilhado | O nome da política de acesso compartilhado. Deve ter permissões de leitura |
    | Grupo de consumidores |  O grupo de consumidores definido no ponto de extremidade interno do Hub IoT |
    | Propriedades do sistema de eventos | As propriedades do sistema de [eventos IoT Hub.](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages) Ao adicionar propriedades do sistema, [crie](/azure/kusto/management/tables#create-table) ou [atualize](/azure/kusto/management/tables#alter-table-and-alter-merge-table) o esquema de tabela e [mapeie](/azure/kusto/management/mappings) para incluir as propriedades selecionadas. | | | 

    > [!NOTE]
    > Em caso de [falha manual,](/azure/iot-hub/iot-hub-ha-dr#manual-failover)deve recriar a ligação de dados.

    **Tabela-alvo:**

    Existem duas opções para encaminhamento dos dados ingeridos: *estática* e *dinâmica.* 
    Para este artigo, use o roteamento estático, no qual você especifica o nome da tabela, o formato de dados e o mapeamento. Portanto, deixe **os meus dados incluem informações de encaminhamento** não selecionadas.

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Tabela | *TestTable* | A mesa que criou no **testdb.** |
    | Formato de dados | *JSON* | Os formatos com suporte são Avro, CSV, JSON, JSON MULTILINHA, PSV, SOHSV, SCSV, TSV, TSVE e TXT. |
    | Mapeamento de colunas | *TestMapping* | O [mapeamento](/azure/kusto/management/mappings) que criou no **testdb,** que mapeia os dados jSON que chegam aos nomes das colunas e tipos de dados de **testdb**. Necessário para JSON, JSON MULTILINHA e AVRO, e opcional para outros formatos.|
    | | |

    > [!NOTE]
    > * Selecione **Os meus dados incluem informações de encaminhamento** dinâmico para utilizar o encaminhamento dinâmico, onde os seus dados incluem as informações de encaminhamento necessárias, como se pode ver nos comentários da aplicação da [amostra.](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) Se ambas as propriedades estática e dinâmica forem definidas, as propriedades dinâmicas substituirão as estáticas. 
    > * Somente os eventos enfileirados após a criação da conexão de dados são ingeridos.

[!INCLUDE [data-explorer-container-system-properties](../../includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>Gerar dados de exemplo para teste

A aplicação de dispositivo simulado liga-se a um ponto final específico do dispositivo no seu hub IoT e envia telemetria simulada de humidade e de temperatura.

1. Transfira o projeto C# de exemplo de https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip e extraia o arquivo ZIP.

1. Numa janela de terminal local, navegue para a pasta raiz do projeto C# de exemplo. Em seguida, navegue para a pasta **iot-hub\Quickstarts\simulated-device**.

1. Abra o ficheiro **SimulatedDevice.cs** num editor de texto à sua escolha.

    Substitua o valor da variável `s_connectionString` com a cadeia de ligação do dispositivo do [Registo de um dispositivo ao Hub IoT](#register-a-device-to-the-iot-hub). Em seguida, guarde as alterações feitas ao ficheiro **SimulatedDevice.cs**.

1. Na janela de terminal local, execute os seguintes comandos para instalar os pacotes exigidos para a aplicação de dispositivo simulado:

    ```cmd/sh
    dotnet restore
    ```

1. Na janela de terminal local, execute o seguinte comando para compilar e executar a aplicação de dispositivo simulado:

    ```cmd/sh
    dotnet run
    ```

    A seguinte captura de ecrã mostra a saída à medida que a aplicação de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>Rever o fluxo de dados

Com o aplicativo que gera dados, agora você pode ver o fluxo de dados do Hub IoT para a tabela no cluster.

1. Na portal do Azure, em seu hub IoT, você verá o pico na atividade enquanto o aplicativo estiver em execução.

    ![Métricas do Hub IoT](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. Para verificar quantas mensagens chegaram à base de dados até ao momento, execute a consulta seguintes na base de dados de teste.

    ```Kusto
    TestTable
    | count
    ```

1. Para ver o conteúdo das mensagens, execute a seguinte consulta:

    ```Kusto
    TestTable
    ```

    O conjunto de resultados:
    
    ![Mostrar resultados de dados ingeridos](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * O Azure Data Explorer tem uma política de agregação (em lote) para ingestão de dados, projetada para otimizar o processo de ingestão. A política é configurada para 5 minutos ou 500 MB de dados, por padrão, para que você possa experimentar uma latência. Consulte [a política de lotação](/azure/kusto/concepts/batchingpolicy) para opções de agregação. 
    > * Configure sua tabela para dar suporte ao streaming e remova a latência no tempo de resposta. Consulte [a política de streaming.](/azure/kusto/concepts/streamingingestionpolicy) 

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender voltar a utilizar o seu Hub IoT, limpe o **test-hub-rg,** para evitar custos incorridos.

1. No portal do Azure, selecione **Grupos de recursos** à esquerda e, em seguida, selecione o grupo de recursos que criou.  

    Se o menu à esquerda estiver fechado, selecione ![botão Expandir](media/ingest-data-event-hub/expand.png) para expandi-lo.

   ![Selecionar grupo de recursos a eliminar](media/ingest-data-event-hub/delete-resources-select.png)

1. Em **test-resource-group**, selecione **Eliminar grupo de recursos**.

1. Na nova janela, escreva o nome do grupo de recursos a eliminar (*test-hub-rg*) e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

* [Dados de consulta no Explorador de Dados do Azure](web-query-data.md)
