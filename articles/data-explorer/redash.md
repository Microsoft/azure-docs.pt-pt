---
title: Visualizar Data Explorer do Azure com o retraço
description: Neste artigo, você aprenderá a Visualizar dados no Azure Data Explorer com o conector nativo de Dash.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8bb8711bc4a6134ec740a55d9f5d5794b2de77ca
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588593"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Visualizar dados do Azure Data Explorer em Dash

O [Dash](https://www.redash.io/) se conecta e consulta suas fontes de dados, cria painéis para visualizar os dados e compartilhá-los com pares. Neste artigo, você aprende a configurar o Azure Data Explorer como uma fonte de dados para retraço e, em seguida, Visualizar dados.

## <a name="prerequisites"></a>Pré-requisitos

1. [Criar cluster e banco de dados](create-cluster-database-portal.md).
1. Ingerir dados, conforme explicado em [dados de exemplo de ingestão no Azure data Explorer](ingest-sample-data.md). Para obter mais opções de ingestão, consulte [visão geral da ingestão](ingest-data-overview.md).

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Criar conector de Data Explorer do Azure em Dash 

1. Entre para [retraço](https://www.redash.io/). Selecione **introdução** para criar uma conta.
1. Em **vamos**começar, selecione **conectar uma fonte de dados**.

    ![Conectar uma fonte de dados](media/redash/connect-data-source.png)

1. Na janela **criar uma nova fonte de dados** , selecione **Data Explorer do Azure (Kusto)** e, em seguida, selecione **criar**. 

    ![Selecionar fonte de dados do Azure Data Explorer](media/redash/select-adx-data-source.png)

1. Na janela **Data Explorer do Azure (Kusto)** , preencha o formulário a seguir e selecione **criar**.

    ![Janela de configurações do Data Explorer do Azure (Kusto)](media/redash/adx-settings-window.png)

1. Na janela **configurações** , selecione **salvar** e **testar conexão** para testar sua conexão de fonte de dados **do data Explorer do Azure (Kusto)** .

## <a name="create-queries-in-redash"></a>Criar consultas em Dash

1. Na parte superior esquerda da barra, selecione **criar** > **consulta**. Clique em **nova consulta** e renomeie a consulta.

    ![Criar consulta](media/redash/create-query.png)

1. Digite sua consulta no painel de edição superior e selecione **salvar** e **executar**. Selecione **publicar** para publicar consulta para uso futuro.

    ![Salvar e executar consulta](media/redash/save-and-execute-query.png)

    No painel esquerdo, você pode ver o nome da conexão da fonte de dados (**conector do GitHub** em nosso fluxo) no menu suspenso e as tabelas no banco de dados selecionado. 

1. Exiba os resultados da consulta no painel central inferior. Crie uma visualização para ir com a consulta selecionando o botão **nova visualização** .

    ![Nova visualização](media/redash/new-visualization.png)

1. Na tela de visualização, selecione o **tipo de visualização** e os campos relevantes, como **X Column** e **Y Column**. **Salve** a visualização.

    ![Configurar e salvar a visualização](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Criar uma consulta usando um parâmetro

1. **Crie** > **consulta** para criar uma nova consulta. Adicione um parâmetro a ele usando chaves {{}}. Selecione **{{}}** para abrir a janela **Adicionar parâmetro** . Você também pode selecionar o *ícone de configurações* para modificar os atributos de um parâmetro existente e abrir a janela **< parameter_name >** . 

    ![inserir parâmetro](media/redash/insert-parameter.png)

1. Nomeie o parâmetro. Selecione **tipo**: **lista suspensa baseada em consulta** no menu suspenso. Selecione **OK**

    ![lista suspensa baseada em consulta](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > A consulta usa vários valores, portanto, você deve incluir a sintaxe a seguir `| where Type in ((split('{{Type}}', ',')))`. Para obter mais informações, consulte [no operador](/azure/kusto/query/inoperator). Isso resulta em [várias opções de parâmetro de consulta no aplicativo de retraço](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>Criar um painel em Dash

1. Para criar seu painel, **crie** > **painel**. Como alternativa, selecione Painel existente, **painéis** > selecione um painel na lista.

    ![Criar painel](media/redash/create-dashboard.png)

1. Na janela **novo painel** , nomeie o painel e selecione **salvar**. Na janela **< Dashboard_name >** , selecione **Adicionar widget** para criar um novo widget. 

1. Na janela **Adicionar widget** , selecione nome da consulta, **escolha Visualização**e **parâmetros**. Selecione **Adicionar ao painel**

   ![Escolher visualizações e adicionar ao painel](media/redash/add-widget-window.png)

1. Selecione **edição concluída** para concluir a criação do painel.

1.  No modo de edição do painel, selecione **usar filtros de nível de painel** para usar o parâmetro de **tipo** definido anteriormente.

    ![Concluir a criação do painel](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Passos seguintes

* [Escrever consultas do Azure Data Explorer](write-queries.md)


