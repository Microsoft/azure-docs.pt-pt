---
title: Ver e gerir tarefas de matriz Virtual StorSimple | Documentos da Microsoft
description: Descreve a página de tarefas do serviço StorSimple Device Manager e como usá-lo para controlar tarefas recentes e atuais para a matriz Virtual StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: dbab2aaab2c12bef07748f54e5864d042f1c982a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60302504"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Utilizar o serviço StorSimple Device Manager para ver tarefas do StorSimple Virtual Array
## <a name="overview"></a>Descrição geral
O **tarefas** painel fornece um único portal central para ver e gerir tarefas que são iniciadas em matrizes virtuais que estão ligadas ao seu serviço StorSimple Device Manager. Pode ver as tarefas em execução, concluídas e falhadas para vários dispositivos virtuais. Os resultados são apresentados num formato tabular.

![Painel de tarefas](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Pode encontrar rapidamente as tarefas que estão interessadas em ao filtrar em campos, tais como:

* **Intervalo de tempo** – tarefas podem ser filtradas com base no intervalo de data e hora.
* **Dispositivos** – tarefas são iniciadas num dispositivo específico, ligado ao seu serviço. As tarefas, em seguida, são apresentadas com base nos seguintes atributos:
  
  * **Nome** – o nome da tarefa pode ser **todos os**, **cópia de segurança**, **Clone**, **efetuar a ativação pós-falha**, **detransferênciadeatualizações**, ou **instalar atualizações**.
  * **Estado** – tarefas podem ser **todos os**, **em curso**, **Succeeded**, ou **falha**, ou **cancelado**.
  * **Entidade** – as tarefas podem ser associadas um volume, partilha ou dispositivo.
  * **Dispositivo** – o nome do dispositivo no qual a tarefa foi iniciada.
  * **Trabalhar com o** – o tempo quando a tarefa foi iniciada.
  * **Duração** – a duração em que a tarefa foi executada.
* **Estado** – pode pesquisar por todos os, tarefas em execução, concluídas ou falhadas.
* **Tipo de tarefa** – o tipo de tarefa pode ser all, cópia de segurança, restauro, o failover, transferir as atualizações ou instalar atualizações.

A lista de tarefas é atualizada a cada 30 segundos.

## <a name="view-job-details"></a>Ver detalhes da tarefa
Execute os seguintes passos para ver os detalhes de qualquer tarefa.

#### <a name="to-view-job-details"></a>Para ver os detalhes da tarefa
1. Sobre o **tarefas** painel, exibir as tarefas estão interessadas em ao executar uma consulta com filtros adequados. Pode procurar por tarefas em execução ou concluídas.
2. Selecione uma tarefa na lista tabular de tarefas.
   
    ![Painel de tarefas](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Na parte inferior da página, clique em **detalhes**.
4. Na **detalhes** caixa de diálogo, pode ver o estado, detalhes e as estatísticas de tempo. A ilustração seguinte mostra um exemplo do **detalhes da tarefa de cópia de segurança** caixa de diálogo.
   
    ![Detalhes da tarefa](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Falhas de tarefas quando a máquina virtual está em pausa no hipervisor
Quando uma tarefa está em curso na sua matriz Virtual StorSimple e o dispositivo (máquina virtual aprovisionada no hipervisor) está em pausa para mais de 15 minutos, a tarefa falha. Isso é devido a seu tempo de matriz Virtual StorSimple que está a ser sincronizado com a hora do Microsoft Azure. 

Verá o seguinte erro: "Hora do seu dispositivo não está sincronizada com a hora do Microsoft Azure por mais de 15 minutos. Certifique-se de que o hipervisor e o dispositivo vezes são sincronizadas com um servidor NTP. Certifique-se de que não existem não existem problemas de conectividade. Para resolver problemas de conectividade, execute testes de diagnóstico da web local da interface do Usuário do seu dispositivo virtual."

Estas falhas se aplicam a tarefas de cópia de segurança, restauro, atualização e ativação pós-falha. Se a sua máquina virtual é aprovisionada no Hyper-V, a máquina sincroniza eventualmente tempo com seu hipervisor. Depois que isso acontece, pode reiniciar o seu trabalho.

## <a name="next-steps"></a>Passos Seguintes
[Saiba como utilizar a IU da web local para administrar a sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).

