---
title: 'SSMS: Ligar e consultar Synapse SQL'
description: Utilize o Estúdio de Gestão de Servidores SQL (SSMS) para ligar e consultar o Synapse SQL no Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 704da86fd1d816dbf5d6cd9cf67dfee53fce2622
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81423741"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Ligue-se ao Synapse SQL com o SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

Pode utilizar o [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) para se conectar e consultar o Synapse SQL no Azure Synapse Analytics através de recursos de piscina SQL on-demand (pré-visualização) ou SQL. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Ferramentas suportadas para sQL a pedido (pré-visualização)

O SSMS é parcialmente suportado a partir da versão 18.5 com funcionalidades limitadas, tais como ligação e consulta. [O Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) está totalmente suportado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem os seguintes pré-requisitos:  

* [Estúdio de Gestão de Servidores SQL (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* Para piscina SQL, você precisa de um armazém de dados existente. Para criar uma, consulte [Criar uma piscina SQL](../quickstart-create-sql-pool.md). Para a SQL a pedido, já está previsto no seu espaço de trabalho no momento da criação. 
* O nome SQL Server totalmente qualificado. Para encontrar isto, consulte [Connect to Synapse SQL](connect-overview.md).

## <a name="connect"></a>Ligar

### <a name="sql-pool"></a>Conjunto de SQL

Para ligar ao Synapse SQL utilizando piscina SQL, siga estes passos: 

1. Open Sql Server Management Studio (SSMS). 
1. Na caixa de diálogo **Connect to Server,** preencha os campos e, em seguida, selecione **Ligar:** 
  
    ![Ligar ao Servidor](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Nome**do servidor : Introduza o nome do **servidor** previamente identificado.
   * **Autenticação**: Escolha um tipo de autenticação, como autenticação do **servidor SQL** ou autenticação integrada de **diretório ativo.**
   * **Nome e** **Palavra-passe**do utilizador : Introduza o nome de utilizador e a palavra-passe se a Autenticação do Servidor SQL tiver sido selecionada acima.

1. Expanda o seu Servidor Azure SQL no **Object Explorer**. Pode ver as bases de dados associadas ao servidor, como a base de dados AdventureWorksDW. Pode expandir a base de dados para ver as tabelas:
   
    ![Explorar AdventureWorksDW](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="sql-on-demand-preview"></a>SQL on-demand (pré-visualização)

Para ligar ao Synapse SQL utilizando a SQL a pedido, siga estes passos: 

1. Open Sql Server Management Studio (SSMS).
1. Na caixa de diálogo **Connect to Server,** preencha os campos e, em seguida, selecione **Ligar:** 
   
    ![Ligar ao Servidor](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Nome**do servidor : Introduza o nome do **servidor** previamente identificado.
   * **Autenticação**: Escolha um tipo de autenticação, como autenticação do **servidor SQL** ou autenticação integrada do **Diretório Ativo:**
   * **Nome e** **Palavra-passe**do utilizador : Introduza o nome de utilizador e a palavra-passe se a Autenticação do Servidor SQL tiver sido selecionada acima.
   * Clique em **Ligar**.

4. Para explorar, expanda o servidor SQL do Azure. Pode ver as bases de dados associadas ao servidor. Expanda a *demonstração* para ver o conteúdo na sua base de dados de amostras.
   
    ![Explorar AdventureWorksDW](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Executar uma consulta de exemplo

### <a name="sql-pool"></a>Conjunto de SQL

Agora que foi estabelecida uma ligação à base de dados, pode consultar os dados.

1. Clique com o botão direito do rato na base de dados no SQL Server Object Explorer.
2. Selecione **Nova Consulta**. É aberta uma nova janela de consulta.
   
    ![Nova consulta](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Copie esta consulta T-SQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Execute a consulta. Para isso, `Execute` clique ou utilize o `F5`seguinte atalho: .
   
    ![Executar consulta](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Veja os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.
   
    ![Resultados da consulta](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="sql-on-demand"></a>SQL a pedido

Agora que estabeleceu uma ligação à base de dados, pode consultar os dados.

1. Clique com o botão direito do rato na base de dados no SQL Server Object Explorer.
2. Selecione **Nova Consulta**. É aberta uma nova janela de consulta.
   
    ![Nova consulta](./media/get-started-ssms/new-query.png)
3. Copie a seguinte consulta T-SQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Execute a consulta. Para isso, `Execute` clique ou utilize o `F5`seguinte atalho: .
   
    ![Executar consulta](./media/get-started-ssms/execute-query.png)
5. Veja os resultados da consulta. Neste exemplo, a vista usPopulationView tem 3664512 linhas.
   
    ![Resultados da consulta](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Passos seguintes
Agora que pode ligar e consultar, tente [visualizar os dados com O Power BI](get-started-power-bi-professional.md).

Para configurar o seu ambiente para autenticação de Diretório Ativo Azure, consulte [Authenticate to Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

