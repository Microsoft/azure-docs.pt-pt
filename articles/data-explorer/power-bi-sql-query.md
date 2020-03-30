---
title: Visualizar dados do Azure Data Explorer com uma consulta Power BI SQL
description: 'Neste artigo, aprende-se a utilizar uma das três opções para visualizar dados no Power BI: uma consulta SQL contra um cluster Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: d402d4c1ee77d0f97d2a5c3bdf43d0cc62aac096
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560478"
---
# <a name="visualize-data-from-azure-data-explorer-using-a-sql-query-in-power-bi"></a>Visualizar dados do Azure Data Explorer usando uma consulta SQL no Power BI

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Power BI é uma solução de análise de negócios que lhe permite visualizar os seus dados e partilhar os resultados na sua organização.

O Azure Data Explorer fornece três opções para a ligação aos dados no Power BI: utilize o conector incorporado, importe uma consulta do Azure Data Explorer ou utilize uma consulta SQL. Este artigo mostra-lhe como usar uma consulta SQL para obter dados e visualizá-lo num relatório power BI.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Precisa do seguinte para completar este artigo:

* Uma conta de e-mail organizacional que é membro do diretório Azure Ative, para que possa ligar-se ao cluster de ajuda do [Azure Data Explorer.](https://dataexplorer.azure.com/clusters/help/databases/samples)

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selecione **DOWNLOAD GRÁTIS)**

## <a name="get-data-from-azure-data-explorer"></a>Obtenha dados do Azure Data Explorer

Primeiro, liga-se ao cluster de ajuda do Azure Data Explorer, depois traz um subconjunto dos dados da tabela *StormEvents.* [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

Você normalmente usa a linguagem de consulta nativa com O Explorador de Dados Azure, mas também suporta consultas SQL, que você vai usar aqui. O Azure Data Explorer traduz a consulta SQL numa consulta nativa para si.

1. No Power BI Desktop, no separador **Home,** selecione **Obter Dados** e **mais**.

    ![Obter dados](media/power-bi-sql-query/get-data-more.png)

1. Procure a Base de *Dados Azure SQL,* selecione Base de **Dados SQL Azure** **e,** em seguida, ligue .

    ![Pesquisar e obter dados](media/power-bi-sql-query/search-get-data.png)

1. No ecrã de base de dados do **SQL Server,** preencha o formulário com as seguintes informações.

    ![Base de dados, tabela, opções de consulta](media/power-bi-sql-query/database-table-query.png)

    **Definição** | **Valor** | **Descrição do campo**
    |---|---|---|
    | Server | *help.kusto.windows.net* | O URL para o cluster de ajuda (sem *https://*). Para outros clusters, o URL está na forma * \<ClusterName\>.\< \>Região .kusto.windows.net*. |
    | Base de Dados | *Amostras* | A base de dados de amostras que está alojada no cluster a que está a ligar. |
    | Modo de conectividade de dados | *Importar* | Determina se o Power BI importa os dados ou se liga diretamente à fonte de dados. Pode utilizar qualquer uma das opções com este conector. |
    | Tempo de tempo de comando | Deixar em branco | Quanto tempo dura a consulta antes de lançar um erro de tempo. |
    | Declaração SQL | Copie a consulta abaixo desta tabela | A declaração da SQL que o Azure Data Explorer traduz-se numa consulta nativa. |
    | Outras opções | Deixe como valores padrão | As opções não se aplicam aos clusters do Azure Data Explorer. |
    | | | |

    ```SQL
    SELECT TOP 1000 *
    FROM StormEvents
    ORDER BY DamageCrops DESC
    ```

1. Se ainda não tem uma ligação com o aglomerado de ajuda, inscreva-se. Iniciar sessão com uma conta Microsoft e, em seguida, selecione **Connect**.

    ![Iniciar sessão](media/power-bi-sql-query/sign-in.png)

1. No **help.kusto.windows.net: Ecrã de amostras,** **selecione Carregar**.

    ![Carregar dados](media/power-bi-sql-query/load-data.png)

    A tabela abre na janela principal do Power BI, na visualização do relatório, onde pode criar relatórios com base nos dados da amostra.

## <a name="visualize-data-in-a-report"></a>Visualizar dados num relatório

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar do relatório criado para este artigo, elimine o ficheiro Power BI Desktop (.pbix).

## <a name="next-steps"></a>Passos seguintes

[Visualizar dados utilizando o conector Azure Data Explorer para Power BI](power-bi-connector.md)