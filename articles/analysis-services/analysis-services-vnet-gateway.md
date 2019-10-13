---
title: Usar o gateway de dados local para fontes de dados de rede virtual do Azure | Microsoft Docs
description: Saiba como configurar um servidor para usar um gateway para fontes de dados na VNet.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: d1993996c0f982624d6976b50871fa8c4592e2f6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301215"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Usar o gateway para fontes de dados em uma VNet (rede virtual) do Azure

Este artigo descreve a propriedade de servidor **AlwaysUseGateway** para uso quando as fontes de dados estão em uma [VNet (rede virtual) do Azure](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Acesso do servidor a fontes de dados VNet

Se suas fontes de dados forem acessadas por meio de uma VNet, seu servidor de Azure Analysis Services deverá se conectar a essas fontes de dados como se elas fossem locais, em seu próprio ambiente. Você pode configurar a propriedade de servidor **AlwaysUseGateway** para especificar o servidor para acessar todas as fontes de dados por meio de um [Gateway local](analysis-services-gateway.md). 

Instância Gerenciada do Banco de Dados SQL do Azure fontes de dados são executadas na VNet do Azure com um endereço IP privado. Se o ponto de extremidade público estiver habilitado na instância, um gateway não será necessário. Se o ponto de extremidade público não estiver habilitado, um gateway de dados local será necessário e a propriedade AlwaysUseGateway deverá ser definida como true.

> [!NOTE]
> Essa propriedade só é eficaz quando um [Gateway de dados local](analysis-services-gateway.md) é instalado e configurado. O gateway pode estar na VNet.

## <a name="configure-alwaysusegateway-property"></a>Configurar a propriedade AlwaysUseGateway

1. No SSMS > Server > **propriedades** > **geral**, selecione **mostrar propriedades avançadas (todas)** .
2. No **ASPaaS\AlwaysUseGateway**, selecione **true**.

    ![Sempre usar propriedade de gateway](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Ver também
[Conectando-se a fontes de dados locais](analysis-services-gateway.md)   
[Instalar e configurar um gateway de dados local](analysis-services-gateway-install.md)   
[Rede virtual do Azure (VNET)](../virtual-network/virtual-networks-overview.md)   

