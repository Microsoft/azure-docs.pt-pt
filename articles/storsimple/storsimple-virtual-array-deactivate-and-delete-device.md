---
title: Desativar e eliminar um Microsoft Azure StorSimple Virtual Array | Documentos da Microsoft
description: Descreve como remover o dispositivo StorSimple de serviço ao primeiro a desativar e, em seguida, excluí-lo.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60580603"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>Desativar e eliminar uma matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

Ao desativar uma StorSimple Virtual Array, interromperá a ligação entre o dispositivo e o serviço StorSimple Device Manager correspondente. Este tutorial explica como:

* Desativar um dispositivo 
* Eliminar um dispositivo desativado

As informações neste artigo aplica-se apenas para as matrizes virtuais do StorSimple. Para obter informações sobre a 8000 série, aceda a como [desativar ou eliminar um dispositivo](storsimple-deactivate-and-delete-device.md).

## <a name="when-to-deactivate"></a>Quando desativar?

Desativação é uma operação permanente e não pode ser anulada. Não é possível registar um dispositivo desativado novamente com o serviço StorSimple Device Manager. Poderá ter de desativar e eliminar um StorSimple Virtual Array nos seguintes cenários:

* **A ativação pós-falha planeada** : O dispositivo está online e quiser efetuar a ativação pós-falha do seu dispositivo. Se estiver a planear atualizar para um dispositivo maior, terá de efetuar a ativação pós-falha do seu dispositivo. Depois da propriedade dos dados é transferida e a ativação pós-falha estiver concluída, o dispositivo de origem é eliminado automaticamente.
* **Ativação pós-falha não planeada** : O dispositivo está offline e terá de efetuar a ativação pós-falha do dispositivo. Este cenário poderá ocorrer durante um desastre quando houver uma falha no Centro de dados e o dispositivo primário está inativo. Planeja fazer a ativação pós-falha do dispositivo para um dispositivo secundário. Depois da propriedade dos dados é transferida e a ativação pós-falha estiver concluída, o dispositivo de origem é eliminado automaticamente.
* **Desativar** : Pretende desativar o dispositivo. Isso exige que primeiro desativar o dispositivo e, em seguida, eliminá-lo. Ao desativar um dispositivo, já não pode aceder a todos os dados armazenados localmente. Só é possível aceder e recuperar os dados armazenados na cloud. Em seguida, se pretender manter os dados do dispositivo após a desativação, deve tirar um instantâneo de cloud de todos os seus dados antes de desativar um dispositivo. Este instantâneo de cloud permite-lhe recuperar todos os dados num estágio posterior.

## <a name="deactivate-a-device"></a>Desativar um dispositivo

Para desativar o seu dispositivo, execute os seguintes passos.

#### <a name="to-deactivate-the-device"></a>Para desativar o dispositivo

1. No seu serviço, ir para **gestão > dispositivos**. Na **dispositivos** painel, clique e selecione o dispositivo que pretende desativar.
   
    ![Selecione o dispositivo para desativar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. No seu **dashboard do dispositivo** painel, clique em **... Obter mais** e na lista, selecione **desativar**.
   
    ![Clique em Desativar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. Na **Deactivate** painel, escreva o nome do dispositivo e, em seguida, clique em **desativar**. 
   
    ![Confirmar Desativação](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    O processo de desativar é iniciado e demora alguns minutos a concluir.
   
    ![Desativar em curso](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. Após a desativação, atualiza a lista de dispositivos.
   
    ![Desativar concluída](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    Agora pode eliminar este dispositivo.

## <a name="delete-the-device"></a>Eliminar o dispositivo

Um dispositivo tem de ser desativado pela primeira vez para eliminá-lo. A eliminar um dispositivo remove-o na lista de dispositivos ligados ao serviço. O serviço, em seguida, pode deixar de gerir o dispositivo eliminado. Os dados associados ao dispositivo permanecem, no entanto, na cloud. Estes dados, em seguida, é acrescido encargos.

Para eliminar o dispositivo, execute os seguintes passos.

#### <a name="to-delete-the-device"></a>Para eliminar o dispositivo

1. No Gestor de dispositivos do StorSimple, aceda a **gestão > dispositivos**. Na **dispositivos** painel, selecione um dispositivo desativado que pretende eliminar.
2. Na **dashboard do dispositivo** painel, clique em **... Obter mais** e, em seguida, clique em **eliminar**.
   
   ![Selecione o dispositivo para eliminar](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. Na **elimine** painel, escreva o nome do seu dispositivo para confirmar a eliminação e, em seguida, clique em **eliminar**. Eliminar o dispositivo não elimina os dados de cloud associados ao dispositivo. 
   
   ![Confirmar eliminação](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. A eliminação é iniciado e demora alguns minutos a concluir.
   
   ![Eliminação em curso](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    Depois do dispositivo é eliminado, pode ver a lista atualizada de dispositivos.

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre como efetuar a ativação pós-falha, aceda a [ativação pós-falha e recuperação após desastre de sua matriz Virtual StorSimple](storsimple-virtual-array-failover-dr.md).

* Para saber mais sobre como utilizar o serviço StorSimple Device Manager, aceda a [utilizar o serviço StorSimple Device Manager para administrar a sua matriz Virtual StorSimple](storsimple-virtual-array-manager-service-administration.md). 

