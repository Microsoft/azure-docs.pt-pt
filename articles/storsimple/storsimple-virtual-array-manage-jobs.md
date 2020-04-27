---
title: Ver e gerir empregos storSimple Virtual Array [ StorSimple Virtual Array] Microsoft Docs
description: Descreve a página StorSimple Device Manager Jobs e como usá-lo para rastrear trabalhos recentes e atuais para o StorSimple Virtual Array.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "60302504"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Utilize o serviço StorSimple Device Manager para ver trabalhos para o StorSimple Virtual Array
## <a name="overview"></a>Descrição geral
A lâmina **Jobs** fornece um único portal central para visualização e gestão de trabalhos que são iniciados em matrizes virtuais que estão ligadas ao seu serviço StorSimple Device Manager. Pode ver trabalhos em execução, concluídos e falhados para vários dispositivos virtuais. Os resultados são apresentados num formato tabular.

![Lâmina de emprego](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Você pode rapidamente encontrar os empregos que lhe interessa filtrando em campos como:

* **Intervalo** de tempo – Os trabalhos podem ser filtrados com base na data e intervalo de horas.
* **Dispositivos** – Os trabalhos são iniciados num dispositivo específico ligado ao seu serviço. Os trabalhos filtrados são então tabulados com base nos seguintes atributos:
  
  * **Nome** – O nome do trabalho pode ser **All**, **Backup,** **Clone,** **Fail over,** **Download updates,** or **Install updates**.
  * **Estado** – Os empregos podem ser **Todos,** **Em curso,** **Bem sucedidos,** ou **Falhados,** ou **Cancelados**.
  * **Entidade** – Os trabalhos podem ser associados a um volume, partilha ou dispositivo.
  * **Dispositivo** – O nome do dispositivo em que o trabalho foi iniciado.
  * **Começou** – O tempo em que o trabalho foi iniciado.
  * **Duração** – Duração para a qual o trabalho foi executado.
* **Estado** – Pode procurar todos, correr, completar ou falhar.
* **Tipo** de trabalho – O tipo de trabalho pode ser tudo, backup, restaurar, falhar, descarregar atualizações ou instalar atualizações.

A lista de empregos é refrescada a cada 30 segundos.

## <a name="view-job-details"></a>Ver detalhes da tarefa
Execute os seguintes passos para ver os detalhes de qualquer trabalho.

#### <a name="to-view-job-details"></a>Para ver detalhes de trabalho
1. Na lâmina **Jobs,** exiba o trabalho(s) que lhe interessa executando uma consulta com filtros apropriados. Pode procurar empregos completos ou em execução.
2. Selecione um trabalho na lista de trabalhos tabular.
   
    ![Lâmina de trabalho](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Na parte inferior da página, clique em **Detalhes**.
4. Na caixa de diálogo **Details,** pode visualizar o estado, os detalhes e as estatísticas do tempo. A ilustração que se segue mostra um exemplo da caixa de diálogo **backup Job Details.**
   
    ![Detalhes da tarefa](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Falhas de trabalho quando a máquina virtual é interrompida no hipervisor
Quando um trabalho está em andamento no seu StorSimple Virtual Array e o dispositivo (máquina virtual aprovisionado em hipervisor) é interrompido por mais de 15 minutos, o trabalho falha. Isto deve-se ao facto de o seu tempo de Matriz Virtual StorSimple estar dessincronizado com o tempo do Microsoft Azure. 

Verá o seguinte erro: "O tempo do seu dispositivo está dessincronizado com o tempo do Microsoft Azure em mais de 15 minutos. Certifique-se de que os tempos do hipervisor e do dispositivo estão sincronizados com um servidor NTP. Verifique se não há problemas de conectividade. Para resolver problemas de conectividade, efetua testes de diagnóstico a partir da UI web local do seu dispositivo virtual."

Estas falhas aplicam-se a trabalhos de backup, restauro, atualização e failover. Se a sua máquina virtual estiver aprovisionada em Hyper-V, a máquina acaba por sincronizar o tempo com o seu hipervisor. Assim que isso acontecer, podes reiniciar o teu trabalho.

## <a name="next-steps"></a>Passos seguintes
[Aprenda a usar a UI web local para administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

