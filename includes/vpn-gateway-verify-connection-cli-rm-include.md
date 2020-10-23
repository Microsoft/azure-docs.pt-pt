---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4e958026b1167d65f47bddbe5e89ec4d6fed7ee3
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217963"
---
Pode verificar se a sua ligação foi concluída com êxito com o comando [az network vpn-connection show](/cli/azure/network/vpn-connection). No exemplo, '--name' refere-se ao nome da ligação que pretende testar. Quando a ligação ainda estiver a ser estabelecida, o respetivo estado de ligação mostra "A ligar". Quando a ligação estiver estabelecido, o estado muda para "Ligado".

```azurecli-interactive
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
