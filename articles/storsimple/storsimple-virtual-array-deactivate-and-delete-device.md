---
title: Desative e elimine um Microsoft Azure StorSimple Virtual Array [ Matriz Virtual] do Microsoft Azure StorSimple [ Microsoft Docs
description: Descreve como remover o dispositivo StorSimple do serviço, desativando-o primeiro e, em seguida, eliminando-o.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: bb1a56d204a46f89213f20e317494120f0ea565e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580603"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Desativar e eliminar um StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

Quando desativa um StorSimple Virtual Array, quebra a ligação entre o dispositivo e o serviço correspondente storSimple Device Manager. Este tutorial explica como:

* Desativar um dispositivo 
* Eliminar um dispositivo desativado

A informação neste artigo aplica-se apenas às Matrizes Virtuais StorSimple. Para obter informações sobre a série 8000, vá a como [desativar ou apagar um dispositivo](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Quando desativar?

A desativação é uma operação PERMANENTE e não pode ser desfeita. Não é possível registar novamente um dispositivo desativado com o serviço StorSimple Device Manager. Pode ser necessário desativar e eliminar um StorSimple Virtual Array nos seguintes cenários:

* **Falha planeada** : O seu dispositivo está on-line e planeia falhar sobre o seu dispositivo. Se estiver a planear fazer upgrade para um dispositivo maior, poderá ter de falhar sobre o seu dispositivo. Após a transferência da propriedade dos dados e a falha estar completa, o dispositivo de origem é automaticamente eliminado.
* **Falha não planeada** : O seu dispositivo está offline e precisa de falhar sobre o dispositivo. Este cenário pode ocorrer durante um desastre quando há uma falha no datacenter e o seu dispositivo primário está em baixo. Planeias falhar o dispositivo num dispositivo secundário. Após a transferência da propriedade dos dados e a falha estar completa, o dispositivo de origem é automaticamente eliminado.
* **Desativação:** Pretende desmantelar o dispositivo. Isto requer que primeiro desative o dispositivo e depois o apague. Quando desativa um dispositivo, já não é possível aceder a quaisquer dados armazenados localmente. Só é possível aceder e recuperar os dados armazenados na nuvem. Se planeia manter os dados do dispositivo após a desativação, então deve tirar uma imagem em nuvem de todos os seus dados antes de desativar um dispositivo. Este instantâneo de nuvem permite-lhe recuperar todos os dados numa fase posterior.

## <a name="deactivate-a-device"></a>Desativar um dispositivo

Para desativar o seu dispositivo, execute os seguintes passos.

#### <a name="to-deactivate-the-device"></a>Para desativar o dispositivo

1. Ao seu serviço, vá à **Management > Devices**. Na lâmina **do Dispositivo,** clique e selecione o dispositivo que pretende desativar.
   
    ![Selecione dispositivo para desativar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. Na lâmina do **painel de instrumentos** do dispositivo, **clique... Mais** e mais da lista, selecione **Desativar**.
   
    ![Clique em desativar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. Na lâmina **Desativar,** digite o nome do dispositivo e clique em **Desativar**. 
   
    ![Confirmar desativar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    O processo de desativação começa e demora alguns minutos a ser concluído.
   
    ![Desativar em curso](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Após a desativação, a lista de dispositivos atualiza-se.
   
    ![Desativar completo](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Agora pode apagar este dispositivo.

## <a name="delete-the-device"></a>Eliminar o dispositivo

Um dispositivo tem de ser primeiro desativado para o eliminar. A eliminação de um dispositivo retira-o da lista de dispositivos ligados ao serviço. O serviço já não pode gerir o dispositivo apagado. No entanto, os dados associados ao dispositivo permanecem na nuvem. Estes dados acumulam então encargos.

Para eliminar o dispositivo, execute os seguintes passos.

#### <a name="to-delete-the-device"></a>Para apagar o dispositivo

1. No seu Gestor de Dispositivos StorSimple, vá à **Management > Devices**. Na lâmina **do Dispositivo,** selecione um dispositivo desativado que pretende eliminar.
2. Na lâmina do painel de instrumentos do **Dispositivo,** **clique... Mais** e depois clique em **Apagar**.
   
   ![Selecione dispositivo para eliminar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. Na lâmina **Eliminar,** digite o nome do seu dispositivo para confirmar a eliminação e, em seguida, clique em **Apagar**. A eliminação do dispositivo não elimina os dados em nuvem associados ao dispositivo. 
   
   ![Confirmar eliminação](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. A supressão começa e leva alguns minutos para ser completada.
   
   ![Eliminar em curso](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Depois de o dispositivo ser eliminado, pode visualizar a lista atualizada de dispositivos.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre como falhar, vá ao Failover e à [recuperação de desastres do seu StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md).

* Para saber mais sobre como utilizar o serviço StorSimple Device Manager, vá utilizar [o serviço StorSimple Device Manager para administrar o seu StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md). 

