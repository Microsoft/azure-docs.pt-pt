---
title: Trabalhos de backup do StorSimple Snapshot Manager | Microsoft Docs
description: Descreve como usar o snap-in do StorSimple Snapshot Manager MMC para exibir e gerenciar trabalhos de backup agendados, em execução e concluídos no momento.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 094b996cd3227903995c7a74ef14ed8c0561f59d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933341"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Usar o Snapshot Manager do StorSimple para exibir e gerenciar trabalhos de backup

## <a name="overview"></a>Visão geral
O nó **trabalhos** no painel **escopo** mostra as tarefas de backup **agendadas**, **últimas 24 horas**e **em execução** que você iniciou interativamente ou por uma política configurada. 

Este tutorial explica como você pode usar o nó **trabalhos** para exibir informações sobre os trabalhos de backup agendados, recentes e em execução no momento. (A lista de trabalhos e as informações correspondentes são exibidas no painel de **resultados** .) Além disso, você pode clicar com o botão direito do mouse em um trabalho listado e ver um menu de contexto que lista as ações disponíveis.

## <a name="view-scheduled-jobs"></a>Exibir trabalhos agendados
Use o procedimento a seguir para exibir os trabalhos de backup agendados.

#### <a name="to-view-scheduled-jobs"></a>Para exibir os trabalhos agendados
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. No painel **escopo** , expanda o nó **trabalhos** e clique em **agendado**. As informações a seguir são exibidas no painel de **resultados** :
   
   * **Nome** – o nome do instantâneo agendado
   * **Próxima execução** – a data e a hora do próximo instantâneo agendado
   * **Última execução** – a data e a hora do instantâneo agendado mais recente
     
     > [!NOTE]
     > Para instantâneos somente de uma vez, a **próxima execução** e a **última execução** serão as mesmas.
     
     ![Trabalhos de backup agendados](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Para executar ações adicionais em um trabalho específico, clique com o botão direito do mouse no nome do trabalho no painel de **resultados** e selecione uma das opções de menu.

## <a name="view-recent-jobs"></a>Exibir trabalhos recentes
Use o procedimento a seguir para exibir os trabalhos de backup e restauração que foram concluídos nas últimas 24 horas.

#### <a name="to-view-recent-jobs"></a>Para exibir trabalhos recentes
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , expanda o nó **trabalhos** e clique em **últimas 24 horas**. O painel **resultados** mostra os trabalhos de backup das últimas 24 horas (até um máximo de 64 trabalhos). As informações a seguir são exibidas no painel de **resultados** , dependendo das opções de **exibição** que você especificar:
   
   * **Nome** – o nome do instantâneo agendado.
   * **Iniciado** – a data e a hora em que o instantâneo começou.
   * **Parado** – a data e a hora em que o instantâneo foi concluído ou encerrado.
   * **Decorrido** – a quantidade de tempo entre as horas de **início** e de **parada** .
   * **Status** – o estado do trabalho concluído recentemente. **Êxito** indica que o backup foi criado com êxito. **Falha** indica que o trabalho não foi executado com êxito.
   * **Informações** – o motivo da falha.
   * **Bytes processados (MB)** – a quantidade de dados do grupo de volumes que foi processada (em MBS). 
     
     ![Trabalhos que foram executados nas últimas 24 horas](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Para executar ações adicionais em um trabalho específico, clique com o botão direito do mouse no nome do trabalho no painel de **resultados** e selecione uma das opções de menu.
   
    ![Excluir um trabalho](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Exibir trabalhos em execução no momento
Use o procedimento a seguir para exibir os trabalhos que estão em execução no momento.

#### <a name="to-view-currently-running-jobs"></a>Para exibir os trabalhos em execução no momento
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , expanda o nó **trabalhos** e clique em **executando**. Dependendo das opções de **exibição** que você especificar, as seguintes informações são exibidas no painel de **resultados** :
   
   * **Nome** – o nome do instantâneo agendado.
   * **Iniciado** – a data e a hora em que o instantâneo começou.
   * **Ponto de verificação** – a ação atual do backup.
   * **Status** – a porcentagem de conclusão.
   * **Decorrido** – a quantidade de tempo que passou desde o início do backup. 
   * **Taxa de transferência média (MB)** – taxa do total de bytes de dados processados para o tempo total gasto para processamento (MBS).
   * **Bytes processados (MB)** – total de bytes de dados processados (em MBS).
   * **Bytes gravados (MB)** – total de bytes de dados gravados (em MBS). Ele inclui os dados, bem como os metadados e, portanto, geralmente é maior do que os bytes processados.
     
     ![Trabalhos em execução no momento](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Para executar ações adicionais em um trabalho específico, clique com o botão direito do mouse no nome do trabalho no painel de **resultados** e selecione uma das opções de menu.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [usar o snapshot Manager StorSimple para administrar sua solução storsimple](storsimple-snapshot-manager-admin.md).
* Saiba como [usar o snapshot Manager do StorSimple para gerenciar o catálogo de backup](storsimple-snapshot-manager-manage-backup-catalog.md).

