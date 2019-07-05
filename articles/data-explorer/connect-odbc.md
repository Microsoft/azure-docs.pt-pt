---
title: Ligar ao Explorador de dados do Azure com o ODBC
description: Neste artigo, irá aprender a configurar uma ligação de base de dados ODBC (Open Connectivity) ao Explorador de dados do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 65795b5b4dea8d2cdeecf5f78f9de751f275dac0
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537590"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Ligar ao Explorador de dados do Azure com o ODBC

Abrir a conectividade da base de dados ([ODBC](/sql/odbc/reference/odbc-overview)) é uma interface de programação de aplicativos amplamente aceita (API) para acesso de base de dados. Utilize o ODBC para ligar ao Explorador de dados do Azure a partir de aplicações que não têm um conector dedicado.

Em segundo plano, os aplicativos chamar funções na interface do ODBC, que são implementadas nos módulos específicos do banco de dados chamados *drivers*. O Explorador de dados do Azure suporta um subconjunto do protocolo de comunicação do SQL Server ([MS-TDS](/azure/kusto/api/tds/)), por isso, ele pode usar o controlador ODBC para o SQL Server.

Utilizar o seguinte vídeo, pode aprender criar uma ligação de ODBC. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Em alternativa, pode [configurar a origem de dados ODBC](#configure-the-odbc-data-source) conforme detalhado abaixo. 

Neste artigo, irá aprender a utilizar o controlador ODBC do SQL Server, para que possa ligar ao Azure Data Explorer a partir de qualquer aplicação que suporte o ODBC. 

## <a name="prerequisites"></a>Pré-requisitos

É necessário o seguinte:

* [Microsoft ODBC Driver para SQL Server versão 17.2.0.1 ou posterior](/sql/connect/odbc/download-odbc-driver-for-sql-server) do seu sistema operativo.

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

    ![Active Directory integrado](media/connect-odbc/active-directory-integrated.png)

1. Selecione a base de dados com os dados de exemplo, em seguida, **seguinte**.

    ![Banco de dados de alteração padrão](media/connect-odbc/change-default-database.png)

1. No ecrã seguinte, deixe todas as opções, como o padrão é em seguida, selecione **concluir**.

1. Selecione **origem de dados de teste**.

    ![Origem de dados de teste](media/connect-odbc/test-data-source.png)

1. Certifique-se de que o teste foi concluída com êxito em seguida, selecione **OK**. Se o teste não teve êxito, verifique os valores que especificou nas etapas anteriores e certifique-se de que tem permissões suficientes para ligar ao cluster.

    ![Teste foi concluída com êxito](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Passos Seguintes

* [Ligar ao Explorador de dados do Azure a partir Tableau](tableau.md)