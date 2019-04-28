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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60724737"
---
Para eliminar um contentor de volume, tem
 - Elimine volumes no contentor de volume. Se o contentor de volumes tem volumes associados, coloque os volumes offline pela primeira vez. Siga os passos em [colocar um volume offline](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Depois dos volumes estiverem offline, pode eliminá-los. 
 - eliminar políticas de cópia de segurança associadas e instantâneos da cloud. Verifique se o contentor de volumes tem políticas de cópia de segurança e instantâneos de cloud associados. Se assim for, em seguida, [eliminar as políticas de cópia de segurança](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Esta ação também elimina os instantâneos de cloud. 
 
Quando o contentor de volumes tem não existem volumes associados, políticas de cópia de segurança e instantâneos de cloud, pode eliminá-la. Execute o seguinte procedimento para eliminar um contentor de volume.

#### <a name="to-delete-a-volume-container"></a>Para eliminar um contentor de volumes
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Selecione e clique no dispositivo e, em seguida, aceda a **definições > Gerir > contentores de volumes**.

    ![Painel de contentores de volume](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Na lista tabular de contentores de volumes, selecione o contentor de volumes que pretende eliminar, clique com botão direito **...**  e, em seguida, selecione **eliminar**.

    ![Eliminar contentor de volumes](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Se a um contentor de volume tiver não existem volumes associados, políticas de cópia de segurança e instantâneos de cloud, em seguida, pode ser eliminado. Quando lhe for pedida confirmação, reveja e selecione a caixa de verificação que indica o impacto da eliminação do contentor de volume. Clique em **eliminar** , em seguida, eliminar o contentor de volume.

    ![Confirmar eliminação](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

A lista de contentores de volume é atualizada para refletir o contentor de volumes eliminado.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


