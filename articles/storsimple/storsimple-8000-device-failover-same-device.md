---
title: Falha e recuperação de desastres para o mesmo dispositivo StorSimple 8000
description: Aprenda a falhar sobre o seu dispositivo StorSimple para o mesmo dispositivo.
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
ms.openlocfilehash: c8fe2d7ec7649f47f6cb9c8ae2c83f19c15691b6
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471810"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Falhar sobre o seu dispositivo físico StorSimple para o mesmo dispositivo

## <a name="overview"></a>Descrição geral

Este tutorial descreve os passos necessários para falhar sobre um dispositivo físico da série StorSimple 8000 para si mesmo se houver um desastre. O StorSimple utiliza a funcionalidade failover do dispositivo para migrar dados de um dispositivo físico de origem no datacenter para outro dispositivo físico. A orientação neste tutorial aplica-se aos dispositivos físicos da série StorSimple 8000 que executam as versões de software Update 3 e posteriormente.

Para saber mais sobre a falha do dispositivo e como é usado para recuperar de um desastre, vá ao Failover e à recuperação de [desastres para dispositivos da série StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Para falhar sobre um dispositivo físico para outro dispositivo físico, vá para [Fail over to the same StorSimple physical device](storsimple-8000-device-failover-physical-device.md). Para falhar sobre um dispositivo físico StorSimple para um storSimple Cloud Appliance, vá para [fail over to a StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que reviu as considerações para a falha do dispositivo. Para mais informações, vá a [considerações comuns para a falha](storsimple-8000-device-failover-disaster-recovery.md)do dispositivo .


## <a name="steps-to-fail-over-to-the-same-device"></a>Passos para falhar no mesmo dispositivo

Execute os seguintes passos se precisar de falhar no mesmo dispositivo.

1. Tire fotos em nuvem de todos os volumes do seu dispositivo. Para mais informações, vá ao [serviço StorSimple Device Manager para criar backups](storsimple-8000-manage-backup-policies-u2.md).
2. Reponha o seu dispositivo em incumprimentos de fábrica. Siga as instruções detalhadas sobre [como redefinir um dispositivo StorSimple para as definições predefinidas da fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Vá ao serviço StorSimple Device Manager e, em seguida, selecione **Dispositivos**. Na lâmina **do Dispositivo,** o dispositivo antigo deve apresentar-se como **Offline**.

    ![Dispositivo de origem offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Configure o seu dispositivo e registe-o novamente com o serviço StorSimple Device Manager. O dispositivo recém-registado deve mostrar como **Pronto a configurar**. O nome do dispositivo para o novo dispositivo é o mesmo que o dispositivo antigo, mas anexado com um numeral para indicar que o dispositivo foi reposto para o padrão de fábrica e registado novamente.

    ![Dispositivo recém-registado pronto para configurar](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Para o novo dispositivo, complete a configuração do dispositivo. Para mais informações, vá ao Passo 4: Complete a [configuração mínima do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Na lâmina **do Dispositivo,** o estado do dispositivo muda para **Online**.

   > [!IMPORTANT]
   > **Complete primeiro a configuração mínima, ou o SEU DR pode falhar.**

    ![Dispositivo recém-registado online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Selecione o dispositivo antigo (estado offline) e a partir da barra de comando, clique **em Fail over**. Na **lâmina Fail over,** selecione o dispositivo antigo como fonte e especifique o dispositivo-alvo como o dispositivo recém-registado.

    ![Resumo de failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Para obter instruções detalhadas, consulte o Fail over a outro dispositivo físico.

7. É criado um trabalho de restauro de dispositivos que pode monitorizar a partir da lâmina **Jobs.**

8. Depois de concluída a obra com sucesso, aceda ao novo dispositivo e navegue até à lâmina dos **contentores volume.** Verifique se todos os recipientes de volume do antigo dispositivo migraram para o novo dispositivo.

   ![Contentores de volume migrados](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Depois de concluída a falha, pode desativar e eliminar o dispositivo antigo do portal. Selecione o dispositivo antigo (offline), clique à direita e, em seguida, **selecione Desativar**. Após a desativação do dispositivo, o estado do dispositivo é atualizado.

     ![Dispositivo de origem desativado](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Selecione o dispositivo desativado, clique à direita e, em seguida, **selecione Eliminar**. Isto elimina o dispositivo da lista de dispositivos.

    ![Dispositivo de origem eliminado](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Passos seguintes

* Depois de ter realizado uma falha, poderá ter de [desativar ou eliminar o seu dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Para obter informações sobre como utilizar o serviço StorSimple Device Manager, vá utilizar [o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

