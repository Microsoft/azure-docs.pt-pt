---
title: Visualizar dados do Azure Data Explorer com uma consulta importada de Power BI
description: 'Neste artigo, aprende-se a utilizar uma das três opções para visualizar dados no Power BI: importar uma consulta do Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: ff156ab3fe74115bce8f7d6bdd3ba47b514f5ff5
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562484"
---
# <a name="visualize-data-using-a-query-imported-into-power-bi"></a>Visualizar dados usando uma consulta importada para o Power BI

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Power BI é uma solução de análise de negócios que lhe permite visualizar os seus dados e partilhar os resultados na sua organização.

O Azure Data Explorer fornece três opções para a ligação aos dados no Power BI: utilize o conector incorporado, importe uma consulta do Azure Data Explorer ou utilize uma consulta SQL. Este artigo mostra-lhe como importar uma consulta para que possa obter dados e visualizá-lo num relatório do Power BI.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Precisa do seguinte para completar este artigo:

* Uma conta de e-mail organizacional que é membro do diretório Azure Ative, para que possa ligar-se ao cluster de ajuda do [Azure Data Explorer.](https://dataexplorer.azure.com/clusters/help/databases/samples)

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selecione **DOWNLOAD GRÁTIS)**

* [Aplicativo de desktop Azure Data Explorer](/azure/kusto/tools/kusto-explorer)

## <a name="get-data-from-azure-data-explorer"></a>Obtenha dados do Azure Data Explorer

Em primeiro lugar, cria-se uma consulta na aplicação de desktop Do Azure Data Explorer e exporta-a para utilização no Power BI. Em seguida, ligue-se ao cluster de ajuda do Azure Data Explorer e traga um subconjunto dos dados da tabela *StormEvents.* [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. Num browser, vá a [https://help.kusto.windows.net/](https://help.kusto.windows.net/) para lançar a aplicação de desktop Do Azure Data Explorer.

1. Na aplicação de ambiente de trabalho, copie a seguinte consulta na janela de consulta superior direita e, em seguida, executá-la.

    ```Kusto
    StormEvents
    | sort by DamageCrops desc
    | take 1000
    ```

    As primeiras linhas do conjunto de resultados devem parecer semelhantes à imagem seguinte.

    ![Resultados da consulta](media/power-bi-imported-query/query-results.png)

1. No separador **Ferramentas,** selecione **Consulta para Power BI** e depois **OK**.

    ![Consulta de exportação](media/power-bi-imported-query/export-query.png)

1. No Ambiente de Trabalho Power BI, no separador **Home,** selecione **Obter Dados** e depois **consulta em branco**.

    ![Obter dados](media/power-bi-imported-query/get-data.png)

1. No Power Query Editor, no separador **Home,** selecione **Editor Avançado.**

1. Na janela **do editor avançado,** colhe a consulta que exportou e selecione **Done**.

    ![Consulta de pasta](media/power-bi-imported-query/paste-query.png)

1. Na janela principal do Power Query Editor, selecione **credenciais de Edição**. **Selecione a conta Organizacional,** inscreva-se e, em seguida, selecione **Connect**.

    ![Editar credenciais](media/power-bi-imported-query/edit-credentials.png)

1. No separador **Home,** selecione **Fechar e Aplicar**.

    ![Fechar e aplicar](media/power-bi-imported-query/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Visualizar dados num relatório

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar do relatório criado para este artigo, elimine o ficheiro Power BI Desktop (.pbix).

## <a name="next-steps"></a>Passos seguintes

[Visualizar dados utilizando o conector Azure Data Explorer para Power BI](power-bi-connector.md)