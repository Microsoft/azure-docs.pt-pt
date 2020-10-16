---
title: Mover recursos do Observador da Rede Azure Microsoft Docs
description: Mover recursos do Observador da Rede Azure através de regiões
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2020
ms.author: damendo
ms.openlocfilehash: 97349071fee6a95623e5b5efdc0c9818cfe7b811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87388458"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Movimentação de recursos do Observador da Rede Azure em regiões

## <a name="moving-the-network-watcher-resource"></a>Mover o recurso Network Watcher
O recurso Network Watcher representa o serviço de backend para Network Watcher e é totalmente gerido pelo Azure. Os clientes não precisam de o gerir. A operação de movimento não é suportada neste recurso.

## <a name="moving-child-resources-of-network-watcher"></a>Mover recursos infantis do Observador de Rede
Atualmente, a deslocação de recursos através das regiões não é apoiada para qualquer recurso infantil do `*networkWatcher*` tipo de recurso.

## <a name="next-steps"></a>Passos Seguintes
* Leia a [visão geral do Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)
* Ver o [FaQ do Observador de Rede](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions)
