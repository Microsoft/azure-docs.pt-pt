---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b381c5ad8fd81cd9b7411e1f4679b3f5214e6de9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652862"
---
### <a name="noconnection"></a>Para modificar prefixos de endereço de IP do gateway de rede local - sem ligação de gateway

Se não tiver uma ligação de gateway e pretender adicionar ou remover prefixos de endereços IP, utilize o mesmo comando que utilizar para criar o gateway de rede local, [az network local-gateway create](https://docs.microsoft.com/cli/azure/network/local-gateway). Também pode utilizar este comando para atualizar o endereço IP do gateway para o dispositivo VPN. Para substituir as definições atuais, utilize o nome existente do seu gateway de rede local. Se utilizar um nome diferente, irá criar um novo gateway de rede local, em vez de substituir o existente.

Sempre que fizer uma alteração, tem de ser especificada a lista completa de prefixos, não apenas aqueles que pretende alterar. Especifique apenas os prefixos que quer manter. Neste caso, 10.0.0.0/24 e 20.0.0.0/24

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="withconnection"></a>Para modificar prefixos de endereço de IP do gateway de rede local - ligação de gateway existente

Se tiver uma ligação de gateway e pretender adicionar ou remover prefixos de endereços IP, pode atualizar os prefixos a utilizando [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway). Este procedimento resulta num período de indisponibilidade da ligação VPN. Ao modificar os prefixos de endereços IP, não precisa de eliminar o gateway de VPN.

Sempre que fizer uma alteração, tem de ser especificada a lista completa de prefixos, não apenas aqueles que pretende alterar. Neste exemplo, 10.0.0.0/24 e 20.0.0.0/24 já estão presentes. Adicionamos os prefixos 30.0.0.0/24 e 40.0.0.0/24 e especificamos todos os 4 prefixos ao atualizar.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```
