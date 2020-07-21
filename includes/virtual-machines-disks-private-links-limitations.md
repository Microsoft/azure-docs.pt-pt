---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be014a257a68a266a5946bf5822b094c5a968f6e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535810"
---
- Apenas uma rede virtual pode ser ligada a um objeto de acesso a disco.
- A sua rede virtual deve estar na mesma subscrição que o seu objeto de acesso ao disco para os ligar.
- Até 10 discos ou instantâneos podem ser importados ou exportados ao mesmo tempo com o mesmo objeto de acesso ao disco.
- Não é possível solicitar a aprovação manual para ligar uma rede virtual a um objeto de acesso ao disco.
- A capacidade diferencial não é suportada para instantâneos incrementais que estejam associados a um objeto de acesso ao disco.