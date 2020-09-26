---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 293f0f459e1f1e464fdec16b76eaf08336c92e93
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376567"
---
- Apenas uma rede virtual pode ser ligada a um objeto de acesso a disco.
- A sua rede virtual deve estar na mesma subscrição que o seu objeto de acesso ao disco para os ligar.
- Até 10 discos ou instantâneos podem ser importados ou exportados ao mesmo tempo com o mesmo objeto de acesso ao disco.
- Não é possível solicitar a aprovação manual para ligar uma rede virtual a um objeto de acesso ao disco.
- As imagens incrementais não podem ser exportadas quando estão associadas a um objeto de acesso ao disco.
- Não é possível utilizar o AzCopy para descarregar VHD de um disco/instantâneo que é protegido através de links privados para uma conta de armazenamento. No entanto, pode utilizar o AzCopy para baixar O VHD para os seus VMs.
