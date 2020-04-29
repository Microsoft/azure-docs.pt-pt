---
title: Ligue-se e consulta Synapse SQL com Estúdio Visual e SSDT
description: Use o Visual Studio para consultar a piscina SQL utilizando o Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428566"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Ligue-se ao Synapse SQL com O Estúdio Visual e SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Use o Visual Studio para consultar a piscina SQL utilizando o Azure Synapse Analytics. Este método utiliza a extensão SQL Server Data Tools (SSDT) no Visual Studio 2019. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Ferramentas suportadas para sQL a pedido (pré-visualização)

O estúdio visual não é atualmente suportado para sQL on-demand (pré-visualização). No entanto, o Azure Data Studi (pré-visualização)o é uma ferramenta totalmente suportada. O SQL Server Management Studio é parcialmente suportado a partir da versão 18.4 e tem funcionalidades limitadas, tais como ligação e consulta.

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar este tutorial, é necessário ter os seguintes componentes:

* Uma piscina SQL existente. Se não tiver uma, consulte [Criar uma piscina SQL](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para completar este pré-requisito.
* SSDT para Visual Studio. Se tem o Estúdio Visual, provavelmente já tem este componente. Para obter instruções e opções de instalação, veja [Installing Visual Studio and SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (Instalar o Visual Studio e o SSDT).
* O nome de servidor SQL completamente qualificado. Para encontrar isto, consulte [Connect to SQL pool](connect-overview.md).

## <a name="1-connect-to-sql-pool"></a>1. Ligar à piscina SQL
1. Open Visual Studio 2019.
2. Abra o Explorador de Objetos do Servidor SQL. Para isso, selecione **Ver** > Explorador de**Objetos do Servidor SQL**.
   
    ![SQL Server Object Explorer](./media/get-started-visual-studio/open-ssdt.png)
3. Clique no ícone **Adicionar SQL Server**.
   
    ![Adicionar SQL Server](./media/get-started-visual-studio/add-server.png)
4. Preencha os campos na janela Ligar ao Servidor.
   
    ![Ligar ao Servidor](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Nome**do servidor : Introduza o nome do **servidor** previamente identificado.
   * **Autenticação**: Selecione Autenticação do **Servidor SQL** ou Autenticação Integrada do **Diretório Ativo:**
   * **Nome e** **Palavra-passe**do utilizador : Introduza o nome de utilizador e a palavra-passe se a Autenticação do Servidor SQL tiver sido selecionada acima.
   * Clique em **Ligar**.
5. Para explorar, expanda o servidor SQL do Azure. Pode ver as bases de dados associadas ao servidor. Expanda AdventureWorksDW para ver as tabelas na sua base de dados de exemplo.
   
    ![Explorar AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Executar uma consulta de amostra
Agora que foi estabelecida uma ligação à sua base de dados, escreva uma consulta.

1. Clique com o botão direito do rato na base de dados no SQL Server Object Explorer.
2. Selecione **Nova Consulta**. É aberta uma nova janela de consulta.
   
    ![Nova consulta](./media/get-started-visual-studio/new-query2.png)
3. Copie a seguinte consulta T-SQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Execute a consulta. Para tal, clique na seta verde ou utilize o atalho seguinte: `CTRL`+`SHIFT`+`E`.
   
    ![Executar consulta](./media/get-started-visual-studio/run-query.png)
5. Veja os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.
   
    ![Resultados da consulta](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Passos seguintes
Agora que pode ligar e consultar, tente [visualizar os dados com O Power BI](get-started-power-bi-professional.md).
Para configurar o seu ambiente para autenticação de Diretório Ativo Azure, consulte [Authenticate to SQL pool](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 