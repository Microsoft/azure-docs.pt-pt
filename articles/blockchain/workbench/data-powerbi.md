---
title: Utilizar dados do Azure Blockchain Workbench no Microsoft Power BI
description: Saiba como carregar e ver os dados da BD SQL do Azure Blockchain Workbench no Microsoft Power BI.
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 6e1f160c3563a280548c74ebe84f30bf08945c3f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324801"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Utilizar dados do Azure Blockchain Workbench com o Microsoft Power BI

O Microsoft Power BI permite gerir facilmente relatórios poderosos a partir de bases de dados de base SQL com o Power BI Desktop e, em seguida, publicá-los em [https://www.powerbi.com](https://www.powerbi.com).

Este artigo contém instruções passo a passo sobre como ligar à Base de Dados SQL do Azure Blockchain Workbench a partir do Power BI Desktop, criar um relatório e implementá-lo no powerbi.com.

## <a name="prerequisites"></a>Pré-requisitos

* Baixar [Power bi desktop](https://aka.ms/pbidesktopstore).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Conectando Power BI a dados no Azure Blockchain Workbench

1.  Abra o Power BI Desktop.
2.  Selecione **Obter Dados**.

    ![Obter dados](./media/data-powerbi/get-data.png)
3.  Selecione **SQL Server** na lista de tipos de origens de dados.

4.  Introduza o nome do servidor e da base de dados na caixa de diálogo. Especifique se pretende importar os dados ou realizar uma **DirectQuery**. Selecione **OK**.

    ![Selecionar o SQL Server](./media/data-powerbi/select-sql.png)

5.  Introduza as credenciais da base de dados para aceder ao Azure Blockchain Workbench. Selecione **Base de Dados** e introduza as suas credenciais.

    Se estiver a utilizar as credenciais criadas pelo processo de implementação do Azure Blockchain Workbench, o nome de utilizador é **dbadmin** e a palavra-passe é a mesma que forneceu durante a implementação.

    ![Definições da BD SQL](./media/data-powerbi/db-settings.png)

6.  Uma vez ligado à base de dados, a caixa de diálogo **Navegador** apresenta as tabelas e as vistas disponíveis na base de dados. As vistas foram concebidas para a elaboração de relatórios e têm todas o prefixo **vw**.

    ![Navegador](./media/data-powerbi/navigator.png)

7.  Selecione as vistas que pretende incluir. Para fins de demonstração, incluímos **vwContractAction**, que fornece detalhes sobre as ações que ocorreram em um contrato.

    ![Selecionar vistas](./media/data-powerbi/select-views.png)

Pode agora criar e publicar relatórios tal como faria normalmente com o Power BI.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Vistas de base de dados no Azure Blockchain Workbench](database-views.md)