---
title: StorSimple Snapshot Manager trabalhos de backup / Microsoft Docs
description: Descreve como usar o snap-in StorSimple Snapshot Manager MMC para visualizar e gerir trabalhos de backup programados, atualmente em execução e trabalhos de backup concluídos.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 3c26a84e32a17cba83b5ca895f146e561072fa62
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998197"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Use storSimple Snapshot Manager para visualizar e gerir trabalhos de backup

## <a name="overview"></a>Descrição geral
O nó **jobs** in the **Scope** pane mostra as tarefas de backup **agendadas,** **últimas 24 horas** e **executando** tarefas de backup que iniciou interativamente ou por uma política configurada. 

Este tutorial explica como você pode usar o nó **Jobs** para exibir informações sobre trabalhos de backup agendados, recentes e atualmente em execução de trabalhos de backup. (A lista de postos de trabalho e informações correspondentes aparece no painel **de resultados.)** Além disso, pode clicar com o direito de um trabalho listado e ver um menu de contexto que lista as ações disponíveis.

## <a name="view-scheduled-jobs"></a>Ver empregos programados
Utilize o seguinte procedimento para visualizar os trabalhos de reserva programados.

#### <a name="to-view-scheduled-jobs"></a>Para ver os trabalhos programados
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager. 
2. No **painel Scope,** expanda o nó **Jobs** e clique em **Agendamento**. As seguintes informações aparecem no painel **de resultados:**
   
   * **Nome** – o nome do instantâneo agendado
   * **Next Run** - a data e hora do próximo instantâneo agendado
   * **Last Run** – a data e hora do mais recente instantâneo agendado
     
     > [!NOTE]
     > Para instantâneos de uma só vez, a **Próxima Corrida** e **Última Corrida** serão as mesmas.
     
     ![Trabalhos de apoio programados](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Para realizar ações adicionais num trabalho específico, clique com o nome do trabalho no painel **de resultados** e selecione a partir das opções do menu.

## <a name="view-recent-jobs"></a>Ver empregos recentes
Utilize o seguinte procedimento para visualizar o backup e restaurar os trabalhos que foram concluídos nas últimas 24 horas.

#### <a name="to-view-recent-jobs"></a>Para ver empregos recentes
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No **painel Scope,** expanda o nó **Jobs** e clique **nas últimas 24 horas**. O Painel de **Resultados** mostra trabalhos de reserva nas últimas 24 horas (até um máximo de 64 postos de trabalho). As seguintes informações aparecem no painel **de resultados,** dependendo das opções **de Visualização** especifica:
   
   * **Nome** - o nome do instantâneo agendado.
   * **Começou** – a data e a hora em que o instantâneo começou.
   * **Parado** – a data e hora em que o instantâneo terminou ou foi terminado.
   * **Decorridos** – a quantidade de tempo entre os tempos **iniciados** e **parados.**
   * **Status** – o estado do trabalho recentemente concluído. **O sucesso** indica que a cópia de segurança foi criada com sucesso. **Falhas falhadas** indicam que o trabalho não correu com sucesso.
   * **Informação** – o motivo da falha.
   * **Bytes processados (MB)** – a quantidade de dados do grupo de volume que foi processado (em MBs). 
     
     ![Empregos que correram nas últimas 24 horas](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Para realizar ações adicionais num trabalho específico, clique com o nome do trabalho no painel **de resultados** e selecione a partir das opções do menu.
   
    ![Eliminar trabalhos](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Ver trabalhos atualmente em execução
Utilize o seguinte procedimento para visualizar os postos de trabalho que estão atualmente em curso.

#### <a name="to-view-currently-running-jobs"></a>Para ver os empregos atualmente em execução
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No **painel Scope,** expanda o nó **Jobs** e clique em **Executar**. Dependendo das opções **de Visualização** especificada, as seguintes informações aparecem no painel **de resultados:**
   
   * **Nome** - o nome do instantâneo agendado.
   * **Começou** – a data e a hora em que o instantâneo começou.
   * **Checkpoint** – a ação atual do backup.
   * **Estado** – a percentagem de conclusão.
   * **Decorridos** – a quantidade de tempo que passou desde o início do backup. 
   * **Produção média (MB)** – relação entre os bytes totais de dados tratados com o do tempo total de processamento (MBs).
   * **Bytes processados (MB)** – bytes totais de dados processados (em MBs).
   * **Bytes escritos (MB)** – bytes totais de dados escritos (em MBs). Inclui os dados, bem como os metadados e, portanto, é tipicamente maior do que os Bytes Processados.
     
     ![Postos de trabalho atualmente em curso](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Para realizar ações adicionais num trabalho específico, clique com o nome do trabalho no painel **de resultados** e selecione a partir das opções do menu.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar o StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar o StorSimple Snapshot Manager para gerir o catálogo de backup](storsimple-snapshot-manager-manage-backup-catalog.md).

