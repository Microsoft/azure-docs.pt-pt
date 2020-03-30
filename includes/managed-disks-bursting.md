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
ms.openlocfilehash: 871a3edf70690a09d3747703e8bc999dfcce967c
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385185"
---
A rutura do disco é suportada para SSDs premium. A explosão é suportada em quaisquer tamanhos de disco SSD premium <= 512 GiB (P20 ou abaixo). Estes tamanhos de disco suportam rebentar com o melhor esforço e utilizam um sistema de crédito para gerir a explosão. Os créditos acumulam-se num balde de rutura sempre que o tráfego de discos está abaixo do objetivo de desempenho previsto para o seu tamanho de disco, e consomem créditos quando o tráfego explode para além do alvo. O tráfego do disco é rastreado contra iOPS e largura de banda no alvo provisionado. A rutura do disco não contornará as limitações de tamanho da máquina virtual (VM) no IOPS ou na entrada.

A rutura do disco é ativada por padrão em novas implementações dos tamanhos do disco que o suportam. Os tamanhos do disco existentes, se suportarem a rutura do disco, podem permitir a rutura de qualquer um dos seguintes métodos:

- Desaque e recoloque o disco.
- Pare e inicie o VM.

## <a name="burst-states"></a>Estados rebentados

Todos os tamanhos de disco aplicáveis começarão com um balde de crédito completo quando o disco estiver ligado a uma Máquina Virtual. A duração máxima da rajada é determinada pelo tamanho do balde de crédito rebentado. Só se pode acumular créditos não utilizados até ao tamanho do balde de crédito. Em qualquer momento, o balde de crédito rebentado do disco pode estar num dos três estados seguintes: 

- Acumulação, quando o tráfego do disco está a utilizar menos do que o objetivo de desempenho previsto. Pode acumular crédito se o tráfego do disco estiver para além dos objetivos iOPS ou da largura de banda ou ambos. Ainda pode acumular créditos io quando estiver a consumir largura de banda completa do disco, vice-versa.  

- Declinação, quando o tráfego do disco está a utilizar mais do que o objetivo de desempenho previsto. O tráfego de rutura consumirá créditos de iOPS ou largura de banda. 

- Permanecendo constante, quando o tráfego do disco está exatamente no alvo de desempenho previsto. 

Os tamanhos do disco que proporcionam suporte de rutura juntamente com as especificações de rutura são resumidos na tabela abaixo.

## <a name="regional-availability"></a>Disponibilidade regional

A explosão do disco está disponível em todas as regiões da Nuvem Pública.

## <a name="disk-sizes"></a>Tamanhos do disco

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Cenários de exemplo

Para lhe dar uma ideia melhor de como isto funciona, aqui estão alguns cenários de exemplo:

- Um cenário comum que pode beneficiar da explosão do disco é o arranque vm mais rápido e o lançamento da aplicação nos discos OS. Tome um VM Linux com uma imagem de 8 GiB OS como exemplo. Se utilizarmos um disco P2 como disco OS, o alvo previsto é 120 IOPS e 25 MBps. Quando a VM começar, haverá um pico de leitura para o disco OS a carregar os ficheiros de boot. Com a introdução do rebentamento, pode ler-se à velocidade máxima de explosão de 3500 IOPS e 170 MBps, acelerando o tempo de carga em pelo menos 6x. Após o arranque do VM, o nível de tráfego no disco OS é geralmente baixo, uma vez que a maioria das operações de dados por aplicação serão contra os discos de dados anexados. Se o tráfego estiver abaixo do objetivo previsto, irá acumular créditos.

- Se estiver a hospedar um ambiente de ambiente de trabalho virtual remoto, sempre que um utilizador ativo lança uma aplicação como o AutoCAD, leia o tráfego no disco OS aumenta significativamente. Neste caso, o tráfego rebentado consumirá créditos acumulados, permitindo-lhe ir além do alvo previsto, e lançar a aplicação muito mais rapidamente.

- Um disco P1 tem um alvo provisionado de 120 IOPS e 25 MBps. Se o tráfego real no disco foi de 100 IOPS e 20 MBps no intervalo de 1 segundo, então os 20 IOs não utilizados e 5 MB são creditados no balde de rutura do disco. Os créditos no balde de rutura podem ser usados mais tarde quando o tráfego excede o alvo previsto, até ao limite máximo de rutura. O limite máximo de rutura define o teto do tráfego do disco mesmo que tenha créditos de rutura para consumir. Neste caso, mesmo que tenha 10.000 IOs no balde de crédito, um disco P1 não pode emitir mais do que a rajada máxima de 3.500 IO por segundo.  
