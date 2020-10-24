---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 8eff9da82fdfa5749fd1c2bc04652d5c8ce8dfd2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518083"
---
![Screenshot de f i o saída mostrando r=22.8k realçado.](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

O Standard_D8s_v3 pode atingir um total de 28.600 IOPS. Usando as métricas, vamos investigar o que se passa e identificar o nosso engarrafamento de armazenamento. No painel esquerdo, selecione **Métricas:**

![Screenshot mostrando métricas destacadas no painel esquerdo.](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Vamos primeiro dar uma olhada na nossa métrica **VM Cached IOPS Consumido Percentual:**

![Screenshot mostrando V M Cached I O P S Consumido Percentagem.](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Esta métrica diz-nos que 61% dos 16.000 IOPS atribuídos ao IOPS em cache no VM estão a ser usados. Esta percentagem significa que o estrangulamento do IO de armazenamento não é com os discos que estão em cache porque não está a 100%. Agora vamos olhar para a nossa métrica **VM Uncached IOPS Consumido Percentual:**

![Screenshot mostrando V M Uncached I O P S Consumido Percentagem.](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Esta métrica está a 100%. Diz-nos que todos os 12.800 IOPS atribuídos ao IOPS não encatado no VM estão a ser utilizados. Uma forma de remediar esta questão é mudar o tamanho do nosso VM para um tamanho maior que possa lidar com o OI adicional. Mas antes de fazermos isso, vamos olhar para o disco anexo para descobrir quantos IOPS estão a ver. Consulte o disco osso olhando para a **percentagem consumida do disco oss:**

![Screenshot mostrando O S Disk I O P S Consumido Percentual.](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Esta métrica diz-nos que cerca de 90% dos 5.000 IOPS previstos para este disco P30 OS estão a ser usados. Esta percentagem significa que não há estrangulamento no disco de os. Agora vamos verificar os discos de dados que estão ligados ao VM olhando para a **percentagem de consumo de IOPS do disco de dados:**

![Screenshot mostrando data disk I O P S Consumido percentagem.](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Esta métrica diz-nos que a percentagem média de IOPS consumida em todos os discos ligados é de cerca de 42%. Esta percentagem é calculada com base no IOPS que é usado pelos discos, e que não estão sendo servidos a partir da cache do hospedeiro. Vamos aprofundar esta métrica aplicando *a divisão* nestas métricas e dividindo-se pelo valor LUN:

![Screenshot mostrando data disk I O P S Consumeed Percentagem com divisão.](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Esta métrica diz-nos que os discos de dados ligados à LUN 3 e 2 estão a utilizar cerca de 85% do seu IOPS a provisionado. Aqui está um diagrama de como o IO se parece a partir da arquitetura VM e discos:

![Diagrama de armazenamento I O exemplo métricas.](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
