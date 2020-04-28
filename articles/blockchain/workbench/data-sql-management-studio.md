---
title: Dados da bancada de trabalho da Query Azure Blockchain utilizando o Estúdio de Gestão de Servidores SQL
description: Saiba como ligar à Base de Dados SQL do Azure Blockchain Workbench no SQL Server Management Studio.
ms.date: 11/20/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
ms.openlocfilehash: f87d1880c90202fa26b0477e3b4dfbed5965bb82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74326029"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Utilizar os dados do Azure Blockchain Workbench com o SQL Server Management Studio

O Microsoft SQL Server Management Studio fornece a capacidade de escrever e testar rapidamente consultas contra o SQL DB da Bancada de Trabalho da Azure Blockchain. Esta secção contém uma passagem passo a passo de como ligar-se à Base de Dados SQL da Bancada de Trabalho da Azure Blockchain a partir do Estúdio de Gestão de Servidores SQL.

## <a name="prerequisites"></a>Pré-requisitos

* Transfira o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Ligar o SQL Server Management Studio aos dados no Azure Blockchain Workbench

1. Abra o SQL Server Management Studio e selecione **Ligar**.
2. Selecione **Motor de Base de Dados**.

    ![Motor de base de dados](./media/data-sql-management-studio/database-engine.png)

3. Na caixa de diálogo **Ligar ao Servidor**, introduza o nome do servidor e as credenciais da base de dados.

    Se estiver a utilizar as credenciais criadas pelo processo de implementação do Azure Blockchain Workbench, o nome de utilizador é **dbadmin** e a palavra-passe é a mesma que forneceu durante a implementação.

    ![Introduzir as credenciais de SQL](./media/data-sql-management-studio/sql-creds.png)

   1. O SQL Server Management Studio apresenta a lista de bases de dados, vistas de base de dados e procedimentos armazenados na base de dados do Azure Blockchain Workbench.

      ![Lista de bases de dados](./media/data-sql-management-studio/db-list.png)

5. Para ver os dados associados a qualquer uma das vistas de base de dados, pode gerar automaticamente uma instrução select através dos seguintes passos.
6. Clique em qualquer uma das vistas da base de dados no Object Explorer.
7. Selecione **Vista de script como**.
8. Selecione **SELECT para**.
9. Selecione **Nova Janela do Editor de Consultas**.
10. É possível criar uma nova consulta ao selecionar **Nova Consulta**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Vistas de base de dados no Azure Blockchain Workbench](database-views.md)
