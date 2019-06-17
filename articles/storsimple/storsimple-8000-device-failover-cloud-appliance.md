---
title: StorSimple a ativação pós-falha, recuperação após desastre para uma StorSimple Cloud Appliance | Documentos da Microsoft
description: Saiba como fazer a ativação pós-falha do dispositivo físico da série 8000 do StorSimple para uma aplicação da cloud.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 45c521fd044fa258b8052a3f0de48784cf4160e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584446"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Ativação pós-falha para a aplicação da Cloud do StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial descreve os passos necessários para efetuar a ativação pós-falha de um dispositivo físico da série 8000 do StorSimple para uma StorSimple Cloud Appliance se ocorrer um desastre. O StorSimple utiliza a funcionalidade de ativação pós-falha do dispositivo para migrar dados a partir de um dispositivo físico de origem no Centro de dados para uma aplicação da cloud em execução no Azure. As orientações neste tutorial aplica-se a dispositivos físicos da série StorSimple 8000 e cloud appliances que executam versões de software Update 3 e posterior.

Para saber mais sobre a ativação pós-falha do dispositivo e como ela é usada para recuperar a partir de um desastre, aceda a [ativação pós-falha e recuperação após desastre para dispositivos da série StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Para efetuar a ativação pós-falha de um dispositivo físico do StorSimple para outro dispositivo físico, aceda a [a ativação pós-falha para um dispositivo físico StorSimple](storsimple-8000-device-failover-physical-device.md). Para efetuar a ativação pós-falha de um dispositivo para si próprio, aceda a [a ativação pós-falha para o mesmo dispositivo físico StorSimple](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que reviu as considerações para a ativação pós-falha do dispositivo. Para obter mais informações, aceda a [considerações comuns para a ativação pós-falha do dispositivo](storsimple-8000-device-failover-disaster-recovery.md).

- Tem de ter uma StorSimple Cloud Appliance criada e configurada antes de executar este procedimento. Se atualizar a versão do software 3 ou posterior, considere a utilização de uma aplicação da 8020 cloud para a DR de execução. O modelo 8020 tem 64 TB e utiliza o armazenamento Premium. Para obter mais informações, aceda a [implementar e gerir uma StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Passos para efetuar a ativação pós-falha para uma aplicação da cloud

Execute os seguintes passos para restaurar o dispositivo para um destino StorSimple Cloud Appliance.

1.  Certifique-se de que o contentor de volumes que pretende efetuar a ativação pós-falha tem associados instantâneos de cloud. Para obter mais informações, aceda a [serviço de utilização StorSimple Device Manager para criar cópias de segurança](storsimple-8000-manage-backup-policies-u2.md).
2. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na **dispositivos** painel, vá para a lista de dispositivos ligados com o seu serviço.
    ![Selecione o dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Selecione e clique no seu dispositivo de origem. O dispositivo de origem tem os contentores de volumes que pretende efetuar a ativação pós-falha. Aceda a **definições > contentores de volumes**.

    ![Selecione o dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Selecione um contentor de volumes que pretende efetuar a ativação pós-falha para outro dispositivo. Clique no contentor de volumes para apresentar a lista de volumes dentro deste contentor. Selecione um volume, o botão direito do mouse e clique em **tirar Offline** para colocar o volume offline.

    ![Selecione o dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Repita este processo para todos os volumes no contentor de volume.

     ![Selecione o dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Repita o passo anterior para todos os contentores de volume para efetuar a ativação pós-falha para outro dispositivo.

7. Volte para o **dispositivos** painel. Na barra de comandos, clique em **efetuar a ativação pós-falha**.

    ![Clique em ativação pós-falha](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. Na **efetuar a ativação pós-falha** painel, execute os seguintes passos:
   
    1. Clique em **origem**. Selecione os contentores de volumes para a ativação pós-falha. **São apresentados apenas os contentores de volumes com instantâneos de cloud associados e offline volumes.**
        ![Selecionar origem](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Clique em **destino**. Selecione uma aplicação da cloud de destino na lista pendente de dispositivos disponíveis. **Apenas os dispositivos que tem capacidade suficiente para acomodar os contentores de volumes de origem são apresentados na lista.**

        ![Selecionar o destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Reveja as definições de ativação pós-falha sob **resumo** e selecione a caixa de verificação que indica que os volumes nos contentores de volumes selecionados estão offline. 

        ![Reveja as definições de ativação pós-falha](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. É criada uma tarefa de ativação pós-falha. Para monitorizar a tarefa de ativação pós-falha, clique na notificação da tarefa.

    ![Tarefa de ativação pós-falha do monitor](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Depois de concluída a ativação pós-falha, voltar para o **dispositivos** painel.

    1. Selecione o dispositivo que foi utilizado como destino para a ativação pós-falha.

       ![Selecione o dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Clique em **contentores de volumes**. Todos os contentores de volumes, juntamente com os volumes do dispositivo antigo, deverá ser listados.

       Se o contentor de volumes que efetuar a ativação pós-falha tem volumes localmente afixados, esses volumes são pós-falha como volumes em camadas. Os volumes afixados localmente não são suportados numa StorSimple Cloud Appliance.

       ![Contentores de volumes de destino do Vista](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Passos Seguintes

* Após executar uma ativação pós-falha, poderá ter de [desative ou elimine o dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Para informações sobre como utilizar o serviço StorSimple Device Manager, aceda a [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

