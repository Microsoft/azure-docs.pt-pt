---
title: Visualizar dados usando o conector de Data Explorer do Azure para Power BI
description: 'Neste artigo, você aprenderá a usar uma das três opções para visualizar dados no Power BI: o conector do Power BI para o Data Explorer do Azure.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a2ec179321c5d9cb6e9627e397fcb6ae09dc82ed
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349137"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Visualizar dados usando o conector de Data Explorer do Azure para Power BI

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Power BI é uma solução de análise de negócios que lhe permite visualizar os seus dados e partilhar os resultados na sua organização. O Azure Data Explorer fornece três opções para se conectar a dados no Power BI: Use o conector interno, importe uma consulta da Data Explorer do Azure ou use uma consulta SQL. Este artigo mostra como usar o conector interno para obter dados e visualizá-los em um relatório Power BI. Usar o conector nativo do Azure Data Explorer para criar painéis de Power BI é simples. O conector de Power BI dá suporte aos [modos de conectividade de importação e consulta direta](https://docs.microsoft.com/power-bi/desktop-directquery-about). Você pode criar painéis usando o modo de **importação** ou **DirectQuery** dependendo do cenário, da escala e dos requisitos de desempenho. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte para concluir este artigo:

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma conta de email organizacional que seja membro do Azure Active Directory, para que você possa se conectar ao [cluster de ajuda do azure data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples).
* [Power bi desktop](https://powerbi.microsoft.com/get-started/) (selecione **Download gratuito**)

## <a name="get-data-from-azure-data-explorer"></a>Obter dados do Azure Data Explorer

Primeiro, você se conecta ao cluster de ajuda do Data Explorer do Azure e, em seguida, coloca em um subconjunto dos dados da tabela *StormEvents* . [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. No Power BI Desktop, na guia **início** , selecione **obter dados** e **mais**.

    ![Obter dados](media/power-bi-connector/get-data-more.png)

1. Pesquise *Data Explorer do Azure*, selecione **Azure data Explorer** , em seguida, **conectar**.

    ![Pesquisar e obter dados](media/power-bi-connector/search-get-data.png)

1. Na tela **Data Explorer do Azure (Kusto)** , preencha o formulário com as informações a seguir.

    ![Opções do cluster, da base de dados e da tabela](media/power-bi-connector/cluster-database-table.png)

    **Definição** | **Valor** | **Descrição do campo**
    |---|---|---|
    | Cluster | *https://help.kusto.windows.net* | A URL para o cluster de ajuda. Para outros clusters, a URL está no formato *https://\<ClusterName @ no__t-2. @no__t -3Region\>.kusto.windows.net*. |
    | Base de Dados | Deixar em branco | Um banco de dados hospedado no cluster ao qual você está se conectando. Selecionaremos isso em uma etapa posterior. |
    | Nome da tabela | Deixar em branco | Uma das tabelas no banco de dados ou uma consulta como <code>StormEvents \| take 1000</code>. Selecionaremos isso em uma etapa posterior. |
    | Opções avançadas | Deixar em branco | Opções para suas consultas, como tamanho do conjunto de resultados. |
    | Modo de conectividade de dados | *DirectQuery* | Determina se Power BI importa os dados ou se conecta diretamente à fonte de dados. Você pode usar qualquer uma das opções com esse conector. |
    | | | |
    
    > [!NOTE]
    > No modo de **importação** , os dados são movidos para Power bi. No modo **DirectQuery** , os dados são consultados diretamente do cluster de data Explorer do Azure.
    >
    > Use o modo de **importação** quando:
    > * O conjunto de dados é pequeno.
    > * Você não precisa de dados quase em tempo real. 
    > * Seus dados já estão agregados ou você executa a [agregação em Kusto](/azure/kusto/query/summarizeoperator#list-of-aggregation-functions)    
    >
    > Use o modo **DirectQuery** quando:
    > * Seu conjunto de dados é muito grande. 
    > * Você precisa de dados quase em tempo real.   

1. Se você ainda não tiver uma conexão com o cluster de ajuda, entre. Entre com uma conta organizacional e, em seguida, selecione **conectar**.

    ![Iniciar sessão](media/power-bi-connector/sign-in.png)

1. Na tela **navegador** , expanda o banco de dados de **exemplos** , selecione **StormEvents** e **Editar**.

    ![Selecionar a tabela](media/power-bi-connector/select-table.png)

    A tabela abre-se no Editor do Power Query, onde pode editar linhas e colunas antes de importar os dados.

1. No editor de Power Query, selecione a seta ao lado da coluna **DamageCrops** e **classifique em ordem decrescente**.

    ![Classificar DamageCrops decrescente](media/power-bi-connector/sort-descending.png)

1. Na guia **início** , selecione **manter linhas** e, em seguida, **manter as linhas superiores**. Insira um valor de *1000* para trazer as 1000 principais linhas da tabela classificada.

    ![Manter linhas superiores](media/power-bi-connector/keep-top-rows.png)

1. Na guia **início** , selecione **fechar & aplicar**.

    ![Fechar e aplicar](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Visualizar dados em um relatório

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais do relatório criado para este artigo, exclua o arquivo Power BI Desktop (. pbix).

## <a name="next-steps"></a>Passos seguintes

[Dicas para usar o conector de Data Explorer do Azure para Power BI para consultar dados](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)
