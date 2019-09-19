---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 0f528117b19a3f26b964dee7ae453fc184c8dddb
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123052"
---
A Computação do Azure oferece tamanhos de máquinas virtuais que são Isolados para um tipo de hardware específico e dedicados a um único cliente.  Estes tamanhos de máquinas virtuais são mais adequados para cargas de trabalho que exigem um elevado grau de isolamento de outros clientes para cargas de trabalho que envolvem elementos como os requisitos de conformidade e regulamentares.  Os clientes também podem optar por subdividir ainda mais os recursos dessas máquinas virtuais isoladas usando o [suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

A utilização de um tamanho isolado garante que sua máquina virtual será a única em execução nessa instância de servidor específica.  As ofertas atuais de máquinas virtuais isoladas incluem:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Você pode saber mais sobre cada tamanho isolado disponível [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).