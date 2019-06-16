---
title: Solução de VMware ao CloudSimple - ligações de rede do Azure
description: Saiba mais sobre a ligação de rede virtual do Azure à sua rede de região CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ab82b6c1b4b373c186019eaf96f9864861b9d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497594"
---
# <a name="azure-network-connections-overview"></a>Descrição geral das ligações de rede do Azure

Quando cria um serviço de CloudSimple numa região, ele:

* Cria um circuito do ExpressRoute do Azure e liga-o para o serviço nessa região
* Permite a ligação da sua rede de região CloudSimple a rede virtual do Azure ou a sua rede no local utilizam o Azure ExpressRoute
* Fornece serviços de acesso em execução na sua subscrição do Azure ou a sua rede no local, do seu ambiente de nuvem privada

A ligação é:

* Proteger
* Privado
* Largura de banda alta
* Baixa latência

## <a name="benefits"></a>Benefícios

Ligação de rede do Azure permite-lhe:

* Utilize o Azure como destino de cópia de segurança para máquinas virtuais na sua nuvem privada.
* Implemente servidores KMS na sua subscrição do Azure para encriptar o seu arquivo de dados de vSAN de nuvem privada.
* Utilize aplicações híbridas em que a camada web da aplicação é executada na cloud pública, enquanto a aplicação e camadas de base de dados, executar na sua nuvem privada.

## <a name="azure-virtual-network-connection"></a>Ligação de rede virtual do Azure

Nuvens privadas podem ser ligadas aos recursos do Azure através do ExpressRoute.  Pode utilizar esta ligação para aceder a recursos diferentes em execução na sua subscrição do Azure a partir da nuvem privada.  Esta ligação permite-lhe expandir, rede de nuvem privada à sua rede virtual do Azure.

![Ligação do ExpressRoute do Azure para a rede virtual](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Ligação do ExpressRoute para rede no local

Pode ligar o seu circuito do ExpressRoute do Azure existente para sua região CloudSimple. Funcionalidade de alcance Global do ExpressRoute é utilizada para ligar os dois circuitos entre si.  É estabelecida uma ligação entre o local e os circuitos do CloudSimple ExpressRoute.  Esta ligação permite-lhe expandir as redes no local para a rede de nuvem privada.

![Ligação do ExpressRoute no local - alcance Global](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Passos Seguintes

* [Obter informações de peering de rede virtual do Azure para a ligação de CloudSimple](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Ligar no local ao CloudSimple através do ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
