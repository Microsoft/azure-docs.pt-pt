---
title: Mover recursos do Observador da Rede Azure | Microsoft Docs
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
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 4853f485e4424c3c3263a18d27834d0f9ae94918
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019657"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Movimentação de recursos do Observador da Rede Azure em regiões

## <a name="moving-the-network-watcher-resource"></a>Mover o recurso Network Watcher
O recurso Network Watcher representa o serviço de backend para Network Watcher e é totalmente gerido pelo Azure. Os clientes não precisam de o gerir. A operação de movimento não é suportada neste recurso.

## <a name="moving-child-resources-of-network-watcher"></a>Mover recursos infantis do Observador de Rede
Atualmente, a deslocação de recursos através das regiões não é apoiada para qualquer recurso infantil do `*networkWatcher*` tipo de recurso.

## <a name="next-steps"></a>Passos Seguintes
* Leia a [visão geral do Observador de Rede](./network-watcher-monitoring-overview.md)
* Ver o [FaQ do Observador de Rede](./frequently-asked-questions.md)