---
title: Utilize comandos de controlo do Explorador de Dados Azure na Fábrica de Dados Azure
description: Neste tópico, utilize comandos de controlo do Azure Data Explorer na Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264484"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Utilize a atividade de comando da Azure Data Factory para executar comandos de controlo do Explorador de Dados Do Azure

[A Azure Data Factory](/azure/data-factory/) (ADF) é um serviço de integração de dados baseado na nuvem que permite realizar uma combinação de atividades nos dados. Utilize a ADF para criar fluxos de trabalho baseados em dados para orquestrar e automatizar o movimento de dados e a transformação de dados. A atividade do Comando do Explorador de **Dados Azure** na Azure Data Factory permite-lhe executar comandos de controlo do Explorador de [Dados Azure](/azure/kusto/concepts/#control-commands) dentro de um fluxo de trabalho ADF. Este artigo ensina-o a criar um pipeline com uma atividade de procuração e atividade ForEach contendo uma atividade de comando Azure Data Explorer.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster azure data explorer e base de dados](create-cluster-database-portal.md)
* Uma fonte de dados.
* [Uma fábrica de dados](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Criar um novo oleoduto

1. Selecione a ferramenta de lápis **Autor.** 
1. Crie um novo **+** oleoduto selecionando e selecione **pipeline** a partir da queda para baixo.

   ![criar novo oleoduto](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Criar uma atividade de lookup

Uma [atividade de procuração](/azure/data-factory/control-flow-lookup-activity) pode recuperar um conjunto de dados de quaisquer fontes de dados apoiadas pela Azure Data Factory. A saída da atividade lookup pode ser usada numa atividade ForEach ou outra.

1. No painel **de Atividades,** em **Geral,** selecione a atividade **de Lookup.** Arraste e jogue na tela principal à direita.
 
    ![selecionar atividade de procura](media/data-factory-command-activity/select-activity.png)

1. A tela agora contém a atividade de procuração que criou. Utilize os separadores abaixo da lona para alterar os parâmetros relevantes. Em **geral,** mude o nome da atividade. 

    ![editar atividade de lookup](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Clique na área de lona vazia para ver as propriedades do gasoduto. Utilize o separador **Geral** para renomear o gasoduto. O nosso oleoduto *chama-se pipeline-4-docs.*

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Criar um conjunto de dados do Azure Data Explorer na atividade de procuração

1. Em **Definições,** selecione o conjunto de **dados**de Origem do Explorador de Dados do Azure pré-criado, ou selecione **+ Novo** para criar um novo conjunto de dados.
 
    ![adicionar conjunto de dados em definições de procuração](media/data-factory-command-activity/lookup-settings.png)

1. Selecione o conjunto de dados **do Azure Data Explorer (Kusto)** a partir da nova janela **dataset.** Selecione **Continuar** a adicionar o novo conjunto de dados.

   ![selecionar novo conjunto de dados](media/data-factory-command-activity/select-new-dataset.png) 

1. Os novos parâmetros de conjunto de dados do Explorador de Dados Azure são visíveis em **Definições**. Para atualizar os parâmetros, **selecione Editar**.

    ![configurações de procura com conjunto de dados do Explorador de Dados Do Azure](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. O novo separador **AzureDataExplorerTable** abre na tela principal. 
    * Selecione **General** e edite o nome do conjunto de dados. 
    * Selecione **Ligação** para editar as propriedades do conjunto de dados. 
    * Selecione o **serviço Linked** a partir do drop-down, ou selecione **+ Novo** para criar um novo serviço ligado.

    ![Editar propriedades do dataset do Explorador de Dados do Azure](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Ao criar um novo serviço ligado, abre a página New **Linked Service (Azure Data Explorer):**

    ![Novo serviço ligado a ADX](media/data-factory-command-activity/adx-new-linked-service.png)

   * Selecione **Nome** para serviço ligado ao Explorador de Dados Azure. Adicione **descrição,** se necessário.
   * No Connect através do tempo de execução da **integração,** altere as definições atuais, se necessário. 
   * No método de **seleção de Conta** selecione o seu cluster utilizando um de dois métodos: 
        * Selecione o botão de rádio de **subscrição Da Azure** e selecione a sua conta de **subscrição Azure.** Em seguida, selecione o seu **Cluster**. Note que a queda apenas listará clusters que pertencem ao utilizador.
        * Em vez disso, selecione **Introduza manualmente** o botão de rádio e introduza o seu **ponto final** (URL de cluster).
    * Especifique o **Inquilino**.
    * Introduza **o ID principal do serviço**. O ID principal deve ter as permissões adequadas, de acordo com o nível de permissão exigido pelo comando que está a ser utilizado.
    * Selecione o botão principal do **serviço** e introduza **a chave principal do serviço**.
    * Selecione a sua **Base de Dados** no menu suspenso. Em alternativa, selecione **Editar** caixa de verificação e insira o nome da sua base de dados.
    * Selecione **Test Connection** para testar a ligação de serviço ligada que criou. Se conseguir ligar-se à sua configuração, aparecerá uma **ligação** de marca verde de marca de verificação.
    * Selecione **Terminar** para completar a criação de serviço ligado.

1. Assim que tiver configurado um serviço ligado, in **AzureDataExplorerTable** > **Connection,** adicione o nome da **tabela.** Selecione **dados de Pré-visualização,** para se certificar de que os dados são apresentados corretamente.

   O seu conjunto de dados está agora pronto e pode continuar a editar o seu pipeline.

### <a name="add-a-query-to-your-lookup-activity"></a>Adicione uma consulta à sua atividade de procuração

1. Em **pipeline-4-docs** > **As definições** adicionam uma consulta na caixa de texto **Consulta,** por exemplo:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Altere o tempo de intervalo de **consulta** ou **Sem truncação** e propriedades apenas de **primeira linha,** conforme necessário. Neste fluxo, mantemos o tempo de **consulta** padrão e desverificamos as caixas de verificação. 

    ![Configurações finais da atividade de lookup](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Criar uma atividade For-Each 

A atividade [For-Each](/azure/data-factory/control-flow-for-each-activity) é utilizada para iterar sobre uma coleção e executar atividades especificadas em loop. 

1. Agora adicione uma atividade For-Each ao oleoduto. Esta atividade irá processar os dados devolvidos da atividade de Lookup. 
    * No painel **de Atividades,** sob **iteração & Condicionales,** selecione a atividade **ForEach** e arraste-a e deixe-a cair na tela.
    * Desenhe uma linha entre a saída da atividade Lookup e a entrada da atividade ForEach na tela para as ligar.

        ![Atividade ForEach](media/data-factory-command-activity/for-each-activity.png)

1.  Selecione a atividade ForEach na tela. No separador **Definições** abaixo:
    * Verifique a caixa de verificação **sequencial** para obter um processamento sequencial dos resultados do Lookup ou deixe-a desmarcada para criar um processamento paralelo.
    * Definir **a contagem de lote**.
    * Em **itens,** forneça a seguinte referência ao valor de saída: * @activity('Lookup1').output.value*

       ![Definições da atividade ForEach](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Criar uma atividade de Comando do Explorador de Dados Azure dentro da atividade ForEach

1. Clique duas vezes na atividade ForEach na tela para abri-la numa nova tela para especificar as atividades dentro do ForEach.
1. No painel **de Atividades,** no **Azure Data Explorer,** selecione a atividade do Comando do Explorador de **Dados Azure** e arraste-a e deixe-a cair na tela.

    ![Atividade de comando do Explorador de Dados Azure](media/data-factory-command-activity/adx-command-activity.png)

1.  No separador **Ligação,** selecione o mesmo Serviço Linked anteriormente criado.

    ![separador de ligação de atividade de comando do explorador de dados azure](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. No separador **Comando,** forneça o seguinte comando:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    O **Comando** instrui o Azure Data Explorer a exportar os resultados de uma determinada consulta para um armazenamento de bolhas, num formato comprimido. Funciona assíncronamente (utilizando o modificador assinizador).
    A consulta aborda a coluna de base de dados de cada linha no resultado da atividade do Lookup. O **tempo de tempo** do Comando pode ficar inalterado.

    ![atividade de comando](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > A atividade de comando tem os seguintes limites:
    > * Limite de tamanho: 1 TAMANHO DE Resposta MB
    > * Limite de tempo: 20 minutos (predefinido), 1 hora (máximo).
    > * Se necessário, pode anexar uma consulta ao resultado utilizando [a AdminThenQuery,](/azure/kusto/management/index#combining-queries-and-control-commands)para reduzir o tamanho/tempo resultantes.

1.  Agora o oleoduto está pronto. Pode voltar à vista principal do oleoduto clicando no nome do pipeline.

    ![Oleoduto de comando Azure Data Explorer](media/data-factory-command-activity/adx-command-pipeline.png)

1. Selecione **Debug** antes de publicar o gasoduto. O progresso do gasoduto pode ser monitorizado no separador **Saída.**

    ![saída de atividade de comando explorador de dados azure](media/data-factory-command-activity/command-activity-output.png)

1. Pode **publicar Tudo** **e,** em seguida, adicionar gatilho para executar o gasoduto. 

## <a name="control-command-outputs"></a>Controlar saídas de comando

A estrutura da saída da atividade de comando é detalhada abaixo. Esta saída pode ser utilizada pela próxima atividade no gasoduto.

### <a name="returned-value-of-a-non-async-control-command"></a>Valor devolvido de um comando de controlo não assinásino

Num comando de controlo não assinizado, a estrutura do valor devolvido é semelhante à estrutura do resultado da atividade de Lookup. O `count` campo indica o número de registos devolvidos. Um campo `value` de matriz fixo contém uma lista de registos. 

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
 
### <a name="returned-value-of-an-async-control-command"></a>Valor devolvido de um comando de controlo assinásino

Num comando de controlo de sincronização, a atividade faz sondagens à mesa de operações nos bastidores, até que a operação de asincronização esteja concluída ou sem horário. Portanto, o valor devolvido conterá `.show operations OperationId` o resultado desse **imóvel OperationId.** Verifique os valores das propriedades **do Estado** e do **Estado,** para verificar a conclusão bem sucedida da operação.

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

* Saiba como [copiar dados para o Azure Data Explorer utilizando](data-factory-load-data.md)a Azure Data Factory .
* Saiba mais sobre a utilização do [modelo azure data factory para cópia a granel da base de dados para o Azure Data Explorer](data-factory-template.md).
