---
title: Visualizar dados do Azure Data Explorer usando uma consulta SQL no Power BI
description: 'Neste artigo, você aprenderá a usar uma das três opções para visualizar dados no Power BI: uma consulta SQL em um cluster de Data Explorer do Azure.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: e4e7858a54f3002a511269a2519135d5ac24ed68
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024086"
---
# <a name="visualize-data-from-azure-data-explorer-using-a-sql-query-in-power-bi"></a>Visualizar dados do Azure Data Explorer usando uma consulta SQL no Power BI

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Power BI é uma solução de análise de negócios que lhe permite visualizar os seus dados e partilhar os resultados na sua organização.

O Azure Data Explorer fornece três opções para se conectar a dados no Power BI: Use o conector interno, importe uma consulta da Data Explorer do Azure ou use uma consulta SQL. Este artigo mostra como usar uma consulta SQL para obter dados e visualizá-los em um relatório Power BI.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do seguinte para concluir este artigo:

* Uma conta de email organizacional que seja membro do Azure Active Directory, para que você possa se conectar ao [cluster de ajuda do azure data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power bi desktop](https://powerbi.microsoft.com/get-started/) (selecione **Download gratuito**)

## <a name="get-data-from-azure-data-explorer"></a>Obter dados do Azure Data Explorer

Primeiro, você se conecta ao cluster de ajuda do Data Explorer do Azure e, em seguida, coloca em um subconjunto dos dados da tabela *StormEvents* . [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

Normalmente, você usa a linguagem de consulta nativa com o Azure Data Explorer, mas também oferece suporte a consultas SQL, que você usará aqui. O Azure Data Explorer converte a consulta SQL em uma consulta nativa para você.

1. No Power BI Desktop, na guia **início** , selecione **obter dados** e **mais**.

    ![Obter dados](media/power-bi-sql-query/get-data-more.png)

1. Pesquise *banco de dados SQL do Azure*, selecione **banco de dados SQL do Azure** e **conectar**.

    ![Pesquisar e obter dados](media/power-bi-sql-query/search-get-data.png)

1. Na tela **SQL Server banco de dados** , preencha o formulário com as informações a seguir.

    ![Opções de banco de dados, tabela, consulta](media/power-bi-sql-query/database-table-query.png)

    **Definição** | **Valor** | **Descrição do campo**
    |---|---|---|
    | Servidor | *help.kusto.windows.net* | A URL para o cluster de ajuda (sem *https://* ). Para outros clusters, a URL está no formato *\<clustername\>.\<região\>. Kusto.Windows.net*. |
    | Base de Dados | *Amostras* | O banco de dados de exemplo hospedado no cluster ao qual você está se conectando. |
    | Modo de conectividade de dados | *Importar* | Determina se Power BI importa os dados ou se conecta diretamente à fonte de dados. Você pode usar qualquer uma das opções com esse conector. |
    | Tempo limite do comando | Deixar em branco | Por quanto tempo a consulta é executada antes de lançar um erro de tempo limite. |
    | Instrução SQL | Copiar a consulta abaixo desta tabela | A instrução SQL que o Azure Data Explorer converte em uma consulta nativa. |
    | Outras opções | Deixar como valores padrão | As opções não se aplicam aos clusters de Data Explorer do Azure. |
    | | | |

    ```SQL
    SELECT TOP 1000 *
    FROM StormEvents
    ORDER BY DamageCrops DESC
    ```

1. Se você ainda não tiver uma conexão com o cluster de ajuda, entre. Entre com uma conta Microsoft e, em seguida, selecione **conectar**.

    ![Iniciar sessão](media/power-bi-sql-query/sign-in.png)

1. Na tela **help.Kusto.Windows.net: samples** , selecione **carregar**.

    ![Carregar dados](media/power-bi-sql-query/load-data.png)

    A tabela é aberta na janela principal do Power BI, na exibição de relatório, na qual você pode criar relatórios com base nos dados de exemplo.

## <a name="visualize-data-in-a-report"></a>Visualizar dados em um relatório

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais do relatório criado para este artigo, exclua o arquivo Power BI Desktop (. pbix).

## <a name="next-steps"></a>Passos seguintes

[Visualizar dados usando o conector de Data Explorer do Azure para Power BI](power-bi-connector.md)