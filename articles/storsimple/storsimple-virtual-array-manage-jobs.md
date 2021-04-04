---
title: Ver e gerir os trabalhos da StorSimple Virtual Array | Microsoft Docs
description: Descreve a página de serviço storSimple Device Manager Jobs e como usá-lo para rastrear trabalhos recentes e atuais para o StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: d806d8a04dc1bd8547808d20c77bfec310f7dd06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95992991"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Utilize o serviço StorSimple Device Manager para visualizar postos de trabalho para o StorSimple Virtual Array
## <a name="overview"></a>Descrição Geral
A lâmina **Jobs** fornece um único portal central para visualização e gestão de trabalhos que são iniciados em matrizes virtuais que estão ligadas ao seu serviço StorSimple Device Manager. Pode ver trabalhos em execução, concluídos e falhados para vários dispositivos virtuais. Os resultados são apresentados em formato tabular.

![Lâmina de emprego](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Você pode rapidamente encontrar os empregos que lhe interessam filtrando em campos tais como:

* **Intervalo de tempo** – Os trabalhos podem ser filtrados com base na data e intervalo de tempo.
* **Dispositivos** – Os trabalhos são iniciados num dispositivo específico ligado ao seu serviço. Os trabalhos filtrados são então tabulados com base nos seguintes atributos:
  
  * **Nome** – O nome do trabalho pode ser **All**, **Backup**, **Clone**, **Fail over**, **Download updates**, ou **Instalar atualizações**.
  * **Estado** – Empregos podem ser **todos,** **em progresso,** **bem sucedidos,** **falhados** ou **cancelados.**
  * **Entidade** – Os trabalhos podem ser associados a um volume, partilha ou dispositivo.
  * **Dispositivo** – O nome do dispositivo em que o trabalho foi iniciado.
  * **Começou** por ser – O momento em que o trabalho começou.
  * **Duração** – Duração da execução do trabalho.
* **Estado** – Pode procurar todos, correr, completar ou falhar.
* **Tipo de trabalho** – O tipo de trabalho pode ser todo, backup, restaurar, falhar, descarregar atualizações ou instalar atualizações.

A lista de empregos é atualizada a cada 30 segundos.

## <a name="view-job-details"></a>Ver detalhes da tarefa
Execute os seguintes passos para ver os detalhes de qualquer trabalho.

#### <a name="to-view-job-details"></a>Para ver detalhes do trabalho
1. Na lâmina **Jobs,** exiba o(s) trabalho que lhe interessa executando uma consulta com filtros apropriados. Pode procurar empregos completos ou em execução.
2. Selecione um trabalho na lista de empregos tabulares.
   
    ![Lâmina de trabalho](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Na parte inferior da página, clique em **Detalhes.**
4. Na caixa de diálogo **Details,** pode ver o estado, detalhes e estatísticas de tempo. A seguinte ilustração mostra um exemplo da caixa de diálogo **Backup Job Details.**
   
    ![Detalhes da tarefa](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Falhas de emprego quando a máquina virtual é pausada no hipervisor
Quando um trabalho está em andamento no seu StorSimple Virtual Array e o dispositivo (máquina virtual a forrada em hipervisor) é pausado por mais de 15 minutos, o trabalho falha. Isto deve-se ao tempo do seu StorSimple Virtual Array estar dessincronizado com o tempo do Microsoft Azure. 

Verá o seguinte erro: "O tempo do dispositivo está dessincronizado com o tempo do Microsoft Azure por mais de 15 minutos. Certifique-se de que o hipervisor e os tempos do dispositivo estão sincronizados com um servidor NTP. Verifique se não existem problemas de conectividade. Para resolver problemas de conectividade, escorram testes de diagnóstico a partir da UI web local do seu dispositivo virtual."

Estas falhas aplicam-se a trabalhos de backup, restauro, atualização e incumprimento. Se a sua máquina virtual for alocada em Hiper-V, a máquina eventualmente sincroniza o tempo com o seu hipervisor. Assim que isso acontecer, podes reiniciar o teu trabalho.

## <a name="next-steps"></a>Passos seguintes
[Saiba como utilizar a UI web local para administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

