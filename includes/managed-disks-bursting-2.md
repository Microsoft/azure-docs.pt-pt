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
ms.openlocfilehash: 850ace7af15ab37ab9a4a124d20ed4588771f4d4
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594547"
---
## <a name="common-scenarios"></a>Cenários comuns
Os seguintes cenários podem beneficiar muito da explosão:
- **Melhorar os tempos** de arranque – Com o rebentamento, a sua instância arrancará a um ritmo significativamente mais rápido. Por exemplo, o disco osso padrão para VMs ativados por prémios é o disco P4, que é um desempenho provisionado de até 120 IOPS e 25 MB/s. Com o rebentamento, o P4 pode ir até 3500 IOPS e 170 MB/s permitindo um tempo de arranque para acelerar por 6X.
- **Manuseamento** de trabalhos de lote – As cargas de trabalho de algumas aplicações são de natureza cíclica e requerem um desempenho de base durante a maior parte do tempo e requerem um desempenho mais elevado por um curto período de tempo. Um exemplo disso é um programa de contabilidade que processa transações diárias que requerem uma pequena quantidade de tráfego de discos. Depois, no final do mês, faz relatórios de reconciliação que precisam de uma quantidade muito maior de tráfego de discos.
- **Preparação para picos** de tráfego – Os servidores web e as suas aplicações podem experimentar picos de tráfego a qualquer momento. Se o seu servidor web for apoiado por VMs ou discos que utilizem a explosão, os servidores estão mais bem equipados para lidar com picos de tráfego. 

## <a name="bursting-flow"></a>Fluxo de rutura
O sistema de crédito de rutura aplica-se da mesma forma tanto ao nível da máquina virtual como ao nível do disco. O seu recurso, seja um VM ou um disco, começará com créditos totalmente abastecidos. Estes créditos permitir-lhe-ão rebentar durante 30 minutos à taxa máxima de rutura. Os créditos de rebentamento acumulam-se quando o seu recurso está a funcionar abaixo dos seus limites de armazenamento de discos de desempenho. Para todos os IOPS e MB/s que o seu recurso está a utilizar abaixo do limite de desempenho, começa a acumular créditos. Se o seu recurso tiver créditos acumulados para rebentar e a sua carga de trabalho precisar de um desempenho extra, o seu recurso pode usar esses créditos para ultrapassar o seu limite de desempenho para lhe dar o desempenho do IO do disco que precisa para satisfazer a procura.

![Diagrama de balde de rebentamento](media/managed-disks-bursting/bucket-diagram.jpg)

Uma coisa a notar sobre a acumulação de explosão é que é diferente para cada recurso, uma vez que é baseado nos IOPS não utilizados e MB/s abaixo dos seus valores de desempenho. Isto significa que os produtos de desempenho de base mais elevados podem acumular as suas quantidades de rutura mais rapidamente do que os produtos de desempenho de base inferior. Por exemplo, um disco P1 em marcha lenta sem atividade acumulará 120 IOPS por segundo, enquanto um disco P20 acumula 2.300 IOPS por segundo enquanto está em marcha lenta sem atividade.

## <a name="bursting-states"></a>Estados rebentados
Há três estados em que o seu recurso pode estar com a explosão ativada:
- **Acumulação** – O tráfego de IO do recurso está a utilizar menos do que o objetivo de desempenho. A acumulação de créditos de rebentamento para IOPS e MB/s é feita separadamente umas das outras. O seu recurso pode estar a acumular créditos IOPS e a gastar créditos MB/s ou vice-versa.
- **Rebentamento** – O tráfego do recurso está a utilizar mais do que o alvo de desempenho. O tráfego de rajada consumirá créditos, de forma independente, da largura de banda ou do IOPS.
- **Constante** – O tráfego do recurso está exatamente no alvo de desempenho.

## <a name="examples-of-bursting"></a>Exemplos de explosão
Os exemplos seguintes mostram como a explosão funciona com várias combinações de máquinas virtuais e discos. Para tornar os exemplos fáceis de seguir, vamos focar-nos em MB/s, mas a mesma lógica é aplicada de forma independente ao IOPS.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Máquina virtual não-burstable com discos rebentados
**Combinação vm e disco:** 
- Standard_D8as_v4 
    - MB/s não cached: 192
- Disco P4 OS
    - MB/s provisionados: 25
    - Max burst MB/s: 170 
- 2 Discos de Dados P10 
    - MB/s provisionados: 25
    - Max burst MB/s: 170

 Quando o VM arrancar, irá recolher dados do disco OS. Uma vez que o disco OS faz parte de um VM que está a começar, o disco osso estará cheio de créditos de rebentamento. Estes créditos permitirão que o disco osso rebente a sua startup a 170 MB/s segundo, como visto abaixo:

![Startup de disco vm não-rebentando](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-startup.jpg)

Depois de a bota estar completa, uma aplicação é então executada no VM e tem uma carga de trabalho não crítica. Esta carga de trabalho requer 15 MB/S que se espalha uniformemente em todos os discos:

![Vm estourando vm idle](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-idling.jpg)

Em seguida, o pedido precisa processar um trabalho em lote que requer 192 MB/s. 2 MB/s são utilizados pelo Disco OS e os restantes dividem-se uniformemente entre os discos de dados:

![Vm rebentando disco não-bursting](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Máquina virtual de supreensível com discos não-rebentados
**Combinação vm e disco:** 
- Standard_L8s_v2 
    - MB/s não cached: 160
    - Max burst MB/s: 1.280
- Disco Os P50
    - MB/s provisionados: 250 
- 2 Discos de Dados P10 
    - MB/s provisionados: 250

 Após o arranque inicial, uma aplicação é executada no VM e tem uma carga de trabalho não crítica. Esta carga de trabalho requer 30 MB/s que se ![espalha uniformemente em todos os discos: Bursting vm non-bursting disqued](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Em seguida, o pedido precisa processar um trabalho em lote que requer 600 MB/s. O Standard_L8s_v2 rebenta para satisfazer esta procura e, em seguida, os pedidos para os discos são distribuídos uniformemente para discos P50:

![Rebentamento vm não-bursting disco rebentando](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Máquina virtual burstable com discos rebentados
**Combinação vm e disco:** 
- Standard_L8s_v2 
    - MB/s não cached: 160
    - Max burst MB/s: 1.280
- Disco P4 OS
    - MB/s provisionados: 25
    - Max burst MB/s: 170 
- 2 Discos de Dados P4 
    - MB/s provisionados: 25
    - Max burst MB/s: 170 

Quando o VM arrancar, explodirá para solicitar o seu limite de rutura de 1.280 MB/s do disco OS e o disco OS responderá com o seu desempenho de rutura de 170 MB/s:

![Explosão vm arranque de disco](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Em seguida, após a bota estar completa, uma aplicação é então executada no VM. A aplicação tem uma carga de trabalho não crítica que requer 15 MB/s que é distribuída uniformemente por todos os discos:

![Vm estourando disco ocioso](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Em seguida, o pedido precisa processar um trabalho em lote que requer 360 MB/s. O Standard_L8s_v2 rebenta para satisfazer esta exigência e depois pede. Apenas 20 MB/s são necessários pelo disco OS. Os restantes 340 MB/s são manuseados pelos discos de dados P4 em rutura:  

![Rebentamento do disco vm rebentando](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)