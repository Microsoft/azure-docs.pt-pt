---
title: Visualizar os seus dados utilizando livros de trabalho do Monitor Azure em Azure Sentinel [ Microsoft Docs
description: Utilize este tutorial para aprender a visualizar os seus dados usando livros em Azure Sentinel.
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
ms.openlocfilehash: f145b75ba351121bf750f90dbc0a7df382ca04b1
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82739165"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Tutorial: Visualizar e monitorizar os seus dados



Depois de ter [ligado as suas fontes](quickstart-onboard.md) de dados ao Azure Sentinel, pode visualizar e monitorizar os dados utilizando a adoção do Azure Sentinel dos Livros de Trabalho do Monitor Azure, que proporciona versatilidade na criação de dashboards personalizados. Embora os livros de trabalho sejam exibidos de forma diferente no Azure Sentinel, pode ser útil para si ver como [criar relatórios interativos com livros](../azure-monitor/app/usage-workbooks.md)de trabalho do Monitor Azure . O Azure Sentinel permite-lhe criar livros personalizados através dos seus dados, e também vem com modelos de livro incorporados para permitir-lhe obter rapidamente informações sobre os seus dados assim que ligar uma fonte de dados.


Este tutorial ajuda-o a visualizar os seus dados no Azure Sentinel.
> [!div class="checklist"]
> * Utilize livros embutidos
> * Criar novos livros

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter pelo menos permissões de leitor de livro ou contribuinte de livro no grupo de recursos do espaço de trabalho Azure Sentinel.

> [!NOTE]
> Os livros que se podem ver no Azure Sentinel são guardados dentro do grupo de recursos do espaço de trabalho Azure Sentinel e são marcados pelo espaço de trabalho em que foram criados.

## <a name="use-built-in-workbooks"></a>Utilize livros embutidos

1. Vá a Livros de **Trabalho** e, em seguida, selecione **Modelos** para ver a lista completa de livros de trabalho integrados Do Azure Sentinel. Para ver quais são relevantes para os tipos de dados que ligou, o campo de tipos de **dados necessários** em cada livro irá listar o tipo de dados junto a uma marca de verificação verde se já transmitir dados relevantes para o Azure Sentinel.
  ![ir para livros de trabalho](./media/tutorial-monitor-data/access-workbooks.png)
1. Clique em **Ver livro** de trabalho para ver o modelo povoado com os seus dados.
  
1. Para editar o livro de trabalho, selecione **Guardar**, e, em seguida, selecione o local onde pretende guardar o ficheiro json para o modelo. 

   > [!NOTE]
   > Isto cria um recurso Azure baseado no modelo relevante e guarda o próprio ficheiro Modelo Json e não os dados.


1. Selecione **Ver livro**. Em seguida, clique no botão **Editar** na parte superior. Agora pode editar o livro e personalizá-lo de acordo com as suas necessidades. Para obter mais informações sobre como personalizar o livro, consulte como criar relatórios interativos com livros de [trabalho do Monitor Azure.](../azure-monitor/app/usage-workbooks.md)
![ver livros](./media/tutorial-monitor-data/workbook-graph.png)
1. Depois de fazer as suas alterações, pode guardar o livro. 

1. Também pode clonar o livro: Selecione **Editar** e, em seguida, **guardar como**, certificando-se de guardá-lo com outro nome, sob o mesmo grupo de subscrição e recursos. Estes livros são apresentados sob o separador **My Books.**


## <a name="create-new-workbook"></a>Criar novo livro

1. Vá ao Livro de **Trabalhos** e, em seguida, **selecione Adicionar livro** para criar um novo livro de raiz.
  ![ir para livros de trabalho](./media/tutorial-monitor-data/create-workbook.png)

1. Para editar o livro de trabalho, selecione **Editar**, e, em seguida, adicionar texto, consultas e parâmetros, se necessário. Para obter mais informações sobre como personalizar o livro, consulte como criar relatórios interativos com livros de [trabalho do Monitor Azure.](../azure-monitor/app/usage-workbooks.md) 

1. Ao construir uma consulta, definir a **fonte de dados** está definida para **Registos,** o **tipo de Recurso** é definido para **Log Analytics** e, em seguida, escolher o espaço de trabalho relevante. 

1. Depois de criar o seu livro de trabalho, guarde o livro de trabalho certificando-se de que o guarde sob o grupo de subscrição e recursos do seu espaço de trabalho Azure Sentinel.

1. Se quiser deixar que outros na sua organização utilizem o livro, sob **o comando** de Save para selecionar **relatórios Partilhados**. Se quiser que este livro esteja disponível apenas para si, **selecione Os meus relatórios.**

1. Para alternar entre livros de trabalho **Open** ![no seu](./media/tutorial-monitor-data/switch.png)espaço de trabalho, pode selecionar livros de trabalho Open Switch no painel superior de qualquer livro. Na janela que se abre para a direita, alterna entre livros de recção.

   ![Mudar livros](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Como eliminar livros

Pode eliminar livros de trabalho criados a partir de um modelo Azure Sentinel. 

Para eliminar um livro personalizado, na página Livro de Livros, selecione o livro guardado que pretende eliminar e selecionar **Eliminar**. Isto removerá o livro guardado.

> [!NOTE]
> Isto remove o recurso, bem como quaisquer alterações que tenha feito ao modelo. O modelo original permanecerá disponível.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ver os seus dados no Azure Sentinel.

Para aprender a automatizar as suas respostas a ameaças, consulte [Configurar respostas de ameaça automatizadas no Azure Sentinel](tutorial-respond-threats-playbook.md).
