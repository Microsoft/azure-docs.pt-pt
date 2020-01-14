---
title: Ações de menu do StorSimple Snapshot Manager MMC | Microsoft Docs
description: Descreve como usar as ações de menu padrão do console de gerenciamento Microsoft (MMC) no StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: ced075395c4f2a51308d5d3b0cbe4c0153a17816
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931472"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Usar as ações de menu do MMC no StorSimple Snapshot Manager

## <a name="overview"></a>Visão geral
No StorSimple Snapshot Manager, você verá as seguintes ações listadas em todos os menus de ação e todas as variações do painel **ações** .

* Vista
* Nova Janela a Partir Daqui 
* Atualizar 
* Exportar lista 
* Ajuda 

Essas ações fazem parte do MMC (console de gerenciamento Microsoft) e não são específicas do StorSimple Snapshot Manager. Este tutorial descreve essas ações e explica como usar cada uma delas no StorSimple Snapshot Manager.

## <a name="view"></a>Vista
Você pode usar a opção de **exibição** para alterar a exibição do painel de **resultados** e alterar a exibição da janela do console. 

#### <a name="to-change-the-results-pane-view"></a>Para alterar a exibição do painel de resultados
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique com o botão direito do mouse em qualquer nó ou expanda o nó e clique com o botão direito do mouse em um item no painel **resultados** e clique na opção **Exibir** . 
3. Para adicionar ou remover as colunas que aparecem no painel de **resultados** , clique em **Adicionar/remover colunas**. A caixa de diálogo **Adicionar/remover colunas** é exibida.
   
    ![Adicionar ou remover colunas do painel de resultados](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Preencha o formulário da seguinte maneira:
   
   * Selecione itens na lista colunas **disponíveis** e clique em **Adicionar** para adicioná-los à lista **colunas exibidas** . 
   * Clique em itens na lista **colunas exibidas** e clique em **remover** para removê-los da lista. 
   * Selecione um item na lista colunas **exibidas** e clique em **mover para cima** ou **mover para baixo** para mover o item para cima ou para baixo na lista. 
   * Clique em **Restaurar padrões** para retornar à configuração do painel de **resultados** padrão. 
5. Quando tiver terminado com suas seleções, clique em **OK**. 

#### <a name="to-change-the-console-window-view"></a>Para alterar a exibição da janela do console
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique com o botão direito do mouse em qualquer nó, clique em **Exibir**e em **Personalizar**. A caixa de diálogo **Personalizar** é exibida.
   
    ![Personalizar a janela do console](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Marque ou desmarque as caixas de seleção para mostrar ou ocultar itens na janela do console. Quando tiver terminado com suas seleções, clique em **OK**.

## <a name="new-window-from-here"></a>Nova Janela a Partir Daqui
Você pode usar a opção **nova janela a partir de aqui** para abrir uma nova janela do console.

#### <a name="to-open-a-new-console-window"></a>Para abrir uma nova janela de console
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique com o botão direito do mouse em qualquer nó e clique em **nova janela aqui**. 
   
    Uma nova janela é exibida, mostrando apenas o escopo que você selecionou. Por exemplo, se você clicar com o botão direito do mouse no nó **políticas de backup** , a nova janela mostrará apenas o nó **políticas de backup** no painel **escopo** e uma lista de políticas de backup definidas no painel de **resultados** . Veja o seguinte exemplo.
   
    ![Nova Janela a Partir Daqui](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Atualizar
Você pode usar a ação **Atualizar** para atualizar a janela do console.

#### <a name="to-update-the-console-window"></a>Para atualizar a janela do console
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique com o botão direito do mouse em qualquer nó ou expanda o nó e clique com o botão direito do mouse em um item no painel **resultados** e clique em **Atualizar**. 

## <a name="export-list"></a>Exportar lista
Você pode usar a ação **Exportar lista** para salvar uma lista em um arquivo de valores separados por vírgulas (CSV). Por exemplo, você pode exportar a lista de políticas de backup ou o catálogo de backup. Em seguida, você pode importar o arquivo CSV para um aplicativo de planilha para análise.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Para salvar uma lista em um arquivo de valores separados por vírgulas (CSV)
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. No painel **escopo** , clique com o botão direito do mouse em qualquer nó ou expanda o nó e clique com o botão direito do mouse em um item no painel **resultados** e clique em **Exportar lista**. 
3. A caixa de diálogo **Exportar lista** é exibida. Preencha o formulário da seguinte maneira: 
   
   1. Na caixa **nome do arquivo** , digite um nome para o arquivo CSV ou clique na seta para selecionar na lista suspensa.
   2. Na caixa **salvar como tipo** , clique na seta e selecione um tipo de arquivo na lista suspensa.
   3. Para salvar apenas os itens selecionados, selecione as linhas e, em seguida, clique na caixa de seleção **salvar apenas linhas selecionadas** . Para salvar todas as listas exportadas, desmarque a caixa de seleção **salvar somente linhas selecionadas** .
   4. Clique em **Guardar**.
      
      ![Exportar lista como um arquivo de valores separados por vírgulas](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Ajuda
Você pode usar o menu **ajuda** para exibir a ajuda online disponível para o StorSimple snapshot Manager e o MMC.

#### <a name="to-view-available-online-help"></a>Para exibir a ajuda online disponível
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique com o botão direito do mouse em qualquer nó ou expanda o nó e clique com o botão direito do mouse em um item no painel **resultados** e clique em **ajuda**. 

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [interface do usuário do StorSimple snapshot Manager](storsimple-use-snapshot-manager.md).
* Saiba mais sobre como [usar o StorSimple snapshot Manager para administrar sua solução storsimple](storsimple-snapshot-manager-admin.md).

