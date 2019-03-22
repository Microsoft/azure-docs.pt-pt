---
title: Tutorial de cópia de segurança do Microsoft Azure StorSimple Virtual Array | Documentos da Microsoft
description: Descreve como criar cópias de segurança StorSimple Virtual Array compartilhamentos e volumes.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a61dcca1f78b6ba444a2deefcf6b8bb4fd5c5087
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121983"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Fazer cópias de segurança de partilhas ou volumes em sua matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

A matriz Virtual StorSimple é um híbrida na cloud no local virtual dispositivo de armazenamento que pode ser configurado como um servidor de ficheiros ou um servidor de iSCSI. A matriz virtual permite ao utilizador criar cópias de segurança agendadas e manuais de todas as partilhas ou volumes no dispositivo. Quando configurado como um servidor de ficheiros, também permite a recuperação ao nível do item. Este tutorial descreve como criar cópias de segurança agendadas e manuais e efetuar a recuperação ao nível do item para restaurar um ficheiro eliminado na sua matriz virtual.

Este tutorial aplica-se para o StorSimple Virtual matrizes só. Para obter informações sobre a 8000 série, aceda a [criar uma cópia de segurança para o dispositivo da 8000 série](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Criar cópias de segurança compartilhamentos e volumes

As cópias de segurança fornecem proteção de ponto no tempo, melhorar a capacidade de recuperação e minimizar os tempos de restauro para compartilhamentos e volumes. Pode fazer backup de um compartilhamento ou volume no dispositivo StorSimple de duas formas: **Agendado** ou **Manual**. Cada um dos métodos é abordada nas seções a seguir.

## <a name="change-the-backup-start-time"></a>Alterar a hora de início de cópia de segurança

> [!NOTE]
> Nesta versão, as cópias de segurança agendadas são criadas por uma política predefinida que é executada diariamente, numa hora especificada e cria cópias de segurança de todas as partilhas ou volumes no dispositivo. Não é possível criar políticas personalizadas para cópias de segurança agendadas neste momento.


A sua matriz Virtual StorSimple tem uma política de cópia de segurança predefinida que começa numa determinada hora do dia (22:30) e cria cópias de segurança todas as partilhas ou volumes no dispositivo uma vez por dia. Pode alterar a hora em que a cópia de segurança iniciada, mas a frequência e o período de retenção (que especifica o número de cópias de segurança para reter) não podem ser alterados. Durante estas cópias de segurança, todo o dispositivo virtual é uma cópia de segurança. Isso poderia potencialmente afetar o desempenho do dispositivo e afetam as cargas de trabalho implementadas no dispositivo. Por conseguinte, recomendamos que agende estas cópias de segurança para horas de ponta.

 Para alterar a hora de início de cópia de segurança predefinida, execute os seguintes passos no [portal do Azure](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Para alterar a hora de início para a política de cópia de segurança predefinida

1. Aceda a **dispositivos**. Será apresentada a lista de dispositivos registados com o seu serviço StorSimple Device Manager. 
   
    ![Navegue para dispositivos](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Selecione e clique no seu dispositivo. O **definições** é apresentado o painel. Aceda a **gerir > políticas de cópia de segurança**.
   
    ![Selecione o seu dispositivo](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. Na **políticas de cópia de segurança** painel, a hora de início de predefinida é 22:30. Pode especificar a nova hora de início para a agenda diária no fuso horário do dispositivo.
   
    ![Navegue para as políticas de cópia de segurança](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Clique em **Guardar**.

### <a name="take-a-manual-backup"></a>Faça uma cópia de segurança manual

Para além das cópias de segurança agendadas, pode tirar uma manual (a pedido) cópia de segurança de dados do dispositivo em qualquer altura.

#### <a name="to-create-a-manual-backup"></a>Para criar uma cópia de segurança manual

1. Aceda a **dispositivos**. Selecione o seu dispositivo e com o botão direito **...**  na extremidade direita na linha selecionada. No menu de contexto, selecione **efetuar cópia de segurança**.
   
    ![Navegue para efetuar cópia de segurança](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. Na **efetuar cópia de segurança** painel, clique em **efetuar cópia de segurança**. Isto irá criar cópias de segurança todas as partilhas no servidor de ficheiros ou todos os volumes no seu servidor de iSCSI. 
   
    ![cópia de segurança iniciada](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Inicia uma cópia de segurança a pedido e verá que uma tarefa de cópia de segurança foi iniciado.
   
    ![cópia de segurança iniciada](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Assim que a tarefa foi concluída com êxito, será notificado novamente. Em seguida, inicia o processo de cópia de segurança.
   
    ![tarefa de cópia de segurança criada](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Para acompanhar o progresso das cópias de segurança e examinar os detalhes da tarefa, clique na notificação. Isto leva-o para **detalhes da tarefa**.
   
     ![Detalhes da tarefa de cópia de segurança](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Depois de concluída a cópia de segurança, aceda a **gestão > catálogo de cópia de segurança**. Verá um instantâneo de cloud de todas as partilhas de (ou volumes) no seu dispositivo.
   
    ![Cópia de segurança concluída](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Ver cópias de segurança existentes
Para ver as cópias de segurança existentes, execute os seguintes passos no portal do Azure.

#### <a name="to-view-existing-backups"></a>Para ver as cópias de segurança existentes

1. Aceda a **dispositivos** painel. Selecione e clique no seu dispositivo. Na **definições** painel, aceda à **gestão > catálogo de cópia de segurança**.
   
    ![Navegue para o catálogo de cópia de segurança](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Especifique os seguintes critérios para ser utilizado para filtrar:
   
   - **Intervalo de tempo** – pode ser **hora nos últimos 1**, **últimas 24 horas**, **nos últimos 7 dias**, **nos últimos 30 dias**, **último ano** , e **data personalizado**.
    
   - **Dispositivos** – selecione na lista de servidores de ficheiros ou servidores iSCSI registados com o seu serviço StorSimple Device Manager.
   
   - **Iniciada** – pode ser automaticamente **agendada** (por uma política de cópia de segurança) ou **manualmente** iniciada (por si).
   
     ![Filtrar cópias de segurança](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Clique em **Aplicar**. É apresentada a lista filtrada de cópias de segurança no **catálogo de cópia de segurança** painel. Tenha em atenção apenas 100 elementos de cópia de segurança podem ser exibidos num determinado momento.
   
    ![Catálogo de cópia de segurança atualizado](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [administrando sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).

