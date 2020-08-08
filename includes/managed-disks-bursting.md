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
ms.openlocfilehash: d14c18ee77662f3803281413f9a844d10d245933
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88011019"
---
No Azure, oferecemos a capacidade de aumentar o armazenamento de discos IOPS e o desempenho de MB/s referido como estourando tanto em Máquinas Virtuais como em discos. O busting é útil em muitos cenários, como lidar com o tráfego inesperado de discos ou processar trabalhos de lote. Pode efetivamente alavancar o VM e o nível do disco a rebentar para obter um grande desempenho de base e estourando tanto no seu VM como no disco. Desta forma, pode obter um grande desempenho de base e um desempenho de rutura tanto no seu vm como no disco. 

Por favor, note que as explosões nos Discos e VMs são independentes umas das outras. Se tiver um disco estourando, não precisa de um VM estou rebentando para permitir que o seu disco rebente. Se tiver um VM estourante, não precisa de um disco de rutura para permitir que o seu VM rebente. 
