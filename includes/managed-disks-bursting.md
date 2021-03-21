---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 0f85f2770b98507618d5cbfdec1b55f397c7f514
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101749920"
---
O Azure oferece a capacidade de aumentar o desempenho do IOPS e do MB/s de armazenamento de discos, isto é referido como estourando tanto para máquinas virtuais (VM) como para discos. Pode efetivamente alavancar o VM e o disco a rebentar para obter um melhor desempenho de rebentamento tanto nos seus VMs como no disco.

Rebentar por VMs Azure e os recursos do disco não dependem uns dos outros. Não é preciso ter um VM capaz de rebentar para que um disco com capacidade de explosão se rebente. Da mesma forma, não é necessário ter um disco capaz de rebentar ao seu VM capaz de rebentar para que o VM rebente.