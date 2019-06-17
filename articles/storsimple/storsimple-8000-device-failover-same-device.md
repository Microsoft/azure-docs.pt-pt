---
title: StorSimple a ativação pós-falha, recuperação após desastre para dispositivos de 8000 série | Documentos da Microsoft
description: Saiba como fazer a ativação pós-falha do dispositivo StorSimple ao mesmo dispositivo.
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: dd207eaad1a3e821724d51a890d0882bfffda131
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60577380"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Efetuar a ativação pós-falha do dispositivo físico StorSimple para o mesmo dispositivo

## <a name="overview"></a>Descrição geral

Este tutorial descreve os passos necessários para efetuar a ativação pós-falha de um dispositivo físico da série StorSimple 8000 a mesmo se ocorrer um desastre. O StorSimple utiliza a funcionalidade de ativação pós-falha do dispositivo para migrar dados a partir de um dispositivo físico de origem no Centro de dados para outro dispositivo físico. As orientações neste tutorial aplica-se a série StorSimple 8000 dispositivos físicos com versões de software Update 3 e posterior.

Para saber mais sobre a ativação pós-falha do dispositivo e como ela é usada para recuperar a partir de um desastre, aceda a [ativação pós-falha e recuperação após desastre para dispositivos da série StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Para efetuar a ativação pós-falha de um dispositivo físico para outro dispositivo físico, aceda a [a ativação pós-falha para o mesmo dispositivo físico StorSimple](storsimple-8000-device-failover-physical-device.md). Para efetuar a ativação pós-falha de um dispositivo físico do StorSimple para uma StorSimple Cloud Appliance, aceda a [a ativação pós-falha para uma StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que reviu as considerações para a ativação pós-falha do dispositivo. Para obter mais informações, aceda a [considerações comuns para a ativação pós-falha do dispositivo](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Passos para efetuar a ativação pós-falha para o mesmo dispositivo

Se precisar de efetuar a ativação pós-falha para o mesmo dispositivo, execute os seguintes passos.

1. Criar instantâneos de cloud de todos os volumes no seu dispositivo. Para obter mais informações, aceda a [serviço de utilização StorSimple Device Manager para criar cópias de segurança](storsimple-8000-manage-backup-policies-u2.md).
2. Repor o seu dispositivo para as predefinições de fábrica. Siga as instruções detalhadas [como repor um dispositivo StorSimple para as predefinições de fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Vá para o serviço StorSimple Device Manager e, em seguida, selecione **dispositivos**. Na **dispositivos** painel, o dispositivo antigo deve ser apresentado como **Offline**.

    ![Dispositivo de origem offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Configurar o dispositivo e registá-lo novamente com o seu serviço StorSimple Device Manager. O dispositivo recém-registado deve mostrar como **preparado para configurar a**. O nome do dispositivo para o novo dispositivo é o mesmo que o dispositivo antigo mas anexada com um numeral para indicar que o dispositivo foi reposto para padrão de fábrica e registado novamente.

    ![Dispositivo recém-registado pronto para configurar](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Para o novo dispositivo, conclua a configuração do dispositivo. Para obter mais informações, aceda a [passo 4: Concluir a configuração mínima do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Sobre o **dispositivos** painel, o estado do dispositivo muda para **Online**.

   > [!IMPORTANT]
   > **Concluir a configuração mínima em primeiro lugar, ou a DR pode falhar.**

    ![Recentemente registado o dispositivo online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Selecione o dispositivo antigo (status offline) e da barra de comandos, clique em **efetuar a ativação pós-falha**. Na **efetuar a ativação pós-falha** painel, selecione o dispositivo antigo como origem e especifique o dispositivo de destino como o dispositivo recentemente registado.

    ![Resumo de ativação pós-falha](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Para obter instruções detalhadas, consulte a falhar ao longo para outro dispositivo físico.

7. Uma tarefa de restauro do dispositivo é criada, que pode monitorizar a partir da **tarefas** painel.

8. Depois da tarefa foi concluída com êxito, o novo dispositivo de acesso e navegue para o **contentores de volumes** painel. Certifique-se de que todos os contentores de volume do dispositivo antigo tem migrado para o novo dispositivo.

   ![Contentores de volume migrados](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Depois de concluída a ativação pós-falha, pode desativar e eliminar o dispositivo antigo do portal. Selecione o atalho (offline), de dispositivo antigo e, em seguida, selecione **desativar**. Depois do dispositivo é desativado, o estado do dispositivo é atualizado.

     ![Dispositivo de origem desativado](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Selecione o dispositivo desativado, o botão direito do mouse e em seguida, selecione **eliminar**. Esta ação elimina o dispositivo da lista de dispositivos.

    ![Dispositivo de origem eliminado](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Passos Seguintes

* Após executar uma ativação pós-falha, poderá ter de [desative ou elimine o dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Para informações sobre como utilizar o serviço StorSimple Device Manager, aceda a [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

