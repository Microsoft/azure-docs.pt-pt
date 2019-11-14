---
title: Conectar-se ao Azure Data Explorer com o ODBC
description: Neste artigo, você aprenderá a configurar uma conexão ODBC (conectividade aberta de banco de dados) com o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034020"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>Conectar-se ao Azure Data Explorer com o ODBC

[ODBC](/sql/odbc/reference/odbc-overview)(Open Database Connectivity) é uma API (interface de programação de aplicativo) amplamente aceita para acesso ao banco de dados. Use o ODBC para se conectar ao Azure Data Explorer de aplicativos que não têm um conector dedicado.

Nos bastidores, os aplicativos chamam funções na interface ODBC, que são implementados em módulos específicos de banco de dados chamados *drivers*. O Azure Data Explorer dá suporte a um subconjunto do protocolo[MS-TDS](/azure/kusto/api/tds/)(SQL Server Communication Protocol), para que ele possa usar o driver ODBC para SQL Server.

Usando o vídeo a seguir, você pode aprender a criar uma conexão ODBC. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Como alternativa, você pode [Configurar a fonte de dados ODBC](#configure-the-odbc-data-source) conforme detalhado abaixo. 

Neste artigo, você aprenderá a usar o driver ODBC SQL Server, para que possa se conectar ao Azure Data Explorer de qualquer aplicativo que ofereça suporte a ODBC. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisa do seguinte:

* [Microsoft ODBC driver for SQL Server versão 17.2.0.1 ou posterior](/sql/connect/odbc/download-odbc-driver-for-sql-server) para seu sistema operacional.

## <a name="configure-the-odbc-data-source"></a>Configurar a fonte de dados ODBC

Siga estas etapas para configurar uma fonte de dados ODBC usando o driver ODBC para SQL Server.

1. No Windows, pesquise *fontes de dados ODBC*e abra o aplicativo de área de trabalho fontes de dados ODBC.

1. Selecione **Adicionar**.

    ![Adicionar origem de dados](media/connect-odbc/add-data-source.png)

1. Selecione o **driver ODBC 17 para SQL Server** , em seguida, **concluir**.

    ![Selecionar driver](media/connect-odbc/select-driver.png)

1. Insira um nome e uma descrição para a conexão e o cluster ao qual você deseja se conectar e, em seguida, selecione **Avançar**. A URL do cluster deve estar no formato *\<clustername\>.\<região\>. Kusto.Windows.net*.

    ![Selecionar servidor](media/connect-odbc/select-server.png)

1. Selecione **Active Directory integrado** e depois **Avançar**.

    ![Active Directory integrado](media/connect-odbc/active-directory-integrated.png)

1. **Em seguida, selecione**o banco de dados com os exemplos de dado.

    ![Alterar banco de dados padrão](media/connect-odbc/change-default-database.png)

1. Na próxima tela, deixe todas as opções como padrões e selecione **concluir**.

1. Selecione **testar fonte de dados**.

    ![Fonte de dados de teste](media/connect-odbc/test-data-source.png)

1. Verifique se o teste foi bem-sucedido e selecione **OK**. Se o teste não tiver sido bem sucedido, verifique os valores que você especificou nas etapas anteriores e verifique se você tem permissões suficientes para se conectar ao cluster.

    ![Teste bem-sucedido](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Passos seguintes

* [Conectar-se ao Azure Data Explorer do tableau](tableau.md)