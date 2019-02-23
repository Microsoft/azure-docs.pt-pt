---
title: Ligar ao Explorador de dados do Azure com o ODBC
description: Este procedimento, irá aprender a configurar uma ligação de ODBC ao Explorador de dados do Azure, em seguida, utilizar essa ligação para visualizar dados com o Tableau.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: ad00ad247b047d4acf97eb5d0e96229949181ecf
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56739909"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Ligar ao Explorador de dados do Azure com o ODBC

Abrir a conectividade da base de dados ([ODBC](/sql/odbc/reference/odbc-overview)) é uma interface de programação de aplicativos amplamente aceita (API) para acesso de base de dados. Utilize o ODBC para ligar ao Explorador de dados do Azure a partir de aplicações que não têm um conector dedicado.

Em segundo plano, os aplicativos chamar funções na interface do ODBC, que são implementadas nos módulos específicos do banco de dados chamados *drivers*. O Explorador de dados do Azure suporta um subconjunto do protocolo de comunicação do SQL Server ([MS-TDS](/azure/kusto/api/tds/)); por isso ele pode usar o controlador ODBC para o SQL Server.

Neste artigo, irá aprender a utilizar o controlador ODBC do SQL Server, para que possa ligar ao Azure Data Explorer a partir de qualquer aplicação que suporte o ODBC. Pode, em seguida, opcionalmente, ligar ao Explorador de dados do Azure a partir Tableau e importar dados de um cluster de exemplo.

## <a name="prerequisites"></a>Pré-requisitos

É necessário o seguinte para concluir este como:

* [Microsoft ODBC Driver para SQL Server versão 17.2.0.1 ou posterior](/sql/connect/odbc/download-odbc-driver-for-sql-server) do seu sistema operativo.

* Se quiser seguir o nosso exemplo Tableau, também tem de:

  * Ambiente de trabalho de tableau, completa ou [avaliação](https://www.tableau.com/products/desktop/download) versão.

  * Um cluster que inclui os dados de exemplo StormEvents. Para obter mais informações, consulte [início rápido: Criar um cluster do Explorador de dados do Azure e a base de dados](create-cluster-database-portal.md) e [ingerir dados de exemplo no Explorador de dados do Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-odbc-data-source"></a>Configurar a origem de dados ODBC

Siga estes passos para configurar uma origem de dados ODBC com o controlador ODBC para o SQL Server.

1. No Windows, procure *origens de dados de ODBC*e abra a aplicação de ambiente de trabalho de origens de dados de ODBC.

1. Selecione **Adicionar**.

    ![Adicionar origem de dados](media/connect-odbc/add-data-source.png)

1. Selecione **controlador ODBC 17 para SQL Server** , em seguida, **concluir**.

    ![Selecione o controlador](media/connect-odbc/select-driver.png)

1. Introduza um nome e descrição para a ligação e o cluster que pretende ligar-se, em seguida, selecione **seguinte**. O cluster URL deve estar no formato  *\<ClusterName\>.\< Região\>. kusto.windows.net*.

    ![Selecionar servidor](media/connect-odbc/select-server.png)

1. Selecione **integrada do Active Directory** , em seguida, **próxima**.

    ![Active Directory Integrado](media/connect-odbc/active-directory-integrated.png)

1. Selecione a base de dados com os dados de exemplo, em seguida, **seguinte**.

    ![Banco de dados de alteração padrão](media/connect-odbc/change-default-database.png)

1. No ecrã seguinte, deixe todas as opções, como o padrão é em seguida, selecione **concluir**.

1. Selecione **origem de dados de teste**.

    ![Origem de dados de teste](media/connect-odbc/test-data-source.png)

1. Certifique-se de que o teste foi concluída com êxito em seguida, selecione **OK**. Se o teste não teve êxito, verifique os valores que especificou nas etapas anteriores e certifique-se de que tem permissões suficientes para ligar ao cluster.

    ![Teste foi concluída com êxito](media/connect-odbc/test-succeeded.png)

## <a name="visualize-data-in-tableau-optional"></a>Visualizar dados no Tableau (opcional)

Agora que tiver terminado de configurar o ODBC, pode trazer dados de exemplo para o Tableau.

1. Na área de trabalho Tableau, no menu à esquerda, selecione **outras bases de dados (ODBC)**.

    ![Conecte-se ODBC](media/connect-odbc/connect-odbc.png)

1. Para **DSN**, selecione a origem de dados que criou para ODBC, em seguida, selecione **sessão**.

    ![ODBC início de sessão](media/connect-odbc/odbc-sign-in.png)

1. Para **base de dados**, selecione a base de dados no seu cluster de exemplo, como *TestDatabase*. Para **esquema**, selecione *dbo*e para **tabela**, selecione o *StormEvents* tabela de exemplo.

    ![Selecione a base de dados e tabela](media/connect-odbc/select-database-table.png)

1. Agora o tableau mostra o esquema para os dados de exemplo. Selecione **Update agora** para levar os dados para o Tableau.

    ![Atualizar dados](media/connect-odbc/update-data.png)

    Quando os dados são importados, o Tableau mostra com que as linhas de dados semelhante à imagem seguinte.

    ![Conjunto de resultados](media/connect-odbc/result-set.png)

1. Agora, pode criar visualizações no Tableau com base nos dados colocados partir do Explorador de dados do Azure. Para obter mais informações, consulte [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Passos Seguintes

[Escrever consultas do Azure Data Explorer](write-queries.md)

[Tutorial: Visualize os dados a partir do Explorador de dados do Azure no Power BI](visualize-power-bi.md)