---
title: Ligue a rede virtual Azure ao CloudSimple utilizando o ExpressRoute - Azure VMware Solution by CloudSimple
description: Descreve como obter informações de peering para uma ligação entre a rede virtual Azure e o seu ambiente CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77563987"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Ligue a rede virtual Azure à CloudSimple usando o ExpressRoute

Pode estender a sua rede Private Cloud à sua rede virtual Azure e recursos Azure. Uma ligação ExpressRoute permite-lhe aceder a recursos em execução na sua subscrição Azure a partir da sua Cloud Privada.

## <a name="request-authorization-key"></a>Chave de autorização de pedido

É necessária uma chave de autorização para a ligação ExpressRoute entre a sua Nuvem Privada e a rede virtual Azure. Para obter uma chave, preencha um bilhete com <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">Suporte</a>.  Utilize as seguintes informações no pedido:

* Tipo de emissão: **Técnico**
* Subscrição: **Selecione a subscrição onde o serviço CloudSimple é implementado**
* Serviço: **Solução VMware by CloudSimple**
* Tipo de problema: **Pedido de serviço**
* Subtipo de problema: **Chave de autorização para ligação Azure VNET**
* Objeto: **Pedido de autorização chave para ligação Azure VNET**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Obtenha informações de espreitar do portal CloudSimple

Para configurar a ligação, tem de estabelecer uma ligação entre a rede virtual Azure e o seu ambiente CloudSimple.  Como parte do procedimento, deve fornecer o circuito de pares URI e a chave de autorização. Obtenha a chave URI e a chave de autorização do [portal CloudSimple](access-cloudsimple-portal.md).  Selecione **Rede** no menu lateral e, em seguida, selecione **Azure Network Connection**. Ou selecione **Conta** no menu lateral e, em seguida, selecione **a ligação à rede Azure.**

Copie o circuito de pares URI e para a chave de autorização de cada uma das regiões utilizando o ícone de *cópia.* Para cada região CloudSimple pretende ligar:

1. Clique **em Copy** para copiar o URI. Cole-o num ficheiro onde possa estar disponível para adicionar ao portal Azure.  
2. Clique em **Copiar** para copiar a chave de autorização e cole-a também no ficheiro.

Copie a chave de autorização e o circuito de pares URI que está em estado **disponível.**  **O** estado utilizado indica que a chave já foi utilizada para criar uma ligação de rede virtual.

![Página de conexão de rede virtual](media/virtual-network-connection.png)

Para obter mais detalhes sobre a configuração da rede virtual Azure para a ligação CloudSimple, consulte [o ambiente CloudSimple Private Cloud à rede virtual Azure utilizando o ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Próximos passos

* [Ligação de rede virtual Azure à Nuvem Privada](azure-expressroute-connection.md)
* [Ligue-se à rede de instalações utilizando a Azure ExpressRoute](on-premises-connection.md)
