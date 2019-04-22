---
title: 'Início rápido: Visualize os dados a partir do Explorador de dados do Azure com uma consulta SQL no Power BI'
description: 'Neste início rápido, irá aprender a utilizar uma das três opções para visualizar dados no Power BI: uma consulta SQL em relação a um cluster do Explorador de dados do Azure.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/14/2018
ms.openlocfilehash: 4a3a688adaae8fe66c336617cdd0a4807f16ec68
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045512"
---
# <a name="quickstart-visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Início rápido: Visualizar dados utilizando o conector do Explorador de dados do Azure para o Power BI

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Power BI é uma solução de análise de negócios que lhe permite visualizar os seus dados e partilhar os resultados na sua organização.

O Explorador de dados do Azure fornece três opções para ligar aos dados no Power BI: utilizar o conector incorporado, importar uma consulta a partir do Explorador de dados do Azure ou utilizar uma consulta SQL. Este guia de introdução mostra-lhe como utilizar uma consulta SQL para obter dados e visualizá-la num relatório do Power BI.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

É necessário o seguinte para concluir este início rápido:

* Uma conta de e-mail organizacional que seja membro do Azure Active directory, para que possa ligar para o [cluster de ajuda do Explorador de dados do Azure](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selecionar **transferência gratuita**)

## <a name="get-data-from-azure-data-explorer"></a>Obter dados a partir do Explorador de dados do Azure

Em primeiro lugar, crie uma ligar para o cluster de ajuda do Explorador de dados do Azure, em seguida, introduz um subconjunto de dados a partir da *StormEvents* tabela. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

Geralmente usa a linguagem de consulta nativa com o Explorador de dados do Azure, mas também dá suporte a consultas SQL, que irá utilizar aqui. O Explorador de dados do Azure traduz-se a consulta SQL numa consulta nativa para.

1. No Power BI Desktop, sobre o **home page** separador, selecione **obter dados** , em seguida, **mais**.

    ![Obter dados](media/power-bi-sql-query/get-data-more.png)

1. Procure *Azure SQL Database*, selecione **base de dados do Azure SQL** , em seguida, **Connect**.

    ![Pesquisar e obter dados](media/power-bi-sql-query/search-get-data.png)

1. Sobre o **base de dados do SQL Server** ecrã, preencha o formulário com as seguintes informações.

    ![Base de dados, tabela, opções de consulta](media/power-bi-sql-query/database-table-query.png)

    **Definição** | **Valor** | **Descrição do campo**
    |---|---|---|
    | Servidor | *help.kusto.windows.net* | O URL para o cluster de ajuda (sem *https://*). Para outros clusters, o URL está no formato  *\<ClusterName\>.\< Região\>. kusto.windows.net*. |
    | Base de Dados | *Amostras* | A base de dados de exemplo que está alojado no cluster que está a ligar. |
    | Modo de conectividade de dados | *Importar* | Determina se o Power BI importa os dados ou ligar diretamente à origem de dados. Pode usar qualquer uma das opções com este conector. |
    | Tempo limite do comando | Deixar em branco | O tempo que a consulta é executada antes de ele emite um erro de tempo limite. |
    | Instrução SQL | Copiar consulta abaixo desta tabela | A instrução de SQL que o Explorador de dados do Azure que se traduz numa consulta nativa. |
    | Outras opções | Deixe como valores predefinidos | As opções não se aplicam aos clusters do Explorador de dados do Azure. |
    | | | |

    ```SQL
    SELECT TOP 1000 *
    FROM StormEvents
    ORDER BY DamageCrops DESC
    ```

1. Se ainda não tiver uma ligação ao cluster de ajuda, inicie sessão. Inicie sessão com uma conta Microsoft, em seguida, selecione **Connect**.

    ![Iniciar sessão](media/power-bi-sql-query/sign-in.png)

1. Sobre o **help.kusto.windows.net: Exemplos** ecrã, selecione **carga**.

    ![Carregar dados](media/power-bi-sql-query/load-data.png)

    A tabela é aberto no Power BI janela principal, na vista de relatório, onde pode criar relatórios com base nos dados de exemplo.

## <a name="visualize-data-in-a-report"></a>Visualize os dados num relatório

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar do relatório que criou para este início rápido, elimine o ficheiro do Power BI Desktop (. pbix).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Quickstart: Visualizar dados utilizando uma consulta importada no Power BI](power-bi-connector.md)