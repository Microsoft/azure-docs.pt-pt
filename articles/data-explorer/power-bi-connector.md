---
title: 'Início rápido: Visualizar dados utilizando o conector do Explorador de dados do Azure para o Power BI'
description: 'Neste início rápido, irá aprender a utilizar uma das três opções para visualizar dados no Power BI: o conector do Power BI para o Explorador de dados do Azure.'
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/14/2018
ms.openlocfilehash: de920a9addf9d2053a3c101c6b1f9a2c1a2feb27
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106751"
---
# <a name="quickstart-visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Início rápido: Visualizar dados utilizando o conector do Explorador de dados do Azure para o Power BI

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. O Power BI é uma solução de análise de negócios que lhe permite visualizar os seus dados e partilhar os resultados na sua organização.

O Explorador de dados do Azure fornece três opções para ligar aos dados no Power BI: utilizar o conector incorporado, importar uma consulta a partir do Explorador de dados do Azure ou utilizar uma consulta SQL. Este guia de introdução mostra-lhe como utilizar o conector incorporado para obter dados e visualizá-la num relatório do Power BI.

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

É necessário o seguinte para concluir este início rápido:

* Uma conta de e-mail organizacional que seja membro do Azure Active directory, para que possa ligar para o [cluster de ajuda do Explorador de dados do Azure](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selecionar **transferência gratuita**)

## <a name="get-data-from-azure-data-explorer"></a>Obter dados a partir do Explorador de dados do Azure

Em primeiro lugar, crie uma ligar para o cluster de ajuda do Explorador de dados do Azure, em seguida, introduz um subconjunto de dados a partir da *StormEvents* tabela. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. No Power BI Desktop, sobre o **home page** separador, selecione **obter dados** , em seguida, **mais**.

    ![Obter dados](media/power-bi-connector/get-data-more.png)

1. Procure *Explorador de dados do Azure*, selecione **Explorador de dados do Azure (Beta)** , em seguida, **Connect**.

    ![Pesquisar e obter dados](media/power-bi-connector/search-get-data.png)

1. No ecrã **Conetor de pré-visualização**, selecione **Continuar**.

1. No ecrã seguinte, preencha o formulário com as seguintes informações.

    ![Opções do cluster, da base de dados e da tabela](media/power-bi-connector/cluster-database-table.png)

    **Definição** | **Valor** | **Descrição do campo**
    |---|---|---|
    | Cluster | *https://help.kusto.windows.net* | O URL para o cluster de ajuda. Para outros clusters, o URL está no formato *https://\<ClusterName\>.\< Região\>. kusto.windows.net*. |
    | Base de Dados | Deixar em branco | Uma base de dados que está alojado no cluster que está a ligar. Vamos selecionar isto num passo posterior. |
    | Nome da tabela | Deixar em branco | Uma das tabelas na base de dados ou uma consulta como <code>StormEvents \| take 1000</code>. Vamos selecionar isto num passo posterior. |
    | Opções avançadas | Deixar em branco | Opções para as suas consultas, por exemplo, o resultado de definir o tamanho. |
    | Modo de conectividade de dados | *DirectQuery* | Determina se o Power BI importa os dados ou ligar diretamente à origem de dados. Pode usar qualquer uma das opções com este conector. |
    | | | |

1. Se ainda não tiver uma ligação ao cluster de ajuda, inicie sessão. Inicie sessão com uma conta da organização, em seguida, selecione **Connect**.

    ![Iniciar sessão](media/power-bi-connector/sign-in.png)

1. Sobre o **navegador** ecrã, expanda o **exemplos** base de dados, selecione **StormEvents** , em seguida, **editar**.

    ![Selecionar a tabela](media/power-bi-connector/select-table.png)

    A tabela abre-se no Editor do Power Query, onde pode editar linhas e colunas antes de importar os dados.

1. No Editor do Power Query, selecione a seta junto a **DamageCrops** , em seguida, a coluna **ordenar descendente**.

    ![DamageCrops de ordenação descendente](media/power-bi-connector/sort-descending.png)

1. Sobre o **home page** separador, selecione **manter linhas** , em seguida, **manter as primeiras linhas**. Introduza um valor de *1000* para aplicar as primeiras 1000 linhas da tabela ordenada.

    ![Manter linhas principais](media/power-bi-connector/keep-top-rows.png)

1. Sobre o **home page** separador, selecione **fechar e aplicar**.

    ![Fechar e aplicar](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Visualize os dados num relatório

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisar do relatório que criou para este início rápido, elimine o ficheiro do Power BI Desktop (. pbix).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Quickstart: Visualizar dados utilizando uma consulta importada no Power BI](power-bi-imported-query.md)
