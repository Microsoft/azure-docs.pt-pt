---
title: Conecte e consulta Synapse SQL com Visual Studio e SSDT
description: Utilize o Visual Studio para consultar a piscina SQL dedicada utilizando o Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 098256c3174f5a737bec4f6a62cb1d2af99e6f4f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311075"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Conecte-se ao SYNapse SQL com o Visual Studio e o SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Utilize o Visual Studio para consultar a piscina SQL dedicada utilizando o Azure Synapse Analytics. Este método utiliza a extensão SQL Server Data Tools (SSDT) no Visual Studio 2019. 

> [!NOTE]
> A piscina SQL sem servidor (pré-visualização) não é suportada por SSDT.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este tutorial, precisa de ter os seguintes componentes:

* Uma piscina SQL dedicada existente. Se não tiver uma, consulte [criar uma piscina SQL dedicada](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para completar este pré-requisito.
* SSDT para Visual Studio. Se tem o Visual Studio, provavelmente já tem este componente. Para obter instruções e opções de instalação, veja [Installing Visual Studio and SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Instalar o Visual Studio e o SSDT).
* O nome de servidor SQL completamente qualificado. Para encontrar este nome do servidor, consulte [Connect para uma piscina SQL dedicada.](connect-overview.md)

## <a name="1-connect-to-a-dedicated-sql-pool"></a>1. Conecte-se a uma piscina SQL dedicada
1. Open Visual Studio 2019.
2. Abra o SQL Server Object Explorer selecionando **View**  >  **SQL Server Object Explorer**.
   
    ![SQL Server Object Explorer](./media/get-started-visual-studio/open-ssdt.png)
3. Clique no ícone **Adicionar SQL Server**.
   
    ![Adicionar SQL Server](./media/get-started-visual-studio/add-server.png)
4. Preencha os campos na janela Ligar ao Servidor.
   
    ![Ligar ao Servidor](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Nome do servidor** : Introduza o nome do **servidor** previamente identificado.
   * **Autenticação** : Selecione A autenticação do **servidor SQL** ou **a autenticação integrada do Diretório Ativo** :
   * **Nome do utilizador** e **palavra-passe** : Introduza o nome de utilizador e a palavra-passe se a autenticação do servidor SQL tiver sido selecionada acima.
   * Clique em **Ligar**.
5. Para explorar, expanda o servidor SQL do Azure. Pode ver as bases de dados associadas ao servidor. Expanda AdventureWorksDW para ver as tabelas na sua base de dados de exemplo.
   
    ![Explorar AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Executar uma consulta de amostra
Agora que foi estabelecida uma ligação à sua base de dados, irá escrever uma consulta.

1. Clique com o botão direito do rato na base de dados no SQL Server Object Explorer.
2. Selecione **Nova Consulta**. É aberta uma nova janela de consulta.
   
    ![Nova consulta](./media/get-started-visual-studio/new-query2.png)
3. Copie a seguinte consulta T-SQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Executar a consulta clicando na seta verde ou utilize o seguinte atalho: `CTRL` + `SHIFT` + `E` .
   
    ![Executar consulta](./media/get-started-visual-studio/run-query.png)
5. Veja os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.
   
    ![Resultados da consulta](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Passos seguintes
Agora que pode ligar e consultar, tente [visualizar os dados com o Power BI](get-started-power-bi-professional.md).
Para configurar o seu ambiente para a autenticação do Azure Ative Directory, consulte [Authenticate para piscina SQL dedicada.](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
 