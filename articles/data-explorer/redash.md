---
title: Visualizar o Explorador de Dados Azure com o Redash
description: Neste artigo, aprende-se a visualizar dados no Azure Data Explorer com o conector nativo Redash.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0380689ae6ca81e3f31a07f1e205c7773fdea8c6
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773944"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Visualizar dados do Azure Data Explorer em Redash

[O Redash](https://redash.io/) conecta e consulta as suas fontes de dados, constrói dashboards para visualizar dados e partilhá-los com os pares. Neste artigo, aprende-se a configurar o Azure Data Explorer como fonte de dados para o Redash e, em seguida, visualizar dados.

## <a name="prerequisites"></a>Pré-requisitos

1. [Criar cluster e base de dados.](create-cluster-database-portal.md)
1. Ingerir dados como explicados na [ingestão](ingest-sample-data.md)de dados da amostra no Azure Data Explorer . Para mais opções de ingestão, consulte a visão geral da [ingestão](ingest-data-overview.md).

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Criar conector azure data explorer em Redash 

1. Inscreva-se no [Redash.](https://www.redash.io/) Selecione **Get Started** para criar uma conta.
1. Em **Let's start**, Selecione **Ligar uma Fonte de Dados**.

    ![Ligar uma fonte de dados](media/redash/connect-data-source.png)

1. Em Criar uma nova janela **Data Source,** selecione **Azure Data Explorer (Kusto)** e, em seguida, selecione **Criar**. 

    ![Selecione fonte de dados do Explorador de Dados do Azure](media/redash/select-adx-data-source.png)

1. Na janela **Azure Data Explorer (Kusto),** complete o seguinte formulário e selecione **Criar**.

    ![Janela de definições do Azure Data Explorer (Kusto)](media/redash/adx-settings-window.png)

1. Na janela **Definições,** selecione **Save** and **Test Connection** para testar a ligação de fonte de origem do Explorador de **Dados Do Azure (Kusto).**

## <a name="create-queries-in-redash"></a>Criar consultas em Redash

1. Em cima à esquerda do Redash, selecione **Criar** > **Consulta**. Clique em **New Consulta** e mude o nome da consulta.

    ![Criar consulta](media/redash/create-query.png)

1. Digite a sua consulta no painel de edição superior e selecione **Guardar** e **Executar**. Selecione **Publicar** para publicar consulta para uso futuro.

    ![Guardar e executar consulta](media/redash/save-and-execute-query.png)

    No painel esquerdo, pode ver o nome de ligação de fonte de dados **(conector Github** no nosso fluxo) no menu suspenso e as tabelas na base de dados selecionada. 

1. Veja os resultados da consulta no painel central inferior. Crie uma visualização para acompanhar a consulta selecionando o botão **De Visualização Nova.**

    ![Nova visualização](media/redash/new-visualization.png)

1. No ecrã de visualização, selecione o Tipo de **Visualização** e os campos relevantes como **Coluna X** e **Coluna Y**. **Poupe** a visualização.

    ![Configure e salve a visualização](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Criar uma consulta usando um parâmetro

1. **Crie** > **Consulta** para criar uma nova consulta. Adicione-lhe um parâmetro utilizando {{}} suportes encaracolados. Selecione **{{}}** para abrir a janela adicionar **parâmetro.** Também pode selecionar o ícone de *definições* para modificar os atributos de um parâmetro existente e abrir a janela **<parameter_name>** 

    ![inserir parâmetro](media/redash/insert-parameter.png)

1. Diga o seu parâmetro. Selecione **Tipo**: Lista de **dropdown baseada em consulta** do menu dropdown. Selecione **OK**

    ![consulta lista de dropdown baseado](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > A consulta utiliza vários valores, pelo que deve incluir a seguinte `| where Type in ((split('{{Type}}', ',')))`de sintaxe . Para mais informações, consulte [o operador.](/azure/kusto/query/inoperator) Isto resulta em [várias opções de parâmetros de consulta na aplicação redash](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>Crie um dashboard em Redash

1. Para criar o seu painel de instrumentos, **Crie** > **Dashboard**. Em alternativa, selecione dashboard existente, **Dashboards** > selecione um dashboard da lista.

    ![Criar painel de instrumentos](media/redash/create-dashboard.png)

1. Na janela **New Dashboard,** nomeie o seu painel de instrumentos e selecione **Guardar**. In **<Dashboard_name>** janela, **selecione Add Widget** para criar um novo widget. 

1. Na janela **Add Widget,** selecione o nome da consulta, **escolha visualização,** e **parâmetros**. **Selecione Adicionar ao Dashboard**

   ![Escolha visualizações e adicione ao dashboard](media/redash/add-widget-window.png)

1. Selecione **Edição Feita para** completar a criação do dashboard.

1.  No modo de edição do painel de instrumentos, selecione **Use filtros de nível do painel** para utilizar o parâmetro **tipo** previamente definido.

    ![Criação completa do dashboard](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Passos seguintes

* [Escrever consultas do Azure Data Explorer](write-queries.md)


