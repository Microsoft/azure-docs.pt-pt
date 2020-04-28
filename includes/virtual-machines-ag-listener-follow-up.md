---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184206"
---
Depois de criar o ouvinte do grupo de disponibilidade, pode ser necessário ajustar os parâmetros do cluster RegisterAllProvidersIP e HostRecordTTL para o recurso ouvinte. Estes parâmetros podem reduzir o tempo de religação após uma falha, o que pode impedir os intervalos de ligação. Para obter mais informações sobre estes parâmetros, bem como código de amostra, consulte [Criar ou configurar um ouvinte de grupo](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)de disponibilidade .

