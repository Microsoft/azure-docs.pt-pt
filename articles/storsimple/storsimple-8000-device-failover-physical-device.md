---
title: StorSimple a ativação pós-falha, recuperação após desastre para um dispositivo físico da série StorSimple 8000 | Documentos da Microsoft
description: Saiba como fazer a ativação pós-falha do dispositivo físico da série 8000 do StorSimple para outro dispositivo físico.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 5fcf95a1a3033a5150945dbd841f12d50ebb023b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577244"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Ativação pós-falha para um dispositivo físico da série StorSimple 8000

## <a name="overview"></a>Descrição geral

Este tutorial descreve os passos necessários para efetuar a ativação pós-falha de um dispositivo físico da série 8000 do StorSimple para outro dispositivo físico do StorSimple se ocorrer um desastre. O StorSimple utiliza a funcionalidade de ativação pós-falha do dispositivo para migrar dados a partir de um dispositivo físico de origem no Centro de dados para outro dispositivo físico. As orientações neste tutorial aplica-se a série StorSimple 8000 dispositivos físicos com versões de software Update 3 e posterior.

Para saber mais sobre a ativação pós-falha do dispositivo e como ela é usada para recuperar a partir de um desastre, aceda a [ativação pós-falha e recuperação após desastre para dispositivos da série StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Para efetuar a ativação pós-falha de um dispositivo físico do StorSimple para uma StorSimple Cloud Appliance, aceda a [a ativação pós-falha para uma StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md). Para efetuar a ativação pós-falha de um dispositivo físico a próprio, aceda a [a ativação pós-falha para o mesmo dispositivo físico StorSimple](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que reviu as considerações para a ativação pós-falha do dispositivo. Para obter mais informações, aceda a [considerações comuns para a ativação pós-falha do dispositivo](storsimple-8000-device-failover-disaster-recovery.md).

- Tem de ter um dispositivo físico de StorSimple 8000 series implementado no Centro de dados. O dispositivo tem de executar a atualização 3 ou versão posterior do software. Para obter mais informações, aceda a [implementar no seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Passos para efetuar a ativação pós-falha para um dispositivo físico

Execute os seguintes passos para restaurar o seu dispositivo para um dispositivo físico de destino.

1. Certifique-se de que o contentor de volumes que pretende efetuar a ativação pós-falha tem associados instantâneos de cloud. Para obter mais informações, aceda a [serviço de utilização StorSimple Device Manager para criar cópias de segurança](storsimple-8000-manage-backup-policies-u2.md).
2. Vá para o StorSimple Device Manager e, em seguida, clique em **dispositivos**. Na **dispositivos** painel, vá para a lista de dispositivos ligados com o seu serviço.
    ![Selecione o dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Selecione e clique no seu dispositivo de origem. O dispositivo de origem tem os contentores de volumes que pretende efetuar a ativação pós-falha. Aceda a **definições > contentores de volumes**.
4. Selecione um contentor de volumes que pretende efetuar a ativação pós-falha para outro dispositivo. Clique no contentor de volumes para apresentar a lista de volumes dentro deste contentor. Selecione um volume, o botão direito do mouse e clique em **tirar Offline** para colocar o volume offline. Repita este processo para todos os volumes no contentor de volume.
5. Repita o passo anterior para todos os contentores de volume para efetuar a ativação pós-falha para outro dispositivo.
6. Volte para o **dispositivos** painel. Na barra de comandos, clique em **efetuar a ativação pós-falha**.
    ![Clique em ativação pós-falha](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. Na **efetuar a ativação pós-falha** painel, execute os seguintes passos:
   
   1. Clique em **origem**. Os contentores de volumes com volumes associados com instantâneos de cloud são apresentados. Apenas os contentores apresentados são elegíveis para a ativação pós-falha. Na lista de contentores de volumes, selecione os contentores de volumes que pretende efetuar a ativação pós-falha. **São apresentados apenas os contentores de volumes com instantâneos de cloud associados e offline volumes.**

       ![Selecionar origem](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Clique em **destino**. Para os contentores de volumes que selecionou no passo anterior, selecione um dispositivo de destino na lista pendente de dispositivos disponíveis. Apenas os dispositivos que tem capacidade suficiente para acomodar os contentores de volumes de origem são apresentados na lista.

        ![Selecionar o destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Por fim, reveja todas as definições de ativação pós-falha sob **resumo**. Depois de rever as definições, selecione a caixa de verificação que indica que os volumes nos contentores de volumes selecionados estão offline. Clique em **OK**.

       ![Reveja as definições de ativação pós-falha](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple cria uma tarefa de ativação pós-falha. Clique na notificação da tarefa para monitorizar a tarefa de ativação pós-falha através de **tarefas** painel.

    Se o contentor de volumes que efetuar a ativação pós-falha tem volumes locais, em seguida, verá as tarefas de restauro individual para cada volume local (não para volumes em camadas) no contentor. Estes restauro tarefas poderão demorar bastante tempo a concluir. É provável que a tarefa de ativação pós-falha pode ser concluída anteriormente. Estes volumes terão garantias locais apenas depois de concluir as tarefas de restauro.

    ![Tarefa de ativação pós-falha do monitor](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Depois de concluída a ativação pós-falha, vá para o **dispositivos** painel.
   
   1. Selecione o dispositivo que foi utilizado como o dispositivo de destino para o processo de ativação pós-falha.

       ![Selecionar dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Vá para o **contentores de volumes** painel. Todos os contentores de volumes, juntamente com os volumes do dispositivo antigo, deverá ser listados.

       ![Contentores de volumes de destino do Vista](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Passos Seguintes

* Após executar uma ativação pós-falha, poderá ter de [desative ou elimine o dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Para informações sobre como utilizar o serviço StorSimple Device Manager, aceda a [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

