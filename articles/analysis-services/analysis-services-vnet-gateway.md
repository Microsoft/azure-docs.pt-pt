---
title: Configure Serviços de Análise Azure para fontes de dados VNet | Microsoft Docs
description: Saiba como configurar um servidor Azure Analysis Services para utilizar um portal para fontes de dados na Rede Virtual Azure (VNet).
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 912dab16cd854ae940b7bbdfe88a8da7adf1c5e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84197200"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Utilizar o gateway das origens de dados numa Rede Virtual do Azure (VNet)

Este artigo descreve a propriedade do servidor **AlwaysUseGateway** dos Serviços de Análise Azure para utilização quando as fontes de dados estão numa [Rede Virtual Azure (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Acesso ao servidor a fontes de dados VNet

Se as suas fontes de dados forem acedidas através de um VNet, o seu servidor Azure Analysis Services deve ligar-se a essas fontes de dados como se estivessem no local, no seu próprio ambiente. Pode configurar a propriedade do servidor **AlwaysUseGateway** para especificar o servidor para aceder a todas as fontes de dados através de um [gateway no local](analysis-services-gateway.md). 

Fontes de dados de instância gerida Azure SQL são executadas dentro do Azure VNet com um endereço IP privado. Se o ponto final público estiver ativado no caso, não é necessário um portal de entrada. Se o ponto final público não estiver ativado, é necessário um Data Gateway no local e a propriedade AlwaysUseGateway deve ser definida como verdadeira.

> [!NOTE]
> Esta propriedade só é eficaz quando um [Data Gateway no local](analysis-services-gateway.md) é instalado e configurado. O portal pode estar no VNet.

## <a name="configure-alwaysusegateway-property"></a>Configure Propriedade AlwaysUseGateway

1. No SSMS > servidor > **Propriedades**  >  **Gerais,** selecione **Mostrar Propriedades Avançadas (Todas)**.
2. No **ASPaaS\AlwaysUseGateway,** selecione **true**.

    ![Use sempre a propriedade gateway](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Ver também
[Ligação a fontes de dados no local](analysis-services-gateway.md)   
[Instale e configuure um portal de dados no local](analysis-services-gateway-install.md)   
[Rede Virtual Azure (VNET)](../virtual-network/virtual-networks-overview.md)   

