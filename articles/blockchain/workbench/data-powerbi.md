---
title: Utilizar dados do Azure Blockchain Workbench no Microsoft Power BI
description: Saiba como carregar e ver os dados da BD SQL do Azure Blockchain Workbench no Microsoft Power BI.
ms.date: 04/22/2020
ms.topic: how-to
ms.reviewer: sunri
ms.openlocfilehash: 7e0e585ce45616c2402972c725b502f4b704d1cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000152"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Utilizar dados do Azure Blockchain Workbench com o Microsoft Power BI

O Microsoft Power BI fornece a capacidade de gerar facilmente relatórios poderosos a partir de bases de dados DB SQL utilizando o Power BI Desktop e depois publicá-los para [https://www.powerbi.com](https://www.powerbi.com) .

Este artigo contém instruções passo a passo sobre como ligar à Base de Dados SQL do Azure Blockchain Workbench a partir do Power BI Desktop, criar um relatório e implementá-lo no powerbi.com.

## <a name="prerequisites"></a>Pré-requisitos

* Baixar [Power BI Desktop](https://powerbi.microsoft.com/desktop/).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Ligar o Power BI aos dados na Bancada Azure Blockchain

1.  Abra o Power BI Desktop.
2.  Selecione **Obter Dados**.

    ![Obter dados](./media/data-powerbi/get-data.png)
3.  Selecione **SQL Server** na lista de tipos de origens de dados.

4.  Introduza o nome do servidor e da base de dados na caixa de diálogo. Especifique se pretende importar os dados ou realizar uma **DirectQuery**. Selecione **OK**.

    ![Selecionar SQL Server](./media/data-powerbi/select-sql.png)

5.  Introduza as credenciais da base de dados para aceder ao Azure Blockchain Workbench. Selecione **Base de Dados** e introduza as suas credenciais.

    Se estiver a utilizar as credenciais criadas pelo processo de implementação do Azure Blockchain Workbench, o nome de utilizador é **dbadmin** e a palavra-passe é a mesma que forneceu durante a implementação.

    ![Definições da BD SQL](./media/data-powerbi/db-settings.png)

6.  Uma vez ligado à base de dados, a caixa de diálogo **Navegador** apresenta as tabelas e as vistas disponíveis na base de dados. As vistas foram concebidas para a elaboração de relatórios e têm todas o prefixo **vw**.

    ![Captura de ecrã do ambiente de trabalho Power BI com a caixa de diálogo Navigator com vwContractAction selecionada.](./media/data-powerbi/navigator.png)

7.  Selecione as vistas que pretende incluir. Para fins de demonstração, incluímos **a VWContractAction,** que fornece detalhes sobre as ações que têm ocorrido num contrato.

    ![Selecionar vistas](./media/data-powerbi/select-views.png)

Pode agora criar e publicar relatórios tal como faria normalmente com o Power BI.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Vistas de base de dados no Azure Blockchain Workbench](database-views.md)