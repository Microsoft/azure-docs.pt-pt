---
title: Tutorial - Connect Azure Analysis Services with Power BI Desktop [ Microsoft Docs
author: minewiskan
description: Aprenda a obter um nome de servidor de Serviços de Análise a partir do portal Azure e, em seguida, ligue-se ao servidor utilizando o Power BI Desktop.
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 4d8c753f06e58fd1cce1c55eca213637cb70e436
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73572322"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>Tutorial: Ligar com o Power BI Desktop

Neste tutorial, utiliza o Power BI Desktop para ligar à base de dados do modelo de exemplo adventureworks no seu servidor. As tarefas que concluiu simulam uma ligação de utilizador típica ao modelo e criação de um relatório básico a partir de dados do modelo.

> [!div class="checklist"]
> * Obter o nome do seu servidor a partir do portal
> * Ligar com o Power BI Desktop
> * Criar um relatório básico

## <a name="prerequisites"></a>Pré-requisitos

- [Adicionar a base dados do modelo de exemplo adventureworks](../analysis-services-create-sample-model.md) ao seu servidor.
- Tenha [*permissões*](../analysis-services-server-admins.md) de leitura para a base de dados do modelo de exemplo adventureworks.
- [Instale o Power BI Desktop mais recente](https://powerbi.microsoft.com/desktop).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Neste tutorial, você canta no portal para obter apenas o nome do servidor. Normalmente, os utilizadores iriam obter o nome do servidor a partir do administrador do servidor.

Inicie sessão no [portal](https://portal.azure.com/).

## <a name="get-server-name"></a>Obter nome do servidor
Para ligar ao seu servidor do Power BI Desktop, primeiro precisa do nome do servidor. Pode obter o nome do servidor a partir do portal.

No **portal do Azure** > servidor > **Descrição geral** > **Nome do servidor**, copie o nome do servidor.
   
   ![Obter o nome do servidor no Azure](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Ligar no Power BI Desktop

1. No Power BI Desktop, clique em **Obter dados** > **Azure** > **Azure Analysis Services**base de dados .

   ![Ligar em Obter dados](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. No **Servidor**, cole o nome do servidor e, em seguida, na **Base de dados**, introduza **adventureworks** e clique em **OK**.

   ![Especificar a base de dados e o modelo do servername](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. Quando lhe for pedido, introduza as suas credenciais. A conta que introduzir tem de ter, pelo menos, permissões de leitura para a base de dados do modelo de exemplo adventureworks.

    O modelo adventureworks abre-se no Power BI Desktop com um relatório em branco na vista de Relatório. A lista **Campos** apresenta todos os objetos de modelo não oculto. O estado de ligação é apresentado no canto inferior direito.

4. Em **VISUALIZAÇÕES**, selecione **Gráfico de Barras Agrupadas** e, em seguida, clique em **Formato** (ícone de rolo de pintura) e ative as **Etiquetas de dados**. 

   ![Visualizações](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. Na tabela **FIELDS** > **Internet Sales,** selecione **Internet Sales Total** and **Margin** measures. Na tabela **Categoria do Produto**, selecione **Nome da Categoria de Produto**.

   ![Concluir relatório](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    Dispense alguns minutos para explorar o modelo de exemplo adventureworks ao criar visualizações diferentes e repartir em dados e métricas. Quando estiver satisfeito com o relatório, não se esqueça de o guardar.

## <a name="clean-up-resources"></a>Limpar recursos

Se já não for preciso, não guarde o relatório ou elimine o ficheiro se o tiver guardado.

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a utilizar o Power BI Desktop para ligar a um modelo de dados num servidor e criar um relatório básico. Se não está familiarizado com a forma de criar um modelo de dados, consulte o tutorial de modelação de [dados tabular Adventure Works Internet Sales](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) nos docs dos Serviços de Análise de Servidores SQL.