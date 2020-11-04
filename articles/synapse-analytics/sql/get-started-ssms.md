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
ms.openlocfilehash: 4337d6bb108042a909250b3d87d13ab60357cfec
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311123"
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

Pode utilizar [o SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) para ligar e consultar o Synapse SQL em Azure Synapse Analytics através de um pool SQL sem servidor (pré-visualização) ou recursos dedicados do pool SQL. 

### <a name="supported-tools-for-serverless-sql-pool-preview"></a>Ferramentas suportadas para piscina SQL sem servidor (pré-visualização)

[O Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) está totalmente suportado a partir da versão 1.18.0. O SSMS é parcialmente suportado a partir da versão 18.5, pode usá-lo apenas para ligar e consultar.

> [!NOTE]
> Se o login da AAD tiver a ligação aberta durante mais de 1 hora no momento da execução da consulta, qualquer consulta que dependa do AAD falhará. Isto inclui consulta de armazenamento utilizando o transitário AAD e declarações que interagem com a AAD (como CREATE EXTERNAL PROVIDER). Isto afeta todas as ferramentas que mantêm a ligação aberta, como no editor de consultas em SSMS e ADS. As ferramentas que abrem uma nova ligação para executar consultas não são afetadas, como o Synapse Studio.
> Pode reiniciar sSMS ou ligar e desligar em ADS para mitigar este problema. .
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de ter os seguintes pré-requisitos:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* Para uma piscina SQL dedicada, você precisa de um armazém de dados existente. Para criar um, consulte [Criar uma piscina SQL dedicada.](../quickstart-create-sql-pool-portal.md) Para a piscina SQL sem servidor, um já está a provisionado, chamado Built-in, no seu espaço de trabalho na hora da criação. 
* O nome do Servidor SQL totalmente qualificado. Para encontrar este nome, consulte [Connect to Synapse SQL](connect-overview.md).

## <a name="connect"></a>Ligar

### <a name="dedicated-sql-pool"></a>Piscina SQL dedicada

Para ligar ao Synapse SQL utilizando uma piscina SQL dedicada, siga estes passos: 

1. Open SQL Server Management Studio (SSMS). 
1. Na caixa de diálogo **'Ligar ao Servidor',** preencha os campos e, em seguida, selecione **Connect** : 
  
    ![Ligue-se ao Servidor 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Nome do servidor** : Introduza o nome do **servidor** previamente identificado.
   * **Autenticação** : Escolha um tipo de autenticação, como autenticação do **servidor SQL** ou **autenticação integrada do Diretório Ativo.**
   * **Nome do utilizador** e **palavra-passe** : Introduza o nome de utilizador e a palavra-passe se a autenticação do servidor SQL tiver sido selecionada acima.

1. Expanda o seu Servidor Azure SQL no **Explorador de Objetos**. Pode ver as bases de dados associadas ao servidor, como a base de dados AdventureWorksDW da amostra. Pode expandir a base de dados para ver as tabelas:
   
    ![Explore AdventureWorksDW 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="serverless-sql-pool-preview"></a>Piscina SQL sem servidor (pré-visualização)

Para ligar ao Sinaapse SQL utilizando a piscina SQL sem servidor, siga estes passos: 

1. Open SQL Server Management Studio (SSMS).
1. Na caixa de diálogo **'Ligar ao Servidor',** preencha os campos e, em seguida, selecione **Connect** : 
   
    ![Ligar ao Servidor 2](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Nome do servidor** : Introduza o nome do **servidor** previamente identificado.
   * **Autenticação** : Escolha um tipo de autenticação, como autenticação do **servidor SQL** ou **autenticação integrada do Diretório Ativo:**
   * **Nome do utilizador** e **palavra-passe** : Introduza o nome de utilizador e a palavra-passe se a autenticação do servidor SQL tiver sido selecionada acima.
   * Selecione **Connect** (Ligar).

4. Para explorar, expanda o servidor SQL do Azure. Pode ver as bases de dados associadas ao servidor. Expanda a *demonstração* para ver o conteúdo na sua base de dados de amostras.
   
    ![Explore AdventureWorksDW 2](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Executar uma consulta de exemplo

### <a name="dedicated-sql-pool"></a>Piscina SQL dedicada

Agora que foi estabelecida uma ligação de base de dados, pode consultar os dados.

1. Clique com o botão direito do rato na base de dados no SQL Server Object Explorer.
2. Selecione **Nova Consulta**. É aberta uma nova janela de consulta.
   
    ![Nova consulta 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Copie a seguinte consulta T-SQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Executar a consulta selecionando `Execute` ou utilizar o seguinte atalho: `F5` .
   
    ![Consulta de execução 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Veja os resultados da consulta. No exemplo seguinte, a tabela FactInternetSales conta com 60398 linhas.
   
    ![Resultados de consulta 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="serverless-sql-pool"></a>Piscina SQL sem servidor

Agora que estabeleceu uma ligação de base de dados, pode consultar os dados.

1. Clique com o botão direito do rato na base de dados no SQL Server Object Explorer.
2. Selecione **Nova Consulta**. É aberta uma nova janela de consulta.
   
    ![Nova consulta 2](./media/get-started-ssms/new-query.png)
3. Copie a seguinte consulta T-SQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Executar a consulta selecionando `Execute` ou utilizar o seguinte atalho: `F5` .
   
    ![Consulta de execução 2](./media/get-started-ssms/execute-query.png)
5. Veja os resultados da consulta. Neste exemplo, a vista usPopulationView tem 3664512 linhas.
   
    ![Resultados de consulta 2](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Passos seguintes
Agora que pode ligar e consultar, tente [visualizar os dados com o Power BI](get-started-power-bi-professional.md).

Para configurar o seu ambiente para a autenticação do Azure Ative Directory, consulte [Authenticate to Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

