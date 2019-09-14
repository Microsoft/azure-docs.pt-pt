---
title: Usar o gateway de dados local para fontes de dados de rede virtual do Azure | Microsoft Docs
description: Saiba como configurar um servidor para usar um gateway para fontes de dados na VNet.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 675d8ecd3d6a3310a9b102df37df18bed02df3de
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70958811"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Usar o gateway para fontes de dados em uma VNet (rede virtual) do Azure

Este artigo descreve a propriedade de servidor **AlwaysUseGateway** para uso quando as fontes de dados estão em uma [VNet (rede virtual) do Azure](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Acesso do servidor a fontes de dados VNet

Se suas fontes de dados forem acessadas por meio de uma VNet, seu servidor de Azure Analysis Services deverá se conectar a essas fontes de dados como se elas fossem locais, em seu próprio ambiente. Você pode configurar a propriedade de servidor **AlwaysUseGateway** para especificar o servidor para acessar todas as fontes de dados por meio de um [Gateway local](analysis-services-gateway.md). 

Instância Gerenciada do Banco de Dados SQL do Azure fontes de dados são executadas na VNet do Azure com um endereço IP privado. Se o ponto de extremidade público estiver habilitado na instância, um gateway não será necessário. Se o ponto de extremidade público não estiver habilitado, um gateway de dados local será necessário e a propriedade AlwaysUseGateway deverá ser definida como true.

> [!NOTE]
> Essa propriedade só é eficaz quando um [Gateway de dados local](analysis-services-gateway.md) é instalado e configurado. O gateway pode estar na VNet.

## <a name="configure-alwaysusegateway-property"></a>Configurar a propriedade AlwaysUseGateway

1. No SSMS > Server > **Propriedades** > **geral**, selecione **mostrar propriedades avançadas (todas)** .
2. No **ASPaaS\AlwaysUseGateway**, selecione **true**.

    ![Sempre usar propriedade de gateway](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Consulte também
[Conectando-se a fontes de dados locais](analysis-services-gateway.md)   
[Instalar e configurar um gateway de dados local](analysis-services-gateway-install.md)   
[Rede virtual do Azure (VNET)](../virtual-network/virtual-networks-overview.md)   

