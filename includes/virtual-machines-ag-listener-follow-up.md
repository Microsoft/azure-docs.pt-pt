---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67184206"
---
Depois de criar o ouvinte do grupo de disponibilidade, poderá ser necessário ajustar os parâmetros do cluster RegisterAllProvidersIP e HostRecordTTL para o recurso do ouvinte. Estes parâmetros podem reduzir o tempo de reconexão após uma falha, o que pode impedir os intervalos de ligação. Para obter mais informações sobre estes parâmetros, bem como o código de amostra, consulte [Criar ou configurar um ouvinte de grupo de disponibilidade](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

