---
title: 'Porta da frente Azure: Gerente de Ponto Final'
description: Este artigo fornece uma visão geral do Azure Front Door Endpoint Manager.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: c916be9a54d62e16f488c94f4fa88a2207fb8788
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101100099"
---
# <a name="what-is-azure-front-door-standardpremium-preview-endpoint-manager"></a>O que é Azure Front Door Standard/Premium (Preview) Endpoint Manager?

> [!NOTE]
> * Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [Azure Front Door Docs](../front-door-overview.md).

O Endpoint Manager fornece uma visão geral dos pontos finais que configuraste para a sua Porta Frontal Azure. Um ponto final é um agrupamento lógico de um domínio e suas configurações associadas. O Endpoint Manager ajuda-o a gerir a sua coleção de pontos finais para a operação CRUD (criar, ler, atualizar e eliminar). Pode gerir os seguintes elementos para os seus pontos finais através do Endpoint Manager:

* Domínios
* Grupos de Origem
* Rotas
* Segurança

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-1.png" alt-text="Screenshot do Endpoint Manager sem configurações." lightbox="../media/concept-endpoint-manager/endpoint-manager-1-expanded.png":::

Lista de gestores de pontos finais quantas instâncias de cada elemento são criadas dentro de um ponto final. O estado de associação de cada elemento também será apresentado. Por exemplo, pode criar vários domínios e grupos de origem, e atribuir a associação entre eles com diferentes rotas.

> [!IMPORTANT]
> * Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [**termos de utilização suplementares para pré-visualizações do Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="linked-view"></a>Vista ligada

Com a vista ligada dentro do Endpoint Manager, pode facilmente identificar a associação entre os elementos da Porta Frontal Azure, tais como:

* Que domínios estão associados ao ponto final atual?
* Que grupo de origem está associado a que domínio?
* Qual a política da WAF associada a que domínio?

:::image type="content" source="../media/concept-endpoint-manager/endpoint-manager-2.png" alt-text="Screenshot de Endpoint Manager com configurações." lightbox="../media/concept-endpoint-manager/endpoint-manager-2-expanded.png":::

## <a name="next-steps"></a>Passos Seguintes

Saiba como [criar um Padrão/Premium da Porta da Frente.](create-front-door-portal.md)
