---
title: Tutorial - Falha sobre o dispositivo físico StorSimple para o mesmo dispositivo
description: Aprenda os passos necessários para falhar sobre um dispositivo físico da série StorSimple 8000 para si mesmo se houver um desastre.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: 17c116194aa52a82246bcee9114824e8a918ebbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017105"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Falha no seu dispositivo físico StorSimple para o mesmo dispositivo

## <a name="overview"></a>Descrição Geral

Este tutorial descreve os passos necessários para falhar sobre um dispositivo físico da série StorSimple 8000 para si mesmo se houver um desastre. O StorSimple utiliza a função de falha do dispositivo para migrar dados de um dispositivo físico de origem no datacenter para outro dispositivo físico. A orientação neste tutorial aplica-se a dispositivos físicos da série StorSimple 8000 que executam as versões de software Update 3 e posteriormente.

Para saber mais sobre o failover do dispositivo e como é usado para recuperar de um desastre, vá para [Failover e recuperação de desastres para dispositivos da série StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Para falhar um dispositivo físico a outro dispositivo físico, vá para [Fail para o mesmo dispositivo físico StorSimple](storsimple-8000-device-failover-physical-device.md). Para falhar um dispositivo físico StorSimple para um aparelho de nuvem StorSimple, vá para ['Falha' num aparelho de nuvem StorSimple](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que reviu as considerações para o failover do dispositivo. Para obter mais informações, aceda a [considerações comuns para falha do dispositivo.](storsimple-8000-device-failover-disaster-recovery.md)


## <a name="steps-to-fail-over-to-the-same-device"></a>Passos para falhar para o mesmo dispositivo

Execute os seguintes passos se precisar de falhar no mesmo dispositivo.

1. Tire fotos em nuvem de todos os volumes do seu dispositivo. Para obter mais informações, aceda ao [serviço StorSimple Device Manager para criar cópias de segurança.](storsimple-8000-manage-backup-policies-u2.md)
2. Reinicie o seu dispositivo em defeitos de fábrica. Siga as instruções detalhadas [sobre como redefinir um dispositivo StorSimple para as definições predefinidos da fábrica](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Vá ao serviço StorSimple Device Manager e, em seguida, selecione **Dispositivos**. Na lâmina **dispositivos,** o dispositivo antigo deve aparecer como **Offline**.

    ![Dispositivo de origem offline](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Configure o seu dispositivo e registe-o novamente com o seu serviço StorSimple Device Manager. O dispositivo recém-registado deve mostrar como **Pronto para configurar**. O nome do dispositivo para o novo dispositivo é o mesmo que o dispositivo antigo, mas anexado a um numeral que indica que o dispositivo foi reiniciado para o padrão de fábrica e registado novamente.

    ![Dispositivo recém-registado pronto para configurar](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Para o novo dispositivo, preencha a configuração do dispositivo. Para mais informações, aceda ao [Passo 4: Configuração mínima completa do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Na lâmina **dispositivos,** o estado do dispositivo muda para **Online**.

   > [!IMPORTANT]
   > **Complete primeiro a configuração mínima, ou o seu DR pode falhar.**

    ![Dispositivo recentemente registado online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Selecione o dispositivo antigo (estado offline) e a partir da barra de comando, clique **em ''Falha'.** Na lâmina **'Falha' sobre** a lâmina, selecione o dispositivo antigo como fonte e especifique o dispositivo alvo como o dispositivo recém-registado.

    ![Resumo de failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Para obter instruções detalhadas, consulte 'Falha' noutro dispositivo físico.

7. É criado um trabalho de restauro do dispositivo que pode monitorizar a partir da lâmina **Jobs.**

8. Depois de concluído o trabalho com sucesso, aceda ao novo dispositivo e navegue até à lâmina dos **recipientes volume.** Verifique se todos os recipientes de volume do dispositivo antigo migraram para o novo dispositivo.

   ![Contentores de volume migrados](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. Depois de concluída a falha, pode desativar e eliminar o dispositivo antigo do portal. Selecione o dispositivo antigo (offline), clique à direita e, em seguida, **selecione Desativar**. Depois de desativado o dispositivo, o estado do dispositivo é atualizado.

     ![Dispositivo de origem desativado](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Selecione o dispositivo desativado, clique à direita e, em seguida, **selecione Delete**. Isto elimina o dispositivo da lista de dispositivos.

    ![Dispositivo de origem eliminado](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Passos seguintes

* Depois de ter efetuado uma falha, poderá ter de [desativar ou eliminar o seu dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Para obter informações sobre como utilizar o serviço StorSimple Device Manager, vá utilizar [o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

