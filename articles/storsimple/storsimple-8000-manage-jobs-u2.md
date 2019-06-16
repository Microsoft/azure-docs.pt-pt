---
title: Ver e gerir tarefas para a série StorSimple 8000 | Documentos da Microsoft
description: Descreve o painel de tarefas do serviço StorSimple Device Manager e como usá-lo para controlar as tarefas de cópia de segurança agendadas, atuais e recentes.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 462f8dafdffa7ee01e6ccf7945a1abfdff90db42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720689"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Utilizar o serviço StorSimple Device Manager para ver e gerir tarefas (atualização 3 e posterior)

## <a name="overview"></a>Descrição geral
O **tarefas** painel fornece um único portal central para visualização e gestão de tarefas que foram iniciadas em dispositivos ligados ao seu serviço StorSimple Device Manager. Pode ver as tarefas agendadas, em execução, concluídas, canceladas e com falhas para vários dispositivos. Os resultados são apresentados num formato tabular.

![Painel de tarefas](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Pode encontrar rapidamente as tarefas que estão interessadas em ao filtrar em campos, tais como:

* **Estado** – tarefas podem estar em curso, foi concluída com êxito, foi cancelados, com falha, cancelamento ou com êxito e com erros.
* **Intervalo de tempo** – tarefas podem ser filtradas com base no intervalo de data e hora. Os intervalos são a última 1 hora, últimas 24 horas, últimos 7 dias, após 30 dias, o último ano ou datas personalizadas.
* **Tipo de** – o tipo de tarefa pode ser a cópia de segurança agendada, a cópia de segurança manual, a cópia de segurança de restauro, clonar volume, ativação pós-falha de contentores de volumes, Criar volume afixado localmente, modificar o volume, instalar atualizações, recolher registos de suporte e criar a aplicação da cloud.
* **Dispositivos** – tarefas são iniciadas num determinado dispositivo ligada ao seu serviço.
  
As tarefas, em seguida, são apresentadas com base nos seguintes atributos:
  
* **Nome** – cópia de segurança agendada, manual de cópia de segurança, cópia de segurança do restauro, volume de clone, efetuar a ativação pós-falha contentores de volumes, Criar volume afixado localmente, modificar o volume, instalar atualizações, recolher registos de suporte ou criar aplicação da cloud.
* **Estado** – em execução, concluída, cancelada, com falha, cancelamento ou foi concluída com erros.
* **Entidade** – as tarefas podem ser associadas um volume, uma política de cópia de segurança ou um dispositivo. Por exemplo, uma tarefa de clonagem é associada a um volume, ao passo que uma tarefa de cópia de segurança agendada está associada uma política de cópia de segurança. Como resultado de uma recuperação após desastre (DR) ou uma operação de restauro, é criada uma tarefa de dispositivo.
* **Dispositivo** – o nome do dispositivo no qual a tarefa foi iniciada.
* **Trabalhar com o** – o tempo quando a tarefa foi iniciada.
* **Duração** – o tempo necessário para concluir a tarefa.

A lista de tarefas é atualizada a cada 30 segundos.

Pode efetuar as seguintes ações relacionadas com a tarefa nesta página:

* Ver detalhes da tarefa
* Cancelar uma tarefa

## <a name="view-job-details"></a>Ver detalhes da tarefa
Execute os seguintes passos para ver os detalhes de qualquer tarefa.

#### <a name="to-view-job-details"></a>Para ver os detalhes da tarefa
1. Aceda ao seu serviço StorSimple Device Manager e, em seguida, clique em **tarefas**.

2. Na **tarefas** painel, exibir as tarefas estão interessadas em ao executar uma consulta com filtros adequados. Pode pesquisar para concluída, em execução ou foi cancelada tarefas.

    ![Painel de tarefas](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Selecione e clique numa tarefa.

    ![Painel de tarefas](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. No painel de detalhes da tarefa, pode ver o estado, os detalhes, as estatísticas de tempo e estatísticas de dados.
   
    ![Detalhes da tarefa](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Cancelar uma tarefa
Execute os seguintes passos para cancelar uma tarefa em execução.

> [!NOTE]
> Não não possível cancelar algumas tarefas, como modificar um volume para alterar o tipo de volume ou expandir um volume.


### <a name="to-cancel-a-job"></a>Para cancelar uma tarefa
1. Sobre o **tarefas** página, exibir as tarefas em execução que pretende cancelar ao executar uma consulta com filtros adequados. Selecione a tarefa.

2. Com o botão direito sobre a tarefa selecionada para invocar o menu de contexto e clique em **Cancelar**.

    ![Detalhes da tarefa](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Quando lhe for pedida a confirmação, clique em **Sim**. Esta tarefa agora é cancelada.

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [gerir as políticas de cópia de segurança do StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Saiba como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

