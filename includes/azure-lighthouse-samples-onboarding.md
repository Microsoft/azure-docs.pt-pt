---
title: incluir ficheiro
description: incluir ficheiro
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: e32b55ed655b1e47f85640eb7f494a89f3274667
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456793"
---
Disponibilizamos modelos diferentes para abordar cenários de integração específicos. Escolha a melhor opção para si e certifique-se de que modifica o ficheiro de parâmetros de forma a refletir o seu ambiente. Para obter mais informações sobre como utilizar estes ficheiros na sua implementação, veja [Integrar um cliente na gestão de recursos delegados do Azure ](../articles/lighthouse/how-to/onboard-customer.md).

| **Modelo** | **Descrição** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Integra a subscrição de um cliente na gestão de recursos delegados do Azure. Tem de fazer uma implementação separada para cada subscrição. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Integra um ou mais grupos de recursos de um cliente na gestão de recursos delegados do Azure. Utilize **rgDelegatedResourceManagement** para um único grupo de recursos ou **multipleRgDelegatedResourceManagement** para integrar vários grupos de recursos na mesma subscrição. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Se [publicou uma oferta de serviços geridos no Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), pode optar por utilizar este modelo para integrar recursos para os clientes que tenham aceitado a oferta. Os valores do Marketplace no ficheiro de parâmetros têm de corresponder aos valores que utilizou quando publicou a sua oferta. |

Normalmente, é necessária uma implementação separada por cada subscrição que está a integrar, mas também pode implementar modelos em várias subscrições.

| **Modelo** | **Descrição** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Implementa modelos do Azure Resource Manager em várias subscrições. |
