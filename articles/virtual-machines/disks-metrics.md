---
title: Métricas de disco
description: Exemplos de métricas de rebentamento de disco
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 60486c41ad843cf193ee0648dfcfef66f7668e47
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674989"
---
# <a name="disk-performance-metrics"></a>Métricas de desempenho do disco
O Azure oferece métricas no portal Azure que fornecem informações sobre como as suas máquinas virtuais (VM) e discos funcionam. As métricas também podem ser recuperadas através de uma chamada de API. Este artigo é dividido em 3 subsecções:

- **Métricas de profundidade de disco, produção e profundidade** de fila - Estas métricas permitem-lhe ver o desempenho do armazenamento a partir da perspetiva de um disco e de uma máquina virtual.
- **Métricas de explosão de disco** - Estas são as métricas que fornecem a observabilidade na nossa funcionalidade [de explosão](disk-bursting.md) nos nossos discos premium.
- **Métricas de utilização de IO de armazenamento** - Estas métricas ajudam a diagnosticar estrangulamentos no seu desempenho de armazenamento com discos. 

Todas as métricas são emitidas a cada minuto, exceto a métrica da percentagem de crédito rebentando, que é emitida a cada 5 minutos.

## <a name="disk-io-throughput-and-queue-depth-metrics"></a>IO do disco, métricas de profundidade de produção e fila
As seguintes métricas estão disponíveis para obter informações sobre VM e Disk IO, produção e desempenho de profundidade de fila:

- **Profundidade da fila do disco os**: O número de pedidos atuais pendentes de IO que estão à espera de serem lidos ou escritos para o disco OS.
- **Leitura de discos de OS/Sec**: O número de bytes que são lidos num segundo a partir do disco OS.
- **Operações de leitura do disco oss/sec**: O número de operações de entrada que são lidas num segundo a partir do disco OS.
- **Os Discos Write Bytes/Sec**: O número de bytes que são escritos num segundo a partir do disco OS.
- Operações de escrita do **disco oss/sec**: O número de operações de saída que são escritas num segundo a partir do disco OS.
- Profundidade da fila do disco de **dados**: O número de pedidos atuais de IO pendentes que estão à espera de serem lidos ou escritos para o ou dos discos de dados.
- **Ficha de dados Ler Bytes/Sec**: O número de bytes que são lidos num segundo a partir do ou dos discos de dados.
- Operações de leitura do disco de **dados/Sec**: O número de operações de entrada que são lidas num segundo a partir do ou dos discos de dados.
- **Data Disk Write Bytes/Sec**: O número de bytes que são escritos num segundo a partir do ou dos discos de dados.
- Operações de escrita de **discos de dados/seg**: O número de operações de saída que são escritas num segundo a partir do ou dos discos de dados.
- **Leitura de discos/seg**: O número de bytes totais que são lidos num segundo de todos os discos ligados a um VM.
- Operações de leitura do **disco/Sec**: O número de operações de entrada que são lidas num segundo de todos os discos ligados a um VM.
- **Discos Write Bytes/Sec**: O número de bytes que são escritos num segundo de todos os discos ligados a um VM.
- **Operações de escrita em disco/Sec**: O número de operações de saída que são escritas num segundo de todos os discos ligados a um VM.

## <a name="bursting-metrics"></a>Métricas de rebentamento
As seguintes métricas ajudam com a observabilidade na nossa funcionalidade [de explosão](disk-bursting.md) nos nossos discos premium:

- **Data Disk Max Burst Bandwidth**: O limite de produção a que os discos de dados podem rebentar.
- **Largura de banda De Disco Max Burst**: O limite de produção a que o disco DE PODE rebentar.
- **Data Disk Max Burst IOPS**: o limite IOPS que o(s) disco de dados pode rebentar.
- **OS Disco Max Burst IOPS**: O limite de IOPS ao que o disco DEL pode rebentar.
- **Largura de banda do alvo** do disco de dados : O limite de produção que o disco de dados pode alcançar sem rebentar.
- **Largura de banda alvo do disco os**: O limite de produção que o disco SO pode alcançar sem rebentar.
- **IOPS alvo do disco** de dados : O limite de IOPS que o(s) disco de dados pode alcançar sem rebentar.
- **ALVO do disco de OS IOPS**: O limite de IOPS que o(s) disco de dados pode alcançar sem rebentar.
- **Disco de dados Usado Burst BPS Credits Percentagem**: A percentagem acumulada da explosão de produção utilizada para o ou dos discos de dados. Emitida num intervalo de 5 minutos.
- **Os Discos Usados Burst BPS Credits Percentagem**: A percentagem acumulada da rutura de produção utilizada para o disco DE. Emitida num intervalo de 5 minutos.
- **Percentagem de créditos io de rebentamento usados:** A percentagem acumulada da explosão de IOPS utilizada para o ou dos discos de dados. Emitida num intervalo de 5 minutos.
- **Percentagem de créditos io de explosão de disco usados** em disco : A percentagem acumulada da explosão de IOPS utilizada para o disco so. Emitida num intervalo de 5 minutos.

## <a name="storage-io-utilization-metrics"></a>Métricas de utilização de IO de armazenamento
As seguintes métricas ajudam a diagnosticar o estrangulamento na sua combinação de Máquina Virtual e Disco. Estas métricas só estão disponíveis quando se utiliza VM habilitado a prémio. Estas métricas estão disponíveis para todos os tipos de discos, exceto para o Ultra. 

Métricas que ajudam a diagnosticar a tampa do IO do disco:

- **Percentagem consumida** pelo disco de dados : A percentagem calculada pelo disco de dados IOPS concluída sobre o disco de dados iOPS. Se este valor estiver a 100%, a sua aplicação em execução é IO limitada a partir do limite de IOPS do seu disco de dados.
- **Percentagem consumida** pela largura de banda do disco de dados : A percentagem calculada pela produção do disco de dados concluída sobre o rendimento do disco de dados fortado. Se este valor estiver a 100%, a sua aplicação em execução é IO limitada do limite de largura de banda do seu disco de dados.
- **Percentagem consumida** pelo disco oss : A percentagem calculada pelo disco os IOPS concluída sobre o disco de oss IOPS. Se este valor estiver a 100%, a sua aplicação em execução é IO limitada do limite de IOPS do seu disco de SO.
- **Percentagem consumida** pela largura de banda do disco OS : A percentagem calculada pela produção do disco OS concluída sobre a produção do disco os provisionado. Se este valor estiver a 100%, a sua aplicação em execução é IO limitada do limite de largura de banda do seu disco de SO.

Métricas que ajudam a diagnosticar a tampa de IO VM:

- **VM Cached IOPS Percentagem consumida**: A percentagem calculada pelo total de IOPS concluída acima do limite máximo da máquina virtual em cache IOPS. Se este valor estiver a 100%, a sua aplicação em execução é IO limitada do limite de IOPS em cache do seu VM.
- **VM Cached Bandwidth Percentagem consumida**: A percentagem calculada pela produção total do disco concluída sobre a potência máxima da máquina virtual em cache. Se este valor estiver a 100%, a sua aplicação em execução é IO limitada do limite de largura de banda em cache do seu VM.
- **VM IOPS Percentagem consumida sem cómoda**: A percentagem calculada pelo total de IOPS numa máquina virtual concluída sobre o limite máximo de IOPS da máquina virtual não-acolhida. Se este valor estiver a 100%, a sua aplicação em execução é IO limitada do limite de IOPS não encaberto do seu VM.
- **VM Percentagem consumida** pela largura de banda não colada : A percentagem calculada pela produção total do disco numa máquina virtual concluída sobre a potência máxima da máquina virtual. Se este valor estiver a 100%, a sua aplicação em execução é IO limitada do limite de largura de banda não coberta do seu VM.

## <a name="storage-io-metrics-example"></a>Exemplo de métricas de IO de armazenamento

Vamos analisar um exemplo de como usar estas novas métricas de utilização de IO de armazenamento para nos ajudar a depurar onde um estrangulamento está no nosso sistema. A configuração do sistema é a mesma que o exemplo anterior, exceto que desta vez o disco de SO anexado *não* está em cache.

**Configuração:**

- Standard_D8s_v3
  - IOPS em cache: 16.000
  - IOPS sem cocó: 12.800
- Disco P30 OS
  - IOPS: 5.000
  - Caching hospedeiro: **Deficiente**
- Dois discos de dados P30 × 2
  - IOPS: 5.000
  - Caching do anfitrião: **Ler/escrever**
- Dois discos de dados P30 × 2
  - IOPS: 5.000
  - Caching hospedeiro: **Deficiente**

Vamos fazer um teste de benchmarking nesta combinação de máquina virtual e disco que cria atividade de IO. Para aprender a comparar o IO de armazenamento de referência no Azure, consulte [Benchmark a sua aplicação no Azure Disk Storage](disks-benchmarks.md). A partir da ferramenta de benchmarking, pode ver que a combinação VM e disco pode alcançar 22.800 IOPS:

![Screenshot de f i o saída mostrando r=22.8k realçado.](media/disks-metrics/utilization-metrics-example/fio-output.jpg)



O Standard_D8s_v3 pode atingir um total de 28.600 IOPS. Usando as métricas, vamos investigar o que se passa e identificar o nosso engarrafamento de armazenamento. No painel esquerdo, selecione **Métricas:**

![Screenshot mostrando métricas destacadas no painel esquerdo.](media/disks-metrics/utilization-metrics-example/metrics-menu.jpg)

Vamos primeiro dar uma olhada na nossa métrica **VM Cached IOPS Consumido Percentual:**

![Screenshot mostrando V M Cached I O P S Consumido Percentagem.](media/disks-metrics/utilization-metrics-example/vm-cached.jpg)

Esta métrica diz-nos que 61% dos 16.000 IOPS atribuídos ao IOPS em cache no VM estão a ser usados. Esta percentagem significa que o estrangulamento do IO de armazenamento não é com os discos que estão em cache porque não está a 100%. Agora vamos olhar para a nossa métrica **VM Uncached IOPS Consumido Percentual:**

![Screenshot mostrando V M Uncached I O P S Consumido Percentagem.](media/disks-metrics/utilization-metrics-example/vm-uncached.jpg)

Esta métrica está a 100%. Diz-nos que todos os 12.800 IOPS atribuídos ao IOPS não encatado no VM estão a ser utilizados. Uma forma de remediar esta questão é mudar o tamanho do nosso VM para um tamanho maior que possa lidar com o OI adicional. Mas antes de fazermos isso, vamos olhar para o disco anexo para descobrir quantos IOPS estão a ver. Consulte o disco osso olhando para a **percentagem consumida do disco oss:**

![Screenshot mostrando O S Disk I O P S Consumido Percentual.](media/disks-metrics/utilization-metrics-example/os-disk.jpg)

Esta métrica diz-nos que cerca de 90% dos 5.000 IOPS previstos para este disco P30 OS estão a ser usados. Esta percentagem significa que não há estrangulamento no disco de so. Agora vamos verificar os discos de dados que estão ligados ao VM olhando para a **percentagem de consumo de IOPS do disco de dados:**

![Screenshot mostrando data disk I O P S Consumido percentagem.](media/disks-metrics/utilization-metrics-example/data-disks-no-splitting.jpg)

Esta métrica diz-nos que a percentagem média de IOPS consumida em todos os discos ligados é de cerca de 42%. Esta percentagem é calculada com base no IOPS que é usado pelos discos, e não está sendo servido a partir da cache do hospedeiro. Vamos aprofundar esta métrica aplicando *a divisão* nestas métricas e dividindo-se pelo valor LUN:

![Screenshot mostrando data disk I O P S Consumeed Percentagem com divisão.](media/disks-metrics/utilization-metrics-example/data-disks-splitting.jpg)

Esta métrica diz-nos que os discos de dados ligados à LUN 3 e 2 estão a utilizar cerca de 85% do seu IOPS a provisionado. Aqui está um diagrama de como o IO se parece a partir da arquitetura VM e discos:

![Diagrama de armazenamento I O exemplo métricas.](media/disks-metrics/utilization-metrics-example/metrics-diagram.jpg)

## <a name="next-steps"></a>Passos seguintes

- [Métricas do Monitor Azure](../azure-monitor/essentials/data-platform-metrics.md)
- [Agregação de métricas explicada](../azure-monitor/essentials/metrics-aggregation-explained.md)
- [Criar, ver e gerir alertas de métricas com o Azure Monitor](../azure-monitor/alerts/alerts-metric.md)