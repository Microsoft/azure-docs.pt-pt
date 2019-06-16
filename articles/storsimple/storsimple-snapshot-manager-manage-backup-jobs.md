---
title: Tarefas de cópia de segurança do Snapshot Manager do StorSimple | Documentos da Microsoft
description: Descreve como utilizar o snap-in do MMC do Snapshot Manager do StorSimple para ver e gerir tarefas de cópia de segurança agendadas, atualmente em execução e concluídas.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: c34ff487f03d90b16b6660fbad77c3a16699e165
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64719885"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Utilizar o Snapshot Manager do StorSimple para ver e gerir tarefas de cópia de segurança

## <a name="overview"></a>Descrição geral
O **trabalhos** nó a **âmbito** painel mostra o **agendada**, **últimas 24 horas**, e **em execução** tarefas de cópia de segurança iniciada interativamente ou por uma política configurada. 

Este tutorial explica como pode utilizar o **tarefas** nó para apresentar informações sobre tarefas de cópia de segurança agendadas, recentes e que está em execução. (A lista de tarefas e informações correspondentes aparece no **resultados** painel.) Além disso, pode uma tarefa listada com o botão direito e verá um menu de contexto que apresenta uma lista de ações disponíveis.

## <a name="view-scheduled-jobs"></a>Ver tarefas agendadas
Utilize o procedimento seguinte para ver tarefas de cópia de segurança agendadas.

#### <a name="to-view-scheduled-jobs"></a>Para ver as tarefas agendadas
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. Na **âmbito** painel, expanda o **trabalhos** nó e clique em **agendada**. As seguintes informações aparecem no **resultados** painel:
   
   * **Nome** – o nome do instantâneo agendado
   * **Em seguida execute** – a data e hora do próximo instantâneo agendado
   * **Última execução** – a data e hora do instantâneo agendada mais recente
     
     > [!NOTE]
     > Para instantâneos de apenas uma única vez, o **executar próxima** e **executar última** será o mesmo.
     
     ![Tarefas de cópia de segurança agendadas](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Para executar ações adicionais numa tarefa específica, faça duplo clique o nome da tarefa no **resultados** painel e selecione de entre as opções de menu.

## <a name="view-recent-jobs"></a>Ver tarefas recentes
Utilize o procedimento seguinte para ver a cópia de segurança e restaurar as tarefas que foram concluídas nas últimas 24 horas.

#### <a name="to-view-recent-jobs"></a>Para ver tarefas recentes
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, expanda o **trabalhos** nó e clique em **últimas 24 horas**. O **resultados** painel mostra as tarefas de cópia de segurança nas últimas 24 horas (para um máximo de 64 tarefas). As seguintes informações aparecem no **resultados** painel, dependendo da **vista** as opções especificadas:
   
   * **Nome** – o nome do instantâneo agendado.
   * **Iniciado** – a data e hora quando o instantâneo começou.
   * **Parado** – a data e hora quando o instantâneo foi concluída ou foi terminado.
   * **Decorrido** – a quantidade de tempo entre o **iniciado** e **parado** vezes.
   * **Estado** – o estado do trabalho concluído. **Êxito** indica que a cópia de segurança foi criada com êxito. **Falha ao** indica que a tarefa não foi executado com êxito.
   * **Informações** – o motivo da falha.
   * **Bytes processados (MB)** – a quantidade de dados do grupo de volume que foi processado (em MB). 
     
     ![Tarefas que tenha sido executada nas últimas 24 horas](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Para executar ações adicionais numa tarefa específica, faça duplo clique o nome da tarefa no **resultados** painel e selecione de entre as opções de menu.
   
    ![Eliminar uma tarefa](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Vista de tarefas atualmente em execução
Utilize o procedimento seguinte para ver as tarefas atualmente em execução.

#### <a name="to-view-currently-running-jobs"></a>Para ver tarefas atualmente em execução
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, expanda o **trabalhos** nó e clique em **em execução**. Consoante a **View** as opções especificadas, as seguintes informações aparecem no **resultados** painel:
   
   * **Nome** – o nome do instantâneo agendado.
   * **Iniciado** – a data e hora quando o instantâneo começou.
   * **Ponto de verificação** – a ação atual da cópia de segurança.
   * **Estado** – a percentagem de conclusão.
   * **Decorrido** – a quantidade de tempo decorrido desde início a cópia de segurança. 
   * **Taxa de transferência média (MB)** – proporção de total de bytes de dados processados para que o tempo total decorrido para processamento (MB).
   * **Bytes processados (MB)** – total de bytes de dados processados (em MB).
   * **Bytes escritos (MB)** – total de bytes de dados gravados (em MB). Inclui os dados, bem como os metadados e, por conseguinte, é geralmente maior do que o processamento de Bytes.
     
     ![Tarefas atualmente em execução](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Para executar ações adicionais numa tarefa específica, faça duplo clique o nome da tarefa no **resultados** painel e selecione de entre as opções de menu.

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [utilizar o Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar o Snapshot Manager do StorSimple para gerir o catálogo de cópia de segurança](storsimple-snapshot-manager-manage-backup-catalog.md).

