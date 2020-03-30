---
title: incluir ficheiro
description: incluir ficheiro
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 06/08/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: e683d17422321b780a1c01b3011292f2e2c631cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184306"
---
Para eliminar um recipiente de volume, deve
 - eliminar volumes no recipiente de volume. Se o recipiente de volume tiver volumes associados, desative primeiro esses volumes. Siga os passos em [Tomar um volume offline](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Depois de os volumes estarem offline, pode eliminá-los. 
 - eliminar políticas de backup associadas e instantâneos em nuvem. Verifique se o recipiente de volume tem políticas de backup associadas e instantâneos em nuvem. Em caso afirmativo, [elimine as políticas](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy)de backup . Isto também eliminará as imagens da nuvem. 
 
Quando o recipiente de volume não tiver volumes associados, políticas de backup e instantâneos em nuvem, pode eliminá-lo. Efetue o seguinte procedimento para eliminar um recipiente de volume.

#### <a name="to-delete-a-volume-container"></a>Para eliminar um recipiente de volume
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Selecione e clique no dispositivo e, em seguida, vá para **Definições > Gerir > Volume recipientes**.

    ![Lâmina de recipientes de volume](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. A partir da lista tabular de recipientes de volume, selecione o recipiente de volume que pretende eliminar, clique à **direita...** e, em seguida, **selecione Delete**.

    ![Eliminar recipiente de volume](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Se um contentor de volume não tiver volumes associados, políticas de backup e instantâneos em nuvem, então pode ser eliminado. Quando solicitado para confirmação, reveja e selecione a caixa de verificação indicando o impacto da aparação do recipiente de volume. Clique em **Apagar** para, em seguida, apagar o recipiente de volume.

    ![Confirmar eliminação](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

A lista de contentores de volume é atualizada para refletir o recipiente de volume eliminado.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


