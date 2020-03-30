---
title: Ações de menu StorSimple Snapshot Manager MMC [ StorSimple Snapshot Manager MMC] Microsoft Docs
description: Descreve como utilizar as ações padrão do menu Microsoft Management Console (MMC) no StorSimple Snapshot Manager.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931472"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Utilize as ações do menu MMC no StorSimple Snapshot Manager

## <a name="overview"></a>Descrição geral
No StorSimple Snapshot Manager, você verá as seguintes ações listadas em todos os menus de ação e todas as variações do painel **de Ações.**

* Vista
* Nova Janela a Partir Daqui 
* Atualizar 
* Lista de Exportações 
* Ajuda 

Estas ações fazem parte da Consola de Gestão da Microsoft (MMC) e não são específicas do StorSimple Snapshot Manager. Este tutorial descreve estas ações e explica como usar cada uma delas no StorSimple Snapshot Manager.

## <a name="view"></a>Vista
Pode utilizar a opção **'Ver'** para alterar a vista do painel **de resultados** e alterar a vista da janela da consola. 

#### <a name="to-change-the-results-pane-view"></a>Para alterar a visão do painel de resultados
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** clique à direita em qualquer nó ou expanda o nó e clique à direita num item no painel **de resultados** e, em seguida, clique na opção **Ver.** 
3. Para adicionar ou remover as colunas que aparecem no painel **resultados,** clique em **Adicionar/Remover Colunas**. Aparece a caixa de diálogo **Add/Remove Columns.**
   
    ![Adicione ou remova colunas do painel resultados](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Preencha o formulário da seguinte forma:
   
   * Selecione itens da lista de colunas **disponíveis** e clique **em adicionar** aos comentários à lista de **colunas apresentadas.** 
   * Clique em itens na lista de **colunas visualizadas** e clique em **Removê-los** para removê-los da lista. 
   * Selecione um item na lista de colunas **visualizadas** e clique **em Mover-se** ou **mover para baixo** para mover o item para cima ou para baixo na lista. 
   * Clique em **Restaurar os Predefinições** para voltar à configuração padrão do painel **de resultados.** 
5. Quando terminar as suas seleções, clique **ok**. 

#### <a name="to-change-the-console-window-view"></a>Para alterar a vista da janela da consola
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **scope,** clique à direita em qualquer nó, clique em **Ver**, e, em seguida, clique em **Personalizar**. A caixa de diálogo **Personalizar** aparece.
   
    ![Personalize a janela da consola](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Selecione ou limpe as caixas de verificação para mostrar ou esconder itens na janela da consola. Quando terminar as suas seleções, clique **ok**.

## <a name="new-window-from-here"></a>Nova Janela a Partir Daqui
Pode utilizar a opção **New Window from Here** para abrir uma nova janela de consola.

#### <a name="to-open-a-new-console-window"></a>Para abrir uma nova janela de consola
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **scope,** clique à direita em qualquer nó e, em seguida, clique em **Nova Janela a partir daqui**. 
   
    Aparece uma nova janela, mostrando apenas o âmbito que selecionou. Por exemplo, se clicar no nó de Políticas de **Backup,** a nova janela mostrará apenas o nó de Políticas de **Backup** no painel **scope** e uma lista de políticas de backup definidas no painel **de resultados.** Veja o seguinte exemplo.
   
    ![Nova Janela a Partir Daqui](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Atualizar
Pode utilizar a ação **Refresh** para atualizar a janela da consola.

#### <a name="to-update-the-console-window"></a>Para atualizar a janela da consola
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** clique à direita em qualquer nó ou expanda o nó e clique à direita num item no painel **de resultados** e, em seguida, clique em **Refresh**. 

## <a name="export-list"></a>Lista de Exportações
Pode utilizar a ação da Lista de **Exportação** para guardar uma lista num ficheiro de valor separado de vírem (CSV). Por exemplo, pode exportar a lista de políticas de backup ou o catálogo de backup. Em seguida, pode importar o ficheiro CSV num pedido de análise da folha de cálculo.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Para guardar uma lista num ficheiro de valor separado de vírem (CSV)
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager. 
2. No painel **scope,** clique à direita em qualquer nó ou expanda o nó e clique à direita num item no painel **de resultados** e, em seguida, clique na Lista de **Exportação**. 
3. A caixa de diálogo **da Lista de Exportação** aparece. Preencha o formulário da seguinte forma: 
   
   1. Na caixa de **nomes de ficheiros,** digite um nome para o ficheiro CSV ou clique na seta para selecionar a partir da lista de lançamentos.
   2. Na caixa **de resguardo como tipo,** clique na seta e selecione um tipo de ficheiro da lista de drop-down.
   3. Para guardar apenas itens selecionados, selecione as linhas e, em seguida, clique na caixa de verificação Save **Only Selected Rows.** Para guardar todas as listas exportadas, limpe a caixa de verificação **Save Only Selected Rows.**
   4. Clique em **Guardar**.
      
      ![Lista de exportação como ficheiro de valor separado de vírinas](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Ajuda
Pode utilizar o menu **Ajuda** para visualizar a ajuda online disponível para o StorSimple Snapshot Manager e o MMC.

#### <a name="to-view-available-online-help"></a>Para ver a ajuda online disponível
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **scope,** clique à direita em qualquer nó ou expanda o nó e clique à direita num item no painel **de resultados** e, em seguida, clique em **Ajuda**. 

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a interface de utilizador do [StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
* Saiba mais sobre [a utilização do StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).

