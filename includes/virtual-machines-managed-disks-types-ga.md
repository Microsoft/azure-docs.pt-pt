---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6740ea320f2d950386da12eb44726e2c826b60a4
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80386131"
---
## <a name="premium-ssd"></a>SSD Premium

Os SSDs premium Azure fornecem suporte de disco de alto desempenho e baixa latência para máquinas virtuais (VMs) com cargas de trabalho intensivas de entrada/saída (IO). Para aproveitar a velocidade e desempenho dos discos de armazenamento premium, pode migrar os discos VM existentes para SSDs Premium. Os SSDs Premium são adequados para aplicações de produção críticas de missão. Os SSDs Premium só podem ser utilizados com séries VM compatíveis com armazenamento premium.

Para saber mais sobre tipos e tamanhos vm individuais em Azure para Windows, incluindo quais os tamanhos compatíveis com armazenamento premium, consulte os [tamanhos do Windows VM](../articles/virtual-machines/windows/sizes.md). Para saber mais sobre tipos e tamanhos vm individuais em Azure para Linux, incluindo quais tamanhos são compatíveis com armazenamento premium, consulte [tamanhos De VM Linux](../articles/virtual-machines/linux/sizes.md). A partir de qualquer um desses artigos, você precisa verificar cada artigo de tamanho VM individual para determinar se é compatível com armazenamento premium.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Ao fornecer um disco de armazenamento premium, ao contrário do armazenamento padrão, é-lhe garantida a capacidade, iopS e a entrada desse disco. Por exemplo, se criar um disco P50, o Azure disponibiliza capacidade de armazenamento de 4.095-GB, 7.500 IOPS e 250-MB/s para esse disco. A sua aplicação pode utilizar a toda ou parte da capacidade e desempenho. Os discos SSD Premium são projetados para fornecer baixas latenciências de milissegundos de um dígito e iOPS alvo e entrada descrita na tabela anterior 99,9% das vezes.

## <a name="bursting"></a>Rebentando

Os tamanhos Premium SSD inferiores ao P30 oferecem agora a rutura do disco e podem rebentar o seu IOPS por disco até 3.500 e a sua largura de banda até 170 Mbps. A explosão é automatizada e funciona com base num sistema de crédito. Os créditos são automaticamente acumulados num balde de rutura quando o tráfego do disco está abaixo do objetivo de desempenho previsto e os créditos são automaticamente consumidos quando o tráfego explode para além do alvo, até ao limite máximo de disparo. O limite máximo de rutura define o teto do disco IOPS & largura de banda mesmo que tenha créditos de rutura para consumir. A rutura do disco proporciona uma melhor tolerância em mudanças imprevisíveis dos padrões io. Você pode melhor alavancar para o bota de disco OS e aplicações com tráfego espetado.    

O suporte de rutura dos discos será ativado em novas implementações de tamanhos de disco aplicáveis por padrão, sem necessidade de ação do utilizador. Para os discos existentes dos tamanhos aplicáveis, pode ativar a explosão com uma das duas opções: desmontar e voltar a ligar o disco ou parar e reiniciar o VM anexo. Todos os tamanhos de disco aplicáveis começarão com um balde de crédito completo quando o disco estiver ligado a uma Máquina Virtual que suporta uma duração máxima no limite máximo de explosão de 30 minutos. Para saber mais sobre como funcionam os Discos Azure, veja [o SSD Premium a rebentar.](../articles/virtual-machines/linux/disk-bursting.md) 

### <a name="transactions"></a>Transações

Para SSDs premium, cada operação de I/S inferior ou igual a 256 KiB de entrada é considerada uma única operação de I/S. As operações de I/O superiores a 256 KiB de entrada são consideradas múltiplas I/Os de tamanho 256 KiB.

## <a name="standard-ssd"></a>SSD Standard

Os SSDs padrão Azure são uma opção de armazenamento rentável otimizada para cargas de trabalho que precisam de um desempenho consistente em níveis de IOPS mais baixos. O Standard SSD oferece uma boa experiência de nível de entrada para quem deseja mover-se para a nuvem, especialmente se tiver problemas com a variação de cargas de trabalho em funcionamento nas suas soluções HDD nas instalações. Em comparação com os HDDs padrão, os SSDs padrão proporcionam uma melhor disponibilidade, consistência, fiabilidade e latência. Os SSDs standard são adequados para servidores Web, servidores de aplicações IOPS baixos, aplicações empresariais levemente utilizadas e cargas de trabalho Dev/Test. Tal como os HDDs padrão, os SSDs padrão estão disponíveis em todos os VMs Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Os SSDs standard são projetados para fornecer latenciências de milissegundode de um dígito e o IOPS e a sua entrada até aos limites descritos na tabela anterior 99% do tempo. Os IOPS reais e a produção podem variar, por vezes, dependendo dos padrões de tráfego. Os SSDs padrão fornecerão um desempenho mais consistente do que os discos HDD com a latência mais baixa.

### <a name="transactions"></a>Transações

Para os SSDs padrão, cada operação de I/S inferior ou igual a 256 KiB de entrada é considerada uma única operação de I/S. As operações de I/O superiores a 256 KiB de entrada são consideradas múltiplas I/Os de tamanho 256 KiB. Estas transações têm um impacto na faturação.

## <a name="standard-hdd"></a>HDD Standard

Os HDDs padrão Azure oferecem suporte fiável e de baixo custo para os VMs que executam cargas de trabalho insensíveis à latência. Com armazenamento padrão, os dados são armazenados em discos rígidos (HDDs). A latência, o IOPS e a entrada de discos HDD standard podem variar mais em comparação com os discos baseados em SSD. Os Discos HDD standard são projetados para entregar latenciências escritas abaixo de 10 ms e ler latenciências abaixo de 20ms para a maioria das operações io, no entanto o desempenho real pode variar dependendo do tamanho da IO e do padrão de carga de trabalho. Ao trabalhar com VMs, pode utilizar discos HDD padrão para cenários de dev/teste e cargas de trabalho menos críticas. HDDs standard estão disponíveis em todas as regiões do Azure e podem ser usados com todos os VMs Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transações

Para os HDDs Standard, cada operação IO é considerada como uma única transação, independentemente do tamanho de I/O. Estas transações têm um impacto na faturação.

## <a name="billing"></a>Faturação

Ao utilizar discos geridos, aplicam-se as seguintes considerações de faturação:

- Tipo de disco
- tamanho do disco gerido
- Instantâneos
- Transferências de dados de saída
- Número de transações

**Tamanho do disco gerido**: os discos geridos são faturados no tamanho provisionado. O Azure mapeia o tamanho provisionado (arredondado) para o tamanho do disco oferecido mais próximo. Para obter detalhes sobre os tamanhos do disco oferecidos, consulte as tabelas anteriores. Cada disco mapeia uma oferta de tamanho de disco suportado e é faturado em conformidade. Por exemplo, se forprovisionado um SSD Standard 200 GiB, ele mapeia a oferta de tamanho do disco de E15 (256 GiB). A faturação de qualquer disco provisionado é avaliada de hora em hora utilizando o preço mensal da oferta de Armazenamento Premium. Por exemplo, se forres um disco E10 e o apagares após 20 horas, és cobrado para a oferta E10, com prorated a 20 horas. Isto é independentemente da quantidade de dados reais escritos no disco.

**Snapshots**: As imagens são faturadas com base no tamanho utilizado. Por exemplo, se criar uma imagem instantânea de um disco gerido com capacidade de 64 GiB e tamanho real de dados utilizados de 10 GiB, o instantâneo é faturado apenas para o tamanho de dados utilizado saturado de 10 GiB.
