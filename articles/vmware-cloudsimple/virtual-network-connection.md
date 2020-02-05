---
title: Ligue a rede virtual Azure ao AVS utilizando o ExpressRoute
description: Descreve como obter informações de procura de uma ligação entre a rede virtual Azure e o seu ambiente AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1ff11bbafe6f9057ce70c799e0437691d89ccb40
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014407"
---
# <a name="connect-azure-virtual-network-to-avs-using-expressroute"></a>Ligue a rede virtual Azure ao AVS utilizando o ExpressRoute

Pode estender a sua rede AVS Private Cloud à sua rede virtual Azure e aos recursos Azure. Uma ligação ExpressRoute permite-lhe aceder a recursos que executam a subscrição do Azure a partir da sua Nuvem Privada AVS.

## <a name="request-authorization-key"></a>Chave de autorização de pedido

É necessária uma chave de autorização para a ligação ExpressRoute entre a sua Nuvem Privada AVS e a rede virtual Azure. Para obter uma chave, preencha um bilhete com <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">Suporte</a>. Utilize as seguintes informações no pedido:

* Tipo de emissão: **Técnico**
* Subscrição: **Selecione a subscrição onde o serviço AVS é implementado**
* Serviço: **Soluções VMware (AVS)**
* Tipo de problema: **Pedido de serviço**
* Subtipo de problema: **Chave de autorização para ligação Azure VNET**
* Objeto: **Pedido de autorização para ligação Azure VNET**

## <a name="get-peering-information-from-avs-portal"></a>Obtenha informações de peering do portal AVS

Para configurar a ligação, deve estabelecer uma ligação entre a rede virtual Azure e o seu ambiente AVS. Como parte do procedimento, deve fornecer o circuito de pares URI e a chave de autorização. Obtenha a chave URI e autorização do [portal AVS](access-cloudsimple-portal.md). Selecione **Rede** no menu lateral e, em seguida, selecione **Ligação de Rede Azure**. Ou selecione **Conta** no menu lateral e, em seguida, selecione a **ligação de rede Azure**.

Copiar o circuito de pares URI e para a chave de autorização para cada uma das regiões usando o ícone *de cópia.* Para cada região AVS pretende ligar:

1. Clique em **Copiar** para copiar o URI. Cola-o num ficheiro onde pode estar disponível para adicionar ao portal Azure. 
2. Clique em **Copiar** para copiar a chave de autorização e cole-a também no ficheiro.

Copie a chave de autorização e o circuito de pares URI que se encontra em estado **disponível.** **O** estado utilizado indica que a chave já foi utilizada para criar uma ligação de rede virtual.

![Página de ligação de rede virtual](media/virtual-network-connection.png)

Para mais detalhes sobre a configuração da rede virtual Azure para a ligação AVS, consulte [Connect your AVS Private Cloud ambiente para a rede virtual Azure utilizando expressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Passos seguintes

* [Ligação de rede virtual Azure à Nuvem Privada AVS](azure-expressroute-connection.md)
* [Conectar-se à rede local usando o Azure ExpressRoute](on-premises-connection.md)
