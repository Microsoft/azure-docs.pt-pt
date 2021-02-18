---
title: Proteja a sua Origem com Link Privado em Azure Front Door Standard/Premium (Pré-visualização)
description: Esta página fornece informações sobre como garantir a conectividade à sua origem usando o Private Link.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tyao
ms.custom: references_regions
ms.openlocfilehash: dead60b9d8e0872f3d46b1f223ccf5e6697cbd90
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100055"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>Proteja a sua Origem com Link Privado em Azure Front Door Standard/Premium (Pré-visualização)

> [!Note]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [Azure Front Door Docs](../front-door-overview.md).

## <a name="overview"></a>Descrição Geral

[O Azure Private Link](../../private-link/private-link-overview.md) permite-lhe aceder aos serviços Azure PaaS e Azure hospedados num Ponto Final Privado na sua rede virtual. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Front Door Premium SKU pode ligar-se à sua origem utilizando o serviço Private Link. As suas aplicações podem ser hospedadas na sua rede virtual privada ou por trás de um serviço PaaS, não acessível a partir da Internet pública.

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="Arquitetura de endpoints privados da porta da frente":::

Quando ativa o Link Privado à sua origem na configuração Azure Front Door Premium, a Porta Frontal cria um ponto final privado em seu nome a partir da rede privada regional da Front Door. Este ponto final é gerido pela Porta frontal Azure. Receberá um pedido de aprovação da Porta Frontal Azure na sua origem. Depois de aprovar o pedido, um endereço IP privado é atribuído a partir da rede virtual da Porta Frontal, o tráfego entre a Porta Frontal Azure e a sua origem atravessa a ligação privada estabelecida com a espinha dorsal da rede Azure. O tráfego de entrada para a sua origem está agora seguro quando vem da sua Porta frontal Azure.

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="Ativar o ponto de final privado":::

O Azure Front Door Premium suporta vários tipos de origem. Se a sua origem estiver hospedada num conjunto de máquinas virtuais na sua rede privada, tem primeiro de criar um balanceador de carga padrão interno, ativar o serviço de ligação privada ao balanceador de carga padrão e, em seguida, selecionar o tipo de origem personalizada. Para a configuração de ligação privada, selecione "Microsoft.Network/PrivateLinkServices como Tipo de recurso. Para serviços PaaS como Azure Web App e Conta de Armazenamento, pode ativar o Serviço de Ligação Privada a partir dos serviços correspondentes primeiro e selecionar Microsoft.Web/Sites para Web App e Microsoft.Storage/StorageAccounts para tipos de serviços de ligação privada de conta de armazenamento.

## <a name="limitations"></a>Limitações

Os pontos finais privados da Azure Front Door estão disponíveis nas seguintes regiões durante a pré-visualização pública: East US, West 2 US e South Central US.

Para obter a melhor latência, deve sempre escolher uma região Azure mais próxima da sua origem ao optar por ativar o ponto final de ligação privada front door.

Os pontos finais privados da Azure Front Door são geridos pela plataforma e sob a assinatura da Azure Front Door. A Azure Front Door permite ligações de ligação privadas à mesma subscrição do cliente que é usada para criar o perfil da Porta Frontal.

## <a name="next-steps"></a>Passos seguintes

* Para ligar Azure Front Door Premium a Máquinas Virtuais utilizando o serviço de ligação privada, consulte [Criar um Ponto final privado](../../private-link/create-private-endpoint-portal.md).
* Para ligar o Azure Front Door Premium à sua Web App através do serviço Private Link, consulte [Connect a uma aplicação web utilizando um ponto final privado.](../../private-link/tutorial-private-endpoint-webapp-portal.md)
* Para ligar o Azure Front Door Premium à sua Conta de Armazenamento através do serviço de ligação privada, consulte [Connect a uma conta de armazenamento utilizando o ponto final privado.](../../private-link/tutorial-private-endpoint-storage-portal.md)
