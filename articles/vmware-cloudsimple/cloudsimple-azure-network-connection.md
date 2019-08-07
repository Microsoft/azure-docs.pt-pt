---
title: Solução VMware por CloudSimple-conexões de rede do Azure
description: Saiba como conectar sua rede virtual do Azure à sua rede de região CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8e99da05f71cb01744111b41c301b11a0969057
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812721"
---
# <a name="azure-network-connections-overview"></a>Visão geral das conexões de rede do Azure

Quando você cria um serviço CloudSimple em uma região, ele:

* Cria um circuito do Azure ExpressRoute e o anexa ao serviço nessa região
* Permite a conexão de sua rede de região CloudSimple para sua rede virtual do Azure ou sua rede local usando o Azure ExpressRoute
* Fornece serviços de acesso em execução na sua assinatura do Azure ou em sua rede local, do seu ambiente de nuvem privada

A conexão é:

* Proteger
* Privado
* Largura de banda alta
* Baixa latência

## <a name="benefits"></a>Benefícios

A conexão de rede do Azure permite que você:

* Use o Azure como um destino de backup para máquinas virtuais em sua nuvem privada.
* Implante servidores KMS em sua assinatura do Azure para criptografar seu repositório de armazenamento vSAN de nuvem privada.
* Use aplicativos híbridos em que a camada da Web do aplicativo é executada na nuvem pública enquanto as camadas do aplicativo e do banco de dados são executadas em sua nuvem privada.

## <a name="azure-virtual-network-connection"></a>Conexão de rede virtual do Azure

Nuvens privadas podem ser conectadas aos recursos do Azure usando o ExpressRoute.  Você pode usar essa conexão para acessar diferentes recursos em execução na sua assinatura do Azure de sua nuvem privada.  Essa conexão permite que você estenda sua rede de nuvem privada para sua rede virtual do Azure.

![Conexão do Azure ExpressRoute com a rede virtual](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Conexão do ExpressRoute à rede local

Você pode conectar seu circuito do Azure ExpressRoute existente à sua região do CloudSimple. O recurso de Alcance Global ExpressRoute é usado para conectar os dois circuitos entre si.  Uma conexão é estabelecida entre os circuitos do ExpressRoute local e CloudSimple.  Essa conexão permite que você estenda suas redes locais para a rede de nuvem privada.

![Conexão do ExpressRoute local-Alcance Global](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Passos Seguintes

* [Obter informações de emparelhamento para a rede virtual do Azure para conexão CloudSimple](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Conectar de local para CloudSimple usando o ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
