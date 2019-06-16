---
title: Utilize a ligação de base de dados ODBC (Open Connectivity) para o Azure Data Explorer para visualizar dados com o Tableau
description: Neste artigo, irá aprender a utilizar uma ligação de base de dados ODBC (Open Connectivity) para a ligação Explorador de dados do Azure para visualizar dados com o Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: a0948ae35a5c23768df117979db819861ac64529
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66499088"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Visualizar dados do Azure Data Explorer no Tableau

 [Tableau](https://www.tableau.com/) é uma plataforma de análise visual para business intelligence. Para ligar ao Explorador de dados do Azure a partir Tableau e importar dados de um cluster de exemplo, utilize o controlador do SQL Server da base de dados ODBC (Open Connectivity). 

## <a name="prerequisites"></a>Pré-requisitos

É necessário o seguinte para concluir este artigo:

* [Ligar ao Explorador de dados do Azure com o ODBC](connect-odbc.md) usando o controlador ODBC do SQL Server, para ligar ao Explorador de dados do Azure a partir Tableau. 

* Ambiente de trabalho de tableau, completa, ou [avaliação](https://www.tableau.com/products/desktop/download) versão.

* Um cluster que inclui os dados de exemplo StormEvents. Para obter mais informações, consulte [criar um cluster do Explorador de dados do Azure e a base de dados](create-cluster-database-portal.md) e [ingerir dados de exemplo no Explorador de dados do Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Visualizar dados no Tableau 

Quando tiver terminado de configurar o ODBC, pode colocar dados de exemplo para o Tableau.

1. Na área de trabalho Tableau, no menu à esquerda, selecione **outras bases de dados (ODBC)** .

    ![Entrar em contacto com o ODBC](media/tableau/connect-odbc.png)

1. Para **DSN**, selecione a origem de dados que criou para ODBC, em seguida, selecione **sessão**.

    ![ODBC início de sessão](media/tableau/odbc-sign-in.png)

1. Para **base de dados**, selecione a base de dados no seu cluster de exemplo, como *TestDatabase*. Para **esquema**, selecione *dbo*e para **tabela**, selecione o *StormEvents* tabela de exemplo.

    ![Selecione a base de dados e tabela](media/tableau/select-database-table.png)

1. Agora o tableau mostra o esquema para os dados de exemplo. Selecione **Update agora** para levar os dados para o Tableau.

    ![Atualizar dados](media/tableau/update-data.png)

    Quando os dados são importados, o Tableau mostra com que as linhas de dados semelhante à imagem seguinte.

    ![Conjunto de resultados](media/tableau/result-set.png)

1. Agora, pode criar visualizações no Tableau com base nos dados colocados partir do Explorador de dados do Azure. Para obter mais informações, consulte [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Passos Seguintes

* [Escrever consultas do Azure Data Explorer](write-queries.md)