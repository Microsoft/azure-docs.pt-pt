---
title: Configure Azure Analysis Services for VNet data sources [ Microsoft Docs
description: Saiba como configurar um servidor de Serviços de Análise Azure para utilizar uma porta de entrada para fontes de dados na Rede Virtual Azure (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572267"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Utilize o portal para fontes de dados numa Rede Virtual Azure (VNet)

Este artigo descreve a propriedade do servidor Do servidor **AlwaysUseGateway** Services De análise Azure para utilização quando as fontes de dados estão numa [Rede Virtual Azure (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Acesso do servidor a fontes de dados VNet

Se as suas fontes de dados forem acedidas através de um VNet, o seu servidor Azure Analysis Services deve ligar-se a essas fontes de dados como se estivessem no local, no seu próprio ambiente. Pode configurar a propriedade do servidor **AlwaysUseGateway** para especificar o servidor para aceder a todas as fontes de dados através de um [gateway On-premises](analysis-services-gateway.md). 

As fontes de dados geridas pela Base de Dados Azure SQL executam as fontes de dados do Azure VNet com um endereço IP privado. Se o ponto final público estiver ativado na instância, não é necessária uma porta de entrada. Se o ponto final público não estiver ativado, é necessário um Gateway de Dados no local e a propriedade AlwaysUseGateway deve ser definida como verdadeira.

> [!NOTE]
> Esta propriedade só é eficaz quando um [Gateway de Dados no local](analysis-services-gateway.md) é instalado e configurado. O portal pode estar na VNet.

## <a name="configure-alwaysusegateway-property"></a>Configure propriedade AlwaysUseGateway

1. No SSMS > servidor > **Propriedades Gerais,** > **General**selecione **Propriedades Avançadas (Todas)**.
2. No **ASPaaS\AlwaysUseGateway,** selecione **true**.

    ![Use sempre a propriedade gateway](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Consulte também
[Ligação a fontes de dados no local](analysis-services-gateway.md)   
[Instalar e configurar um portal de dados no local](analysis-services-gateway-install.md)   
[Rede Virtual Azure (VNET)](../virtual-network/virtual-networks-overview.md)   

