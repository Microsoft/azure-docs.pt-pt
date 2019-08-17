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
ms.openlocfilehash: eca3e316d866814f6727dd8ef2c3fa490a551383
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563160"
---
# <a name="azure-network-connections-overview"></a>Visão geral das conexões de rede do Azure

Quando você cria um serviço CloudSimple em uma região, ele:

* Cria um circuito do Azure ExpressRoute e o anexa ao serviço nessa região.
* Conecta sua rede de região do CloudSimple à sua rede virtual do Azure ou à sua rede local usando o Azure ExpressRoute.
* Fornece acesso a serviços em execução na sua assinatura do Azure ou sua rede local de seu ambiente de nuvem privada.

A conexão do ExpressRoute é de alta largura de banda com baixa latência.

## <a name="benefits"></a>Benefícios

A conexão de rede do Azure permite que você:

* Use o Azure como um destino de backup para máquinas virtuais em sua nuvem privada.
* Implante servidores KMS em sua assinatura do Azure para criptografar seu repositório de armazenamento vSAN de nuvem privada.
* Use aplicativos híbridos em que a camada da Web do aplicativo é executada na nuvem pública enquanto as camadas do aplicativo e do banco de dados são executadas em sua nuvem privada.

## <a name="azure-virtual-network-connection"></a>Conexão de rede virtual do Azure

Nuvens privadas podem ser conectadas aos recursos do Azure usando o ExpressRoute.  A conexão do ExpressRoute permite que você acesse recursos em execução na sua assinatura do Azure de sua nuvem privada.  Essa conexão permite que você estenda sua rede de nuvem privada para sua rede virtual do Azure.

[![Conexão do Azure ExpressRoute com a rede virtual](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Conexão do ExpressRoute à rede local

Você pode conectar seu circuito do Azure ExpressRoute existente à sua região do CloudSimple. O recurso de Alcance Global ExpressRoute é usado para conectar os dois circuitos entre si.  Uma conexão é estabelecida entre os circuitos do ExpressRoute local e CloudSimple.  Essa conexão permite que você estenda suas redes locais para a rede de nuvem privada.

![Conexão do ExpressRoute local-Alcance Global](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>Passos Seguintes

* [Conectar a rede virtual do Azure ao CloudSimple usando o ExpressRoute](virtual-network-connection.md)
* [Conectar de local para CloudSimple usando o ExpressRoute](on-premises-connection.md)
