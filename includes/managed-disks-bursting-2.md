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
ms.openlocfilehash: 1a51cd242eb26709464aa502938cd04807aea721
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97805606"
---
## <a name="common-scenarios"></a>Cenários comuns
Os seguintes cenários podem beneficiar muito da explosão:
- **Melhorando os tempos de arranque**  – Com a explosão, a sua instância arrancará a uma taxa significativamente mais rápida. Por exemplo, o disco de OS padrão para VMs habilitados a prémios é o disco P4, que é um desempenho previsto de até 120 IOPS e 25 MB/s. Com a explosão, o P4 pode ir até 3500 IOPS e 170 MB/s permitindo que um tempo de arranque acelere em 6X.
- **Manuseamento de trabalhos** em lotes – Algumas cargas de trabalho da aplicação são de natureza cíclica e requerem um desempenho de base durante a maior parte do tempo e exigem um desempenho mais elevado por um curto período de tempo. Um exemplo disso é um programa de contabilidade que processa transações diárias que requerem uma pequena quantidade de tráfego de discos. Depois, no final do mês, concilia relatórios que precisam de uma quantidade muito maior de tráfego de discos.
- **Preparação para picos de tráfego** – Os servidores web e as suas aplicações podem experimentar picos de tráfego a qualquer momento. Se o seu servidor web for apoiado por VMs ou discos que utilizem explosões, os servidores estão mais bem equipados para lidar com picos de tráfego. 

## <a name="bursting-flow"></a>Fluxo de rebentamento
O sistema de crédito estourando aplica-se da mesma forma tanto ao nível da máquina virtual como ao nível do disco. O seu recurso, seja um VM ou um disco, começará com créditos totalmente armazenados. Estes créditos permitir-lhe-ão rebentar durante 30 minutos à taxa máxima de explosão. Os créditos de rebentamento acumulam-se quando o seu recurso está a funcionar sob os seus limites de armazenamento de discos de desempenho. Para todos os IOPS e MB/s que o seu recurso está a utilizar abaixo do limite de desempenho, começa a acumular créditos. Se o seu recurso tiver créditos acumulados para usar para rebentar e a sua carga de trabalho necessitar do desempenho extra, o seu recurso pode usar esses créditos para ir além do seu limite de desempenho para lhe dar o desempenho do IO do disco que precisa para satisfazer a procura.



![Diagrama de balde de explosão](media/managed-disks-bursting/bucket-diagram.jpg)

Depende de si sobre como quer usar os 30 minutos de explosão. Pode usá-lo durante 30 minutos consecutivos ou esporadicamente ao longo do dia. Quando o produto é implantado, fica pronto com todos os créditos e quando esgota os créditos demora menos de um dia a ficar totalmente abastecido de créditos novamente. Você pode acumular e gastar os seus créditos de rebentamento a seu critério e o balde de 30 minutos não precisa estar cheio novamente para explodir. Uma coisa a notar sobre a acumulação de explosão é que é diferente para cada recurso, uma vez que é baseado no IOPS não uused e MB/s abaixo dos seus valores de desempenho. Isto significa que os produtos de desempenho de base mais elevados podem acumular as suas quantidades de rebentamento mais rapidamente do que os produtos de desempenho de base mais baixos. Por exemplo, um disco P1 em escoamento sem atividade irá acumular 120 IOPS por segundo, enquanto um disco P20 acumula 2.300 IOPS por segundo enquanto está em óquelinho sem atividade.

## <a name="bursting-states"></a>Estados em explosão
Existem três estados em que o seu recurso pode estar com a explosão ativada:
- **Acumulação** – O tráfego de IO do recurso está a utilizar menos do que o objetivo de desempenho. Os créditos de rebentamento acumulados para IOPS e MB/s são feitos separados uns dos outros. O seu recurso pode estar a acumular créditos IOPS e a gastar créditos MB/s ou vice-versa.
- **Rebentamento** – O tráfego do recurso está a utilizar mais do que o alvo de desempenho. O tráfego de rajada consumirá créditos, de forma independente, da largura de banda ou do IOPS.
- **Constante** – O tráfego do recurso está exatamente no alvo de desempenho.

## <a name="examples-of-bursting"></a>Exemplos de estouragens
Os exemplos a seguir mostram como a explosão funciona com várias combinações de máquinas virtuais e discos. Para tornar os exemplos fáceis de seguir, vamos focar-nos em MB/s, mas a mesma lógica é aplicada independentemente ao IOPS.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Máquina virtual não rebentada com discos rebentados
**VM e combinação de discos:** 
- Standard_D8as_v4 
    - MB/s sem cócos: 192
- Disco P4 OS
    - MB/s provisionados: 25
    - Max burst MB/s: 170 
- 2 Discos de dados P10 
    - MB/s provisionados: 100
    - Max burst MB/s: 170

 Quando o VM arrancar, recuperará dados do disco oss. Uma vez que o disco DE faz parte de um VM que está a começar, o disco de SO estará cheio de créditos rebentados. Estes créditos permitirão que o disco DE rebentasse o seu arranque em 170 MB/s segundo, como se vê abaixo:

![Arranque de disco de rutura vm não rebentando](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-startup.jpg)

Após o arranque estar concluído, uma aplicação é executada no VM e tem uma carga de trabalho não crítica. Esta carga de trabalho requer 15 MB/S que é distribuído uniformemente por todos os discos:

![O disco de rutura vm não rebentando ocioso](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-idling.jpg)

Em seguida, a aplicação precisa processar um trabalho em lotado que requer 192 MB/s. 2 MB/s são utilizados pelo disco oss e os restantes são divididos uniformemente entre os discos de dados:

![Não estourando vm rebentando disco rebentando](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Máquina virtual rebentada com discos não rebentados
**VM e combinação de discos:** 
- Standard_L8s_v2 
    - MB/s sem cócos: 160
    - Max burst MB/s: 1.280
- Disco P50 OS
    - MB/s provisionados: 250 
- 2 Discos de dados P10 
    - MB/s provisionados: 250

 Após o arranque inicial, uma aplicação é executada no VM e tem uma carga de trabalho não crítica. Esta carga de trabalho requer 30 MB/s que se espalha uniformemente por todos os discos: ![ Estourando vm não estourando o disco inativo](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Em seguida, a aplicação precisa processar um trabalho em massa que requer 600 MB/s. O Standard_L8s_v2 rebenta para satisfazer esta procura e, em seguida, os pedidos para os discos são distribuídos uniformemente para discos P50:

![Rebentamento do disco não rebentando vm](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Máquina virtual rebentada com discos rebentados
**VM e combinação de discos:** 
- Standard_L8s_v2 
    - MB/s sem cócos: 160
    - Max burst MB/s: 1.280
- Disco P4 OS
    - MB/s provisionados: 25
    - Max burst MB/s: 170 
- 2 discos de dados P4 
    - MB/s provisionados: 25
    - Max burst MB/s: 170 

Quando o VM arrancar, irá rebentar para solicitar o seu limite de explosão de 1.280 MB/s do disco de so e o disco de so responderá com o seu desempenho de 170 MB/s:

![Startup de disco estourando vm rebentando](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Em seguida, após o arranque estar completo, uma aplicação é executada no VM. A aplicação tem uma carga de trabalho não crítica que requer 15 MB/s que é distribuído uniformemente por todos os discos:

![Estourando vm estourando ocioso disco ocioso](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Em seguida, a aplicação precisa processar um trabalho em massa que requer 360 MB/s. O Standard_L8s_v2 rebenta para satisfazer esta exigência e depois pede. Apenas 20 MB/s são necessários pelo disco de so. Os restantes 340 MB/s são tratados pelos discos de dados P4 rebentados:  

![Explosão de vm estourando disco rebentando](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
