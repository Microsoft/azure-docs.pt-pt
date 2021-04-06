---
title: Configure a continuidade do negócio e a recuperação de desastres (BCDR) na rede privada virtual Azure Stack Edge (VPN)
description: Descreve como configurar o BCDR na Azure Stack Edge VPN.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: a35a7e5e5c7eccf006f18badad88656e8bc73453
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367695"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>Configure a continuidade do negócio e a recuperação de desastres para a Azure Stack Edge VPN

[!INCLUDE [applies-to-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

Este artigo descreve como configurar a continuidade do negócio e a recuperação de desastres (BCDR) numa rede privada virtual (VPN) configurada num dispositivo Azure Stack Edge.

Este artigo aplica-se tanto ao dispositivo Azure Stack Edge Pro R como ao dispositivo Azure Stack Edge Mini R.

## <a name="configure-failover-to-a-paired-region"></a>Configure falha para uma região emparelhada

O seu dispositivo Azure Stack Edge utiliza outros serviços Azure, por exemplo, O Armazenamento Azure. Pode configurar o BCDR em qualquer serviço Azure específico que seja utilizado pelo dispositivo Azure Stack Edge. Se um serviço Azure utilizado pelo Azure Stack Edge falhar na sua região emparelhada, o dispositivo Azure Stack Edge irá agora ligar-se aos novos endereços IP e a comunicação não será duplamente encriptada. 

O dispositivo Azure Stack Edge utiliza túneis divididos e todos os dados e serviços configurados na região natal (a região associada ao seu dispositivo Azure Stack Edge) passam por cima do túnel VPN. Se os serviços da Azure falharem numa região emparelhada que está fora da região natal, então os dados deixarão de passar por VPN e, portanto, não são duplamente encriptados. 

Neste cenário, normalmente apenas um punhado de serviços da Azure são impactados. Para resolver este problema, devem ser feitas as seguintes alterações na configuração VPN Azure Stack Edge:

1. Adicione a gama IP de serviço failover Azure nas rotas inclusivas para VPN em Azure Stack Edge. Os serviços começarão então a ser encaminhados através da VPN.

    Para adicionar as rotas inclusivas, você precisa baixar o ficheiro json que tem as rotas específicas do serviço. Certifique-se de atualizar este ficheiro com as novas rotas.
2. Adicione a gama IP de serviço Azure correspondente na tabela Rota Azure.
3. Adicione as rotas para a firewall.

> [!NOTE]
>
> 1. A falha de uma porta de entrada Azure VPN e da Rede Virtual Azure (VNET) é abordada na secção [Recuperar de uma região do Azure que falhou devido a desastre.](#recover-from-a-failed-azure-region)
> 2. As gamas IP adicionadas na tabela de rotas Azure podem ultrapassar o limite de 400. Se isso ocorrer, terá de seguir as orientações na secção, [mover-se de uma região de Azure para outra região de Azure.](#move-from-an-azure-region-to-another)

## <a name="recover-from-a-failed-azure-region"></a>Recuperar de uma região de Azure fracassada

No caso de toda a região de Azure falhar devido a um evento catastrófico como o terramoto, todos os serviços Azure naquela região, incluindo o serviço Azure Stack Edge, falharão. Uma vez que existem múltiplos serviços, as rotas inclusivas podem facilmente ir até algumas centenas. Azure tem uma limitação de 400 rotas. 

Quando a região falha, a rede virtual (Vnet) também falha na nova região, assim como o gateway de rede Virtual (gateway VPN). Para resolver esta alteração, faça as seguintes alterações na configuração VPN Azure Stack Edge:

1. Mova o seu Vnet para a região alvo. Para mais informações, consulte: [Mover uma rede virtual Azure para outra região através do portal Azure](../virtual-network/move-across-regions-vnet-portal.md).
2. Implemente uma nova porta de entrada Azure VPN na região alvo onde moveu o Vnet. Para obter mais informações, consulte [Criar um gateway de rede virtual.](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw)
3. Atualize a configuração VPN Azure Stack Edge para utilizar o gateway VPN acima na ligação VPN e, em seguida, selecione a região alvo para adicionar rotas que usam o gateway VPN.
4. Atualize a tabela de rota Azure de entrada se o conjunto de endereços do cliente também mudar. 

## <a name="move-from-an-azure-region-to-another"></a>Passar de uma região de Azure para outra

Pode mover o seu dispositivo Azure Stack Edge de um local para outro local. Para utilizar uma região mais próxima do local onde o seu dispositivo está implantado, terá de configurar o dispositivo para uma nova região doméstica. Efetue as seguintes alterações:

1. Pode atualizar a configuração VPN do Azure Stack Edge para utilizar o gateway VPN de uma nova região e selecionar a nova região para adicionar rotas que utilizem gateway VPN.

## <a name="next-steps"></a>Passos seguintes

[Apoie o seu dispositivo Azure Stack Edge](azure-stack-edge-gpu-prepare-device-failure.md).