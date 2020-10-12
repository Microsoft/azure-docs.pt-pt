---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0e009354e66ab13cdb9fbc3cf9e4b37e904bdfd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184089"
---
Pode verificar se a sua ligação foi concluída com êxito com o comando [az network vpn-connection show](/cli/azure/network/vpn-connection). No exemplo, '--name' refere-se ao nome da ligação que pretende testar. Quando a ligação ainda estiver a ser estabelecida, o respetivo estado de ligação mostra "A ligar". Quando a ligação estiver estabelecido, o estado muda para "Ligado".

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
