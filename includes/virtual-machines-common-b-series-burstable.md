---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: ecf70bbbeae8fd68309f3343615f021038fb10b6
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66111440"
---
A família VM de série B permite-lhe escolher o tamanho VM fornece-lhe o desempenho de nível de base necessárias para a sua carga de trabalho, a capacidade de desempenho de CPU de rajada de até 100% de um v4 Intel® Broadwell E5-2673 2.3 GHz ou um processador de v3 Intel® Haswell 2.4 GHz E5-2673 vCPU.

As VMs de série B são ideais para cargas de trabalho que não é necessário um desempenho total da CPU continuamente, como servidores web, prova de conceitos, pequenas bases de dados e ambientes de compilação de desenvolvimento. Estas cargas de trabalho normalmente têm requisitos de desempenho burstable. A série B fornece-lhe a possibilidade de comprar um tamanho de VM com o desempenho de linha de base e a instância VM cria créditos quando está a utilizar menos do que sua linha de base. Quando a VM acumular crédito, a VM pode ultrapassar os limites acima a linha de base com até 100% da vCPU quando a sua aplicação necessitar de desempenho de CPU superior.

A série B possui os seguintes seis tamanhos VM:

| Tamanho             | vCPU  | Memória: GiB | Armazenamento (SSD) temporário GiB | Base de desempenho de CPU da VM | Desempenho de CPU máxima da VM | Créditos iniciais | Créditos banked / hora | Máx. Banked créditos | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS / MBps | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs máximos |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |

<sup>1</sup> B1ls só é suportado no Linux

## <a name="q--a"></a>P&R 

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>P. Como obtém 135% de desempenho de linha de base de uma VM?
**A**: A % de 135 é partilhada entre os 8 Vcpus que compõem o tamanho da VM. Por exemplo, se a sua aplicação utiliza 4 de 8 núcleos a trabalhar no processamento em lotes e cada um desses 4 vCPU executem a 30% de utilização a quantidade total de desempenho de VM CPU seria igual a % de 120.  O que significa que a VM seria criar tempo de crédito com base no delta de 15% do desempenho da linha de base.  Mas isso também significa que quando tem os créditos disponíveis que a mesma VM pode utilizar 100% da vCPU de 8 todas as estamos a dar essa VM um desempenho de CPU máxima de % de 800.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>P. Como posso monitorizar o meu saldo de crédito e o consumo
**A**: Podemos irá apresentar o 2 novas métricas nas próximas semanas, a **crédito** métrica permite-lhe ver quantos créditos a VM tem banked e o **ConsumedCredit** métrica mostra quantos créditos CPU tem a sua VM consumido do banco.    Poderá ver estas métricas a partir do painel de métricas no portal ou através de programação através das APIs de Monitor do Azure.

Para obter mais informações sobre como acessar os dados das métricas para o Azure, consulte [descrição geral das métricas no Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>P. Como são acumulados os créditos?
**A**: As tarifas de acumulação e consumo de VM são definidas, de modo a que uma VM em execução em exatamente seu nível de desempenho de base terá nem uma acumulação de rede ou o consumo de extrapolação créditos.  Uma VM terá um aumento de net em créditos sempre que estiver em execução abaixo seu nível de desempenho de base e terá uma diminuição líquida créditos sempre que a VM estiver a utilizar a CPU mais do que seu nível de desempenho de base.

**Exemplo**:  Posso implementar uma VM com o tamanho de B1ms para meu pequeno tempo e o aplicativo de banco de dados de participação. Este tamanho permite que a minha aplicação utilizar até 20% de um vCPU como minha linha de base, o que é 0,2 créditos por minuto, que posso usar ou banco. 

Meu aplicativo está ocupado no início e fim do dia de trabalho meus funcionários, entre 7:00 - 9:00 e 4: - 6 das 12:00:00. Durante as outras 20 horas do dia, meu aplicativo é, normalmente, em inatividade, usando apenas 10% da vCPU. Para as horas de pico, eu Ganhe créditos 0,2 por minuto, mas só consumir 0.l créditos por minuto, para que a minha VM será bancária 0,1 x 60 = 6 créditos por hora.  Para 20 horas que estou fora de pico, eu será bancária 120 créditos.  

Durante o horário de pico meu aplicativo calcula a média 60% de utilização de vCPU, eu ainda Ganhe créditos 0,2 por minuto, mas eu consumir 0,6 créditos por minuto, um custo líquido de 0.4 créditos um minuto ou 0.4 x 60 = 24 créditos, por hora. Tenho 4 horas por dia de pico do uso de, pelo que custa 24 x 4 = 96 créditos quanto ao meu uso de pico.

Se eu tirar os 120 créditos que obter fora de pico e subtrair os 96 créditos que usei para meu as horas de ponta, eu bancária um créditos adicionais 24 por dia em que posso usar para outros absorver picos de atividade.


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>P. A série B suporta discos de dados do armazenamento Premium?
**A**: Sim, todos os tamanhos de série B suportam discos de dados do armazenamento Premium.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>P. Por que motivo é meu crédito restante definido como 0 após uma voltar a implementar ou iniciar/parar?
**A** : Quando uma VM é "REDPLOYED" e a VM passa para outro nó, o crédito acumulado é perdido. Se a VM é iniciada/interrompida, mas permanece no mesmo nó, a VM mantém o crédito acumulado. Sempre que a VM entrar atualizada num nó, obtém um crédito inicial, para Standard_B8ms é 240 minutos.
    
### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>P. O que acontece se eu implantar uma imagem do SO não suportada no B1ls?
**A** : B1ls só suporta imagens do Linux e se implementa qualquer outra imagem de sistema operacional não poderá obter a melhor experiência do cliente.
    
### <a name="q-why-is-there-no-pricing-information-for-b1ls-windows"></a>P. Por que motivo existe sem informações de preços para o B1ls windows?
**A** : B1ls só suporta imagens do Linux e se implementa qualquer outra imagem de SO não pode obter a melhor experiência do cliente, mas será cobrado.


    

    
