---
title: Conecte-se a piscina SQL dedicada (anteriormente SQL DW) com VSTS
description: Consulta dedicada piscina SQL (anteriormente SQL DW) em Azure Synapse Analytics com Visual Studio.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2b81ddedbcb254a840e85d41cf9d69c78b149bbd
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121401"
---
# <a name="connect-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Conecte-se a piscina SQL dedicada (anteriormente SQL DW) em Azure Synapse Analytics com Visual Studio e SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Utilize o Visual Studio para consultar uma piscina SQL dedicada (anteriormente SQL DW) dentro de Azure Synapse em apenas alguns minutos. Este método utiliza a extensão SQL Server Data Tools (SSDT) no Visual Studio 2019. 

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar este tutorial, precisa do seguinte:

* Uma piscina SQL dedicada existente (anteriormente SQL DW). Para criar um, consulte [Criar uma piscina SQL dedicada (anteriormente SQL DW)](create-data-warehouse-portal.md).
* SSDT para Visual Studio. Se tem Estúdio Visual, provavelmente já tem SSDT para Estúdio Visual. Para obter instruções e opções de instalação, veja [Installing Visual Studio and SSDT](sql-data-warehouse-install-visual-studio.md) (Instalar o Visual Studio e o SSDT).
* O nome de servidor SQL completamente qualificado. Para encontrar estas informações, consulte [Connect a uma piscina SQL dedicada (anteriormente SQL DW)](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1. Ligue-se à sua piscina SQL dedicada (anteriormente SQL DW)
1. Open Visual Studio 2019.
2. Abra o SQL Server Object Explorer selecionando **View**  >  **SQL Server Object Explorer**.
   
    ![SQL Server Object Explorer](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Clique no ícone **Adicionar SQL Server**.
   
    ![Adicionar SQL Server](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Preencha os campos na janela Ligar ao Servidor.
   
    ![Ligar ao Servidor](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Nome do servidor**. Introduza o **nome do servidor** que identificou anteriormente.
   * **Autenticação.** Selecione **Autenticação do SQL Server** ou **Autenticação Integrada do Active Directory**.
   * **Nome de Utilizador** e **Palavra-passe**. Introduza o nome de utilizador e a palavra-passe, se a Autenticação do SQL Server tiver sido selecionada acima.
   * Clique em **Ligar**.
5. Para explorar, expanda o servidor SQL do Azure. Pode ver as bases de dados associadas ao servidor. Expanda AdventureWorksDW para ver as tabelas na sua base de dados de exemplo.
   
    ![Explorar AdventureWorksDW](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Executar uma consulta de amostra
Agora que foi estabelecida uma ligação à base de dados, vamos escrever uma consulta.

1. Clique com o botão direito do rato na base de dados no SQL Server Object Explorer.
2. Selecione **Nova Consulta**. É aberta uma nova janela de consulta.
   
    ![Nova consulta](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Copie a seguinte consulta T-SQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Executar a consulta clicando na seta verde ou utilize o seguinte atalho: `CTRL` + `SHIFT` + `E` .
   
    ![Executar consulta](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Veja os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.
   
    ![Resultados da consulta](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Passos seguintes
Agora que pode ligar e consultar, tente [visualizar os dados com o Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect).

Para configurar o seu ambiente para a autenticação do Azure Ative Directory, consulte [Authenticate para piscina SQL dedicada (anteriormente SQL DW)](sql-data-warehouse-authentication.md).