---
title: Conecte rede virtual Azure à CloudSimple utilizando a ExpressRoute - Azure VMware Solution by CloudSimple
description: Descreve como obter informações de procura de uma ligação entre a rede virtual Azure e o seu ambiente CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77563987"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Ligue a rede virtual Azure à CloudSimple usando o ExpressRoute

Pode estender a sua rede Private Cloud à sua rede virtual Azure e aos recursos Azure. Uma ligação ExpressRoute permite-lhe aceder a recursos que executam a subscrição do Azure a partir da sua Cloud Privada.

## <a name="request-authorization-key"></a>Chave de autorização de pedido

É necessária uma chave de autorização para a ligação ExpressRoute entre a sua Nuvem Privada e a rede virtual Azure. Para obter uma chave, preencha um bilhete com <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">Suporte</a>.  Utilize as seguintes informações no pedido:

* Tipo de emissão: **Técnico**
* Subscrição: **Selecione a subscrição onde o serviço CloudSimple é implementado**
* Serviço: **Solução VMware by CloudSimple**
* Tipo de problema: **Pedido de serviço**
* Subtipo de problema: **Chave de autorização para ligação Azure VNET**
* Objeto: **Pedido de autorização para ligação Azure VNET**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Obtenha informações de peering do portal CloudSimple

Para configurar a ligação, deve estabelecer uma ligação entre a rede virtual Azure e o seu ambiente CloudSimple.  Como parte do procedimento, deve fornecer o circuito de pares URI e a chave de autorização. Obtenha a chave URI e autorização do [portal CloudSimple](access-cloudsimple-portal.md).  Selecione **Rede** no menu lateral e, em seguida, selecione **Ligação de Rede Azure**. Ou selecione **Conta** no menu lateral e, em seguida, selecione a **ligação de rede Azure**.

Copiar o circuito de pares URI e para a chave de autorização para cada uma das regiões usando o ícone *de cópia.* Para cada região cloudSimple pretende ligar:

1. Clique em **Copiar** para copiar o URI. Cola-o num ficheiro onde pode estar disponível para adicionar ao portal Azure.  
2. Clique em **Copiar** para copiar a chave de autorização e cole-a também no ficheiro.

Copie a chave de autorização e o circuito de pares URI que se encontra em estado **disponível.**  **O** estado utilizado indica que a chave já foi utilizada para criar uma ligação de rede virtual.

![Página de ligação de rede virtual](media/virtual-network-connection.png)

Para mais detalhes sobre a configuração da rede virtual Azure para o link CloudSimple, consulte [Connect your Cloud Private Cloud ambiente para a rede virtual Azure utilizando expressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Passos seguintes

* [Ligação de rede virtual Azure à Cloud Privada](azure-expressroute-connection.md)
* [Ligue-se à rede no local utilizando o Azure ExpressRoute](on-premises-connection.md)
