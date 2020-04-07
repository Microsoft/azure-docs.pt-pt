---
title: Ligar com o SSMS
description: Utilize o Estúdio de Gestão de Servidores SQL (SSMS) para ligar e consultar o Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0d65ea9b684439cab384bbaa09fcdef1302b6bcf
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743856"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Ligue-se ao Azure Synapse Analytics com o SQL Server Management Studio (SSMS)

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Utilize o SQL Server Management Studio (SSMS) para ligar e consultar um armazém de dados dentro do Azure Synapse.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este tutorial, precisa do seguinte:

* Uma piscina SQL existente. Para criar uma, consulte [Criar uma piscina SQL](create-data-warehouse-portal.md).
* Estúdio de Gestão de Servidores SQL (SSMS) instalado. [Baixe gratuitamente o SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) se ainda não o tiver.
* O nome de servidor SQL completamente qualificado. Para encontrar esta informação, consulte [Connect to SQL pool](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-sql-pool"></a>1. Ligue-se à sua piscina SQL

1. Abra o SQL Server Management Studio.
2. Abra o Object Explorer selecionando o Explorador de**Objetos de Ligação**de **Ficheiros** > .

    ![SQL Server Object Explorer](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Preencha os campos na janela Ligar ao Servidor.

   ![Ligar ao Servidor](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * Nome do **servidor.** Introduza o **nome do servidor** que identificou anteriormente.
   * **Autenticação**. Selecione **Autenticação do SQL Server** ou **Autenticação Integrada do Active Directory**.
   * **Nome de Utilizador** e **Palavra-passe**. Introduza o nome de utilizador e a palavra-passe, se a Autenticação do SQL Server tiver sido selecionada acima.
   * Clique em **Ligar**.
4. Para explorar, expanda o servidor SQL do Azure. Pode ver as bases de dados associadas ao servidor. Expanda AdventureWorksDW para ver as tabelas na sua base de dados de exemplo.

   ![Explorar AdventureWorksDW](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. Executar uma consulta de amostra

Agora que foi estabelecida uma ligação à base de dados, vamos escrever uma consulta.

1. Clique com o botão direito do rato na base de dados no SQL Server Object Explorer.
2. Selecione **Nova Consulta**. É aberta uma nova janela de consulta.

   ![Nova consulta](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Copie a seguinte consulta T-SQL na janela de consulta:

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. Executar a consulta clicando `Execute` ou use `F5`o seguinte atalho: .

   ![Executar consulta](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Veja os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.

   ![Resultados da consulta](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Passos seguintes

Agora que pode ligar e consultar, tente [visualizar os dados com O Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md). Para configurar o seu ambiente para autenticação de Diretório Ativo Azure, consulte [Authenticate to SQL pool](sql-data-warehouse-authentication.md).
