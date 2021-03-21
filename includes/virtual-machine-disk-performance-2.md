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
ms.openlocfilehash: 82b4c127f983f3133326bf7fb538e40713ef9655
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580392"
---
![Gráfico mostrando as especificações D s v 3.](media/vm-disk-performance/dsv3-documentation.jpg)

- A produção máxima de disco *não acoched* é o limite máximo de armazenamento predefinido que a máquina virtual pode suportar.
- O limite máximo de produção *em cache* é um limite separado quando ativa o cache do anfitrião.

O caching do anfitrião funciona aproximando o armazenamento do VM que pode ser escrito ou lido rapidamente. A quantidade de armazenamento disponível para o VM para o caching hospedeiro está na documentação. Por exemplo, pode ver o Standard_D8s_v3 vem com 200 GiB de armazenamento de cache.

Pode ativar o cache do anfitrião quando criar a sua máquina virtual e anexar discos. Também pode ligar e desligar o hospedeiro a cache nos seus discos num VM existente.

![Screenshot mostrando o caching do anfitrião.](media/vm-disk-performance/host-caching.jpg)

Pode ajustar o cache do anfitrião para corresponder aos seus requisitos de carga de trabalho para cada disco. Pode definir o seu caching anfitrião para:

- **Leia apenas**: Para cargas de trabalho que apenas lêem operações
- **Leia/escreva**: Para cargas de trabalho que fazem um balanço de operações de leitura e escrita

Se a sua carga de trabalho não seguir nenhum destes padrões, não recomendamos que use o caching do hospedeiro.

Vamos analisar alguns exemplos de diferentes configurações de cache do anfitrião para ver como isso afeta o fluxo de dados e o desempenho. Neste primeiro exemplo, vamos ver o que acontece com os pedidos de IO quando a definição de caching do anfitrião está definida **apenas** para Ler .

**Configuração:**

- Standard_D8s_v3
  - IOPS em cache: 16.000
  - IOPS sem cocó: 12.800
- Disco de dados P30
  - IOPS: 5.000
  - Caching do anfitrião: **Apenas para leitura**

Quando uma leitura é realizada e os dados desejados estão disponíveis na cache, a cache devolve os dados solicitados. Não há necessidade de ler do disco. Esta leitura é contada em direção aos limites em cache do VM.

![Diagrama mostrando um hospedeiro de leitura leitura de sucesso.](media/vm-disk-performance/host-caching-read-hit.jpg)

Quando uma leitura é realizada e os dados pretendidos *não* estão disponíveis na cache, o pedido de leitura é retransmitido para o disco. Em seguida, o disco superfície-lo tanto para a cache como para o VM. Esta leitura é contada tanto para o limite não encatado do VM como para o limite de cache do VM.

![Diagrama mostrando um anfitrião de leitura leitura leitura miss.](media/vm-disk-performance/host-caching-read-miss.jpg)

Quando uma escrita é executada, a escrita tem de ser escrita tanto para a cache como para o disco antes de ser considerada completa. Esta escrita é contada para o limite não encatado do VM e o limite de cache do VM.

![Diagrama mostrando um hospedeiro de leitura a escrever.](media/vm-disk-performance/host-caching-write.jpg)

Em seguida, vamos ver o que acontece com os pedidos de IO quando a definição de cache do anfitrião está definida para **Ler/escrever**.

**Configuração:**

- Standard_D8s_v3
  - IOPS em cache: 16.000
  - IOPS sem cocó: 12.800
- Disco de dados P30
  - IOPS: 5.000
  - Caching do anfitrião: **Ler/escrever**

Uma leitura é tratada da mesma forma que uma leitura. As escritas são a única coisa diferente com o tiaching de leitura/escrita. Ao escrever com o cache do anfitrião está definido para **Ler/escrever,** a escrita só precisa de ser escrita para a cache do anfitrião para ser considerada completa. A escrita é então preguiçosamente escrita para o disco como um processo de fundo. Isto significa que uma escrita é contada para IO em cache quando é escrita para a cache. Quando é preguiçosamente escrito no disco, conta para o IO sem escoar.

![Diagrama mostrando leitura/escrever escrita do anfitrião.](media/vm-disk-performance/host-caching-read-write.jpg)

Vamos continuar com a nossa Standard_D8s_v3 máquina virtual. Só que desta vez, vamos permitir o cache do hospedeiro nos discos. Além disso, agora o limite de IOPS do VM é de 16.000 IOPS. Anexados ao VM estão três discos P30 subjacentes que conseguem manusear 5.000 IOPS.

**Configuração:**

- Standard_D8s_v3
  - IOPS em cache: 16.000
  - IOPS sem cocó: 12.800
- Disco P30 OS
  - IOPS: 5.000
  - Caching do anfitrião: **Ler/escrever**
- Dois discos de dados P30 × 2
  - IOPS: 5.000
  - Caching do anfitrião: **Ler/escrever**

![Diagrama mostrando um exemplo de caching anfitrião.](media/vm-disk-performance/host-caching-example-without-remote.jpg)

A aplicação utiliza uma máquina virtual Standard_D8s_v3 com caching ativada. Faz um pedido de 15.000 IOPS. Os pedidos são divididos como 5.000 IOPS a cada disco subjacente ligado. Não ocorre uma cobertura de desempenho.

## <a name="combined-uncached-and-cached-limits"></a>Limites combinados sem cocó e em cache

Os limites em cache de uma máquina virtual são separados dos seus limites não encadados. Isto significa que pode ativar o cache do anfitrião em discos ligados a um VM, sem permitir a cache do anfitrião em outros discos. Esta configuração permite que as suas máquinas virtuais obtenham uma IO de armazenamento total do limite de cache mais o limite não colado.

Vamos analisar um exemplo para ajudá-lo a entender como estes limites funcionam em conjunto. Continuaremos com a Standard_D8s_v3 máquina virtual e a configuração de discos premium anexados.

**Configuração:**

- Standard_D8s_v3
  - IOPS em cache: 16.000
  - IOPS sem cocó: 12.800
- Disco P30 OS
  - IOPS: 5.000
  - Caching do anfitrião: **Ler/escrever**
- Dois discos de dados P30 × 2
  - IOPS: 5.000
  - Caching do anfitrião: **Ler/escrever**
- Dois discos de dados P30 × 2
  - IOPS: 5.000
  - Caching hospedeiro: **Deficiente**

![Diagrama mostrando um exemplo de caching anfitrião com armazenamento remoto.](media/vm-disk-performance/host-caching-example-with-remote.jpg)

Neste caso, a aplicação em execução numa máquina virtual Standard_D8s_v3 faz um pedido de 25.000 IOPS. O pedido é dividido como 5.000 IOPS para cada um dos discos anexos. Três discos usam cache hospedeiro e dois discos não usam cache hospedeiro.

- Uma vez que os três discos que utilizam o cacheing estão dentro dos limites em cache de 16.000, esses pedidos são concluídos com sucesso. Não ocorre uma cobertura de desempenho de armazenamento.
- Uma vez que os dois discos que não usam o cache do hospedeiro estão dentro dos limites não encatados de 12.800, esses pedidos também são concluídos com sucesso. Não ocorre a tampa.

