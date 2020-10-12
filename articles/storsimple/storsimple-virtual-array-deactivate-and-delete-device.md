---
title: Desativar e apagar um Microsoft Azure StorSimple Virtual Array Microsoft Docs
description: Descreve como remover o dispositivo StorSimple do serviço, desativando-o primeiro e depois eliminando-o.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: c787df901fc33c2dcd2c8a901202c72ea6de45d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85513605"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Desativar e eliminar um StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

Quando desativa um Conjunto Virtual StorSimple, quebra a ligação entre o dispositivo e o serviço de Gestor de Dispositivos StorSimple correspondente. Este tutorial explica como:

* Desativar um dispositivo 
* Eliminar um dispositivo desativado

As informações neste artigo aplicam-se apenas a Matrizes Virtuais StorSimple. Para obter informações sobre a série 8000, aceda à forma [de desativar ou eliminar um dispositivo](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Quando desativar?

A desativação é uma operação PERMANENTE e não pode ser desfeita. Não é possível voltar a registar um dispositivo desativado com o serviço StorSimple Device Manager. Poderá ser necessário desativar e eliminar um StorSimple Virtual Array nos seguintes cenários:

* **Falha planeada:** O seu dispositivo está online e planeia falhar sobre o seu dispositivo. Se estiver a planear fazer upgrade para um dispositivo maior, poderá ter de falhar por cima do seu dispositivo. Após a transferência da propriedade dos dados e a falha estar completa, o dispositivo de origem é automaticamente eliminado.
* **Falha não planeada:** O seu dispositivo está desligado e tem de falhar por cima do dispositivo. Este cenário pode ocorrer durante um desastre quando há uma paragem no datacenter e o seu dispositivo principal está em baixo. Planeia falhar o dispositivo para um dispositivo secundário. Após a transferência da propriedade dos dados e a falha estar completa, o dispositivo de origem é automaticamente eliminado.
* **Desativação:** Pretende desmantelar o dispositivo. Isto requer que desative primeiro o dispositivo e depois o elimine. Quando desativa um dispositivo, já não é possível aceder a quaisquer dados armazenados localmente. Só é possível aceder e recuperar os dados armazenados na nuvem. Se planeia manter os dados do dispositivo após a desativação, então deve tirar uma imagem em nuvem de todos os seus dados antes de desativar um dispositivo. Esta imagem em nuvem permite-lhe recuperar todos os dados numa fase posterior.

## <a name="deactivate-a-device"></a>Desativar um dispositivo

Para desativar o seu dispositivo, execute os seguintes passos.

#### <a name="to-deactivate-the-device"></a>Para desativar o dispositivo

1. Ao seu serviço, aceda a **Dispositivos de > de Gestão.** Na lâmina **dispositivos,** clique e selecione o dispositivo que deseja desativar.
   
    ![Selecione dispositivo para desativar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. Na lâmina **do painel de instrumentos do dispositivo,** **clique... Mais** e da lista, **selecione Desativar**.
   
    ![Clique em desativar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. Na lâmina **desativar, digite** o nome do dispositivo e, em seguida, clique em **Desativar**. 
   
    ![Confirmar desativar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    O processo de desativar começa e demora alguns minutos a ser concluído.
   
    ![Desativar em curso](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Após a desativação, a lista de dispositivos atualiza-se.
   
    ![Desativar completo](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Agora pode eliminar este dispositivo.

## <a name="delete-the-device"></a>Eliminar o dispositivo

Um dispositivo tem de ser primeiro desativado para o eliminar. A eliminação de um dispositivo retira-o da lista de dispositivos ligados ao serviço. O serviço não pode então gerir o dispositivo eliminado. Os dados associados ao dispositivo permanecem, no entanto, na nuvem. Estes dados acumulam então encargos.

Para eliminar o aparelho, execute os seguintes passos.

#### <a name="to-delete-the-device"></a>Para eliminar o dispositivo

1. No seu Gestor de Dispositivos StorSimple, vá a **Dispositivos de > de Gestão.** Na lâmina **dispositivos,** selecione um dispositivo desativado que deseje eliminar.
2. Na lâmina do **painel de instrumentos** do dispositivo, **clique... Mais** e, em seguida, clique em **Eliminar**.
   
   ![Selecione o dispositivo para eliminar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. Na lâmina **Eliminar,** digite o nome do seu dispositivo para confirmar a eliminação e, em seguida, clique em **Eliminar**. A eliminação do dispositivo não elimina os dados em nuvem associados ao dispositivo. 
   
   ![Confirmar eliminação](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. A eliminação começa e leva alguns minutos para ser concluída.
   
   ![Excluir em curso](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Depois de o dispositivo ser eliminado, pode visualizar a lista atualizada de dispositivos.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre como falhar, vá ao Failover e à [recuperação de desastres do seu StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md).

* Para saber mais sobre como utilizar o serviço StorSimple Device Manager, vá utilizar [o serviço StorSimple Device Manager para administrar o seu StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md). 

