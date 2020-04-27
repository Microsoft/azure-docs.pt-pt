---
title: Tutorial de backup microsoft Azure StorSimple Virtual Array Microsoft Docs
description: Descreve como apoiar as ações e volumes da StorSimple Virtual Array.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "60581399"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Back up shares ou volumes no seu StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

O StorSimple Virtual Array é um dispositivo virtual híbrido de armazenamento em nuvem no local que pode ser configurado como um servidor de ficheiros ou um servidor iSCSI. A matriz virtual permite ao utilizador criar cópias de segurança agendadas e manuais de todas as partilhas ou volumes do dispositivo. Quando configurado como um servidor de ficheiros, também permite a recuperação do nível do item. Este tutorial descreve como criar backups agendados e manuais e executar a recuperação do nível do item para restaurar um ficheiro eliminado na sua matriz virtual.

Este tutorial aplica-se apenas às Matrizes Virtuais StorSimple. Para obter informações sobre a série 8000, vá criar um dispositivo de [backup para 8000 séries](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Back up shares and volumes

As cópias de segurança proporcionam proteção pontual, melhoram a recuperabilidade e minimizam os tempos de restauro das ações e volumes. Pode fazer o back up de uma parte ou volume no seu dispositivo StorSimple de duas formas: **Agendado** ou **Manual**. Cada um dos métodos é discutido nas seguintes secções.

## <a name="change-the-backup-start-time"></a>Alterar a hora de início de reserva

> [!NOTE]
> Nesta versão, as cópias de segurança programadas são criadas por uma política padrão que funciona diariamente num determinado momento e faz backup de todas as ações ou volumes do dispositivo. Não é possível criar políticas personalizadas para backups agendados neste momento.


O Seu StorSimple Virtual Array tem uma política de backup padrão que começa a uma hora especificada do dia (22:30) e faz backup de todas as ações ou volumes do dispositivo uma vez por dia. Pode alterar o tempo em que a cópia de segurança começa, mas a frequência e a retenção (que especifica o número de cópias de segurança a reter) não podem ser alteradas. Durante estas cópias de segurança, todo o dispositivo virtual está apoiado. Isto pode potencialmente afetar o desempenho do dispositivo e afetar as cargas de trabalho implementadas no dispositivo. Por isso, recomendamos que agende estes backups para horas fora do pico.

 Para alterar o tempo de arranque de reserva predefinido, execute os seguintes passos no [portal Azure](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Para alterar o tempo de início da política de backup padrão

1. Ir para **dispositivos.** Será apresentada a lista de dispositivos registados no seu serviço StorSimple Device Manager. 
   
    ![navegar para dispositivos](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Selecione e clique no seu dispositivo. A lâmina **Definições** será exibida. Vá para **gerir as políticas de backup >.**
   
    ![selecionar o seu dispositivo](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. Na lâmina das políticas de **backup,** a hora de início padrão é 22:30. Pode especificar a nova hora de início do horário diário no fuso horário do dispositivo.
   
    ![navegar para políticas de backup](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Clique em **Guardar**.

### <a name="take-a-manual-backup"></a>Pegue uma cópia de segurança manual

Além das cópias de segurança programadas, pode obter uma cópia de segurança manual (on-demand) dos dados do dispositivo a qualquer momento.

#### <a name="to-create-a-manual-backup"></a>Para criar uma cópia de segurança manual

1. Ir para **dispositivos.** Selecione o seu dispositivo e clique à **direita...** na linha da extrema direita na linha selecionada. A partir do menu de contexto, selecione **Tomar cópia de segurança**.
   
    ![navegar para receber reforços](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. Na lâmina **de reserva Tomar,** clique em **retirar a cópia**de segurança . Isto irá fazer backup de todas as ações do servidor de ficheiros ou de todos os volumes do seu servidor iSCSI. 
   
    ![backup começando](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Um reforço a pedido começa e vê-se que começou um trabalho de reserva.
   
    ![backup começando](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Uma vez concluído o trabalho com sucesso, será novamente notificado. O processo de reserva começa então.
   
    ![trabalho de backup criado](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Para acompanhar o progresso das cópias de segurança e ver os detalhes do trabalho, clique na notificação. Isto leva-o a **detalhes de Trabalho.**
   
     ![detalhes do trabalho de backup](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Depois de concluída a cópia de segurança, vá ao **catálogo de Backup Management >**. Você verá uma imagem em nuvem de todas as ações (ou volumes) no seu dispositivo.
   
    ![Cópia de segurança concluída](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Ver backups existentes
Para visualizar as cópias de segurança existentes, execute os seguintes passos no portal Azure.

#### <a name="to-view-existing-backups"></a>Para ver os backups existentes

1. Vai para a lâmina **dos Dispositivos.** Selecione e clique no seu dispositivo. Na lâmina **Definições,** vá ao **'Management > Backup Catalog**.
   
    ![Navegue para o catálogo de backup](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Especifique os seguintes critérios a utilizar para a filtragem:
   
   - **Intervalo** de tempo – pode ser **passado 1 hora,** **passados 24 horas,** **últimos 7 dias,** **últimos 30 dias,** **ano passado,** e **data personalizada.**
    
   - **Dispositivos** – selecione a partir da lista de servidores de ficheiros ou servidores iSCSI registados com o seu serviço StorSimple Device Manager.
   
   - **Iniciado** – pode ser **automaticamente agendado** (por uma política de backup) ou iniciado **manualmente** (por si).
   
     ![Backups de filtro](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Clique em **Aplicar**. A lista filtrada de backups é apresentada na lâmina do **catálogo De reserva.** Note que apenas 100 elementos de reserva podem ser apresentados num dado momento.
   
    ![Catálogo de backup atualizado](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a administração do seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

