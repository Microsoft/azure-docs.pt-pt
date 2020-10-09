---
title: Utilizar dados do Azure Blockchain Workbench no Microsoft Excel
description: Saiba como carregar e ver os dados do Azure Blockchain Workbench Preview SQL DB no Microsoft Excel.
ms.date: 09/05/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: 4dd941abdded6a93510f4a71119769cd73855bcc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85253550"
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Ver dados do Azure Blockchain Workbench com o Microsoft Excel

Pode utilizar o Microsoft Excel para ver os dados na BD SQL do Azure Blockchain Workbench. Este artigo explica os passos necessários para:

* Ligar à base de dados do Blockchain Workbench a partir do Microsoft Excel
* Observar as vistas e tabelas de base de dados do Blockchain Workbench
* Carregar dados de vistas do Blockchain Workbench para Excel

## <a name="connect-to-the-blockchain-workbench-database"></a>Ligar à base de dados do Blockchain Workbench

Para ligar à base de dados do Blockchain Workbench:

1. Abra o Microsoft Excel.
2. No separador **Dados**, escolha **Obter Dados**.
3. Selecione **A Partir do Azure** e, em seguida, selecione **Da Base de Dados SQL do Azure**.

   ![Ligar à Base de Dados SQL do Azure](./media/data-excel/connect-sql-db.png)

4. Na caixa de diálogo **Base de dados do SQL Server**:

    * Em **Servidor**, introduza o nome do servidor do Blockchain Workbench.
    * Em **Base de dados (opcional)**, introduza o nome da base de dados.

   ![Indicar o servidor de base de dados e a base de dados](./media/data-excel/provide-server-db.png)

5. Na barra de navegação da caixa de diálogo **Base de dados do SQL Server**, selecione **Base de dados**. Introduza o **Nome de utilizador** e a **Palavra-passe** e selecione **Ligar**.

    > [!NOTE]
    > Se estiver a utilizar as credenciais que criou durante o processo de implementação do Azure Blockchain Workbench, o **Nome de utilizador** é `dbadmin`. A **Palavra-passe** é aquela que criou quando implementou o Blockchain Workbench.
    
   ![Fornecer credenciais para aceder à base de dados](./media/data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Observar vistas e tabelas de base de dados

A caixa de diálogo do Navegador do Excel abre depois de ligar à base de dados. Pode utilizar o Navegador para observar as tabelas e vistas na base de dados. As vistas foram concebidas para a elaboração de relatórios e os respetivos nomes têm o prefixo **vw**.

   ![Pré-visualização de uma vista do Navegador do Excel](./media/data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Carregar dados de vistas para um livro do Excel

O exemplo seguinte mostra como pode carregar dados de uma vista para um livro do Excel.

1. Na barra de deslocamento do **Navegador**, selecione a vista **vwContractAction**. A pré-visualização de **vwContractAction** mostra todas as ações relacionadas com um contrato na base de dados do Blockchain Workbench.
2. Selecione **Carregar** para obter todos os dados da vista e colocá-los no seu livro do Excel.

   ![Dados carregados a partir de uma vista](./media/data-excel/view-data.png)

Agora que tem os dados carregados, pode utilizar as funcionalidades do Excel para criar os seus próprios relatórios com os metadados e os dados de transações da base de dados do Azure Blockchain Workbench.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Vistas de base de dados no Azure Blockchain Workbench](database-views.md)