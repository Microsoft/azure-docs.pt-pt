---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 17c9ea33f4706053ccde5e99686887aab7a823b6
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942822"
---
A família de VMs da série B permite que você escolha qual tamanho de VM fornece o desempenho de nível base necessário para sua carga de trabalho, com a capacidade de aumentar o desempenho da CPU de até 100% de um processador Intel® Broadwell E5-2673 v4 2,3 GHz ou um Intel® Haswell 2,4 GHz E5-2673 v3 vCPU.

As VMs da série B são ideais para cargas de trabalho que não precisam do desempenho total da CPU continuamente, como servidores Web, prova de conceitos, bancos de dados pequenos e ambientes de compilação de desenvolvimento. Essas cargas de trabalho normalmente têm requisitos de desempenho intermitentes. A série B fornece a capacidade de comprar um tamanho de VM com desempenho de linha de base e a instância de VM cria créditos quando ele está usando menos de sua linha de base. Quando a VM tiver acumulado o crédito, a VM poderá ultrapassar a linha de base usando até 100% do vCPU quando seu aplicativo exigir um desempenho de CPU maior.

A série B vem nos seguintes tamanhos de VM:

| Tamanho             | vCPU  | Memória: GiB | Armazenamento (SSD) temporário em GiB | Desempenho base da CPU da VM | Desempenho máximo de CPU da VM | Créditos iniciais | Créditos ganhos/hora | Créditos acumulados máximos | Máximo de discos de dados | Taxa de transferência máxima de armazenamento em cache e temporária: IOPS/MBps | Débito máximo do disco não colocado em cache: IOPS/MBps | NICs máximos |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320/50                                 | 4320/50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202%                  | 1200%                   | 360                   | 121                 | 2909           | 16                                     | 6480 / 75                                 | 4320/50                                  | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270%                  | 1600%                   | 480                   | 162                 | 3888           | 32                                     | 8640 / 100                                 | 4320/50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337%                  | 2000%                   | 600                   | 203                 | 4860           | 32                                     | 10800/125                                 | 4320/50                                 | 8  |

<sup>1</sup> B1ls tem suporte apenas no Linux

## <a name="workload-example"></a>Exemplo de carga de trabalho

Considere um aplicativo de check-in/saída do Office. O aplicativo precisa de picos de CPU durante o horário comercial, mas não muito poder de computação fora do horário de expediente. Neste exemplo, a carga de trabalho requer uma máquina virtual 16vCPU com 64GiB de RAM para trabalhar com eficiência.

A tabela mostra os dados de tráfego por hora e o gráfico é uma representação visual desse tráfego.

Características do B16:

Desempenho máximo da CPU: 16vCPU * 100% = 1600%

Linha de base: 270%

![Gráfico de dados de tráfego por hora](./media/virtual-machines-common-b-series-burstable/office-workload.png)

| Cenário | Tempo | Uso da CPU (%) | Créditos acumulados<sup>1</sup> | Créditos disponíveis |
| --- | --- | --- | --- | --- |
| Implantação do B16ms | Implementação | Implementação  | 480 (créditos iniciais) | 480 |
| Nenhum tráfego | 0:00 | 0 | 162 | 642 |
| Nenhum tráfego | 1:00 | 0 | 162 | 804 |
| Nenhum tráfego | 2:00 | 0 | 162 | 966 |
| Nenhum tráfego | 3:00 | 0 | 162 | 1128 |
| Nenhum tráfego | 4:00 | 0 | 162 | 1290 |
| Nenhum tráfego | 5:00 | 0 | 162 | 1452 |
| Baixo tráfego | 6:00 | 270 | 0 | 1452 |
| Os funcionários chegam ao Office (o aplicativo precisa de 80% vCPU) | 7:00 | 1280 | -606 | 846 |
| Os funcionários continuam chegando ao Office (as necessidades do aplicativo são de 80% vCPU) | 8:00 | 1280 | -606 | 240 |
| Baixo tráfego | 9:00 | 270 | 0 | 240 |
| Baixo tráfego | 10:00 | 100 | 102 | 342 |
| Baixo tráfego | 11:00 | 50 | 132 | 474 |
| Baixo tráfego | 12:00 | 100 | 102 | 576 |
| Baixo tráfego | 13:00 | 100 | 102 | 678 |
| Baixo tráfego | 14:00 | 50 | 132 | 810 |
| Baixo tráfego | 15:00 | 100 | 102 | 912 |
| Baixo tráfego | 16:00 | 100 | 102 | 1014 |
| Os funcionários estão fazendo check-out (o aplicativo precisa de 100% vCPU) | 17:00 | 1600 | -798 | 216 |
| Baixo tráfego | 18:00 | 270 | 0 | 216 |
| Baixo tráfego | 19:00 | 270 | 0 | 216 |
| Baixo tráfego | 20:00 | 50 | 132 | 348 |
| Baixo tráfego | 21:00 | 50 | 132 | 480 |
| Nenhum tráfego | 22:00 | 0 | 162 | 642 |
| Nenhum tráfego | 23:00 | 0 | 162 | 804 |

<sup>1</sup> os créditos acumulados/créditos usados em uma hora são equivalentes a: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`.  

Para um D16s_v3 que tem 16 vCPUs e 64 GiB de memória, a taxa horária é $0.936 por hora (mensalmente $673.92) e para B16ms com 16 vCPUs e 64 GiB de memória a taxa é $0.794 por hora (mensalmente $547.86). <b>Isso resulta em uma economia de 15%!</b>

## <a name="q--a"></a>P&R

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>P: como obter o desempenho de linha de base de 135% de uma VM?
**R**: o 135% é compartilhado entre os 8 vCPU que compõem o tamanho da VM. Por exemplo, se seu aplicativo usa 4 dos 8 núcleos trabalhando no processamento em lotes e cada um desses 4 vCPU está sendo executado a 30% de utilização, a quantidade total de desempenho da CPU da VM é igual a 120%.  O que significa que sua VM estaria criando um tempo de crédito com base no Delta de 15% do desempenho da linha de base.  Mas isso também significa que, quando você tiver créditos disponíveis, a mesma VM poderá usar 100% de todos os 8 vCPU dando a VM um desempenho máximo de CPU de 800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>P: como posso monitorar meu saldo de crédito e consumo
**R**: apresentaremos 2 novas métricas nas próximas semanas, a métrica de **crédito** permitirá que você EXIBA quantos créditos sua VM ganhou e a métrica de **ConsumedCredit** mostrará quantos créditos de CPU sua VM consumiu do banco.    Você poderá exibir essas métricas no painel de métricas no portal ou programaticamente por meio das APIs de Azure Monitor.

Para obter mais informações sobre como acessar os dados de métricas para o Azure, consulte [visão geral das métricas em Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>P: como os créditos são acumulados?
**R**: as taxas de acumulação e consumo da VM são definidas de modo que uma VM em execução exatamente em seu nível de desempenho base não terá um acúmulo líquido ou consumo de créditos de intermitência.  Uma VM terá um aumento em créditos sempre que estiver em execução abaixo de seu nível de desempenho base e terá uma redução líquida em créditos sempre que a VM estiver utilizando a CPU mais do que seu nível de desempenho básico.

**Exemplo**: Eu implanto uma VM usando o tamanho de B1ms para meu aplicativo de banco de dados pequeno de horário e presença. Esse tamanho permite que meu aplicativo use até 20% de um vCPU como minha linha de base, que é de 0,2 créditos por minuto que posso usar ou bancárias. 

Meu aplicativo está ocupado no início e no final do dia de trabalho de meus funcionários, entre 7:00-9:00 AM e 4:00-6:13h. Durante as outras 20 horas do dia, meu aplicativo normalmente está ocioso, usando apenas 10% do vCPU. Para o horário fora do pico, recebi 0,2 créditos por minuto, mas consumindo apenas 0. l créditos por minuto, portanto, minha VM terá o banco 0,1 x 60 = 6 créditos por hora.  Para as 20 horas em que estou fora do horário de pico, eu vou usar os créditos do banco de 120.  

Durante o horário de pico, meu aplicativo calcula a média de 60% vCPU de utilização, ainda recebo 0,2 créditos por minuto, mas utilizo 0,6 créditos por minuto, para um custo líquido de 0,4 créditos por minuto ou 0,4 x 60 = 24 créditos por hora. Tenho 4 horas por dia de uso de pico e, portanto, custa 4 x 24 = 96 créditos para o uso de pico.

Se eu pegar os créditos de 120 que obtiveram fora de pico e subtrair os créditos de 96 que usei para meus horários de pico, eu Banko um adicional 24 créditos por dia que posso usar para outras intermitências de atividade.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>P: como posso calcular os créditos acumulados e usados?
**R**: você pode usar a seguinte fórmula: 

(Desempenho base da CPU da VM-uso da CPU)/100 = banco de créditos ou uso por minuto

por exemplo, na instância acima, sua linha de base é de 20% e se você usar 10% da CPU que está acumulando (20%-10%)/100 = 0,1 crédito por minuto.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>P: a série B dá suporte a discos de dados de armazenamento Premium?
**R**: Sim, todos os tamanhos da série B dão suporte a discos de dados de armazenamento Premium.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>P: por que meu crédito restante é definido como 0 após uma reimplantação ou parada/início?
**R** : quando uma VM é "REDPLOYED" e a VM é movida para outro nó, o crédito acumulado é perdido. Se a VM for interrompida/iniciada, mas permanecer no mesmo nó, a VM manterá o crédito acumulado. Sempre que a VM começa a ser atualizada em um nó, ela obtém um crédito inicial, por Standard_B8ms é de 240 minutos.
    
### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>P: o que acontecerá se eu implantar uma imagem de sistema operacional sem suporte no B1ls?
**R: o** B1ls só dá suporte a imagens do Linux e, se você implantar qualquer outra imagem do sistema operacional, talvez não obtenha a melhor experiência do cliente.

### <a name="q-what-happens-if-i-run-out-of-credits"></a>P: o que acontece se eu ficar sem créditos?
**R** : se a VM gastar todos os créditos disponíveis, ela terá permissão para usar apenas a CPU de linha de base e não poderá disparar acima dessa linha de base. 
