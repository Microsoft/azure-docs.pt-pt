---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 03/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 3035b5d2803ff91e84bc6b47a99963185f9195d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623191"
---
## <a name="disk-level-bursting"></a>Rebentamento do nível do disco

### <a name="on-demand-bursting-preview"></a>Rebentamento a pedido (pré-visualização)

Os discos que utilizam o modelo de rebentamento a pedido de rebentamento de discos podem rebentar para além dos alvos originais previstos, sempre que necessários pela sua carga de trabalho, até ao alvo máximo de rutura. Por exemplo, num disco P30 de 1-TiB, o IOPS provisionado é de 5000 IOPS. Quando o disco rebenta ativado neste disco, as suas cargas de trabalho podem emitir IOs neste disco até ao desempenho máximo de 30.000 IOPS e 1.000 MBps.

Se espera que as suas cargas de trabalho ultrapassem frequentemente o objetivo perf previsto, a explosão do disco não será rentável. Neste caso, recomendamos que altere o nível de desempenho do disco para um [nível mais alto,](../articles/virtual-machines/disks-performance-tiers.md) para um melhor desempenho de base. Reveja os seus dados de faturação e avalie isso contra o padrão de tráfego das suas cargas de trabalho.

Antes de permitir o rebentamento a pedido, compreenda o seguinte:

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>Faturação

Os discos que utilizam o modelo de rebentamento a pedido são cobrados uma taxa fixa de ativação por hora e os custos de transação aplicam-se a quaisquer transações de rutura para além do objetivo previsto. Os custos de transação são cobrados utilizando o modelo pay-as-you go, com base em IOs de disco não encaixotado, incluindo leituras e escritos que excedam os alvos previstos. Segue-se um exemplo de padrões de tráfego de discos durante uma hora de faturação:

Configuração do disco: Premium SSD – 1 TiB (P30), rebentamento do disco ativado.

- 00:00:00 - 00:10:00 Disco IOPS abaixo do objetivo previsto de 5.000 IOPS 
- 00:10:01 - 00:10:10 A aplicação emitiu um trabalho de lote fazendo com que o disco IOPS explodisse a 6.000 IOPS durante 10 segundos 
- 00:10:11 - 00:59:00 Disco IOPS abaixo do objetivo previsto de 5.000 IOPS 
- 00:59:01 - 01:00:00 A aplicação emitiu outro lote de trabalho, fazendo com que o disco IOPS explodisse a 7.000 IOPS durante 60 segundos 

Nesta hora de faturação, o custo de rebentamento consiste em duas taxas:

A primeira carga é a taxa plana de ativação de $X (determinada pela sua região). Esta taxa fixa é sempre cobrada no desrespeito do disco do estado da anexação até que seja desativada. 

Em segundo lugar, o custo da transação de rutura. A explosão do disco ocorreu em duas faixas horárias. Das 00:10:01 às 00:10:10, a transação de explosão acumulada é (6.000 - 5.000) X 10 = 10.000. Das 00:59:01 às 01:00:00, a transação de explosão acumulada é (7.000 - 5.000) X 60 = 120.000. O total de transações de explosão é de 10.000 + 120.000 = 130.000. O custo de transação de explosão será cobrado em $Y com base em 13 unidades de 10.000 transações (com base nos preços regionais).

Com isso, o custo total do disco rebentando desta hora de faturação equivale a $X + $Y. O mesmo cálculo aplicar-se-ia à explosão sobre o objetivo previsto dos MBps. Traduzimos o excesso de MB para transações com tamanho IO de 256KB. Se o tráfego do disco exceder o alvo de IOPS e MBps, pode consultar o exemplo abaixo para calcular as transações de explosão. 

Configuração do disco: Premium SSD – 1 TB (P30), rebentamento do disco ativado.

- 00:00:01 - 00:00:05 A aplicação emitiu um trabalho de lote fazendo com que o disco IOPS explodisse a 10.000 IOPS e 300 MBps durante cinco segundos.
- 00:00:06 - 00:00:10 A aplicação emitiu um trabalho de recuperação fazendo com que o disco IOPS explodisse em 6.000 IOPS e 600 MBps durante cinco segundos.

A transação de explosão é contabilizada como o número máximo de transações de IOPS ou MBps rebentando. Das 00:00:01 às 00:00:05, a transação de rebentamento acumulada é má ((10.000 – 5.000), (300 - 200) * 1024 / 256)) * 5 = 25.000 transações. Das 00:00:06 às 00:00:10, a transação de rebentamento acumulada é má ((6.000 – 5.000), (600 - 200) * 1024 / 256)) * 5 = 8.000 transações. Além disso, você inclui a taxa plana de ativação de explosão para obter o custo total para permitir a explosão de disco baseado na procura. 

Pode consultar a página de preços dos [Discos Geridos](https://azure.microsoft.com/pricing/details/managed-disks/) para obter detalhes sobre preços e utilizar a [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=storage) para fazer a avaliação da sua carga de trabalho. 

### <a name="credit-based-bursting"></a>Rebentamento baseado em crédito

A explosão baseada em crédito está disponível para tamanhos de disco P20 e menores em todas as regiões em Azure Public, Government e China Clouds. Por predefinição, a rutura do disco é ativada em todas as implementações novas e existentes de tamanhos de disco suportados. A explosão ao nível de VM só usa rebentamento baseado em crédito.

## <a name="virtual-machine-level-bursting"></a>Rebentamento de nível de máquina virtual

A explosão ao nível de VM utiliza apenas o modelo baseado no crédito para rebentar, é ativado por padrão para todos os VMs que o suportam.

A explosão ao nível de VM é ativada em todas as regiões da Nuvem Pública de Azure nestes tamanhos suportados: 
- [Série Lsv2](../articles/virtual-machines/lsv2-series.md)
- [Séries Dv3 e Dsv3](../articles/virtual-machines/dv3-dsv3-series.md)
- [Séries Ev3 e Esv3](../articles/virtual-machines/ev3-esv3-series.md)

## <a name="bursting-flow"></a>Fluxo de rebentamento

O sistema de crédito estourando aplica-se da mesma forma tanto ao nível do VM como ao nível do disco. O seu recurso, seja um VM ou um disco, começará com créditos totalmente abastecidos no seu próprio balde de rebentamento. Estes créditos permitem-lhe rebentar até 30 minutos à taxa máxima de explosão. Acumula créditos sempre que o IOPS ou MB/s do recurso estiver a ser utilizado abaixo do objetivo de desempenho do recurso. Se o seu recurso tiver acumulado créditos de rebentamento e a sua carga de trabalho necessitar do desempenho extra, o seu recurso pode usar esses créditos para ultrapassar os seus limites de desempenho e aumentar o seu desempenho para satisfazer as exigências de carga de trabalho.

![Diagrama de balde rebentando.](media/managed-disks-bursting/bucket-diagram.jpg)

A forma como gastas os teus créditos disponíveis depende de ti. Você pode usar seus 30 minutos de créditos de rajada consecutivamente ou esporadicamente durante todo o dia. Quando os recursos são utilizados, vêm com uma alocação total de créditos. Quando estes se esgotam, leva menos de um dia para reabastecer. Os créditos podem ser gastos a seu critério, o balde de rebentamento não precisa de estar cheio para que os recursos rebentem. A acumulação de explosão varia consoante cada recurso, uma vez que se baseia em IOPS não uused e MB/s abaixo dos seus objetivos de desempenho. Os recursos de desempenho de base mais elevados podem acumular os seus créditos de rebentamento mais rapidamente do que os recursos de desempenho de base mais baixos. Por exemplo, um disco P1 em idling irá acumular 120 IOPS por segundo, enquanto um disco P20 em 20 em 200 acumula 2.300 IOPS por segundo.

## <a name="bursting-states"></a>Estados em explosão
Existem três estados em que o seu recurso pode estar com a explosão ativada:
- **Acumulação** – O tráfego de IO do recurso está a utilizar menos do que o objetivo de desempenho. Os créditos de rebentamento acumulados para IOPS e MB/s são feitos separados uns dos outros. O seu recurso pode estar a acumular créditos IOPS e a gastar créditos MB/s ou vice-versa.
- **Rebentamento** – O tráfego do recurso está a utilizar mais do que o alvo de desempenho. O tráfego de rajada consumirá créditos, de forma independente, da largura de banda ou do IOPS.
- **Constante** – O tráfego do recurso está exatamente no alvo de desempenho.

## <a name="bursting-examples"></a>Exemplos de explosão

Os exemplos a seguir mostram como a explosão funciona com várias combinações de VM e discos. Para tornar os exemplos fáceis de seguir, vamos focar-nos em MB/s, mas a mesma lógica é aplicada independentemente ao IOPS.

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

 Quando o VM arranca, obtém dados do disco os. Uma vez que o disco DE faz parte de um VM que está a arrancar, o disco de SO estará cheio de créditos rebentados. Estes créditos permitirão que o disco SO rebente o seu arranque em 170 MB/s em segundo.

![A VM envia um pedido de 192 MB/s de produção para o disco os, o disco de OS responde com 170 MB/s dados.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

Após o arranque estar concluído, uma aplicação é executada no VM e tem uma carga de trabalho não crítica. Esta carga de trabalho requer 15 MB/S que é distribuído uniformemente por todos os discos.

![A aplicação envia um pedido de 15 MB/s de produção para VM, vM recebe pedido e envia a cada um dos seus discos um pedido de 5 MB/s, cada disco devolve 5 MB/s, VM devolve 15 MB/s à aplicação.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

Em seguida, a aplicação precisa processar um trabalho em lotado que requer 192 MB/s. 2 MB/s são utilizados pelo disco de so e os restantes são divididos uniformemente entre os discos de dados.

![A aplicação envia pedido de 192 MB/s de produção para VM, VM recebe pedido e envia a maior parte do seu pedido para os discos de dados (95 MB/s cada) e 2 MB/s para o disco de OS, os discos de dados rebentam para satisfazer a procura e todos os discos devolvem o rendimento solicitado ao VM, que o devolve à aplicação.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Máquina virtual rebentada com discos não rebentados
**VM e combinação de discos:** 
- Standard_L8s_v2 
    - MB/s sem cócos: 160
    - Max burst MB/s: 1.280
- Disco P50 OS
    - MB/s provisionados: 250 
- 2 Discos de dados P10 
    - MB/s provisionados: 250

 Após o arranque inicial, uma aplicação é executada no VM e tem uma carga de trabalho não crítica. Esta carga de trabalho requer 30 MB/s que é distribuído uniformemente por todos os discos.
![A aplicação envia um pedido de 30 MB/s de produção para VM, VM recebe pedido e envia a cada um dos seus discos um pedido de 10 MB/s, cada disco devolve 10 MB/s, VM devolve 30 MB/s à aplicação.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Em seguida, a aplicação precisa processar um trabalho em massa que requer 600 MB/s. O Standard_L8s_v2 rebenta para satisfazer esta procura e, em seguida, os pedidos para os discos são distribuídos uniformemente para discos P50.

![A aplicação envia um pedido de 600 MB/s de produção para VM, VM leva rajadas para receber o pedido e envia a cada um dos seus discos um pedido de 200 MB/s, cada disco devolve 200 MB/s, explosões de VM para devolver 600 MB/s à aplicação.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
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

Quando o VM começar, irá rebentar para solicitar o seu limite de explosão de 1.280 MB/s do disco de so e o disco de so responderá com o seu desempenho de rajada de 170 MB/s.

![No arranque, o VM explode para enviar um pedido de 1.280 MB/s para o disco de OS, o disco DER rebenta para devolver os 1.280 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Após o arranque, inicia-se uma aplicação com uma carga de trabalho não crítica. Esta aplicação requer 15 MB/s que é distribuído uniformemente por todos os discos.

![A aplicação envia um pedido de 15 MB/s de produção para VM, vM recebe pedido e envia a cada um dos seus discos um pedido de 5 MB/s, cada disco devolve 5 MB/s respostas, VM devolve 15 MB/s à aplicação.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Em seguida, a aplicação precisa processar um trabalho em massa que requer 360 MB/s. O Standard_L8s_v2 rebenta para satisfazer esta exigência e depois pede. Apenas 20 MB/s são necessários pelo disco de so. Os restantes 340 MB/s são tratados pelos discos de dados P4 rebentados.

![A aplicação envia um pedido de 360 MB/s de produção para VM, VM leva rajadas para receber o pedido e envia a cada um dos seus discos de dados um pedido de 170 MB/s e 20 MB/s do disco de OS, cada disco devolve os MB/s solicitados, explosões de VM para devolver 360 MB/s à aplicação.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
