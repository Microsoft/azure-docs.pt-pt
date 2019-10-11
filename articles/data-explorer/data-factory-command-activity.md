---
title: Usar os comandos do controle de Data Explorer do Azure no Azure Data Factory
description: Neste tópico, use os comandos do controle de Data Explorer do Azure no Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264484"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Usar Azure Data Factory atividade de comando para executar comandos do controle de Data Explorer do Azure

[Azure data Factory](/azure/data-factory/) (ADF) é um serviço de integração de dados baseado em nuvem que permite executar uma combinação de atividades nos dados. Use o ADF para criar fluxos de trabalho orientados a dados para orquestrar e automatizar a movimentação de dados e a transformação de dados. A atividade de **comando do data Explorer do Azure** no Azure data Factory permite que você execute [comandos do controle de data Explorer do Azure](/azure/kusto/concepts/#control-commands) em um fluxo de trabalho do ADF. Este artigo ensina como criar um pipeline com uma atividade de pesquisa e uma atividade ForEach que contém uma atividade de comando do Azure Data Explorer.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster de Data Explorer do Azure e um banco de dados](create-cluster-database-portal.md)
* Uma fonte de dados.
* [Um data factory](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Criar um novo pipeline

1. Selecione a ferramenta **criar** lápis. 
1. Crie um novo pipeline selecionando **+** e, em seguida, selecione **pipeline** na lista suspensa.

   ![criar novo pipeline](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Criar uma atividade de pesquisa

Uma [atividade de pesquisa](/azure/data-factory/control-flow-lookup-activity) pode recuperar um conjunto de dados de qualquer fonte de dado com suporte Azure data Factory. A saída da atividade de pesquisa pode ser usada em um ForEach ou outra atividade.

1. No painel **atividades** , em **geral**, selecione a atividade de **pesquisa** . Arraste e solte-o na tela principal à direita.
 
    ![selecionar atividade de pesquisa](media/data-factory-command-activity/select-activity.png)

1. A tela agora contém a atividade de pesquisa que você criou. Use as guias abaixo da tela para alterar os parâmetros relevantes. Em **geral**, renomeie a atividade. 

    ![editar atividade de pesquisa](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Clique na área de tela vazia para exibir as propriedades do pipeline. Use a guia **geral** para renomear o pipeline. Nosso pipeline é denominado *pipeline-4-docs*.

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Criar um conjunto de Data Explorer do Azure na atividade de pesquisa

1. Em **configurações**, selecione o conjunto de data Explorer de **origem**do Azure criado previamente ou selecione **+ novo** para criar um novo conjunto de um.
 
    ![Adicionar conjunto de conjuntos nas configurações de pesquisa](media/data-factory-command-activity/lookup-settings.png)

1. Selecione o conjunto de **Kusto (Azure data Explorer)** DataSet na **nova janela DataSet** . Selecione **continuar** para adicionar o novo conjunto de um.

   ![Selecionar novo conjunto de novos](media/data-factory-command-activity/select-new-dataset.png) 

1. Os novos parâmetros do conjunto de Data Explorer do Azure são visíveis nas **configurações**. Para atualizar os parâmetros, selecione **Editar**.

    ![configurações de pesquisa com o conjunto de Data Explorer do Azure](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. A nova guia **AzureDataExplorerTable** é aberta na tela principal. 
    * Selecione **geral** e edite o nome do conjunto de um. 
    * Selecione **conexão** para editar as propriedades do conjunto de os. 
    * Selecione o **serviço vinculado** na lista suspensa ou selecione **+ novo** para criar um novo serviço vinculado.

    ![Editar propriedades do conjunto de Data Explorer do Azure](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Ao criar um novo serviço vinculado, a página **novo serviço vinculado (Azure data Explorer)** é aberta:

    ![ADX novo serviço vinculado](media/data-factory-command-activity/adx-new-linked-service.png)

   * Selecione o **nome** do serviço vinculado do Azure data Explorer. Adicione uma **Descrição** , se necessário.
   * Em **conectar por meio do Integration Runtime**, altere as configurações atuais, se necessário. 
   * Em **método de seleção de conta** , selecione o cluster usando um dos dois métodos: 
        * Selecione o botão **de opção assinatura do Azure** e selecione sua conta de **assinatura do Azure** . Em seguida, selecione o **cluster**. Observe que o menu suspenso listará apenas os clusters que pertencem ao usuário.
        * Em vez disso, selecione Inserir botão de opção **manualmente** e insira seu **ponto de extremidade** (URL do cluster).
    * Especifique o **locatário**.
    * Insira a **ID da entidade de serviço**. A ID da entidade de segurança deve ter as permissões adequadas, de acordo com o nível de permissão exigido pelo comando que está sendo usado.
    * Selecione o botão **chave da entidade de serviço** e insira a chave da entidade de **serviço**.
    * Selecione seu **banco de dados** no menu suspenso. Como alternativa, marque a caixa de seleção **Editar** e insira o nome do banco de dados.
    * Selecione **testar conexão** para testar a conexão de serviço vinculado que você criou. Se você puder se conectar à sua configuração, uma marca de seleção verde **com êxito** será exibida.
    * Selecione **concluir** para concluir a criação do serviço vinculado.

1. Depois de configurar um serviço vinculado, na**conexão**do **AzureDataExplorerTable** > , adicione o nome da **tabela** . Selecione **Visualizar dados**para certificar-se de que os dados são apresentados corretamente.

   O conjunto de seus conjuntos de seus agora está pronto e você pode continuar editando seu pipeline.

### <a name="add-a-query-to-your-lookup-activity"></a>Adicionar uma consulta à sua atividade de pesquisa

1. No **pipeline-4-docs** > **configurações** , adicione uma consulta na caixa de texto de **consulta** , por exemplo:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Altere as propriedades **tempo limite da consulta** ou **nenhuma truncamento** e **primeira linha** , conforme necessário. Nesse fluxo, mantemos o **tempo limite de consulta** padrão e desmarcamos as caixas de seleção. 

    ![Configurações finais da atividade de pesquisa](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Criar uma atividade for-each 

A atividade [for-each](/azure/data-factory/control-flow-for-each-activity) é usada para iterar em uma coleção e executar atividades especificadas em um loop. 

1. Agora você adiciona uma atividade for-each ao pipeline. Essa atividade processará os dados retornados da atividade de pesquisa. 
    * No painel **atividades** , em **iteração & condicionais**, selecione a atividade **foreach** e arraste-a e solte-a na tela.
    * Desenhe uma linha entre a saída da atividade de pesquisa e a entrada da atividade ForEach na tela para conectá-las.

        ![Atividade ForEach](media/data-factory-command-activity/for-each-activity.png)

1.  Selecione a atividade ForEach na tela. Na guia **configurações** abaixo:
    * Marque a caixa de seleção **sequencial** para um processamento sequencial dos resultados da pesquisa ou deixe-a desmarcada para criar processamento paralelo.
    * Definir **contagem de lote**.
    * Em **itens**, forneça a seguinte referência para o valor de saída: *@activity (' Lookup1 '). Output. Value*

       ![Definições da atividade ForEach](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Criar uma atividade de comando de Data Explorer do Azure dentro da atividade ForEach

1. Clique duas vezes na atividade ForEach na tela para abri-la em uma nova tela para especificar as atividades em ForEach.
1. No painel **atividades** , em **Data Explorer do Azure**, selecione a atividade de **comando do data Explorer do Azure** e arraste-a e solte-a na tela.

    ![Atividade de comando do Data Explorer do Azure](media/data-factory-command-activity/adx-command-activity.png)

1.  Na guia **conexão** , selecione o mesmo serviço vinculado criado anteriormente.

    ![guia conexão de atividade de comando do Azure data Explorer](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. Na guia **comando** , forneça o seguinte comando:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    O **comando** instrui o Azure data Explorer a exportar os resultados de uma determinada consulta para um armazenamento de BLOBs, em um formato compactado. Ele é executado de forma assíncrona (usando o modificador assíncrono).
    A consulta aborda a coluna de banco de dados de cada linha no resultado da atividade de pesquisa. O **tempo limite do comando** pode ser deixado inalterado.

    ![atividade de comando](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > A atividade de comando tem os seguintes limites:
    > * Limite de tamanho: tamanho de resposta de 1 MB
    > * Limite de tempo: 20 minutos (padrão), 1 hora (máximo).
    > * Se necessário, você pode acrescentar uma consulta ao resultado usando [AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands)para reduzir o tamanho/tempo resultante.

1.  Agora o pipeline está pronto. Você pode voltar para o modo de exibição de pipeline principal clicando no nome do pipeline.

    ![Pipeline de comando do Azure Data Explorer](media/data-factory-command-activity/adx-command-pipeline.png)

1. Selecione **depurar** antes de publicar o pipeline. O progresso do pipeline pode ser monitorado na guia **saída** .

    ![saída da atividade de comando do Azure data Explorer](media/data-factory-command-activity/command-activity-output.png)

1. Você pode **publicar tudo** e, em seguida, **Adicionar o gatilho** para executar o pipeline. 

## <a name="control-command-outputs"></a>Saídas de comando de controle

A estrutura da saída da atividade de comando é detalhada abaixo. Essa saída pode ser usada pela próxima atividade no pipeline.

### <a name="returned-value-of-a-non-async-control-command"></a>Valor retornado de um comando de controle não assíncrono

Em um comando de controle não assíncrono, a estrutura do valor retornado é semelhante à estrutura do resultado da atividade de pesquisa. O campo `count` indica o número de registros retornados. Um campo de matriz fixo `value` contém uma lista de registros. 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>Valor retornado de um comando de controle assíncrono

Em um comando de controle assíncrono, a atividade sonda a tabela de operações nos bastidores até que a operação assíncrona seja concluída ou o tempo limite seja excedido. Portanto, o valor retornado conterá o resultado de `.show operations OperationId` para essa propriedade **operationId** fornecida. Verifique os valores das propriedades **State** e **status** para verificar a conclusão bem-sucedida da operação.

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como [copiar dados para o Azure data Explorer usando Azure data Factory](data-factory-load-data.md).
* Saiba como usar o [modelo Azure data Factory para cópia em massa do banco de dados para o Azure data Explorer](data-factory-template.md).
