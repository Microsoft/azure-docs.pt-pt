---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c2ce39c40ad535b4a66349fcbb4529333218555a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512668"
---
O SSDs Premium dá suporte à intermitência em qualquer tamanho de disco < = 512 GiB (P20 ou abaixo). Esses tamanhos de disco dão suporte à intermitência em uma base de melhor esforço e utilizam um sistema de crédito para gerenciar a intermitência. Os créditos se acumulam em um Bucket de intermitência sempre que o tráfego de disco está abaixo do destino de desempenho provisionado para seu tamanho de disco e consome créditos quando o tráfego ultrapassa o destino. O tráfego de disco é rastreado em IOPS e largura de banda no destino provisionado.

A intermitência de disco é habilitada por padrão em novas implantações dos tamanhos de disco que dão suporte a ela. Os tamanhos de disco existentes, se oferecerem suporte a intermitência de disco, podem habilitar a intermitência por meio de qualquer um dos seguintes métodos:

- Desanexe e anexe novamente o disco.
- Pare e inicie a VM.

## <a name="burst-states"></a>Estados de intermitência

Todos os tamanhos de disco aplicáveis de intermitência começarão com um Bucket de crédito de intermitência completa quando o disco for anexado a uma máquina virtual. A duração máxima da intermitência é determinada pelo tamanho do Bucket de crédito de intermitência. Você só pode acumular créditos não utilizados até o tamanho do Bucket de crédito. A qualquer momento, o Bucket de crédito de intermitência de disco pode estar em um dos três seguintes Estados: 

- A acumulação, quando o tráfego de disco está usando menos do que o destino de desempenho provisionado. Você pode acumular crédito se o tráfego de disco estiver além de destinos de largura de banda ou IOPS ou ambos. Você ainda pode acumular créditos de e/s quando estiver consumindo largura de banda completa do disco, vice-versa.  

- Recusando, quando o tráfego de disco estiver usando mais do que o destino de desempenho provisionado. O tráfego de intermitência consumirá de forma independente os créditos de IOPS ou largura de banda. 

- Constante restante, quando o tráfego de disco está exatamente no destino de desempenho provisionado. 

Os tamanhos de disco que fornecem suporte de intermitência juntamente com as especificações de intermitência são resumidos na tabela a seguir.

## <a name="regional-availability"></a>Disponibilidade regional

Atualmente, a intermitência de disco está disponível apenas na região do EUA Central ocidental.

## <a name="disk-sizes"></a>Tamanhos de disco

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Cenários de exemplo

Para dar uma ideia melhor de como isso funciona, veja alguns cenários de exemplo:

- Um cenário comum que pode se beneficiar da intermitência de disco é a inicialização de VM mais rápida e o início do aplicativo em discos do sistema operacional. Pegue uma VM do Linux com uma imagem do sistema operacional 8 GiB como exemplo. Se usarmos um disco P2 como o disco do sistema operacional, o destino provisionado será de 120 IOPS e 25 MBps. Quando a VM for iniciada, haverá um pico de leitura para o disco do sistema operacional que carrega os arquivos de inicialização. Com a introdução da intermitência, você pode ler a velocidade máxima de intermitência de 3500 IOPS e 170 MBps, acelerando o tempo de carregamento por pelo menos 6 vezes. Após a inicialização da VM, o nível de tráfego no disco do sistema operacional geralmente é baixo, pois a maioria das operações de dados pelo aplicativo será feita nos discos de dados anexados. Se o tráfego estiver abaixo do destino provisionado, você acumulará créditos.

- Se você estiver hospedando um ambiente de área de trabalho virtual remota, sempre que um usuário ativo iniciar um aplicativo como o AutoCAD, a leitura do tráfego para o disco do sistema operacional aumentará significativamente. Nesse caso, o tráfego de intermitência consumirá Créditos acumulados, permitindo que você vá além do destino provisionado e inicie o aplicativo muito mais rapidamente.

- Um disco P1 tem um destino provisionado de 120 IOPS e 25 MBps. Se o tráfego real no disco tiver 100 IOPS e 20 MBps no intervalo de 1 segundo passado, os 20 IOs não utilizados e 5 MB serão creditados no Bucket de intermitência do disco. Os créditos no Bucket de intermitência podem ser usados posteriormente quando o tráfego excede o destino provisionado, até o limite máximo de intermitência. O limite máximo de intermitência define o teto do tráfego de disco, mesmo se você tiver créditos de intermitência a serem consumidos. Nesse caso, mesmo se você tiver 10.000 IOs no Bucket de crédito, um disco P1 não poderá emitir mais do que a intermitência máxima de 3.500 e/s por segundo.  
