---
title: 'Início rápido: Visualize os dados a partir do Explorador de dados do Azure com uma consulta importado no Power BI '
description: 'Neste início rápido, irá aprender a utilizar uma das três opções para visualizar dados no Power BI: importar uma consulta a partir do Explorador de dados do Azure.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/14/2018
ms.openlocfilehash: d14de1f25cc432cb2a9fed2149bd0870aa3ce16a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60828991"
---
# <a name="quickstart-visualize-data-using-a-query-imported-into-power-bi"></a>Início rápido: Visualizar dados utilizando uma consulta importada para o Power BI

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Power BI é uma solução de análise de negócios que lhe permite visualizar os seus dados e partilhar os resultados na sua organização.

O Explorador de dados do Azure fornece três opções para ligar aos dados no Power BI: utilizar o conector incorporado, importar uma consulta a partir do Explorador de dados do Azure ou utilizar uma consulta SQL. Este início rápido mostra como importar uma consulta para que possa obter dados e visualizá-la num relatório do Power BI.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

É necessário o seguinte para concluir este início rápido:

* Uma conta de e-mail organizacional que seja membro do Azure Active directory, para que possa ligar para o [cluster de ajuda do Explorador de dados do Azure](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selecionar **transferência gratuita**)

* [Aplicação de ambiente de trabalho de Explorador de dados do Azure](/azure/kusto/tools/kusto-explorer)

## <a name="get-data-from-azure-data-explorer"></a>Obter dados a partir do Explorador de dados do Azure

Em primeiro lugar, crie uma consulta na aplicação de ambiente de trabalho do Explorador de dados do Azure e exportá-lo para utilização no Power BI. Em seguida, ligue ao cluster de ajuda do Explorador de dados do Azure e obter um subconjunto de dados a partir da *StormEvents* tabela. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. Num browser, aceda a [ https://help.kusto.windows.net/ ](https://help.kusto.windows.net/) para iniciar a aplicação de ambiente de trabalho do Explorador de dados do Azure.

1. Na aplicação de ambiente de trabalho, copie a seguinte consulta para a janela de consulta do canto superior direito, em seguida, executá-lo.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    As primeiras linhas do conjunto de resultados devem ser semelhantes à seguinte imagem.

    ![Resultados da consulta](media/power-bi-imported-query/query-results.png)

1. Sobre o **ferramentas** separador, selecione **consulta para o Power BI** , em seguida, **OK**.

    ![Consulta de exportação](media/power-bi-imported-query/export-query.png)

1. No Power BI Desktop, sobre o **home page** separador, selecione **obter dados** , em seguida, **consulta em branco**.

    ![Obter dados](media/power-bi-imported-query/get-data.png)

1. No Editor do Power Query, sobre o **home page** separador, selecione **editor avançado**.

1. Na **editor avançado** janela, colar, em seguida, selecione a consulta que exportou **feito**.

    ![Consulta de colar](media/power-bi-imported-query/paste-query.png)

1. Na janela principal do Editor do Power Query, selecione **editar credenciais**. Selecione **conta institucional**, iniciar sessão, em seguida, selecione **Connect**.

    ![Editar credenciais](media/power-bi-imported-query/edit-credentials.png)

1. Sobre o **home page** separador, selecione **fechar e aplicar**.

    ![Fechar e aplicar](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Visualize os dados num relatório

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar do relatório que criou para este início rápido, elimine o ficheiro do Power BI Desktop (. pbix).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Quickstart: Visualizar dados utilizando uma consulta importada no Power BI](power-bi-sql-query.md)