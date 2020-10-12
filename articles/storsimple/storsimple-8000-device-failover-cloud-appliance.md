---
title: Falha e recuperação de desastres para um aparelho de nuvem StorSimple
description: Aprenda a falhar sobre o seu dispositivo físico da série StorSimple 8000 para um aparelho em nuvem.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 18b34ff466f3935cb5cd18d46e6d26e36e756a9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91774465"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Falhe no seu StorSimple Cloud Appliance

## <a name="overview"></a>Descrição geral

Este tutorial descreve os passos necessários para falhar sobre um dispositivo físico da série StorSimple 8000 para um StorSimple Cloud Appliance se houver um desastre. O StorSimple utiliza a função de falha do dispositivo para migrar dados de um dispositivo físico de origem no datacenter para um aparelho em nuvem em funcionamento em Azure. A orientação neste tutorial aplica-se a dispositivos físicos da série StorSimple 8000 e a aparelhos em nuvem que executam versões de software Update 3 e posteriormente.

Para saber mais sobre o failover do dispositivo e como é usado para recuperar de um desastre, vá para [Failover e recuperação de desastres para dispositivos da série StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Para falhar um dispositivo físico StorSimple para outro dispositivo físico, vá para [Fail over over to a StorSimple physical device](storsimple-8000-device-failover-physical-device.md). Para falhar por cima de um dispositivo para si mesmo, vá para [Falhar para o mesmo dispositivo físico StorSimple](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que reviu as considerações para o failover do dispositivo. Para obter mais informações, aceda a [considerações comuns para falha do dispositivo.](storsimple-8000-device-failover-disaster-recovery.md)

- Deve ter um Aparelho StorSimple cloud criado e configurado antes de executar este procedimento. Se estiver a executar a versão de software Update 3 ou mais tarde, considere utilizar um aparelho em nuvem 8020 para o DR. O modelo 8020 tem 64 TB e utiliza o Premium Storage. Para obter mais informações, aceda ao [Implementar e gerencie um StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Passos para falhar para um aparelho em nuvem

Execute os seguintes passos para restaurar o dispositivo num aparelho de nuvem StorSimple.

1.  Verifique se o recipiente de volume que pretende falhar tem instantâneos de nuvem associados. Para obter mais informações, aceda ao [serviço StorSimple Device Manager para criar cópias de segurança.](storsimple-8000-manage-backup-policies-u2.md)
2. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Na lâmina **dispositivos,** aceda à lista de dispositivos ligados ao seu serviço.
    ![Selecione dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Selecione e clique no seu dispositivo de origem. O dispositivo de origem tem os recipientes de volume que pretende falhar. Aceda às **definições > recipientes de volume**.

    ![Selecione dispositivo 2](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Selecione um recipiente de volume que pretende falhar noutro dispositivo. Clique no recipiente de volume para visualizar a lista de volumes dentro deste recipiente. Selecione um volume, clique à direita e clique em **"Tirar Offline"** para tirar o volume offline.

    ![Selecione dispositivo 3](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Repita este processo para todos os volumes do recipiente de volume.

     ![Selecione dispositivo 4](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Repita o passo anterior para todos os recipientes de volume que pretende falhar noutro dispositivo.

7. Volta para a lâmina **dos dispositivos.** A partir da barra de comando, clique **em 'Falha'.**

    ![Clique falha mais](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. Na lâmina **De Falha sobre** a lâmina, execute os seguintes passos:
   
    1. Clique **em Fonte**. Selecione os recipientes de volume para falhar. **Apenas são apresentados os recipientes de volume com instantâneos de nuvem associados e volumes offline.**
        ![Selecionar origem](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Clique **no Target**. Selecione um aparelho em nuvem-alvo da lista de dispositivos disponíveis. **Apenas os dispositivos que tenham capacidade suficiente para acomodar recipientes de volume de origem são apresentados na lista.**

        ![Selecionar o destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Reveja as definições de failover em **Resumo** e selecione a caixa de verificação indicando que os volumes em recipientes de volume selecionados estão offline. 

        ![Rever definições de failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Um trabalho de fracasso é criado. Para monitorizar o trabalho de failover, clique na notificação de trabalho.

    ![Monitor falha no trabalho](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. Depois de concluída a falha, volte para a lâmina **dispositivos.**

    1. Selecione o dispositivo que foi utilizado como o alvo para a falha.

       ![Selecione dispositivo 5](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Clique em **Recipientes de Volume**. Todos os recipientes de volume, juntamente com os volumes do dispositivo antigo, devem ser listados.

       Se o recipiente de volume que falhou tiver volumes fixados localmente, esses volumes são falhados como volumes hierárquicos. Os volumes fixados localmente não são suportados num aparelho de nuvem StorSimple.

       ![Ver recipientes de volume alvo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Passos seguintes

* Depois de ter efetuado uma falha, poderá ter de [desativar ou eliminar o seu dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).

* Para obter informações sobre como utilizar o serviço StorSimple Device Manager, vá utilizar [o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

