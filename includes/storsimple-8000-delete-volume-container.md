---
title: incluir ficheiro
description: incluir ficheiro
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 386a39d76bf47da45f07054124886e174104849e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545414"
---
Para apagar um recipiente de volume, é preciso
 - apagar volumes no recipiente de volume. Se o recipiente de volume tiver volumes associados, desative primeiro esses volumes. Siga os passos em [Tirar um volume offline](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Depois de os volumes estarem offline, pode eliminá-los. 
 - eliminar políticas de backup associadas e instantâneos em nuvem. Verifique se o recipiente de volume tem políticas de backup associadas e instantâneos em nuvem. Em caso afirmativo, [elimine as políticas de backup](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Isto também irá eliminar as imagens em nuvem. 
 
Quando o recipiente de volume não tiver volumes associados, políticas de backup e instantâneos em nuvem, pode eliminá-lo. Execute o seguinte procedimento para eliminar um recipiente de volume.

#### <a name="to-delete-a-volume-container"></a>Para apagar um recipiente de volume

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple e clique em **Dispositivos**. Selecione e clique no dispositivo e, em seguida, vá para **Configurações > Gerir > recipientes de volume**.

    ![Lâmina de recipientes de volume](./media/storsimple-8000-delete-volume-container/create-volume-container.png)

2. A partir da lista tabular de recipientes de volume, selecione o recipiente de volume que pretende eliminar, clique à **direita...** e, em seguida, **selecione Delete**.

    ![Eliminar recipiente de volume](./media/storsimple-8000-delete-volume-container/delete-volume-container-01.png)

3. Se um recipiente de volume não tiver volumes associados, políticas de backup e instantâneos em nuvem, então pode ser eliminado. Quando solicitado para confirmação, reveja e selecione a caixa de verificação indicando o impacto da eliminação do recipiente de volume. Clique **em Eliminar** para eliminar o recipiente de volume.

    ![Confirmar eliminação](./media/storsimple-8000-delete-volume-container/delete-volume-container-02.png)

A lista de recipientes de volume é atualizada para refletir o recipiente de volume eliminado.

![Screenshot da página do recipiente volume. A lista tabular dos recipientes de volume já não contém o recipiente apagado.](./media/storsimple-8000-delete-volume-container/delete-volume-container-05.png)
