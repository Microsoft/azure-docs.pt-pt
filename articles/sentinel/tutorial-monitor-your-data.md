---
title: Visualize os seus dados utilizando os livros de trabalho do Azure Monitor em Azure Sentinel Microsoft Docs
description: Utilize este tutorial para aprender a visualizar os seus dados utilizando livros de trabalho em Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2020
ms.author: yelevin
ms.openlocfilehash: 8d8f1343d92f66dc464ab7064949bbabb813268e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83798537"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Tutorial: Visualizar e monitorizar os seus dados



Uma vez que tenha [conectado as suas fontes](quickstart-onboard.md)de dados ao   Azure Sentinel, pode visualizar e monitorizar os dados utilizando a adoção do Azure Sentinel dos Livros de Trabalho do Monitor Azure, que proporciona versatilidade na criação de dashboards personalizados. Embora os livros de trabalho sejam apresentados de forma diferente no Azure Sentinel, pode ser útil para você ver como [criar relatórios interativos com os livros de trabalho do Monitor Azure](../azure-monitor/platform/workbooks-overview.md). O Azure Sentinel permite-lhe criar livros de trabalho personalizados através dos seus dados, e também vem com modelos de livros incorporados para lhe permitir obter rapidamente informações sobre os seus dados assim que ligar uma fonte de dados.


Este tutorial ajuda-o a visualizar os seus dados no Azure Sentinel.
> [!div class="checklist"]
> * Use livros embutidos
> * Criar novos livros

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter pelo menos permissões de leitor de livros de trabalho ou de contribuinte de livros de trabalho no grupo de recursos do espaço de trabalho Azure Sentinel.

> [!NOTE]
> Os livros que pode ver no Azure Sentinel são guardados dentro do grupo de recursos do espaço de trabalho Azure Sentinel e são marcados pelo espaço de trabalho em que foram criados.

## <a name="use-built-in-workbooks"></a>Use livros embutidos

1. Vá a **Livros de Trabalho** e, em seguida, selecione **Modelos** para ver a lista completa de livros embutidos Azure Sentinel. Para ver quais são relevantes para os tipos de dados que ligou, o campo **de tipos de dados necessários** em cada livro irá listar o tipo de dados ao lado de uma marca de verificação verde se já transmitir dados relevantes para o Azure Sentinel.
  ![ir para livros de trabalho](./media/tutorial-monitor-data/access-workbooks.png)
1. Clique **em Ver Livro para** ver o modelo preenchido com os seus dados.
  
1. Para editar o livro, **selecione Guardar**e, em seguida, selecione o local onde pretende guardar o ficheiro json para o modelo. 

   > [!NOTE]
   > Isto cria um recurso Azure baseado no modelo relevante e guarda o próprio ficheiro Json do modelo e não os dados.


1. Selecione **Ver livro**. Em seguida, clique no botão **Editar** na parte superior. Pode agora editar o livro e personalizá-lo de acordo com as suas necessidades. Para obter mais informações sobre como personalizar o livro, consulte como [criar relatórios interativos com os livros de trabalho do Monitor Azure.](../azure-monitor/platform/workbooks-overview.md)
![ver livros](./media/tutorial-monitor-data/workbook-graph.png)
1. Depois de fazer as alterações, pode guardar o livro. 

1. Também pode clonar o livro: Selecione **Editar** e, em seguida, **Guarde como**, certificando-se de que o guarde com outro nome, no mesmo grupo de subscrição e recursos. Estes livros são apresentados no separador **"Os meus livros** de trabalho".


## <a name="create-new-workbook"></a>Criar novo livro

1. Vá a **Livros de Trabalho** e, em seguida, selecione Adicionar livro de **trabalho** para criar um novo livro de trabalho do zero.
  ![ir para livros de trabalho](./media/tutorial-monitor-data/create-workbook.png)

1. Para editar o livro, **selecione Editar**e, em seguida, adicione texto, consultas e parâmetros, se necessário. Para obter mais informações sobre como personalizar o livro, consulte como [criar relatórios interativos com os livros de trabalho do Monitor Azure.](../azure-monitor/platform/workbooks-overview.md) 

1. Ao construir uma consulta, desaprote a fonte de **dados** está definida para **Logs,** o **tipo de recurso** é definido para Log **Analytics** e, em seguida, escolher o(s) espaço de trabalho relevante. 

1. Depois de criar o seu livro, guarde o livro de trabalho certificando-se de que o guarda sob o grupo de subscrição e recursos do seu espaço de trabalho Azure Sentinel.

1. Se quiser deixar que outros na sua organização utilizem o livro, em **Guardar para** selecionar **relatórios partilhados.** Se quiser que este livro esteja disponível apenas para si, selecione **os meus relatórios**.

1. Para alternar entre livros de trabalho no seu espaço de trabalho, pode selecionar os livros **de** ![ abertura do Switch no painel superior de qualquer ](./media/tutorial-monitor-data/switch.png) livro. Na janela que se abre para a direita, altere entre os livros.

   ![Trocar livros](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Como eliminar livros

Pode eliminar livros de trabalho criados a partir de um modelo Azure Sentinel. 

Para eliminar um livro personalizado, na página Workbooks, selecione o livro de trabalho guardado que pretende eliminar e selecione **Delete**. Isto removerá o livro de trabalho guardado.

> [!NOTE]
> Isto remove o recurso, bem como quaisquer alterações que tenha feito ao modelo. O modelo original permanecerá disponível.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu a ver os seus dados no Azure Sentinel.

Para aprender a automatizar as suas respostas a ameaças, consulte [Configurar respostas automáticas de ameaças em Azure Sentinel](tutorial-respond-threats-playbook.md).
