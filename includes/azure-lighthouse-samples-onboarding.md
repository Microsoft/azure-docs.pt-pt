---
title: incluir ficheiro
description: incluir ficheiro
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 07/07/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: d80786bbdfc4f53254dc98d79ac3badcf5dd3b24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86111672"
---
Disponibilizamos modelos diferentes para abordar cenários de integração específicos. Escolha a melhor opção para si e certifique-se de que modifica o ficheiro de parâmetros de forma a refletir o seu ambiente. Para obter mais informações sobre como utilizar estes ficheiros na sua implementação, veja [Incluir um cliente no Azure Lighthouse](../articles/lighthouse/how-to/onboard-customer.md).

| **Modelo** | **Descrição** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Integre a subscrição de um cliente no Azure Lighthouse. Tem de fazer uma implementação separada para cada subscrição. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Integre um ou mais grupos de recursos de um cliente no Azure Lighthouse. Utilize **rgDelegatedResourceManagement** para um único grupo de recursos ou **multipleRgDelegatedResourceManagement** para integrar vários grupos de recursos na mesma subscrição. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Se [publicou uma oferta de serviços geridos no Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), pode optar por utilizar este modelo para integrar recursos para os clientes que tenham aceitado a oferta. Os valores do Marketplace no ficheiro de parâmetros têm de corresponder aos valores que utilizou quando publicou a sua oferta. |

Normalmente, é necessária uma implementação separada por cada subscrição que está a integrar, mas também pode implementar modelos em várias subscrições.

| **Modelo** | **Descrição** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Implementa modelos do Azure Resource Manager em várias subscrições. |
