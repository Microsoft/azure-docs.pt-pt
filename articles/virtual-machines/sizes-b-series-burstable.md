---
title: Série B burstable - Máquinas Virtuais Azure
description: Descreve a série B de tamanhos VM Azure burstable.
services: virtual-machines
author: ayshakeen
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: ayshak
ms.openlocfilehash: 5121ef8eb3123d830233e91968c69b331d255bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161085"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>Tamanhos de máquina virtual de série B

Os VMs da série B são ideais para cargas de trabalho que não necessitam de todo o desempenho do CPU continuamente, como servidores web, prova de conceitos, pequenas bases de dados e ambientes de construção de desenvolvimento. Estas cargas de trabalho normalmente têm requisitos de desempenho insuportáveis. A série B proporciona-lhe a capacidade de adquirir um tamanho VM com desempenho de base e a instância VM acumula créditos quando está a usar menos do que a sua linha de base. Quando o VM tiver crédito acumulado, o VM pode rebentar acima da linha de base utilizando até 100% do vCPU quando a sua aplicação requer um desempenho cpu mais elevado.

A série B vem nos seguintes tamanhos VM:

Armazenamento Premium: Suportado

Caching de armazenamento premium: Não suportado

Migração Ao Vivo: Apoiado

Atualizações de preservação da memória: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Base CPU Perf da VM | Max CPU Perf da VM | Créditos Iniciais | Créditos bancários/hora | Créditos Bancários Max | Discos de dados máximos | Entrada de armazenamento em cache e temperatura máxima: IOPS/MBps | Potência de disco max uncached: IOPS/MBps | NICs máximos |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| <sup>Standard_B1ls 1</sup> | 1  | 0,5 | 4   | 5%   | 100%  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
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

Considere um pedido de check-in/out do escritório. A aplicação precisa de explosões de CPU durante o horário de trabalho, mas não muito poder de computação durante o horário de folga. Neste exemplo, a carga de trabalho requer uma máquina virtual de 16vCPU com 64GiB de RAM para funcionar de forma eficiente.

A tabela mostra os dados de tráfego de hora a hora e a tabela é uma representação visual desse tráfego.

Características B16:

Max CPU perf: 16vCPU * 100% = 1600%

Linha de base: 270%

![Gráfico dos dados de tráfego de hora a hora](./media/b-series-burstable/office-workload.png)

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
| Os colaboradores vêm ao escritório (app precisa de 80% vCPU) | 7:00 | 1280 | -606 | 846 |
| Os colaboradores continuam a vir ao escritório (app precisa de 80% vCPU) | 8:00 | 1280 | -606 | 240 |
| Tráfego baixo | 9:00 | 270 | 0 | 240 |
| Tráfego baixo | 10:00 | 100 | 102 | 342 |
| Tráfego baixo | 11:00 | 50 | 132 | 474 |
| Tráfego baixo | 12:00 | 100 | 102 | 576 |
| Tráfego baixo | 13:00 | 100 | 102 | 678 |
| Tráfego baixo | 14:00 | 50 | 132 | 810 |
| Tráfego baixo | 15:00 | 100 | 102 | 912 |
| Tráfego baixo | 16:00 | 100 | 102 | 1014 |
| Funcionários que check-out (app precisa de 100% vCPU) | 17:00 | 1600 | -798 | 216 |
| Tráfego baixo | 18:00 | 270 | 0 | 216 |
| Tráfego baixo | 19:00 | 270 | 0 | 216 |
| Tráfego baixo | 20:00 | 50 | 132 | 348 |
| Tráfego baixo | 21:00 | 50 | 132 | 480 |
| Sem trânsito | 22:00 | 0 | 162 | 642 |
| Sem trânsito | 23:00 | 0 | 162 | 804 |

<sup>1</sup> Os créditos acumulados/créditos utilizados numa hora equivalem a: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`.  

Para um D16s_v3 que tem 16 vCPUs e 64 GiB de memória, a taxa horária é de $0,936 por hora ($673,92 mensais) e para B16ms com 16 vCPUs e 64 GiB de memória a taxa é de $0,794 por hora ($547,86 mensal). <b>Isto resulta em poupanças de 15%!</b>

## <a name="q--a"></a>Perguntas e Respostas

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>P: Como obtém um desempenho de base de 135% de um VM?

**R**: Os 135% são partilhados entre os 8 vCPU's que compõem o tamanho VM. Por exemplo, se a sua aplicação utilizar 4 dos 8 núcleos que trabalham no processamento de lotes e cada um desses 4 vCPU's estiver em execução a 30% a quantidade total de desempenho do VM CPU equivaleria a 120%.  O que significa que o seu VM estaria a construir tempo de crédito com base no delta de 15% do seu desempenho de base.  Mas também significa que quando se tem créditos disponíveis, o mesmo VM pode usar 100% de todos os 8 VCPU's dando a esse VM um desempenho de CPU Max de 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>P: Como posso monitorizar o meu saldo e consumo de crédito

**R:** Vamos introduzir 2 novas métricas nas próximas semanas, a métrica do **Crédito** permitir-lhe-á visualizar quantos créditos o seu VM depositou e a métrica **Consumircrédito** mostrará quantos créditos cpu o seu VM consumiu do banco.    Poderá visualizar estas métricas a partir do painel de métricas no portal ou programáticamente através das APIs do Monitor Azure.

Para obter mais informações sobre como aceder aos dados das métricas para o Azure, consulte a [visão geral das métricas no Microsoft Azure](../azure-monitor/platform/data-platform.md).

### <a name="q-how-are-credits-accumulated"></a>P: Como são acumulados os créditos?

**R:** As taxas de acumulação e consumo de VM são definidas de tal forma que um VM que funciona exatamente ao seu nível de desempenho base não terá nem uma acumulação líquida nem o consumo de créditos rebentados.  Um VM terá um aumento líquido de créditos sempre que esteja abaixo do seu nível de desempenho base e terá uma diminuição líquida de créditos sempre que o VM estiver a utilizar o CPU mais do que o seu nível de desempenho base.

**Exemplo:** Implemento um VM utilizando o tamanho B1ms para a minha aplicação de base de dados de pouco tempo e presença. Este tamanho permite que a minha aplicação use até 20% de um vCPU como base, que é 0,2 créditos por minuto que posso usar ou banco.

A minha candidatura está ocupada no início e no fim do dia de trabalho dos meus empregados, entre as 7:00 e as 9:00 e as 16:00 às 18:00. Durante as outras 20 horas do dia, a minha candidatura está normalmente inativa, usando apenas 10% da vCPU. Para as horas não-álteras, ganho 0,2 créditos por minuto, mas só consumo 0,l créditos por minuto, por isso o meu VM irá depositar 0,1 x 60 = 6 créditos por hora.  Nas 20 horas que estou fora do pico, vou depositar 120 créditos.  

Durante as horas de ponta a minha aplicação media 60% de utilização vCPU, ainda ganho 0,2 créditos por minuto, mas consumo 0,6 créditos por minuto, por um custo líquido de 0,4 créditos por minuto ou 0,4 x 60 = 24 créditos por hora. Tenho 4 horas por dia de uso máximo, por isso custa 4 x 24 = 96 créditos para o meu uso máximo.

Se eu pegar nos 120 créditos que ganhei fora do pico e subtrair os 96 créditos que usei para os meus tempos de pico, eu deposito mais 24 créditos por dia que posso usar para outras explosões de atividade.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>P: Como posso calcular os créditos acumulados e utilizados?

**R:** Pode utilizar a seguinte fórmula:

(Perf base CPU de VM - UTILIZAção CPU) / 100 = Banco de créditos ou utilização por minuto

por exemplo, em caso sem conta, a sua linha de base é de 20% e se utilizar 10% do CPU está a acumular (20%-10%)/100 = 0,1 crédito por minuto.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>P: A Série B suporta discos de dados premium de armazenamento?

**R:** Sim, todos os tamanhos da Série B suportam discos de dados premium de armazenamento.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>P: Porque é que o meu crédito remanescente está definido para 0 após uma reimplantação ou uma paragem/início?

**R:** Quando um VM é "REDPLOYED" e o VM muda para outro nó, o crédito acumulado perde-se. Se o VM for parado/iniciado, mas permanecer no mesmo nó, o VM mantém o crédito acumulado. Sempre que o VM começa fresco num nó, recebe um crédito inicial, por Standard_B8ms é de 240 minutos.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>P: O que acontece se eu colocar uma imagem de Os não suportada em B1ls?

**A** : B1ls apenas suporta imagens Linux e se implementar outra imagem de OS poderá não ter a melhor experiência do cliente.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.