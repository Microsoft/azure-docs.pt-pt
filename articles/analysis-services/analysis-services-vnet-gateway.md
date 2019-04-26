---
title: Gateway de dados no local utilize para origens de dados de rede Virtual do Azure | Documentos da Microsoft
description: Saiba como configurar um servidor para utilizar um gateway para origens de dados na VNet.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7e97bd50e3d37218e0f88f722387fd1a53167e27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60534170"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Utilizar o gateway para origens de dados numa rede Virtual do Azure (VNet)

Este artigo descreve os **AlwaysUseGateway** propriedade do servidor para utilização quando são de origens de dados num [rede Virtual do Azure (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Acesso ao servidor para origens de dados de VNet

Se as origens de dados são acedidas através de uma VNet, tem de ligar o servidor Azure Analysis Services para essas origens de dados como se fossem locais, em seu próprio ambiente. Pode configurar o **AlwaysUseGateway** propriedade do servidor para especificar o servidor para aceder a todos os dados de origem de dados por meio de um [gateway no local](analysis-services-gateway.md). 

> [!NOTE]
> Esta propriedade é eficaz apenas quando uma [gateway de dados no local](analysis-services-gateway.md) está instalado e configurado. O gateway pode ser na VNet.

## <a name="configure-alwaysusegateway-property"></a>Configurar a propriedade AlwaysUseGateway

1. No SSMS > servidor > **propriedades** > **geral**, selecione **propriedades Mostrar avançadas (todos)**.
2. Na **ASPaaS\AlwaysUseGateway**, selecione **verdadeiro**.

    ![Utilize sempre a propriedade de gateway](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Consulte também
[Ligar a origens de dados no local](analysis-services-gateway.md)   
[Instalar e configurar um gateway de dados no local](analysis-services-gateway-install.md)   
[Rede Virtual do Azure (VNET)](../virtual-network/virtual-networks-overview.md)   

