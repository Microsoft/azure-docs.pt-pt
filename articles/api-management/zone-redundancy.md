---
title: Suporte de zona de disponibilidade para gestão da API Azure
description: Saiba como melhorar a resiliência do seu serviço Azure API Management numa região, permitindo redundância de zona.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 817aebab6af8de59071b5d767b24d15cf46d59d9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559288"
---
# <a name="availability-zone-support-for-azure-api-management"></a>Suporte de zona de disponibilidade para gestão da API Azure 

Este artigo mostra como permitir a redundância de zona para o seu caso de Gestão de API utilizando o portal Azure. [A redundância da zona](../availability-zones/az-overview.md#availability-zones) proporciona resiliência e elevada disponibilidade a uma instância de serviço numa região específica de Azure (localização). Com redundância de zona, o gateway e o plano de controlo da sua instância de Gestão de API (API de gestão, portal de desenvolvimento, configuração Git) são replicados através de datacenters em zonas fisicamente separadas, tornando-o resiliente a uma falha de zona. 

A API Management também apoia [implementações multi-regiões,](api-management-howto-deploy-multi-region.md)o que ajuda a reduzir a latência de pedidos percebida pelos consumidores de API distribuídos geograficamente e melhora a disponibilidade da componente gateway se uma região ficar offline. A combinação de zonas de disponibilidade para redundância dentro de uma região, e implementações multi-regiões para melhorar a disponibilidade de gateways em caso de uma paragem regional, ajuda a melhorar a fiabilidade e o desempenho da sua instância de Gestão de API.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="supported-regions"></a>Regiões suportadas

A gestão configurante da API para o despedimento de zona é atualmente apoiada nas seguintes regiões do Azure.

* Leste da Austrália
* Sul do Brasil
* Canadá Central
* Índia Central
* E.U.A. Leste
* E.U.A. Leste 2
* França Central
* Leste do Japão
* E.U.A. Centro-Sul
* Sudeste Asiático
* Sul do Reino Unido
* E.U.A. Oeste 2
* Oeste DOS EUA 3

## <a name="prerequisites"></a>Pré-requisitos

* Se ainda não criou uma instância de serviço de Gestão API, consulte [criar uma instância de serviço de Gestão API.](get-started-create-service-instance.md) Selecione o nível de serviço Premium.
* Se o seu caso de Gestão API for implantado numa [rede virtual,](api-management-using-with-vnet.md)certifique-se de que configura uma rede virtual, sub-rede e endereço IP público em qualquer novo local onde planeie ativar o despedimento de zona.

## <a name="enable-zone-redundancy---portal"></a>Ativar a redundância da zona - portal

No portal, opcionalmente, ativar a redundância de zona quando adicionar uma localização ao seu serviço de Gestão API, ou atualizar a configuração de uma localização existente.

1. No portal Azure, navegue para o seu serviço de Gestão API e selecione **Localizações** no menu.
1. Selecione uma localização existente ou selecione **+ Adicione** na barra superior. A localização deve [suportar zonas de disponibilidade.](#supported-regions)
1. Selecione o número de **[unidades](upgrade-and-scale.md)** de escala no local.
1. Em **Zonas de Disponibilidade,** selecione uma ou mais zonas. O número de unidades selecionadas deve distribuir uniformemente pelas zonas de disponibilidade. Por exemplo, se selecionar 3 unidades, selecione 3 zonas para que cada zona acolhe uma unidade.
1. Se a instância de Gestão da API for implantada numa [rede virtual,](api-management-using-with-vnet.md)configure as definições de rede virtual na localização. Selecione uma rede virtual existente, sub-rede e endereço IP público que estão disponíveis no local.
1. **Selecione Aplicar** e, em seguida, selecione **Guardar**.

:::image type="content" source="media/zone-redundancy/add-location-zones.png" alt-text="Ativar a redundância entre zonas":::

> [!IMPORTANT]
> O endereço IP público na localização muda quando ativa, adiciona ou remove zonas de disponibilidade. Ao atualizar zonas de disponibilidade numa região com definições de rede, deve configurar um recurso de endereço IP público diferente daquele que configura anteriormente.

> [!NOTE]
> Pode levar 15 a 45 minutos para aplicar a alteração na sua instância de Gestão de API.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a implementação de um serviço de gestão Azure API para várias regiões do Azure.](api-management-howto-deploy-multi-region.md)
* Também pode ativar a redundância de zona usando um [modelo de Gestor de Recursos Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-api-management-simple-zones).
* Saiba mais sobre [os serviços Azure que suportam zonas de disponibilidade.](../availability-zones/az-region.md)
* Saiba mais sobre a construção para [a fiabilidade](/azure/architecture/framework/resiliency/overview) em Azure.