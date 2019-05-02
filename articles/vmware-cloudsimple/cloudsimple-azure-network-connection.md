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
ms.openlocfilehash: 8ea98d6493b824bfa232ef8193388e93b97c506b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577004"
---
# <a name="azure-network-connection-overview"></a>Descrição geral da ligação de rede do Azure

Quando cria um serviço de CloudSimple numa região, ele:

* Cria o circuito do ExpressRoute do Azure e liga-o para o serviço nessa região
* Liga-se a rede de região CloudSimple a rede virtual do Azure ou a sua rede no local utilizam o Azure ExpressRoute
* Fornece serviços de acesso em execução na sua subscrição do Azure ou a sua rede no local, do seu ambiente de nuvem privada

Esta ligação é a largura de banda alta com baixa latência.

## <a name="benefits"></a>Benefícios

Ligação de rede do Azure permite-lhe:

* Utilize o Azure como destino de cópia de segurança para máquinas virtuais na sua nuvem privada.
* Implemente servidores KMS na sua subscrição do Azure para encriptar o seu arquivo de dados de vSAN de nuvem privada.
* Utilize aplicações híbridas em que a camada web da aplicação é executada na cloud pública, enquanto a aplicação e camadas de base de dados, executar na sua nuvem privada.

## <a name="expressroute-connection-to-on-premises-network"></a>Ligação do ExpressRoute para rede no local

Pode ligar o seu circuito do ExpressRoute do Azure existente para sua região CloudSimple. Funcionalidade de alcance Global do ExpressRoute é utilizada para ligar os dois circuitos entre si.  É estabelecida uma ligação entre locais e circuitos do CloudSimple ExpressRoute.

Este método estabelece uma ligação entre os dois ambientes é:

* Proteger
* Privado
* Largura de banda alta
* Baixa latência

Para criar uma ligação do ExpressRoute para uma rede no local, [contacte o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos Seguintes

* [Configurar a ligação de rede virtual](https://docs.azure.cloudsimple.com/virtual-network-connection)