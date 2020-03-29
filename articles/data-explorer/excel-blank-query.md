---
title: Visualizar dados utilizando uma consulta do Azure Data Explorer Kusto importada para o Microsoft Excel
description: Neste artigo, aprende-se a importar uma consulta do Azure Data Explorer Kusto para o Microsoft Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849093"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Visualizar dados utilizando uma consulta do Azure Data Explorer Kusto importada para o Microsoft Excel

O Azure Data Explorer fornece duas opções para a ligação aos dados do Excel: utilize o conector nativo ou importe uma consulta do Azure Data Explorer. Este artigo mostra-lhe como importar uma consulta do Azure Data Explorer para o Excel para visualizar dados. Adicione a consulta de Kusto como uma fonte de dados do Excel para fazer cálculos adicionais ou visualizações nos dados.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma conta de e-mail organizacional que é membro do diretório Azure Ative, para que possa ligar-se ao cluster de ajuda do [Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>ou</br>
* Crie um cluster de [teste e base](create-cluster-database-portal.md) de dados e inscreva-se na [aplicação Azure Data Explorer Web UI](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Defina a consulta de Kusto como uma fonte de dados do Excel

1. No [Azure Data Explorer Web UI,](https://dataexplorer.azure.com/clusters/help/databases/Samples)faça a consulta e verifique os resultados.

1. Selecione o separador **Partilhar** e selecione **Consulta para Power BI**.

    ![Consulta de UI web para Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Uma janela aparece com a seguinte notificação:

    ![consulta de exportação para clipboard](media/excel-blank-query/query-exported-to-clipboard.png)

1. Abra o **Microsoft Excel.**

1. No separador **Dados,** selecione **Obter Dados** > **de Outras Fontes** > **Consulta em branco**.

    ![Obtenha dados e selecione consulta em branco](media/excel-blank-query/get-data-blank-query.png)

1. A janela do Editor de **Consulta de Energia** abre. Na janela, selecione **Advanced Editor**.

    ![Janela do editor de consulta de poder](media/excel-blank-query/power-query-editor.png)

1. Na janela **Do Editor Avançado,** colhe a consulta que exportou para a pasta e selecione **Done**.

    ![Consulta de editor avançado](media/excel-blank-query/advanced-editor-query.png)    

1. Para autenticar, selecione **Editar Credenciais**.

    ![Editar credenciais](media/excel-blank-query/edit-credentials.png)

1. **Selecione conta organizacional** e **inscreva-se**. Complete o processo de iniciar sessão e, em seguida, selecione **Ligar**.

    ![Iniciar a inscrição](media/excel-blank-query/complete-sign-in.png)

    Repita os passos anteriores para adicionar mais consultas. Pode mudar o nome das consultas para nomes mais significativos.

1. Selecione o botão **Close & Carregar** para obter os seus dados no Excel.

    ![Selecione fechar e carregar](media/excel-blank-query/close-and-load.png)

1. Agora os seus dados estão em Excel. Selecione o botão **Refresh** para refrescar a consulta.

    ![Ver dados em excel](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Passos seguintes

[Visualizar dados utilizando o conector Azure Data Explorer para excel](excel-connector.md)