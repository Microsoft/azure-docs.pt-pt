---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227334"
---
Depois de criar o serviço de escuta do grupo de disponibilidade, poderá ser necessário ajustar os parâmetros de cluster RegisterAllProvidersIP e HostRecordTTL para o recurso de serviço de escuta. Estes parâmetros podem reduzir o tempo de restabelecimento de ligação após uma ativação pós-falha, o que poderá impedir o tempo limite da conexão. Para obter mais informações sobre estes parâmetros, bem como código de exemplo, consulte [criar ou configurar um serviço de escuta do grupo de disponibilidade](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

