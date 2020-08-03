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
ms.openlocfilehash: 138e7efd33ee36a77fd56deda155a29017432302
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420295"
---
- Apenas uma rede virtual pode ser ligada a um objeto de acesso a disco.
- A sua rede virtual deve estar na mesma subscrição que o seu objeto de acesso ao disco para os ligar.
- Até 10 discos ou instantâneos podem ser importados ou exportados ao mesmo tempo com o mesmo objeto de acesso ao disco.
- Não é possível solicitar a aprovação manual para ligar uma rede virtual a um objeto de acesso ao disco.
- A capacidade diferencial não é suportada para instantâneos incrementais que estejam associados a um objeto de acesso ao disco.
- Não é possível utilizar o AzCopy para descarregar VHD de um disco/instantâneo que é protegido através de links privados para uma conta de armazenamento. No entanto, pode utilizar o AzCopy para baixar O VHD para os seus VMs.
