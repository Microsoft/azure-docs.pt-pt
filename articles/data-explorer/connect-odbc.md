---
title: Ligue-se ao Azure Data Explorer com a ODBC
description: Neste artigo, aprende-se a configurar uma ligação de conectividade de base de dados aberta (ODBC) ao Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034020"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Ligue-se ao Azure Data Explorer com a ODBC

Open Database Connectivity[(ODBC)](/sql/odbc/reference/odbc-overview)é uma interface de programação de aplicações amplamente aceite (API) para acesso à base de dados. Utilize a ODBC para se ligar ao Azure Data Explorer a partir de aplicações que não possuam um conector dedicado.

Nos bastidores, as aplicações chamam funções de chamada na interface ODBC, que são implementadas em módulos específicos da base de dados *chamados controladores*. O Azure Data Explorer suporta um subconjunto do protocolo de comunicação do Servidor SQL[(MS-TDS),](/azure/kusto/api/tds/)para que possa utilizar o controlador ODBC para o Servidor SQL.

Utilizando o seguinte vídeo, pode aprender a criar uma ligação ODBC. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Em alternativa, pode [configurar a fonte de dados da ODBC](#configure-the-odbc-data-source) conforme detalhado abaixo. 

No artigo, aprende a utilizar o controlador SQL Server ODBC, para que possa ligar-se ao Azure Data Explorer a partir de qualquer aplicação que suporte a ODBC. 

## <a name="prerequisites"></a>Pré-requisitos

Precisa do seguinte:

* [Microsoft ODBC Driver for SQL Server versão 17.2.0.1 ou posterior para](/sql/connect/odbc/download-odbc-driver-for-sql-server) o seu sistema operativo.

## <a name="configure-the-odbc-data-source"></a>Configure a fonte de dados da ODBC

Siga estes passos para configurar uma fonte de dados da ODBC utilizando o controlador ODBC para o Servidor SQL.

1. No Windows, procure *por Fontes de Dados ODBC*e abra a aplicação de ambiente de trabalho DaDBC Data Sources.

1. Selecione **Adicionar**.

    ![Adicionar origem de dados](media/connect-odbc/add-data-source.png)

1. Selecione **ODBC Driver 17 para O QL Server** e, em seguida, **termine**.

    ![Selecione motorista](media/connect-odbc/select-driver.png)

1. Introduza um nome e descrição para a ligação e o cluster a que pretende ligar e, em seguida, selecione **Next**. O URL do cluster deve estar na forma * \<ClusterName\>.\< \>Região .kusto.windows.net*.

    ![Selecione servidor](media/connect-odbc/select-server.png)

1. Selecione **Diretório Ativo Integrado** **e**seguinte .

    ![Diretório Ativo Integrado](media/connect-odbc/active-directory-integrated.png)

1. Selecione a base de dados com os dados da amostra e depois **Seguinte**.

    ![Alterar base de dados por defeito](media/connect-odbc/change-default-database.png)

1. No ecrã seguinte, deixe todas as opções como predefinições e, em seguida, selecione **Terminar**.

1. Selecione **A Fonte de Dados**do Teste .

    ![Fonte de dados de teste](media/connect-odbc/test-data-source.png)

1. Verifique se o teste foi bem sucedido e selecione **OK**. Se o teste não tiver sucesso, verifique os valores que especificou em etapas anteriores e certifique-se de que tem permissões suficientes para se ligar ao cluster.

    ![Teste bem sucedido](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Passos seguintes

* [Ligue-se ao Explorador de Dados Azure a partir de Tableau](tableau.md)