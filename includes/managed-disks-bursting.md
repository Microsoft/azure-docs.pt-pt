---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/29/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 84736b7f1dcdf8b186fddbced5dd773e008c0dd2
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887453"
---
O modo de rajada do disco é suportado para SSDs Premium. O modo de rajada é suportado para todos os tamanhos de disco SSD Premium <= 512 GiB (P20 ou inferior). Estes tamanhos de disco suportam o modo de rajada numa base do melhor esforço e utilizam um sistema de créditos para gerir as rajadas. Os créditos acumulam-se num registo de rajadas sempre que o tráfego do disco está abaixo do objetivo de desempenho aprovisionado para o tamanho do disco e consome créditos quando o tráfego excede o objetivo. O tráfego do disco é rastreado em relação a IOPS e largura de banda no objetivo aprovisionado. O modo de rajada do disco não ignorará as limitações de tamanho da máquina virtual (VM) no IOPS ou no débito.

O modo de rajada do disco é ativado por predefinição nas novas implementações dos tamanhos de disco que o suportem. Os tamanhos de disco existentes, caso suportem o modo de rajada do disco, podem permitir a rajadas através de um dos seguintes métodos:

- Desanexar e anexar o disco.
- Parar e iniciar a VM.

## <a name="burst-states"></a>Estados das rajadas

Todos os tamanhos de disco onde é possível aplicar as rajadas começarão com um registo de créditos de rajadas completo quando o disco é anexado a uma Máquina Virtual. A duração máxima da rajada é determinada pelo tamanho do registo de crédito de rajadas. Só pode acumular créditos não utilizados até ao tamanho do registo de crédito. A qualquer momento, o registo de créditos de rajadas do disco pode estar num dos seguintes três estados: 

- A acumular, quando o tráfego do disco está a utilizar menos do que o objetivo de desempenho aprovisionado. Poderá acumular crédito se o tráfego do disco ultrapassar o objetivo da largura de banda, do IOPS ou de ambos. Ainda pode acumular créditos de E/S quando está a consumir a totalidade da largura de banda do disco e vice-versa.  

- A recusar, quando o tráfego do disco está a utilizar mais do que o objetivo de desempenho aprovisionado. O tráfego de rajada consumirá créditos, de forma independente, da largura de banda ou do IOPS. 

- Permanecer constante, quando o tráfego do disco é igual ao objetivo de desempenho aprovisionado. 

Os tamanhos do disco que proporcionam suporte de rajada, juntamente com as especificações de rajada, são resumidos na tabela abaixo.

## <a name="regional-availability"></a>Disponibilidade regional

O modo de rajada do disco está disponível em todas as regiões na Cloud Pública.

## <a name="disk-sizes"></a>Tamanhos de disco

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Cenários de exemplo

Para lhe dar uma ideia melhor de como isto funciona, apresentamos alguns cenários de exemplo:

- Um cenário comum que pode beneficiar do modo de rajada do disco é um arranque mais rápido da VM e o lançamento da aplicação nos discos do SO. Utilize uma VM do Linux com uma imagem de SO de 8 Gib como exemplo. Se utilizarmos um disco P2 como disco OS, o alvo previsto é 120 IOPS e 25 MiB. Quando a VM for iniciada, ocorrerá um pico de leitura para o disco do SO que está a carregar os ficheiros de arranque. Com a introdução do rebentamento, pode ler-se à velocidade máxima de explosão de 3500 IOPS e 170 MiB, acelerando o tempo de carga em pelo menos 6x. Após o arranque da VM, o nível de tráfego no disco do SO é normalmente baixo, dado que a maioria das operações de dados executadas pela aplicação serão nos discos de dados anexados. Se o tráfego estiver abaixo do objetivo aprovisionado, acumulará créditos.

- Se estiver a alojar um ambiente do Virtual Desktop Remoto, sempre que um utilizador ativo iniciar uma aplicação, como o AutoCAD, o tráfego de leitura no disco do SO aumentará significativamente. Neste caso, o tráfego de rajada consumirá os créditos acumulados, o que lhe permite ultrapassar o objetivo aprovisionado e iniciar a aplicação muito mais rapidamente.

- Um disco P1 tem um alvo provisionável de 120 IOPS e 25 MiB. Se o tráfego real no disco foi de 100 IOPS e 20 MiB no intervalo de 1 segundo, então os 20 IOs não utilizados e 5 MB são creditados no balde de rutura do disco. Os créditos no registo de rajada podem ser utilizados posteriormente quando o tráfego ultrapassar o objetivo aprovisionado, até ao limite máximo de rajada. O limite máximo de rajada define o teto do tráfego do disco mesmo que tenha créditos de rajada por consumir. Neste caso, mesmo que tenha 10 000 E/S no registo de crédito, um disco P1 não poderá emitir mais do que a rajada máxima de 3500 E/S por segundo.  
