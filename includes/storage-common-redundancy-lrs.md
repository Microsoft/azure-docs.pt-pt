---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b4b050befe160d69fbc44b7f0a2ebbbbad3d705a
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015919"
---
O LRS (armazenamento com redundância local) fornece pelo menos 99,999999999% (11 noves) de durabilidade de objetos em um determinado ano. O LRS fornece essa durabilidade do objeto replicando seus dados para uma unidade de escala de armazenamento. Um datacenter, localizado na região em que você criou sua conta de armazenamento, hospeda a unidade de escala de armazenamento. Uma solicitação de gravação para uma conta de armazenamento LRS retorna com êxito somente depois que os dados são gravados em todas as réplicas. Cada réplica reside em domínios de falha e domínios de atualização separados em uma unidade de escala de armazenamento.

Uma unidade de escala de armazenamento é uma coleção de racks de nós de armazenamento. Um domínio de falha (FD) é um grupo de nós que representam uma unidade física de falha. Imagine um domínio de falha como nós que pertencem ao mesmo rack físico. Um domínio de atualização (UD) é um grupo de nós que são atualizados juntos durante o processo de atualização de um serviço (distribuição). As réplicas são distribuídas entre UDs e FDs em uma unidade de escala de armazenamento. Essa arquitetura garante que os dados estejam disponíveis se uma falha de hardware afetar um único rack ou quando os nós forem atualizados durante uma atualização de serviço.

LRS é a opção de replicação de menor custo e oferece a menor durabilidade em comparação com outras opções. Se ocorrer um desastre no nível de datacenter (por exemplo, incêndio ou inundação), todas as réplicas poderão ser perdidas ou irrecuperáveis. Para atenuar esse risco, a Microsoft recomenda o uso de armazenamento com redundância de zona (ZRS), armazenamento com redundância geográfica (GRS) ou armazenamento com redundância de zona geográfica (GZRS).

* Se seu aplicativo armazenar dados que podem ser facilmente reconstruídos se ocorrer perda de dados, você poderá optar por LRS.
* Alguns aplicativos são restritos à replicação de dados somente em um país/região devido a requisitos de governança de dados. Em alguns casos, as regiões emparelhadas em que os dados são replicados para contas GRS podem estar em outro país/região. Para obter mais informações sobre regiões emparelhadas, consulte [regiões do Azure](https://azure.microsoft.com/regions/).
