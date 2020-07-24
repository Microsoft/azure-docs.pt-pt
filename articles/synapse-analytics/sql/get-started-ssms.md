---
title: Conecte-se ao SQL Synapse com o SQL Server Management Studio (SSMS)
description: Utilize o SQL Server Management Studio (SSMS) para ligar e consultar o Synapse SQL em Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: dc467eebbd7346777765af7143d13c76627ab648
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077712"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Conecte-se ao SQL Synapse com o SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

Pode utilizar [o SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) para ligar e consultar o Synapse SQL em Azure Synapse Analytics através de recursos de piscina SQL on-demand (pré-visualização) ou SQL pool. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Ferramentas suportadas para SQL on-demand (pré-visualização)

[O Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) está totalmente suportado a partir da versão 1.18.0. O SSMS é parcialmente suportado a partir da versão 18.5, pode usá-lo apenas para ligar e consultar.

> [!NOTE]
> Se o login da AAD tiver a ligação aberta durante mais de 1 hora no momento da execução da consulta, qualquer consulta que dependa do AAD falhará. Isto inclui consulta de armazenamento utilizando o transitário AAD e declarações que interagem com a AAD (como CREATE EXTERNAL PROVIDER). Isto afeta todas as ferramentas que mantêm a ligação aberta, como no editor de consultas em SSMS e ADS. As ferramentas que abrem uma nova ligação para executar consultas não são afetadas, como o Synapse Studio.
> Pode reiniciar sSMS ou ligar e desligar em ADS para mitigar este problema. .
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de ter os seguintes pré-requisitos:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* Para piscina SQL, você precisa de um armazém de dados existente. Para criar um, consulte [Criar uma piscina SQL.](../quickstart-create-sql-pool-portal.md) Para o SQL a pedido, um já está a provisionado no seu espaço de trabalho no momento da criação. 
* O nome do Servidor SQL totalmente qualificado. Para encontrar isto, consulte [Connect to Synapse SQL](connect-overview.md).

## <a name="connect"></a>Ligar

### <a name="sql-pool"></a>Conjunto de SQL

Para ligar ao Sinaapse SQL utilizando a piscina SQL, siga estes passos: 

1. Open SQL Server Management Studio (SSMS). 
1. Na caixa de diálogo **'Ligar ao Servidor',** preencha os campos e, em seguida, selecione **Connect**: 
  
    ![Ligar ao Servidor](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Nome do servidor**: Introduza o nome do **servidor** previamente identificado.
   * **Autenticação**: Escolha um tipo de autenticação, como autenticação do **servidor SQL** ou **autenticação integrada do Diretório Ativo.**
   * **Nome do utilizador** e **palavra-passe**: Introduza o nome de utilizador e a palavra-passe se a autenticação do servidor SQL tiver sido selecionada acima.

1. Expanda o seu Servidor Azure SQL no **Explorador de Objetos**. Pode ver as bases de dados associadas ao servidor, como a base de dados AdventureWorksDW da amostra. Pode expandir a base de dados para ver as tabelas:
   
    ![Explorar AdventureWorksDW](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="sql-on-demand-preview"></a>SQL on demand (pré-visualização)

Para ligar ao SQL synapse utilizando SQL a pedido, siga estes passos: 

1. Open SQL Server Management Studio (SSMS).
1. Na caixa de diálogo **'Ligar ao Servidor',** preencha os campos e, em seguida, selecione **Connect**: 
   
    ![Ligar ao Servidor](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Nome do servidor**: Introduza o nome do **servidor** previamente identificado.
   * **Autenticação**: Escolha um tipo de autenticação, como autenticação do **servidor SQL** ou **autenticação integrada do Diretório Ativo:**
   * **Nome do utilizador** e **palavra-passe**: Introduza o nome de utilizador e a palavra-passe se a autenticação do servidor SQL tiver sido selecionada acima.
   * Clique em **Ligar**.

4. Para explorar, expanda o servidor SQL do Azure. Pode ver as bases de dados associadas ao servidor. Expanda a *demonstração* para ver o conteúdo na sua base de dados de amostras.
   
    ![Explorar AdventureWorksDW](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Executar uma consulta de exemplo

### <a name="sql-pool"></a>Conjunto de SQL

Agora que foi estabelecida uma ligação de base de dados, pode consultar os dados.

1. Clique com o botão direito do rato na base de dados no SQL Server Object Explorer.
2. Selecione **Nova Consulta**. É aberta uma nova janela de consulta.
   
    ![Nova consulta](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Copie esta consulta T-SQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Execute a consulta. Para isso, clique `Execute` ou utilize o seguinte atalho: `F5` .
   
    ![Executar consulta](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Veja os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.
   
    ![Resultados da consulta](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="sql-on-demand"></a>SQL a pedido

Agora que estabeleceu uma ligação de base de dados, pode consultar os dados.

1. Clique com o botão direito do rato na base de dados no SQL Server Object Explorer.
2. Selecione **Nova Consulta**. É aberta uma nova janela de consulta.
   
    ![Nova consulta](./media/get-started-ssms/new-query.png)
3. Copie a seguinte consulta T-SQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Execute a consulta. Para isso, clique `Execute` ou utilize o seguinte atalho: `F5` .
   
    ![Executar consulta](./media/get-started-ssms/execute-query.png)
5. Veja os resultados da consulta. Neste exemplo, a vista usPopulationView tem 3664512 linhas.
   
    ![Resultados da consulta](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Passos seguintes
Agora que pode ligar e consultar, tente [visualizar os dados com o Power BI](get-started-power-bi-professional.md).

Para configurar o seu ambiente para a autenticação do Azure Ative Directory, consulte [Authenticate to Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

