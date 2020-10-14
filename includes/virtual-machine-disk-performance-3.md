---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 14e74bfbcd087ccc1d8c5f2f10a8e44ed37cce84
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016551"
---
![Menu métricas](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

No entanto, o Standard_D8s_v3 pode alcançar um total de 28.600 IOPs, usando as métricas permite investigar o que está acontecendo e identificar o nosso garigal de armazenamento IO estrangulamento. Primeiro, localize o menu do lado esquerdo do botão métrica e selecione-o:

![Menu métricas](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Vamos primeiro dar uma olhada na nossa métrica **VM Cached IOPS Consumido Percentual:**

![VM Cached IOPS Percentagem consumida](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Esta métrica está a dizer-nos que dos 16.000 IOPs atribuídos aos IOPs em cache no VM, 61% está a ser usado. Isto significa que o estrangulamento do IO de armazenamento não é com os discos que estão em cache porque não está a 100%. Então vamos agora dar uma olhada na nossa **métrica VM Uncached IOPS Consumido Percentual:**

![VM IOPS EdvolvidoS Percentagem Consumida](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Esta métrica está a 100%, dizendo-nos que todos os 12.800 IOPs atribuídos aos IOPs não encatados no VM estão a ser usados. Uma forma de remediar isto é alterando o tamanho do nosso VM para um tamanho maior que pode lidar com o IO adicional. Mas antes de fazermos isso, vamos dar uma olhada no disco anexado para ver quantos IOPs estão a ver. Vamos primeiro dar uma olhada no disco oss, olhando para a percentagem consumida do **disco oss:**

![Percentagem consumida em disco de OS](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Esta métrica diz-nos que dos 5.000 IOPs previstos para este disco P30 OS, cerca de 90% está a ser utilizado. Isto significa que não há estrangulamento aqui no disco os. Agora vamos dar uma olhada nos discos de dados que estão ligados ao VM, olhando para a **percentagem de IOPS consumido em disco de dados:**

![Percentagem consumida em IOPS do disco de dados](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Esta métrica está a dizer-nos que a percentagem média de IOPs consumida em todos os discos ligados é de cerca de 42%. Esta percentagem é calculada com base nos IOPs que são utilizados pelos discos e não estão a ser servidos a partir da cache do anfitrião. Vamos aprofundar esta métrica para ver o através da aplicação de **divisão** nestas métricas e divisão pelo valor LUN:

![IOPS de disco de dados consumido percentagem com divisão](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Esta métrica está a dizer-nos que os discos de dados ligados aos LUN 3 e 2 estão a utilizar cerca de 85% dos seus IOPs a provisionados. Aqui está um diagrama de como o IO está a olhar da arquitetura VM e Disks:

![Diagnóstico de exemplo de métricas de IO de armazenamento](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
