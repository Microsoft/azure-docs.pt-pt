---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8908ded31b96aac50db1fc25e92c2c0a8e960453
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219044"
---
O LRS (armazenamento com redundância local) Replica seus dados três vezes em um único data center. O LRS fornece pelo menos 99,999999999% (11 noves) de durabilidade de objetos em um determinado ano. LRS é a opção de replicação de menor custo e oferece a menor durabilidade em comparação com outras opções.

Se ocorrer um desastre no nível de datacenter (por exemplo, incêndio ou inundação), todas as réplicas em uma conta de armazenamento usando LRS poderão ser perdidas ou irrecuperáveis. Para atenuar esse risco, a Microsoft recomenda o uso de armazenamento com redundância de zona (ZRS), armazenamento com redundância geográfica (GRS) ou armazenamento com redundância de zona geográfica (GZRS).

Uma solicitação de gravação para uma conta de armazenamento LRS retorna com êxito somente depois que os dados são gravados em todas as três réplicas.

Talvez você queira usar o LRS nos seguintes cenários:

* Se seu aplicativo armazenar dados que podem ser facilmente reconstruídos se ocorrer perda de dados, você poderá optar por LRS.
* Alguns aplicativos são restritos à replicação de dados somente em um país/região devido a requisitos de governança de dados. Em alguns casos, as regiões emparelhadas em que os dados são replicados para contas GRS podem estar em outro país/região. Para obter mais informações sobre regiões emparelhadas, consulte [regiões do Azure](https://azure.microsoft.com/regions/).
