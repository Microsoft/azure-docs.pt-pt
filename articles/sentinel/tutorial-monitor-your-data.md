---
title: Visualize os seus dados utilizando os livros de trabalho do Monitor Azure em Azure Sentinel | Microsoft Docs
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
ms.date: 04/04/2021
ms.author: yelevin
ms.openlocfilehash: c26d86c98c83d9762acb8a75bba8fe464cc2a58e
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491505"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Tutorial: Visualizar e monitorizar os seus dados

Uma vez que tenha [conectado as suas fontes](quickstart-onboard.md) de dados ao Azure Sentinel, pode visualizar e monitorizar os dados utilizando a adoção do Azure Sentinel dos Livros de Trabalho do Monitor Azure, que proporciona versatilidade na criação de dashboards personalizados. Embora os livros de trabalho sejam apresentados de forma diferente no Azure Sentinel, pode ser útil para você ver como [criar relatórios interativos com os livros de trabalho do Monitor Azure](../azure-monitor/visualize/workbooks-overview.md). O Azure Sentinel permite-lhe criar livros de trabalho personalizados através dos seus dados, e também vem com modelos de livros incorporados para lhe permitir obter rapidamente informações sobre os seus dados assim que ligar uma fonte de dados.

Este tutorial ajuda-o a visualizar os seus dados no Azure Sentinel.
> [!div class="checklist"]
> * Use livros embutidos
> * Criar novos livros

## <a name="prerequisites"></a>Pré-requisitos

Deve ter pelo menos permissões **de leitor de livros** de trabalho ou de contribuinte de livros de **trabalho** no grupo de recursos do espaço de trabalho Azure Sentinel.

> [!NOTE]
> Os livros que pode ver no Azure Sentinel são guardados dentro do grupo de recursos do espaço de trabalho Azure Sentinel e são marcados pelo espaço de trabalho em que foram criados.

## <a name="use-built-in-workbooks"></a>Use livros embutidos

1. Vá a **Livros de Trabalho** e, em seguida, selecione **Modelos** para ver a lista completa de livros embutidos Azure Sentinel. 

    Para ver quais são relevantes para os tipos de dados que ligou, o campo **de tipos de dados necessários** em cada livro irá listar o tipo de dados ao lado de uma marca de verificação verde se já transmitir dados relevantes para o Azure Sentinel.

    [![Vai a livros de trabalho. ](media/tutorial-monitor-data/access-workbooks.png)](media/tutorial-monitor-data/access-workbooks.png#lightbox)

1. Selecione **o modelo de** visualização para ver o modelo preenchido com os seus dados.

1. Para editar o livro, **selecione Guardar** e, em seguida, selecione o local onde pretende guardar o ficheiro JSON para o modelo.

   > [!NOTE]
   > Isto cria um recurso Azure baseado no modelo relevante e guarda o ficheiro JSON do livro e não os dados.


1. Selecione **Ver livro guardado**. 

    [![Ver livros de trabalho. ](media/tutorial-monitor-data/workbook-graph.png)](media/tutorial-monitor-data/workbook-graph.png#lightbox)

    Selecione o botão **Editar** na barra de ferramentas do livro para personalizar o livro de trabalho de acordo com as suas necessidades. Quando terminar, **selecione Guardar** para guardar as suas alterações.

    Para obter mais informações, consulte como [criar relatórios interativos com os livros de trabalho do Monitor Azure.](../azure-monitor/visualize/workbooks-overview.md)

> [!TIP]
> Para clonar o seu livro, **selecione Editar** e, em seguida, **Guarde como**, certificando-se de que o guarde com outro nome, no mesmo grupo de subscrição e recursos.
> Os livros de trabalho clonados são apresentados no separador **Meus livros.**
>
## <a name="create-new-workbook"></a>Criar novo livro

1. Vá a **Livros de Trabalho** e, em seguida, selecione Adicionar livro de **trabalho** para criar um novo livro de trabalho do zero.

    [![Novo livro. ](media/tutorial-monitor-data/create-workbook.png)](media/tutorial-monitor-data/create-workbook.png#lightbox)

1. Para editar o livro, **selecione Editar** e, em seguida, adicione texto, consultas e parâmetros, se necessário. Para obter mais informações sobre como personalizar o livro, consulte como [criar relatórios interativos com os livros de trabalho do Monitor Azure.](../azure-monitor/visualize/workbooks-overview.md) 

1. Ao construir uma consulta, certifique-se de que a fonte de **dados** está definida para **Logs** e **o tipo de recurso** está definido para Log **Analytics**, e, em seguida, escolha o(s) espaço de trabalho relevante. 

1. Depois de criar o seu livro, guarde o livro, certificando-se de que o guarda sob o grupo de subscrição e recursos do seu espaço de trabalho Azure Sentinel.

1. Se quiser deixar que outros na sua organização utilizem o livro, em **Guardar para** selecionar **relatórios partilhados.** Se quiser que este livro esteja disponível apenas para si, selecione **os meus relatórios**.

1. Para alternar entre livros de trabalho no seu espaço de trabalho, selecione **Open** ![ Icon para abrir um livro.](./media/tutorial-monitor-data/switch.png) na barra de ferramentas de qualquer livro. O ecrã muda para uma lista de outros livros para os que pode mudar.

    Selecione o livro que pretende abrir:

    [![Troque os livros. ](media/tutorial-monitor-data/switch-workbooks.png)](media/tutorial-monitor-data/switch-workbooks.png#lightbox)

## <a name="refresh-your-workbook-data"></a>Atualizar os seus dados do livro

Refresque o seu livro para exibir dados atualizados. Na barra de ferramentas, selecione uma das seguintes opções:

- :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false":::**Refresh**, para atualizar manualmente os seus dados do livro.

- :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false":::**Refresque automaticamente**, para definir o seu livro de trabalho para atualizar automaticamente num intervalo configurado.

    - Os intervalos de atualização automática suportados variam entre **5 minutos** e **1 dia**.

    - A atualização automática é interrompida enquanto edita um livro e os intervalos são reiniciados cada vez que muda para o modo de visualização do modo de edição.

    - Os intervalos de atualização automática também são reiniciados se atualizar manualmente os seus dados.

    > [!TIP]
    > Por predefinição, a atualização automática é desligada. Para otimizar o desempenho, a atualização automática também é desligada cada vez que fecha um livro e não é executada em segundo plano. Volte a refrescar-se automaticamente da próxima vez que abrir o livro.
    >

## <a name="print-a-workbook-or-save-as-pdf"></a>Imprima um livro de trabalho ou guarde como PDF

Para imprimir um livro de trabalho, ou guardá-lo como UM PDF, use o menu de opções à direita do título do livro.

1. Selecione opções > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Imprimir o conteúdo**. 
2. No ecrã de impressão, ajuste as definições de impressão conforme necessário ou **selecione Guarde como PDF** para o guardar localmente.

Por exemplo:

[![Imprima o seu livro ou guarde como PDF. ](media/whats-new/print-workbook.png)](media/whats-new/print-workbook.png#lightbox)

## <a name="how-to-delete-workbooks"></a>Como eliminar livros

Para eliminar um livro de trabalho guardado (seja um modelo guardado ou um livro personalizado), na página workbooks, selecione o livro de trabalho guardado que pretende eliminar e selecione **Delete**. Isto removerá o livro de trabalho guardado.

> [!NOTE]
> Isto remove o recurso do livro, bem como quaisquer alterações que tenha feito ao modelo. O modelo original permanecerá disponível.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a visualizar os seus dados em Azure Sentinel, utilizando livros de trabalho Azure.

Para aprender a automatizar as suas respostas a ameaças, consulte [Configurar respostas automáticas de ameaças em Azure Sentinel](tutorial-respond-threats-playbook.md).
