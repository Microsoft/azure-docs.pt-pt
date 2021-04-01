---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86231024"
---
- Não suporta discos ultra.
- Não é possível ativar se a encriptação do disco Azure (encriptação guest-VM utilizando bitlocker/VM-Decrypt) estiver ativada nos seus conjuntos de escala VM/máquina virtual.
- A encriptação do disco Azure não pode ser ativada em discos que tenham encriptação no anfitrião ativada.
- A encriptação pode ser ativada no conjunto de escala de máquina virtual existente. No entanto, apenas os novos VMs criados após permitir a encriptação são automaticamente encriptados.
- Os VM existentes devem ser translocados e redistribuídos para serem encriptados.