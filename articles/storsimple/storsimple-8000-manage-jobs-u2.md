---
title: Ver e gerir empregos para a série StorSimple 8000 [ Microsoft Docs
description: Descreve o serviço StorSimple Device Manager Jobs blade e como usá-lo para rastrear trabalhos de backup recentes, atuais e programados.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254875"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Utilize o serviço StorSimple Device Manager para visualizar e gerir postos de trabalho (Atualização 3 e mais tarde)

## <a name="overview"></a>Descrição geral
A lâmina **Jobs** fornece um único portal central para visualização e gestão de trabalhos que foram iniciados em dispositivos ligados ao seu serviço StorSimple Device Manager. Pode ver trabalhos programados, em execução, concluídos, cancelados e falhados para vários dispositivos. Os resultados são apresentados num formato tabular.

![Lâmina de emprego](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Você pode rapidamente encontrar os empregos que lhe interessa filtrando em campos como:

* **Status** – Os empregos podem estar em curso, bem sucedidos, cancelados, falhados, cancelados ou bem sucedidos com erros.
* **Intervalo** de tempo – Os trabalhos podem ser filtrados com base na data e intervalo de horas. As gamas já passaram de 1 hora, mais de 24 horas, passados 7 dias, passados 30 dias, ano passado, ou data personalizada.
* **Tipo** – O tipo de trabalho pode ser programado para backup, cópia de segurança manual, restauro de cópias de segurança, volume de clonagem, falha em recipientes de volume, criar volume localmente fixado, modificar volume, instalar atualizações, recolher registos de suporte e criar um aparelho em nuvem.
* **Dispositivos** – Os trabalhos são iniciados num determinado dispositivo ligado ao seu serviço.
  
Os trabalhos filtrados são então tabulados com base nos seguintes atributos:
  
* **Nome** – cópia de segurança programada, cópia de segurança manual, restauro de cópias de segurança, volume de clones, falha em recipientes de volume, cria volume localmente fixado, modifica volume, instala atualizações, recolha de registos de suporte ou cria um aparelho em nuvem.
* **Estado** – execução, concluída, cancelada, falhada, cancelada ou concluída com erros.
* **Entidade** – Os trabalhos podem ser associados a um volume, uma política de backup ou um dispositivo. Por exemplo, um trabalho de clone está associado a um volume, enquanto um trabalho de backup programado está associado a uma política de backup. Um trabalho de dispositivo é criado como resultado de uma recuperação de desastres (DR) ou de uma operação de restauro.
* **Dispositivo** – O nome do dispositivo em que o trabalho foi iniciado.
* **Começou** – O tempo em que o trabalho foi iniciado.
* **Duração** – O tempo necessário para completar o trabalho.

A lista de empregos é refrescada a cada 30 segundos.

Pode realizar as seguintes ações relacionadas com o trabalho nesta página:

* Ver detalhes da tarefa
* Cancelar um trabalho

## <a name="view-job-details"></a>Ver detalhes da tarefa
Execute os seguintes passos para ver os detalhes de qualquer trabalho.

#### <a name="to-view-job-details"></a>Para ver detalhes de trabalho
1. Vá ao serviço StorSimple Device Manager e, em seguida, clique em **Jobs**.

2. Na lâmina **Jobs,** exiba o trabalho(s) que lhe interessa executando uma consulta com filtros apropriados. Pode procurar empregos completos, em execução ou cancelados.

    ![Lâmina de trabalho](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Selecione e clique num trabalho.

    ![Lâmina de trabalho](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. Na lâmina de detalhes do trabalho, pode ver o estado, detalhes, estatísticas de tempo e estatísticas de dados.
   
    ![Detalhes da tarefa](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Cancelar um trabalho
Execute os seguintes passos para cancelar um trabalho de corrida.

> [!NOTE]
> Alguns trabalhos, como modificar um volume para alterar o tipo de volume ou expandir um volume, não podem ser cancelados.


### <a name="to-cancel-a-job"></a>Para cancelar um trabalho
1. Na página **Jobs,** exiba o trabalho de funcionamento que pretende cancelar executando uma consulta com filtros apropriados. Selecione o trabalho.

2. Clique à direita no trabalho selecionado para invocar o menu de contexto e clique **em Cancelar**.

    ![Detalhes da tarefa](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Quando lhe for pedida a confirmação, clique em **Sim**. Este trabalho está cancelado.

## <a name="next-steps"></a>Passos seguintes
* Saiba como gerir as suas políticas de [backup StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Aprenda a [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

