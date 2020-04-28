---
title: StorSimple Snapshot Manager backup jobs [ StorSimple Snapshot Manager backup jobs ] Microsoft Docs
description: Descreve como usar o snap-in StorSimple Snapshot Manager MMC para visualizar e gerir os trabalhos de backup programados, atualmente em execução e concluídos trabalhos de backup.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75933341"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Use o StorSimple Snapshot Manager para visualizar e gerir trabalhos de backup

## <a name="overview"></a>Descrição geral
O nó **Jobs** no painel **Scope** mostra as tarefas **agendadas**, **últimas 24 horas**e **executar** tarefas de backup que iniciou interativamente ou por uma política configurada. 

Este tutorial explica como você pode usar o nó **Jobs** para mostrar informações sobre trabalhos de backup programados, recentes e atualmente executando trabalhos de backup. (A lista de empregos e **Results** informações correspondentes consta do painel de resultados.) Além disso, pode clicar num trabalho listado e ver um menu de contexto que lista as ações disponíveis.

## <a name="view-scheduled-jobs"></a>Ver trabalhos programados
Utilize o seguinte procedimento para visualizar os trabalhos de backup programados.

#### <a name="to-view-scheduled-jobs"></a>Para ver empregos programados
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager. 
2. No painel **scope,** expanda o nó **Jobs** e clique **agendado**. As seguintes informações aparecem no painel **resultado:**
   
   * **Nome** – o nome do instantâneo programado
   * **Next Run** – a data e a hora do próximo instantâneo agendado
   * **Last Run** – a data e hora do mais recente instantâneo agendado
     
     > [!NOTE]
     > Para fotos únicas, a **Next Run** e last **run** serão as mesmas.
     
     ![Trabalhos de backup programados](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Para realizar ações adicionais num trabalho específico, clique no nome do trabalho no painel **de resultados** e selecione a partir das opções do menu.

## <a name="view-recent-jobs"></a>Ver trabalhos recentes
Utilize o seguinte procedimento para visualizar os trabalhos de backup e restauro que foram concluídos nas últimas 24 horas.

#### <a name="to-view-recent-jobs"></a>Para ver trabalhos recentes
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** expanda o nó **Jobs** e clique nas **últimas 24 horas.** O painel **de resultados** mostra trabalhos de backup nas últimas 24 horas (para um máximo de 64 postos de trabalho). As seguintes informações aparecem no painel **resultados,** dependendo das opções **do View** que especifica:
   
   * **Nome** – o nome do instantâneo programado.
   * **Começou** – a data e a hora em que o instantâneo começou.
   * **Parada** – a data e a hora em que o instantâneo terminou ou foi terminada.
   * **Decorrido** - a quantidade de tempo entre os tempos **iniciados** e **parados.**
   * **Estatuto** – o estado do trabalho recentemente concluído. **O sucesso** indica que o backup foi criado com sucesso. **Falha** indica que o trabalho não correu com sucesso.
   * **Informação** – a razão da falha.
   * **Bytes processados (MB)** – a quantidade de dados do grupo de volume que foi processado (em MBs). 
     
     ![Empregos que correram nas últimas 24 horas](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Para realizar ações adicionais num trabalho específico, clique no nome do trabalho no painel **de resultados** e selecione a partir das opções do menu.
   
    ![Apagar um trabalho](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Ver atualmente a gerir empregos
Utilize o seguinte procedimento para visualizar os postos de trabalho que estão atualmente a funcionar.

#### <a name="to-view-currently-running-jobs"></a>Para ver atualmente a gerir postos de trabalho
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **scope,** expanda o nó **Jobs** e clique em **Running**. Dependendo das opções **de Visualização** especificadas, as seguintes informações aparecem no painel **resultado:**
   
   * **Nome** – o nome do instantâneo programado.
   * **Começou** – a data e a hora em que o instantâneo começou.
   * **Checkpoint** – a ação atual do backup.
   * **Estado** – a percentagem de conclusão.
   * **Decorrido** – o tempo que passou desde o início do backup. 
   * Média de **produção (MB)** – rácio de bytes totais de dados tratados com o do tempo total de processamento (MBs).
   * **Bytes processados (MB)** – bytes totais de dados tratados (em MBs).
   * **Bytes escritos (MB)** – bytes totais de dados escritos (em MBs). Inclui os dados, bem como os metadados e, portanto, é tipicamente maior do que os Bytes Processados.
     
     ![Empregos em execução](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Para realizar ações adicionais num trabalho específico, clique no nome do trabalho no painel **de resultados** e selecione a partir das opções do menu.

## <a name="next-steps"></a>Passos seguintes
* Aprenda a utilizar o [StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar o StorSimple Snapshot Manager para gerir o catálogo de cópias de segurança](storsimple-snapshot-manager-manage-backup-catalog.md).

