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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 9e4e890ab5491e46ffe5ea0e1c168d168f9cc729
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468614"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Falhar num dispositivo físico da série StorSimple 8000

## <a name="overview"></a>Descrição geral

Este tutorial descreve os passos necessários para falhar sobre um dispositivo físico da série StorSimple 8000 para outro dispositivo físico StorSimple se houver um desastre. O StorSimple utiliza a funcionalidade failover do dispositivo para migrar dados de um dispositivo físico de origem no datacenter para outro dispositivo físico. A orientação neste tutorial aplica-se aos dispositivos físicos da série StorSimple 8000 que executam as versões de software Update 3 e posteriormente.

Para saber mais sobre a falha do dispositivo e como é usado para recuperar de um desastre, vá ao Failover e à recuperação de [desastres para dispositivos da série StorSimple 8000](storsimple-8000-device-failover-disaster-recovery.md).

Para falhar sobre um dispositivo físico StorSimple para um storSimple Cloud Appliance, vá para [fail over to a StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md). Para falhar sobre um dispositivo físico para si mesmo, vá para [Fail over to the same StorSimple physical device](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que reviu as considerações para a falha do dispositivo. Para mais informações, vá a [considerações comuns para a falha](storsimple-8000-device-failover-disaster-recovery.md)do dispositivo .

- Deve ter um dispositivo físico da série StorSimple 8000 implantado no datacenter. O dispositivo deve executar a versão de software Update 3 ou posterior. Para mais informações, vá implementar [o seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Passos para falhar num dispositivo físico

Execute os seguintes passos para restaurar o seu dispositivo num dispositivo físico alvo.

1. Verifique se o recipiente de volume que pretende falhar tem imagens de nuvem associadas. Para mais informações, vá ao [serviço StorSimple Device Manager para criar backups](storsimple-8000-manage-backup-policies-u2.md).
2. Vá ao seu Gestor de Dispositivos StorSimple e, em seguida, clique em **Dispositivos**. Na lâmina **dispositivos,** vá à lista de dispositivos ligados ao seu serviço.
    ![Selecione](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png) do dispositivo
3. Selecione e clique no seu dispositivo de origem. O dispositivo de origem tem os recipientes de volume que pretende falhar. Ir a **Definições > Contentores de Volume**.
4. Selecione um recipiente de volume que gostaria de falhar noutro dispositivo. Clique no recipiente de volume para mostrar a lista de volumes dentro deste recipiente. Selecione um volume, clique à direita e clique em **Take Offline** para desligar o volume. Repita este processo para todos os volumes do recipiente de volume.
5. Repita o passo anterior para todos os recipientes de volume que gostaria de falhar noutro dispositivo.
6. Volta para a lâmina dos **Dispositivos.** A partir da barra de comando, clique **em Fail over**.
    ![Click falha sobre](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. Na **lâmina Fail,** execute os seguintes passos:
   
   1. Clique em **Origem**. Os recipientes de volume com volumes associados a imagens em nuvem são apresentados. Apenas os recipientes apresentados são elegíveis para a falha. Na lista de recipientes de volume, selecione os recipientes de volume que gostaria de falhar. **Apenas são apresentados os recipientes de volume com imagens de nuvem associadas e volumes offline.**

       ![Selecionar origem](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Clique no **target**. Para os recipientes de volume selecionados na etapa anterior, selecione um dispositivo-alvo a partir da lista de dispositivos disponíveis. Apenas os dispositivos que possuem capacidade suficiente para acomodar recipientes de volume de origem são apresentados na lista.

        ![Selecionar o destino](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Por fim, reveja todas as definições de failover em **Resumo**. Depois de ter revisto as definições, selecione a caixa de verificação indicando que os volumes em recipientes de volume selecionados estão offline. Clique em **OK**.

       ![Rever definições de failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple cria um trabalho de falha. Clique na notificação de trabalho para monitorizar o trabalho de failover através da lâmina **Jobs.**

    Se o contentor de volume que falhou tiver volumes locais, então verá trabalhos individuais de restauro para cada volume local (não para volumes hierárquicos) no recipiente. Estes trabalhos de restauro podem levar algum tempo a ser concluídos. É provável que o trabalho de failover possa ser concluído mais cedo. Estes volumes só terão garantias locais após a conclusão dos postos de trabalho.

    ![Monitorizar trabalho de failover](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Depois de concluída a falha, vá à lâmina **do Dispositivo.**
   
   1. Selecione o dispositivo que foi usado como dispositivo-alvo para o processo de failover.

       ![Selecione dispositivo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Vá para a lâmina dos contentores de **volume.** Todos os recipientes de volume, juntamente com os volumes do dispositivo antigo, devem ser listados.

       ![Ver recipientes de volume alvo](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Passos seguintes

* Depois de ter realizado uma falha, poderá ter de [desativar ou eliminar o seu dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* Para obter informações sobre como utilizar o serviço StorSimple Device Manager, vá utilizar [o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

