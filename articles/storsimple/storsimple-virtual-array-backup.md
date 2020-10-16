---
title: Microsoft Azure StorSimple Virtual Array tutorial de backup Microsoft Docs
description: Crie cópias de segurança programadas e manuais para o Microsoft Azure StorSimple Virtual Array e execute a recuperação do nível de produto para restaurar um ficheiro eliminado na sua matriz virtual.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5c49a46dfcd97f5feb43b0b910476ef3ad6f402a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91742252"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Retorquir ações ou volumes no seu StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

O StorSimple Virtual Array é um dispositivo virtual de armazenamento em nuvem híbrido no local que pode ser configurado como um servidor de ficheiros ou um servidor iSCSI. A matriz virtual permite ao utilizador criar cópias de segurança programadas e manuais de todas as partilhas ou volumes do dispositivo. Quando configurado como um servidor de ficheiros, também permite a recuperação do nível de item. Este tutorial descreve como criar cópias de segurança programadas e manuais e realizar recuperação ao nível do item para restaurar um ficheiro eliminado na sua matriz virtual.

Este tutorial aplica-se apenas às Matrizes Virtuais StorSimple. Para obter informações sobre a série 8000, vá para [criar uma cópia de segurança para dispositivo da série 8000](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Apoio de ações e volumes

As cópias de segurança fornecem proteção pontual, melhoram a recuperabilidade e minimizam os tempos de restauração das ações e volumes. Pode fazer uma parte ou volume no seu dispositivo StorSimple de duas maneiras: **Agendado** ou **Manual**. Cada um dos métodos é discutido nas seguintes secções.

## <a name="change-the-backup-start-time"></a>Mude a hora de início de backup

> [!NOTE]
> Nesta versão, as cópias de segurança programadas são criadas por uma política padrão que funciona diariamente num determinado momento e que confirma todas as ações ou volumes do dispositivo. Não é possível criar políticas personalizadas para cópias de segurança programadas neste momento.


O seu StorSimple Virtual Array tem uma política de backup padrão que começa a uma hora especificada do dia (22:30) e faz backup de todas as ações ou volumes do dispositivo uma vez por dia. Pode alterar a hora em que a cópia de segurança começa, mas a frequência e a retenção (que especifica o número de backups a reter) não podem ser alteradas. Durante estas cópias de segurança, todo o dispositivo virtual está apoiado. Isto pode potencialmente afetar o desempenho do dispositivo e afetar as cargas de trabalho implementadas no dispositivo. Por isso, recomendamos que marque estas cópias de segurança para horas fora do pico.

 Para alterar a hora de início de backup predefinida, execute os seguintes passos no [portal Azure](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Para alterar a hora de início da política de backup predefinido

1. Ir para **dispositivos.** Será apresentada a lista de dispositivos registados no seu serviço StorSimple Device Manager. 
   
    ![navegar para dispositivos](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Selecione e clique no seu dispositivo. A lâmina **de definições** será exibida. Vá para **gerir > políticas de backup**.
   
    ![selecione o seu dispositivo](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. Na lâmina **das políticas de backup,** a hora de início por defeito é 22:30. Pode especificar a nova hora de início para o horário diário no fuso horário do dispositivo.
   
    ![navegar para políticas de backup](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Clique em **Guardar**.

### <a name="take-a-manual-backup"></a>Pegue uma cópia de segurança manual

Além das cópias de segurança programadas, pode fazer uma cópia de segurança manual (a pedido) dos dados do dispositivo a qualquer momento.

#### <a name="to-create-a-manual-backup"></a>Para criar uma cópia de segurança manual

1. Ir para **dispositivos.** Selecione o seu dispositivo e clique à **direita...** na extrema direita na linha selecionada. A partir do menu de contexto, **selecione Take backup**.
   
    ![navegar para tomar backup](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. Na lâmina **de reserva Take,** clique **em Retirar backup**. Isto irá fazer backup de todas as ações no servidor de ficheiros ou de todos os volumes no seu servidor iSCSI. 
   
    ![início de backup](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Começa-se um backup a pedido e vê-se que começou um trabalho de reserva.
   
    ![backup a partir de 2](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Uma vez concluído o trabalho com sucesso, é notificado novamente. O processo de reserva começa então.
   
    ![trabalho de backup criado](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. Para acompanhar o progresso das cópias de segurança e ver os detalhes do trabalho, clique na notificação. Isto leva-o aos **detalhes do Trabalho.**
   
     ![detalhes de trabalho de backup](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. Depois de a cópia de segurança estar completa, vá ao **catálogo Management > Backup**. Verá uma imagem em nuvem de todas as ações (ou volumes) do seu dispositivo.
   
    ![Cópia de segurança completa](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Ver backups existentes
Para visualizar as cópias de segurança existentes, execute os seguintes passos no portal Azure.

#### <a name="to-view-existing-backups"></a>Para ver as cópias de segurança existentes

1. Vai para a lâmina **dos dispositivos.** Selecione e clique no seu dispositivo. Na lâmina **Definições,** aceda ao **Catálogo de Cópias de Segurança > Gestão**.
   
    ![Navegue para catálogo de backup](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Especificar os seguintes critérios a utilizar para filtragem:
   
   - **Intervalo de tempo** – pode ser **passado 1 hora,** **Últimas 24 horas,** **Passados 7 dias,** **Passados 30 dias,** **Último ano**e **Data Personalizada**.
    
   - **Dispositivos** – selecione a partir da lista de servidores de ficheiros ou servidores iSCSI registados no seu serviço StorSimple Device Manager.
   
   - **Iniciado** – pode ser automaticamente **agendado** (por uma política de backup) ou iniciado **manualmente** (por si).
   
     ![Backups de filtros](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Clique em **Aplicar**. A lista filtrada de cópias de segurança é apresentada na lâmina do **catálogo Backup.** Note que apenas 100 elementos de backup podem ser exibidos num dado momento.
   
    ![Catálogo de backup atualizado](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a administração do seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

