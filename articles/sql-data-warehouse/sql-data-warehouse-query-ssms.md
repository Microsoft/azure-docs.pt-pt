---
title: Ligar ao armazém de dados SQL do Azure - SSMS | Documentos da Microsoft
description: Utilize o SQL Server Management Studio (SSMS) para ligar e consultar o Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 64ea7c175b733f974eba6c081ee2c98814cbcda2
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873711"
---
# <a name="connect-to-sql-data-warehouse-with-sql-server-management-studio-ssms"></a>Ligar ao SQL Data Warehouse com o SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Utilize o SQL Server Management Studio (SSMS) para ligar e consultar o Azure SQL Data Warehouse. 

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar este tutorial, precisa do seguinte:

* Um SQL Data Warehouse existente. Para criar um, veja [Create a SQL Data Warehouse][Create a SQL Data Warehouse] (Criar um armazém do SQL Data Warehouse).
* SQL Server Management Studio (SSMS) instalado. [Instalar o SSMS] [ Install SSMS] gratuitamente se não o tiver.
* O nome de servidor SQL completamente qualificado. Para o descobrir, veja [Ligar ao SQL Data Warehouse][Connect to SQL Data Warehouse].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Ligar ao seu SQL Data Warehouse
1. Abra o SQL Server Management Studio.
2. Abra o Explorador de objetos. Para tal, selecione **arquivo** > **ligar Object Explorer**.
   
    ![SQL Server Object Explorer][1]
3. Preencha os campos na janela Ligar ao Servidor.
   
    ![Ligar ao Servidor][2]
   
   * **Nome do servidor**. Introduza o **nome do servidor** que identificou anteriormente.
   * **Autenticação**. Selecione **Autenticação do SQL Server** ou **Autenticação Integrada do Active Directory**.
   * **Nome de Utilizador** e **Palavra-passe**. Introduza o nome de utilizador e a palavra-passe, se a Autenticação do SQL Server tiver sido selecionada acima.
   * Clique em **Ligar**.
4. Para explorar, expanda o servidor SQL do Azure. Pode ver as bases de dados associadas ao servidor. Expanda AdventureWorksDW para ver as tabelas na sua base de dados de exemplo.
   
    ![Explorar AdventureWorksDW][3]

## <a name="2-run-a-sample-query"></a>2. Executar uma consulta de exemplo
Agora que foi estabelecida uma ligação à base de dados, vamos escrever uma consulta.

1. Clique com o botão direito do rato na base de dados no SQL Server Object Explorer.
2. Selecione **Nova Consulta**. É aberta uma nova janela de consulta.
   
    ![Nova consulta][4]
3. Copie esta consulta TSQL para a janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Execute a consulta. Para tal, clique em `Execute` ou utilize o atalho seguinte: `F5`.
   
    ![Executar consulta][5]
5. Veja os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.
   
    ![Resultados da consulta][6]

## <a name="next-steps"></a>Passos Seguintes
Agora que pode ligar e fazer consultas, experimente [visualizar os dados com o PowerBI][visualizing the data with PowerBI].

Para configurar o seu ambiente para a autenticação do Azure Active Directory, veja [Authenticate to SQL Data Warehouse][Authenticate to SQL Data Warehouse] (Autenticação no SQL Data Warehouse).

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md 

<!--Other-->
[Azure portal]: https://portal.azure.com
[Install SSMS]: https://msdn.microsoft.com/library/hh213248.aspx


<!--Image references-->

[1]: media/sql-data-warehouse-query-ssms/connect-object-explorer.png
[2]: media/sql-data-warehouse-query-ssms/connect-object-explorer1.png
[3]: media/sql-data-warehouse-query-ssms/explore-tables.png
[4]: media/sql-data-warehouse-query-ssms/new-query.png
[5]: media/sql-data-warehouse-query-ssms/execute-query.png
[6]: media/sql-data-warehouse-query-ssms/results.png
