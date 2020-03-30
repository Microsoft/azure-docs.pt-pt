---
title: Visualizar dados com o conector Azure Data Explorer para O BI de Energia
description: 'Neste artigo, aprende-se a utilizar uma das três opções para visualizar dados no Power BI: o conector Power BI para o Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a95d45481bed17e46429e3a22dff4b8cc62354a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560495"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Visualizar dados utilizando o conector Azure Data Explorer para Power BI

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Power BI é uma solução de análise de negócios que lhe permite visualizar os seus dados e partilhar os resultados na sua organização. O Azure Data Explorer fornece três opções para a ligação aos dados no Power BI: utilize o conector incorporado, importe uma consulta do Azure Data Explorer ou utilize uma consulta SQL. Este artigo mostra-lhe como usar o conector incorporado para obter dados e visualizá-lo num relatório do Power BI. A utilização do conector nativo do Azure Data Explorer para criar dashboards Power BI é simples. O conector Power BI suporta modos de [conectividade Import e Direct Query](https://docs.microsoft.com/power-bi/desktop-directquery-about). Pode construir dashboards utilizando o modo **Import** ou **DirectQuery,** dependendo dos requisitos de cenário, escala e desempenho. 

## <a name="prerequisites"></a>Pré-requisitos

Precisa do seguinte para completar este artigo:

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma conta de e-mail organizacional que é membro do diretório Azure Ative, para que possa ligar-se ao cluster de ajuda do [Azure Data Explorer.](https://dataexplorer.azure.com/clusters/help/databases/samples)
* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selecione **DOWNLOAD GRÁTIS)**

## <a name="get-data-from-azure-data-explorer"></a>Obtenha dados do Azure Data Explorer

Primeiro, liga-se ao cluster de ajuda do Azure Data Explorer, depois traz um subconjunto dos dados da tabela *StormEvents.* [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. No Power BI Desktop, no separador **Home,** selecione **Obter Dados** e **mais**.

    ![Obter dados](media/power-bi-connector/get-data-more.png)

1. Procure o *Azure Data Explorer,* selecione **Azure Data Explorer** e, em seguida, **ligue**.

    ![Pesquisar e obter dados](media/power-bi-connector/search-get-data.png)

1. No ecrã **Azure Data Explorer (Kusto),** preencha o formulário com as seguintes informações.

    ![Opções do cluster, da base de dados e da tabela](media/power-bi-connector/cluster-database-table.png)

    **Definição** | **Valor** | **Descrição do campo**
    |---|---|---|
    | Cluster | *https://help.kusto.windows.net* | O URL para o cluster de ajuda. Para outros clusters, o URL está na forma *https://\<ClusterName\>.\< \>Região .kusto.windows.net*. |
    | Base de Dados | Deixar em branco | Uma base de dados que está alojada no cluster a que está a ligar. Vamos selecionar isto num passo mais tarde. |
    | Nome da tabela | Deixar em branco | Uma das tabelas na base de <code>StormEvents \| take 1000</code>dados, ou uma consulta como. Vamos selecionar isto num passo mais tarde. |
    | Opções avançadas | Deixar em branco | Opções para as suas consultas, tais como o tamanho do conjunto de resultados. |
    | Modo de conectividade de dados | *DirectQuery* | Determina se o Power BI importa os dados ou se liga diretamente à fonte de dados. Pode utilizar qualquer uma das opções com este conector. |
    | | | |
    
    > [!NOTE]
    > No modo **Import,** os dados são transferidos para Power BI. No modo **DirectQuery,** os dados são consultados diretamente do seu cluster Azure Data Explorer.
    >
    > Utilize o modo **de importação** quando:
    > * O seu conjunto de dados é pequeno.
    > * Não precisas de dados em tempo real. 
    > * Os seus dados já estão agregados ou realizam [agregação em Kusto](/azure/kusto/query/summarizeoperator#list-of-aggregation-functions)    
    >
    > Utilize o modo **DirectQuery** quando:
    > * O seu conjunto de dados é muito grande. 
    > * Precisa de dados em tempo real.   

1. Se ainda não tem uma ligação com o aglomerado de ajuda, inscreva-se. Inscreva-se numa conta organizacional e, em seguida, selecione **Connect**.

    ![Iniciar sessão](media/power-bi-connector/sign-in.png)

1. No ecrã do **Navigator,** expanda a base de dados **de Amostras,** selecione **StormEvents** e **edite**.

    ![Tabela selecionada](media/power-bi-connector/select-table.png)

    A tabela abre-se no Editor do Power Query, onde pode editar linhas e colunas antes de importar os dados.

1. No Editor de Consulta de Potência, selecione a seta ao lado da coluna **DamageCrops** e, em seguida, ordenar a **descida**.

    ![Ordenar danos Culturas descendo](media/power-bi-connector/sort-descending.png)

1. No separador **Home,** selecione **Mantenha as linhas** de fundo e mantenha as linhas **superiores**. Insira um valor de *1000* para trazer as 1000 melhores filas da tabela ordenada.

    ![Mantenha as filas de cima](media/power-bi-connector/keep-top-rows.png)

1. No separador **Home,** selecione **Fechar & Aplicar**.

    ![Fechar e aplicar](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Visualizar dados num relatório

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar do relatório criado para este artigo, elimine o ficheiro Power BI Desktop (.pbix).

## <a name="next-steps"></a>Passos seguintes

[Dicas para a utilização do conector Azure Data Explorer para Power BI para consultar dados](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)
