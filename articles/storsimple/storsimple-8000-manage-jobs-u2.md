---
title: Ver e gerir postos de trabalho para a série StorSimple 8000 Microsoft Docs
description: Descreve o serviço StorSimple Device Manager Jobs e como usá-lo para rastrear trabalhos de backup recentes, atuais e programados.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: dc23a0a68471b82c990d5d8a3086e2e71e88d0c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85513935"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Utilize o serviço StorSimple Device Manager para visualizar e gerir postos de trabalho (atualização 3 e posterior)

## <a name="overview"></a>Descrição geral
A lâmina **Jobs** fornece um único portal central para visualização e gestão de trabalhos que foram iniciados em dispositivos ligados ao seu serviço StorSimple Device Manager. Pode ver trabalhos programados, em execução, concluídos, cancelados e falhados para vários dispositivos. Os resultados são apresentados em formato tabular.

![Lâmina de emprego](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Você pode rapidamente encontrar os empregos que lhe interessam filtrando em campos tais como:

* **Estado** – Os empregos podem estar em curso, bem sucedidos, cancelados, falhados, cancelados ou sucedidos com erros.
* **Intervalo de tempo** – Os trabalhos podem ser filtrados com base na data e intervalo de tempo. Os intervalos são passados 1 hora, últimas 24 horas, passados 7 dias, últimos 30 dias, ano passado, ou data personalizada.
* **Tipo** de trabalho – O tipo de trabalho pode ser programado como cópia de segurança, cópia de segurança manual, restaurar a cópia de segurança, volume de clones, falhar em recipientes de volume, criar volume fixo local, modificar volume, instalar atualizações, recolher registos de suporte e criar aparelhos em nuvem.
* **Dispositivos** – Os trabalhos são iniciados num determinado dispositivo ligado ao seu serviço.
  
Os trabalhos filtrados são então tabulados com base nos seguintes atributos:
  
* **Nome** – cópia de segurança programada, cópia de segurança manual, restaurar a cópia de segurança, volume de clones, falhar em recipientes de volume, criar volume fixo localmente, modificar volume, instalar atualizações, recolher registos de suporte ou criar aparelhos em nuvem.
* **Estado** – funcionamento, concluído, cancelado, falhado, cancelado ou concluído com erros.
* **Entidade** – Os trabalhos podem ser associados a um volume, uma política de backup ou a um dispositivo. Por exemplo, um trabalho de clone está associado a um volume, enquanto um trabalho de backup programado está associado a uma política de backup. Um trabalho de dispositivo é criado como resultado de uma recuperação de desastres (DR) ou de uma operação de restauro.
* **Dispositivo** – O nome do dispositivo em que o trabalho foi iniciado.
* **Começou** por ser – O momento em que o trabalho começou.
* **Duração** – O tempo necessário para completar o trabalho.

A lista de empregos é atualizada a cada 30 segundos.

Pode realizar as seguintes ações relacionadas com o trabalho nesta página:

* Ver detalhes da tarefa
* Cancelar um emprego

## <a name="view-job-details"></a>Ver detalhes da tarefa
Execute os seguintes passos para ver os detalhes de qualquer trabalho.

#### <a name="to-view-job-details"></a>Para ver detalhes do trabalho
1. Vá ao seu serviço StorSimple Device Manager e, em seguida, clique em **Jobs**.

2. Na lâmina **Jobs,** exiba o(s) trabalho que lhe interessa executando uma consulta com filtros apropriados. Pode procurar empregos completos, em execução ou cancelados.

    ![Lâmina de trabalho](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Selecione e clique num trabalho.

    ![Lâmina de trabalho](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. Na lâmina de detalhes do trabalho, pode ver o estado, detalhes, estatísticas de tempo e estatísticas de dados.
   
    ![Detalhes da tarefa](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Cancelar um emprego
Execute os seguintes passos para cancelar um trabalho de corrida.

> [!NOTE]
> Alguns postos de trabalho, como a modificação de um volume para alterar o tipo de volume ou a expansão de um volume, não podem ser cancelados.


### <a name="to-cancel-a-job"></a>Para cancelar um emprego
1. Na página **Jobs,** exiba o(s) trabalho de execução que pretende cancelar executando uma consulta com filtros apropriados. Selecione o trabalho.

2. Clique com o botão direito no trabalho selecionado para invocar o menu de contexto e clique em **Cancelar**.

    ![Detalhes da tarefa](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Quando lhe for pedida a confirmação, clique em **Sim**. Este trabalho está cancelado.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [gerir as suas políticas de backup StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Saiba como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

