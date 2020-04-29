---
title: Falha e recuperação de desastres para um storSimple Cloud Appliance
description: Aprenda a falhar sobre o seu dispositivo físico da série StorSimple 8000 para um aparelho em nuvem.
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
ms.openlocfilehash: 347b899608d4322a7873b9f80f38ca1c767194d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77468750"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Falhe no seu storSimple Cloud Appliance

## <a name="overview"></a>Descrição geral

Este tutorial descreve os passos necessários para falhar sobre um dispositivo físico da série StorSimple 8000 para um StorSimple Cloud Appliance se houver um desastre. O StorSimple utiliza a funcionalidade failover do dispositivo para migrar dados de um dispositivo físico de origem no datacenter para um aparelho em nuvem em funcionamento em Azure. A orientação neste tutorial aplica-se aos dispositivos físicos da série StorSimple 8000 e aos aparelhos em nuvem que executam as versões de software Update 3 e posteriormente.

Para saber mais sobre a falha do dispositivo e como é usado para recuperar de um desastre, vá ao Failover e à recuperação de [desastres para dispositivos da série StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Para falhar sobre um dispositivo físico StorSimple para outro dispositivo físico, vá para [fail até um dispositivo físico StorSimple](storsimple-8000-device-failover-physical-device.md). Para falhar sobre um dispositivo para si mesmo, vá para [Fail over to the same StorSimple physical device](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que reviu as considerações para a falha do dispositivo. Para mais informações, vá a [considerações comuns para a falha](storsimple-8000-device-failover-disaster-recovery.md)do dispositivo .

- Deve ter um StorSimple Cloud Appliance criado e configurado antes de executar este procedimento. Se executar a versão do software Update 3 ou posteriormente, considere utilizar um aparelho de nuvem 8020 para o DR. O modelo 8020 tem 64 TB e utiliza O Armazenamento Premium. Para mais informações, vá para [a Implementação e gerencie um StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Passos para falhar num aparelho de nuvem

Execute os seguintes passos para restaurar o dispositivo num aparelho de nuvem StorSimple target.

1.  Verifique se o recipiente de volume que pretende falhar tem imagens de nuvem associadas. Para mais informações, vá ao [serviço StorSimple Device Manager para criar backups](storsimple-8000-manage-backup-policies-u2.md).
2. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lâmina **dispositivos,** vá à lista de dispositivos ligados ao seu serviço.
    ![Selecione dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Selecione e clique no seu dispositivo de origem. O dispositivo de origem tem os recipientes de volume que pretende falhar. Vá a **Definições > recipientes**de volume .

    ![Selecione dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Selecione um recipiente de volume que gostaria de falhar noutro dispositivo. Clique no recipiente de volume para mostrar a lista de volumes dentro deste recipiente. Selecione um volume, clique à direita e clique em **Take Offline** para desligar o volume.

    ![Selecione dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Repita este processo para todos os volumes do recipiente de volume.

     ![Selecione dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Repita o passo anterior para todos os recipientes de volume que gostaria de falhar noutro dispositivo.

7. Volta para a lâmina dos **Dispositivos.** A partir da barra de comando, clique **em Fail over**.

    ![Clique em falhar](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. Na **lâmina Fail,** execute os seguintes passos:
   
    1. Clique em **Origem**. Selecione os recipientes de volume para falhar. **Apenas são apresentados os recipientes de volume com imagens de nuvem associadas e volumes offline.**
        ![Selecionar origem](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Clique no **target**. Selecione um aparelho de nuvem alvo a partir da lista de dispositivos disponíveis. **Apenas os dispositivos que possuem capacidade suficiente para acomodar recipientes de volume de origem são apresentados na lista.**

        ![Selecionar o destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Reveja as definições de failover em **Resumo** e selecione a caixa de verificação indicando que os volumes em recipientes de volume selecionados estão offline. 

        ![Rever definições de failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. É criado um trabalho de falhanço. Para monitorizar o trabalho de failover, clique na notificação de emprego.

    ![Monitorizar trabalho de failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Depois de concluída a falha, volte à lâmina **do Dispositivo.**

    1. Selecione o dispositivo que foi usado como alvo para a falha.

       ![Selecione dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Clique em **recipientes de volume**. Todos os recipientes de volume, juntamente com os volumes do dispositivo antigo, devem ser listados.

       Se o recipiente de volume que falhou tiver volumes fixados localmente, esses volumes são falhados como volumes hierárquicos. Os volumes fixados localmente não são suportados num StorSimple Cloud Appliance.

       ![Ver recipientes de volume alvo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Passos seguintes

* Depois de ter realizado uma falha, poderá ter de [desativar ou eliminar o seu dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Para obter informações sobre como utilizar o serviço StorSimple Device Manager, vá utilizar [o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

