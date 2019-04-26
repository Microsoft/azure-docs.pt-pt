---
title: Ações do menu de MMC do StorSimple Snapshot Manager | Documentos da Microsoft
description: Descreve como utilizar as ações de menu de consola de gestão da Microsoft (MMC) padrão no Snapshot Manager do StorSimple.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: be761f0a2e59878139ae54dba62634a53b172711
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303031"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Utilizar as ações do menu MMC no Snapshot Manager do StorSimple

## <a name="overview"></a>Descrição geral
No Snapshot Manager do StorSimple, verá as seguintes ações listadas em todos os menus de ação e todas as variações do **ações** painel.

* Vista
* Janela novo a partir daqui 
* Atualizar 
* Exportar lista 
* Ajuda 

Estas ações fazem parte da consola de gestão da Microsoft (MMC) e não são específicas para o StorSimple Snapshot Manager. Este tutorial descreve estas ações e explica como usar cada um no Snapshot Manager do StorSimple.

## <a name="view"></a>Vista
Pode utilizar o **View** opção para alterar a **resultados** vista do painel de e para alterar a vista da janela de consola. 

#### <a name="to-change-the-results-pane-view"></a>Para alterar a vista de painel de resultados
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique com o botão direito qualquer nó ou expanda o nó e com o botão direito num item na **resultados** painel e, em seguida, clique o **vista** opção. 
3. Para adicionar ou remover as colunas que aparecem no **resultados** painel, clique em **Add/Remove Columns**. O **Adicionar/remover colunas** é apresentada a caixa de diálogo.
   
    ![Adicionar ou remover colunas do painel de resultados](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Preencha o formulário da seguinte forma:
   
   * Selecionar itens do **disponível** colunas lista e clique em **Add** adicioná-los para o **as colunas apresentadas** lista. 
   * Clique nos itens na **as colunas apresentadas** lista e clique em **remover** para removê-los a partir da lista. 
   * Selecione um item no **exibido** colunas lista e clique em **mover para cima** ou **mover para baixo** para mover o item para cima ou para baixo na lista. 
   * Clique em **restaurar predefinições** para regressar à predefinição **resultados** configuração de painel. 
5. Quando tiver terminado com as suas seleções, clique em **OK**. 

#### <a name="to-change-the-console-window-view"></a>Para alterar a vista de janela de consola
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, qualquer nó com o botão direito, clique em **vista**e, em seguida, clique em **personalizar**. O **personalizar** é apresentada a caixa de diálogo.
   
    ![Personalizar a janela da consola](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Marque ou desmarque as caixas de verificação para mostrar ou ocultar itens na janela da consola. Quando tiver terminado com as suas seleções, clique em **OK**.

## <a name="new-window-from-here"></a>Janela novo a partir daqui
Pode utilizar o **nova janela a partir daqui** opção de abrir uma nova janela de consola.

#### <a name="to-open-a-new-console-window"></a>Para abrir uma nova janela de consola
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique com o botão direito qualquer nó e, em seguida, clique em **nova janela a partir daqui**. 
   
    É apresentada uma nova janela, que mostra apenas o âmbito que selecionou. Por exemplo, se com o botão direito a **políticas de cópia de segurança** nó, a nova janela será de mostrar apenas o **políticas de cópia de segurança** nó o **âmbito** painel e uma lista de cópia de segurança definida as políticas no **resultados** painel. Veja o seguinte exemplo.
   
    ![Janela novo a partir daqui](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Atualizar
Pode utilizar o **atualizar** ação para atualizar a janela da consola.

#### <a name="to-update-the-console-window"></a>Para atualizar a janela da consola
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique com o botão direito qualquer nó ou expanda o nó e com o botão direito num item na **resultados** painel e, em seguida, clique **atualizar**. 

## <a name="export-list"></a>Exportar lista
Pode utilizar o **exportar lista** ação para guardar uma lista num arquivo de valores separados por vírgulas (CSV). Por exemplo, pode exportar a lista de políticas de cópia de segurança ou o catálogo de cópia de segurança. Pode importar o ficheiro CSV para um aplicativo de folha de cálculo para análise.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Para guardar uma lista num arquivo de valores separados por vírgulas (CSV)
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. Na **âmbito** painel, clique com o botão direito qualquer nó ou expanda o nó e com o botão direito num item na **resultados** painel e, em seguida, clique **exportar lista**. 
3. O **exportar lista** é apresentada a caixa de diálogo. Preencha o formulário da seguinte forma: 
   
   1. Na **nome de ficheiro** caixa, escreva um nome para o ficheiro CSV ou clique na seta para selecionar a lista pendente.
   2. Na **guardar como tipo** caixa, clique na seta e selecione um tipo de ficheiro da lista pendente.
   3. Para guardar apenas itens selecionados, selecione as linhas e, em seguida, clique nas **guardar apenas as linhas selecionadas** caixa de verificação. Para guardar exportadas todas as listas, desmarque a **guardar apenas as linhas selecionadas** caixa de verificação.
   4. Clique em **Guardar**.
      
      ![Exportar lista como um ficheiro de valores separados por vírgulas](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Ajuda
Pode utilizar o **ajudar** menu para ver a ajuda online disponível do Snapshot Manager do StorSimple e o MMC.

#### <a name="to-view-available-online-help"></a>Para ver a ajuda online disponíveis
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique com o botão direito qualquer nó ou expanda o nó e com o botão direito num item na **resultados** painel e, em seguida, clique **ajudar**. 

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre o [interface de utilizador do Snapshot Manager do StorSimple](storsimple-use-snapshot-manager.md).
* Saiba mais sobre [através do Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).

