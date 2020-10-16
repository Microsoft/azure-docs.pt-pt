---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 9f5a1010959658e75dcc809b2ee1d6d9222af056
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540015"
---
Este artigo ajuda a clarificar o desempenho do disco e como funciona quando combina Máquinas Virtuais Azure e Discos Azure. Também descreve como pode diagnosticar estrangulamentos para o seu IO do disco e as alterações que pode fazer para otimizar o desempenho.

## <a name="how-does-disk-performance-work"></a>Como funciona o desempenho do disco?
As máquinas virtuais Azure têm limites de desempenho de IOPS e de produção com base no tipo e tamanho da máquina virtual. Os discos de OS e discos de dados, que podem ser ligados a máquinas virtuais, têm os seus próprios IOPs e limites de produção. Quando a sua aplicação em execução nas suas máquinas virtuais solicita mais IOPS ou produção do que o que é atribuído para a máquina virtual ou os discos anexos, o desempenho da sua aplicação fica limitado. Quando isso acontece, a aplicação experimentará um desempenho sub-ideal e pode levar a algumas consequências negativas, como o aumento da latência. Vamos analisar alguns exemplos para solidificar isto. Para tornar estes exemplos fáceis de seguir, vamos apenas olhar para os IOPs, mas a mesma lógica também se aplica à produção.

## <a name="disk-io-capping"></a>Tampa io do disco
Configurar:
- Standard_D8s_v3 
    - IOPS sem cocó: 12.800
- Disco E30 OS
    - IOPS: 500 
- 2 Discos de dados E30
    - IOPS: 500

![Tampa de nível de disco](media/vm-disk-performance/disk-level-throttling.jpg)

A aplicação em execução na máquina virtual faz um pedido que requer 10.000 IOPs para a máquina virtual. Todas as quais são permitidas pelo VM porque a Standard_D8s_v3 máquina virtual pode executar até 12.800 IOPs. Esses 10.000 pedidos de IOPs são então divididos em três pedidos diferentes para os diferentes discos. São solicitados 1.000 IOPs ao disco do sistema operativo e são solicitados 4.500 IOPs a cada disco de dados. Uma vez que todos os discos ligados são discos E30 e só conseguem lidar com 500 IOPs, respondem com 500 IOPs cada. O desempenho da aplicação é então limitado pelos discos anexos e só pode processar 1.500 IOPs. Pode estar a funcionar no pico de desempenho em 10.000 IOPS se forem usados discos de melhor desempenho, como discos Premium SSD P30.

## <a name="virtual-machine-io-capping"></a>Tampa de IO da máquina virtual
Configurar:
- Standard_D8s_v3 
    - IOPS sem cocó: 12.800
- Disco P30 OS
    - IOPS: 5.000 
- 2 Discos de Dados P30 
    - IOPS: 5.000

![Cobertura de nível de máquina virtual](media/vm-disk-performance/vm-level-throttling.jpg)

A aplicação em execução na máquina virtual faz um pedido que requer 15.000 IOPs. Infelizmente, a máquina virtual Standard_D8s_v3 só está a ser apesada para lidar com 12.800 IOPs. A partir daí, a aplicação é limitada pelos limites da máquina virtual e deve então atribuir os 12.800 IOPs atribuídos. Os 12.800 IOPs solicitados são então divididos em três pedidos diferentes para os diferentes discos. São solicitados 4.267 IOPs ao disco do sistema operativo e são solicitados 4.266 IOPs a cada disco de dados. Uma vez que todos os discos ligados são discos P30, que podem lidar com 5.000 IOPs, respondem com os montantes solicitados.