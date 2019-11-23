---
title: Visualizar dados usando uma consulta do Azure Data Explorer Kusto importada para o Microsoft Excel
description: Neste artigo, você aprenderá a importar uma consulta do Azure Data Explorer Kusto para o Microsoft Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 55198e0c38c2922d69b68d9ce62e16ea25e9cc44
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173732"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Visualizar dados usando uma consulta do Azure Data Explorer Kusto importada para o Microsoft Excel

O Azure Data Explorer fornece duas opções para se conectar a dados no Excel: Use o conector nativo ou importe uma consulta do Data Explorer do Azure. Este artigo mostra como importar uma consulta do Azure Data Explorer para o Excel para visualizar dados. Adicione a consulta Kusto como uma fonte de dados do Excel para fazer cálculos ou visualizações adicionais nos dados.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma conta de email organizacional que seja membro do Azure Active Directory, para que você possa se conectar ao [cluster de ajuda do azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>ou</br>
* Crie [um cluster de teste e um banco de dados](create-cluster-database-portal.md) e entre no [aplicativo de interface do usuário da Web do Azure data Explorer](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Definir a consulta Kusto como uma fonte de dados do Excel

1. No [Azure data Explorer interface do usuário da Web](https://dataexplorer.azure.com/clusters/help/databases/Samples), execute a consulta e verifique os resultados.

1. Selecione a guia **compartilhar** e selecione **consulta para Power bi**.

    ![Consulta de interface do usuário da Web para Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Uma janela é exibida com a seguinte notificação:

    ![exportar consulta para área de transferência](media/excel-blank-query/query-exported-to-clipboard.png)

1. Abra **o Microsoft Excel**.

1. Na guia **dados** , selecione **obter dados** > **de outras fontes** > **consulta em branco**.

    ![Obter dados e Selecionar consulta em branco](media/excel-blank-query/get-data-blank-query.png)

1. A janela do **Editor de Power Query** é aberta. Na janela, selecione **Editor avançado**.

    ![Janela do editor do Power Query](media/excel-blank-query/power-query-editor.png)

1. Na janela **Editor avançado** , Cole a consulta exportada na área de transferência e selecione **concluído**.

    ![Consulta de editor avançado](media/excel-blank-query/advanced-editor-query.png)    

1. Para autenticar, selecione **Editar credenciais**.

    ![Editar credenciais](media/excel-blank-query/edit-credentials.png)

1. Selecione **conta organizacional** e **entre**. Conclua o processo de entrada e, em seguida, selecione **conectar**.

    ![Concluir a entrada](media/excel-blank-query/complete-sign-in.png)

    Repita as etapas anteriores para adicionar mais consultas. Você pode renomear as consultas para nomes mais significativos.

1. Selecione o botão **fechar & carregar** para colocar seus dados no Excel.

    ![Selecione fechar e carregar](media/excel-blank-query/close-and-load.png)

1. Agora, seus dados estão no Excel. Selecione o botão **Atualizar** para atualizar a consulta.

    ![Exibir dados no Excel](media/excel-blank-query/data-in-excel.png)