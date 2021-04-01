---
title: Failover, recuperação de desastres para outro dispositivo StorSimple 8000
description: Aprenda a falhar sobre o seu dispositivo físico da série StorSimple 8000 para outro dispositivo físico.
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
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 67d6fb1f3cc359288ed942d915e186542a62b0fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017139"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Falha num dispositivo físico da série StorSimple 8000

## <a name="overview"></a>Descrição Geral

Este tutorial descreve os passos necessários para falhar sobre um dispositivo físico da série StorSimple 8000 para outro dispositivo físico StorSimple se houver um desastre. O StorSimple utiliza a função de falha do dispositivo para migrar dados de um dispositivo físico de origem no datacenter para outro dispositivo físico. A orientação neste tutorial aplica-se a dispositivos físicos da série StorSimple 8000 que executam as versões de software Update 3 e posteriormente.

Para saber mais sobre o failover do dispositivo e como é usado para recuperar de um desastre, vá para [Failover e recuperação de desastres para dispositivos da série StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Para falhar um dispositivo físico StorSimple para um aparelho de nuvem StorSimple, vá para ['Falha' num aparelho de nuvem StorSimple](storsimple-8000-device-failover-cloud-appliance.md). Para falhar sobre um dispositivo físico para si mesmo, vá para [Fail sobre o mesmo dispositivo físico StorSimple](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que reviu as considerações para o failover do dispositivo. Para obter mais informações, aceda a [considerações comuns para falha do dispositivo.](storsimple-8000-device-failover-disaster-recovery.md)

- Deve ter um dispositivo físico da série StorSimple 8000 implantado no datacenter. O dispositivo deve executar a versão do software Update 3 ou posterior. Para mais informações, aceda ao [dispositivo StorSimple no local.](storsimple-8000-deployment-walkthrough-u2.md)


## <a name="steps-to-fail-over-to-a-physical-device"></a>Passos para falhar num dispositivo físico

Execute os seguintes passos para restaurar o seu dispositivo num dispositivo físico de destino.

1. Verifique se o recipiente de volume que pretende falhar tem instantâneos de nuvem associados. Para obter mais informações, aceda ao [serviço StorSimple Device Manager para criar cópias de segurança.](storsimple-8000-manage-backup-policies-u2.md)
2. Vá ao seu Gestor de Dispositivos StorSimple e, em seguida, clique em **Dispositivos**. Na lâmina **dispositivos,** aceda à lista de dispositivos ligados ao seu serviço.
    ![Selecione dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Selecione e clique no seu dispositivo de origem. O dispositivo de origem tem os recipientes de volume que pretende falhar. Aceda às **definições > recipientes de volume**.
4. Selecione um recipiente de volume que pretende falhar noutro dispositivo. Clique no recipiente de volume para visualizar a lista de volumes dentro deste recipiente. Selecione um volume, clique à direita e clique em **"Tirar Offline"** para tirar o volume offline. Repita este processo para todos os volumes do recipiente de volume.
5. Repita o passo anterior para todos os recipientes de volume que pretende falhar noutro dispositivo.
6. Volta para a lâmina **dos dispositivos.** A partir da barra de comando, clique **em 'Falha'.**
    ![Clique falha mais](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. Na lâmina **De Falha sobre** a lâmina, execute os seguintes passos:
   
   1. Clique **em Fonte**. São apresentados os recipientes de volume com volumes associados a instantâneos em nuvem. Apenas os recipientes apresentados são elegíveis para a falta de segurança. Na lista de recipientes de volume, selecione os recipientes de volume que pretende falhar. **Apenas são apresentados os recipientes de volume com instantâneos de nuvem associados e volumes offline.**

       ![Selecionar origem](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Clique **no Target**. Para os recipientes de volume selecionados na etapa anterior, selecione um dispositivo-alvo da lista de dispositivos disponíveis. Apenas os dispositivos que tenham capacidade suficiente para acomodar recipientes de volume de origem são apresentados na lista.

        ![Selecionar o destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Por último, reveja todas as definições de failover em **Resumo**. Depois de ter revisto as definições, selecione a caixa de verificação indicando que os volumes em recipientes de volume selecionados estão offline. Clique em **OK**.

       ![Rever definições de failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple cria um trabalho de failover. Clique na notificação de trabalho para monitorizar o trabalho de failover através da lâmina **Jobs.**

    Se o recipiente de volume que falhou tem volumes locais, então vê-se os trabalhos individuais de restauro para cada volume local (não para volumes hierárquicos) no recipiente. Estes trabalhos de restauro podem levar algum tempo a concluir. É provável que o trabalho de repusão possa ser concluído mais cedo. Estes volumes só terão garantias locais após a conclusão dos trabalhos de restauro.

    ![Monitor falha no trabalho](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Depois de concluída a falha, aceda à lâmina **dispositivos.**
   
   1. Selecione o dispositivo que foi utilizado como o dispositivo alvo para o processo de falha.

       ![Selecione dispositivo 2](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Vá à lâmina **dos recipientes de volume.** Todos os recipientes de volume, juntamente com os volumes do dispositivo antigo, devem ser listados.

       ![Ver recipientes de volume alvo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Passos seguintes

* Depois de ter efetuado uma falha, poderá ter de [desativar ou eliminar o seu dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Para obter informações sobre como utilizar o serviço StorSimple Device Manager, vá utilizar [o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

