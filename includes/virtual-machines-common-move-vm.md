---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 61990e785741799fcbcd4e6df965953bd9944f4d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110428"
---
## <a name="use-the-azure-portal-to-move-a-vm-to-a-different-subscription"></a>Utilizar o portal do Azure para mover uma VM para uma subscrição diferente
Pode mover uma VM e os respetivos recursos associados a uma subscrição diferente com o portal do Azure.

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Clique em **navegue** > **grupos de recursos** e selecione o grupo de recursos que contém a VM que pretende mover.
3. Na parte superior da página para o grupo de recursos, selecione **mover** e, em seguida, selecione **mover para outra subscrição**. O **mover recursos** é aberta a página.
4. Selecione cada um dos recursos a mover. Na maioria dos casos, deve mover todos os recursos relacionados que estão listados.
5. Selecione o **subscrição** onde pretende que a VM ser movida.
6. Selecione um existente **grupo de recursos**, ou introduza um nome para que um novo grupo de recursos criado.
7. Quando estiver concluído, selecione de que entenda que serão criados novos IDs de recurso e que os IDs de nova tem de ser utilizado com a VM depois de ser movido e, em seguida, selecione **OK**.

## <a name="use-the-azure-portal-to-move-a-vm-to-another-resource-group"></a>Utilizar o portal do Azure para mover uma VM para outro grupo de recursos
Pode mover uma VM e os respetivos recursos associados a outro grupo de recursos com o portal do Azure.

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Clique em **navegue** > **máquinas virtuais** e selecione a VM que pretende mover a partir da lista.
3. Na página para a VM, junto a etiqueta do grupo de recursos, selecione **alteração**. O **mover recursos** é aberta a página.
4. Selecione cada um dos recursos a mover. Na maioria dos casos, deve mover todos os recursos relacionados que estão listados.
5. Selecione um existente **grupo de recursos**, ou introduza um nome para que um novo grupo de recursos criado.
6. Quando estiver concluído, selecione de que entenda que serão criados novos IDs de recurso e que os IDs de nova tem de ser utilizado com a VM depois de ser movido e, em seguida, selecione **OK**.

