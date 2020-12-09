---
title: Série B rebentada - Azure Virtual Machines
description: Descreve a série B dos tamanhos Azure VM rebentado.
services: virtual-machines
ms.subservice: sizes
author: styli365
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: sttsinar
ms.openlocfilehash: ea812df825288eae3822cce01b24ebc82534c541
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928822"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>Tamanhos de máquina virtual rebentado da série B

Os VMs da série B são ideais para cargas de trabalho que não necessitam de um desempenho completo do CPU continuamente, como servidores web, provas de conceitos, pequenas bases de dados e ambientes de construção de desenvolvimento. Estas cargas de trabalho normalmente têm requisitos de desempenho rebeníveis. A série B proporciona-lhe a capacidade de adquirir um tamanho VM com desempenho de base que pode acumular créditos quando está a usar menos do que a sua linha de base. Quando o VM tiver créditos acumulados, o VM pode rebentar acima da linha de base usando até 100% do vCPU quando a sua aplicação requer um desempenho de CPU mais elevado.

A série B vem nos seguintes tamanhos VM:

[Unidade Azure Compute (ACU)](./acu.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): Varia*<br>
[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching de armazenamento premium](premium-storage-performance.md): Não suportado<br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1 e 2<br>
<br>
*Os VMs da série B são rebentados e, portanto, os números de ACU variam consoante as cargas de trabalho e a utilização do núcleo.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | CpU Base Perf de VM | Max CPU Perf de VM | Créditos Iniciais | Créditos bancários/hora | Créditos Max Banked | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0,5 | 4   | 5%   | 100%  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10%  | 100%  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20%  | 100%  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40%  | 200%  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60%  | 200%  | 60  | 36  | 864  | 4  | 2400/22.5 | 1920/22.5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90%  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2000% | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> B1ls é suportado apenas em Linux

## <a name="workload-example"></a>Exemplo de carga de trabalho

Considere um pedido de check-in/out do escritório. A aplicação precisa de explosões de CPU durante o horário comercial, mas não muita energia de computação durante as horas de folga. Neste exemplo, a carga de trabalho requer uma máquina virtual de 16vCPU com 64GiB de RAM para funcionar de forma eficiente.

A tabela mostra os dados de tráfego de hora em hora e o gráfico é uma representação visual desse tráfego.

Características B16:

Max CPU perf: 16vCPU * 100% = 1600%

Linha de Base: 270%

![Gráfico de dados de tráfego de hora em hora](./media/b-series-burstable/office-workload.png)

| Cenário | Hora | Utilização do CPU (%) | Créditos acumulados<sup>1</sup> | Créditos disponíveis |
| --- | --- | --- | --- | --- |
| Implantação de B16ms | Implementação | Implementação  | 480 (Créditos Iniciais) | 480 |
| Sem trânsito | 0:00 | 0 | 162 | 642 |
| Sem trânsito | 1:00 | 0 | 162 | 804 |
| Sem trânsito | 2:00 | 0 | 162 | 966 |
| Sem trânsito | 3:00 | 0 | 162 | 1128 |
| Sem trânsito | 4:00 | 0 | 162 | 1290 |
| Sem trânsito | 5:00 | 0 | 162 | 1452 |
| Tráfego baixo | 6:00 | 270 | 0 | 1452 |
| Os colaboradores chegam ao escritório (a app precisa de 80% de vCPU) | 7:00 | 1280 | -606 | 846 |
| Os colaboradores continuam a chegar ao escritório (a app precisa de 80% de vCPU) | 8:00 | 1280 | -606 | 240 |
| Tráfego baixo | 9:00 | 270 | 0 | 240 |
| Tráfego baixo | 10:00 | 100 | 102 | 342 |
| Tráfego baixo | 11:00 | 50 | 132 | 474 |
| Tráfego baixo | 12:00 | 100 | 102 | 576 |
| Tráfego baixo | 13:00 | 100 | 102 | 678 |
| Tráfego baixo | 14:00 | 50 | 132 | 810 |
| Tráfego baixo | 15:00 | 100 | 102 | 912 |
| Tráfego baixo | 16:00 | 100 | 102 | 1014 |
| Funcionários a verificar (a app precisa de 100% de vCPU) | 17:00 | 1600 | -798 | 216 |
| Tráfego baixo | 18:00 | 270 | 0 | 216 |
| Tráfego baixo | 19:00 | 270 | 0 | 216 |
| Tráfego baixo | 20:00 | 50 | 132 | 348 |
| Tráfego baixo | 21:00 | 50 | 132 | 480 |
| Sem trânsito | 22:00 | 0 | 162 | 642 |
| Sem trânsito | 23:00 | 0 | 162 | 804 |

<sup>1</sup> Os créditos acumulados/créditos utilizados numa hora equivalem a: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes` .  

Para um D16s_v3 que tem 16 vCPUs e 64 GiB de memória, a taxa horária é de $0,936 por hora (mensalmente $673,92) e para B16ms com 16 vCPUs e 64 GiB de memória a taxa é de $0,794 por hora (mensal $547,86). <b> Isto resulta em poupança de 15%!</b>

## <a name="q--a"></a>Perguntas e Respostas

### <a name="q-what-happens-when-my-credits-run-out"></a>P: O que acontece quando os meus créditos se esgotam?
**R**: Quando os créditos estão esgotados, o VM volta ao desempenho de base.

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>P: Como obtém um desempenho de base de 135% de um VM?

**R**: Os 135% são partilhados entre os 8 vCPU's que compõem o tamanho VM. Por exemplo, se a sua aplicação utilizar 4 dos 8 núcleos que trabalham no processamento de lotes e cada um desses 4 vCPU's estiver em execução a 30% de utilização, a quantidade total de desempenho do CPU VM equivaleria a 120%.  O que significa que o seu VM estaria a construir tempo de crédito baseado no delta de 15% do seu desempenho de base.  Mas também significa que quando tiver créditos disponíveis, esse mesmo VM pode usar 100% de todos os 8 vCPU's dando a esse VM um desempenho DE CPU Max de 800%.

### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>P: Como posso monitorizar o meu saldo de crédito e consumo?

**R**: A métrica **de Crédito** permite-lhe visualizar quantos créditos o seu VM foi depositado e a métrica **ConsumedCredit** mostrará quantos créditos cpU o seu VM consumiu do banco.    Poderá ver estas métricas a partir do painel de métricas do portal ou programáticamente através das APIs do Monitor Azure.

Para obter mais informações sobre como aceder aos dados das métricas para Azure, consulte [a visão geral das métricas no Microsoft Azure](../azure-monitor/platform/data-platform.md).

### <a name="q-how-are-credits-accumulated-and-consumed"></a>P: Como são acumulados e consumidos os créditos?

**R**: As taxas de acumulação e de consumo em VM são fixadas de modo a que um VM que esteja a funcionar exatamente ao seu nível de desempenho base não terá nem uma acumulação líquida nem um consumo de créditos de rebentamento.  Um VM terá um aumento líquido de créditos sempre que estiver a correr abaixo do seu nível de desempenho base e terá uma redução líquida de créditos sempre que o VM estiver a utilizar o CPU mais do que o seu nível de desempenho base.

**Exemplo:** Eu implanto um VM utilizando o tamanho B1ms para a minha aplicação de base de dados de tempo pequeno e de atendimento. Este tamanho permite que a minha aplicação use até 20% de um vCPU como base, que é 0,2 créditos por minuto que posso usar ou banco.

A minha candidatura está ocupada no início e no fim do dia de trabalho dos meus empregados, entre as 7:00 e as 9:00 e as 16:00 às 18:00. Durante as outras 20 horas do dia, a minha aplicação está tipicamente inativa, apenas usando 10% do vCPU. Para as horas não-pico, ganho 0,2 créditos por minuto, mas só consumato 0,l créditos por minuto, por isso o meu VM irá depositar 0,1 x 60 = 6 créditos por hora.  Pelas 20 horas que estou fora do pico, vou depositar 120 créditos.  

Durante as horas de ponta a minha aplicação tem uma média de 60% de utilização vCPU, ainda ganho 0,2 créditos por minuto, mas consuma 0,6 créditos por minuto, por um custo líquido de 0,4 créditos por minuto ou 0,4 x 60 = 24 créditos por hora. Tenho 4 horas por dia de utilização máxima, por isso custa 4 x 24 = 96 créditos para o meu pico de utilização.

Se pegar nos 120 créditos que ganhei fora do pico e subtrair os 96 créditos que usei para os meus picos, deposito mais 24 créditos por dia que posso usar para outras explosões de atividade.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>P: Como posso calcular créditos acumulados e utilizados?

**A**: Pode utilizar a seguinte fórmula:

(CpU base perf de VM - Utilização cpu) / 100 = Banco de créditos ou utilização por minuto

por exemplo, acima de exemplo, a sua linha de base é de 20% e se utilizar 10% do CPU está a acumular (20%-10%)/100 = 0,1 crédito por minuto.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>P: A Série B suporta discos de dados premium de armazenamento?

**R:** Sim, todos os tamanhos da Série B suportam discos de dados de armazenamento premium.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>P: Por que o meu crédito restante está definido para 0 após uma recolocação ou uma paragem/arranque?

**R** : Quando um VM é redistribuído e o VM passa para outro nó, perde-se o crédito acumulado. Se o VM for parado/iniciado, mas permanecer no mesmo nó, o VM mantém o crédito acumulado. Sempre que o VM começa fresco num nó, recebe um crédito inicial, para Standard_B8ms é de 240.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>P: O que acontece se eu colocar uma imagem de SO não suportada em B1ls?

**R:** B1ls suporta apenas imagens Linux e se implementar outra imagem de SO poderá não obter a melhor experiência do cliente.

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)

Calculadora de preços: [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos : [tipos de discos](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
