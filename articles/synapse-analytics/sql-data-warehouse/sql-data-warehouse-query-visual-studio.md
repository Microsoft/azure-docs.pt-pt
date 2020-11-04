---
title: Conecte-se com VSTS
description: Consulta Azure Synapse Analytics com Visual Studio.
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
ms.openlocfilehash: 59e0aed3ceda369909c6a4aecd03637a82a2dd1b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305381"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Ligue-se ao Azure Synapse Analytics com o Visual Studio e o SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Utilize o Visual Studio para consultar uma piscina SQL dedicada dentro do Azure Synapse em poucos minutos. Este método utiliza a extensão SQL Server Data Tools (SSDT) no Visual Studio 2019. 

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar este tutorial, precisa do seguinte:

* Uma piscina SQL dedicada existente. Para criar um, consulte [Criar uma piscina SQL dedicada.](create-data-warehouse-portal.md)
* SSDT para Visual Studio. Se tem Estúdio Visual, provavelmente já tem SSDT para Estúdio Visual. Para obter instruções e opções de instalação, veja [Installing Visual Studio and SSDT](sql-data-warehouse-install-visual-studio.md) (Instalar o Visual Studio e o SSDT).
* O nome de servidor SQL completamente qualificado. Para encontrar estas informações, consulte [Connect to a adicated SQL pool](../sql/connect-overview.md).

## <a name="1-connect-to-your-dedicated-sql-pool"></a>1. Conecte-se à sua piscina SQL dedicada
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
Agora que pode ligar e consultar, tente [visualizar os dados com o Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

Para configurar o seu ambiente para a autenticação do Azure Ative Directory, consulte [Authenticate para piscina SQL dedicada.](sql-data-warehouse-authentication.md)
