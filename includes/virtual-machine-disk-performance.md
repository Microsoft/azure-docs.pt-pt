---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4770ac0181c64ef800aa02ba87284c8add357e36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518082"
---
Este artigo ajuda a clarificar o desempenho do disco e como funciona quando combina máquinas virtuais Azure e discos Azure. Também descreve como pode diagnosticar estrangulamentos para o seu IO do disco e as alterações que pode fazer para otimizar o desempenho.

## <a name="how-does-disk-performance-work"></a>Como funciona o desempenho do disco?
As máquinas virtuais Azure têm operações de entrada/saída por segundo (IOPS) e limites de desempenho de produção baseados no tipo e tamanho da máquina virtual. Os discos de SO e discos de dados podem ser ligados a máquinas virtuais. Os discos têm os seus próprios IOPS e limites de produção.

O desempenho da sua aplicação fica limitado quando solicita mais IOPS ou produção do que o que é atribuído para as máquinas virtuais ou discos anexados. Quando limitada, a aplicação experimenta um desempenho subóptocal. Isto pode levar a consequências negativas como o aumento da latência. Vamos analisar alguns exemplos para esclarecer este conceito. Para tornar estes exemplos fáceis de seguir, só vamos olhar para o IOPS. Mas a mesma lógica aplica-se à produção.

## <a name="disk-io-capping"></a>Tampa io do disco

**Configuração:**

- Standard_D8s_v3
  - IOPS sem cocó: 12.800
- Disco E30 OS
  - IOPS: 500
- Dois discos de dados E30 × 2
  - IOPS: 500

![Diagrama mostrando a tampa do nível do disco.](media/vm-disk-performance/disk-level-throttling.jpg)

A aplicação em execução na máquina virtual faz um pedido que requer 10.000 IOPS para a máquina virtual. Todas as quais são permitidas pelo VM porque a Standard_D8s_v3 máquina virtual pode executar até 12.800 IOPS.

Os 10.000 pedidos de IOPS são divididos em três pedidos diferentes para os diferentes discos:

- 1.000 IOPS são solicitados ao disco do sistema operativo.
- São solicitados 4.500 IOPS a cada disco de dados.

Todos os discos anexos são discos E30 e só conseguem manusear 500 IOPS. Então, eles respondem com 500 IOPS cada. O desempenho da aplicação é limitado pelos discos anexos, e só pode processar 1.500 IOPS. A aplicação pode funcionar no pico de desempenho em 10.000 IOPS se forem usados discos de melhor desempenho, como discos Premium SSD P30.

## <a name="virtual-machine-io-capping"></a>Tampa de IO da máquina virtual

**Configuração:**

- Standard_D8s_v3
  - IOPS sem cocó: 12.800
- Disco P30 OS
  - IOPS: 5.000
- Dois discos de dados P30 × 2
  - IOPS: 5.000

![Diagrama mostrando tampa de nível de máquina virtual.](media/vm-disk-performance/vm-level-throttling.jpg)

A aplicação em execução na máquina virtual faz um pedido que requer 15.000 IOPS. Infelizmente, a máquina virtual Standard_D8s_v3 só está a ser ateada para lidar com 12.800 IOPS. A aplicação está limitada pelos limites da máquina virtual e deve atribuir os 12.800 IOPS atribuídos.

Os 12.800 IOPS solicitados são divididos em três pedidos diferentes aos diferentes discos:

- 4.267 IOPS são solicitados ao disco do sistema operativo.
- São solicitados 4.266 IOPS a cada disco de dados.

Todos os discos anexos são discos P30 que aguentam 5.000 IOPS. Então, respondem com os montantes solicitados.
