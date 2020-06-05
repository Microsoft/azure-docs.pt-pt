---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 90cd1d8968963d428eb2d1de4efb458b5f89ca74
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84436172"
---
## <a name="premium-ssd"></a>SSD Premium

Os SSDs premium Azure oferecem suporte de disco de alto desempenho e baixa latência para máquinas virtuais (VMs) com cargas de trabalho intensivas de entrada/saída (IO). Para aproveitar a velocidade e desempenho dos discos de armazenamento premium, pode migrar discos VM existentes para SSDs Premium. Os SSDs premium são adequados para aplicações de produção críticas da missão. Os SSDs Premium só podem ser utilizados com séries VM compatíveis com armazenamento premium.

Para saber mais sobre os tipos e tamanhos VM individuais em Azure para Windows, incluindo quais os tamanhos compatíveis com armazenamento premium, consulte [os tamanhos do Windows VM](../articles/virtual-machines/windows/sizes.md). Para saber mais sobre os tipos e tamanhos VM individuais em Azure para o Linux, incluindo quais os tamanhos compatíveis com armazenamento premium, consulte [os tamanhos Linux VM](../articles/virtual-machines/linux/sizes.md). A partir de qualquer um desses artigos, você precisa verificar cada artigo de tamanho VM individual para determinar se é compatível com armazenamento premium.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Ao providenciar um disco de armazenamento premium, ao contrário do armazenamento padrão, é-lhe garantida a capacidade, iOPS e a produção desse disco. Por exemplo, se criar um disco P50, o Azure prevê capacidade de armazenamento de 4.095 GB, 7.500 IOPS e 250-MB/s para o disco. A sua aplicação pode utilizar a maior ou parte da capacidade e desempenho. Os discos Premium SSD são projetados para fornecer latências de um dígito baixo e IOPS alvo e produção descrita na tabela anterior 99,9% das vezes.

## <a name="bursting"></a>Estourando

Os tamanhos Premium SSD inferiores ao P30 oferecem agora o rebentamento do disco e podem rebentar o seu IOPS por disco até 3.500 e a sua largura de banda até 170 Mbps. A explosão é automatizada e funciona com base num sistema de crédito. Os créditos são automaticamente acumulados num balde de rutura quando o tráfego de disco está abaixo do objetivo de desempenho previsto e os créditos são automaticamente consumidos quando o tráfego rebenta para além do objetivo, até ao limite máximo de rajada. O limite máximo de rutura define o teto do disco IOPS & largura de banda mesmo que tenha créditos de rutura para consumir. A explosão do disco proporciona uma melhor tolerância às mudanças imprevisíveis dos padrões de IO. Pode aproveitar melhor para o arranque do disco OS e aplicações com tráfego espinhoso.    

O suporte de rebentamento de discos será ativado em novas implementações de tamanhos de disco aplicáveis por padrão, sem necessidade de ação do utilizador. Para os discos existentes dos tamanhos aplicáveis, pode ativar a explosão com qualquer uma das opções: desprender e recolocar o disco ou parar e reiniciar o VM anexado. Todos os tamanhos de disco originais rebentados começarão com um balde de crédito de rajada completa quando o disco é ligado a uma Máquina Virtual que suporta uma duração máxima no limite máximo de rajada de 30 minutos. Para saber mais sobre como estourar o trabalho em Discos Azure, veja [o Premium SSD a rebentar.](../articles/virtual-machines/linux/disk-bursting.md) 

### <a name="transactions"></a>Transações

No caso dos SSDs premium, cada operação de E/S inferior ou igual a 256 KiB de produção é considerada uma operação única de E/O. As operações de I/O maiores do que 256 KiB de produção são consideradas múltiplas I/Os do tamanho 256 KiB.

## <a name="standard-ssd"></a>SSD Standard

Os SSDs standard Azure são uma opção de armazenamento rentável otimizada para cargas de trabalho que precisam de um desempenho consistente em níveis de IOPS mais baixos. O Standard SSD oferece uma boa experiência de nível de entrada para quem deseja mover-se para a nuvem, especialmente se sentir problemas com a variação de cargas de trabalho em execução nas suas soluções HDD nas instalações. Em comparação com os HDDs padrão, os SSDs padrão proporcionam uma melhor disponibilidade, consistência, fiabilidade e latência. Os SSDs standard são adequados para servidores Web, servidores de aplicações IOPS baixos, aplicações empresariais levemente utilizadas e cargas de trabalho de Dev/Test. Tal como os HDDs standard, os SSDs standard estão disponíveis em todos os VMs Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Os SSDs standard são concebidos para fornecer latências milisegundos de um dígito e o IOPS e produção até aos limites descritos na tabela anterior 99% das vezes. O IOPS real e a produção podem variar, por vezes, dependendo dos padrões de tráfego. Os SSDs standard proporcionarão um desempenho mais consistente do que os discos HDD com a latência mais baixa.

### <a name="transactions"></a>Transações

Para os SSDs standard, cada operação de E/S inferior ou igual a 256 KiB de produção é considerada uma única operação de E/S. As operações de I/O maiores do que 256 KiB de produção são consideradas múltiplas I/Os do tamanho 256 KiB. Estas transações têm um impacto na faturação.

## <a name="standard-hdd"></a>HDD Standard

Os HDDs standard Azure oferecem suporte fiável e de baixo custo para VMs que executam cargas de trabalho insensíveis à latência. Com o armazenamento padrão, os dados são armazenados em discos rígidos (HDDs). A latência, o IOPS e o Rendimento dos discos HDD standard podem variar mais em comparação com os discos baseados em SSD. Os Discos HDD padrão são projetados para fornecer latências de escrita abaixo de 10ms e ler latências abaixo de 20ms para a maioria das operações de IO, no entanto o desempenho real pode variar dependendo do tamanho e padrão de carga de trabalho de IO. Ao trabalhar com VMs, pode utilizar discos HDD padrão para cenários dev/teste e cargas de trabalho menos críticas. Os HDDs standard estão disponíveis em todas as regiões do Azure e podem ser usados com todos os VMs Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transações

Para os HDDs standard, cada operação IO é considerada como uma única transação, independentemente do tamanho de E/S. Estas transações têm um impacto na faturação.

## <a name="billing"></a>Faturação

Ao utilizar discos geridos, aplicam-se as seguintes considerações de faturação:

- Tipo de disco
- tamanho do disco gerido
- Instantâneos
- Transferências de dados de saída
- Número de transações

**Tamanho do disco gerido**: os discos geridos são faturados no tamanho previsto. Azure mapeia o tamanho provisionado (arredondado) para o tamanho do disco mais próximo oferecido. Para mais detalhes sobre os tamanhos do disco oferecidos, consulte as tabelas anteriores. Cada disco mapeia para uma oferta de tamanho de disco a provisionada suportada e é faturado em conformidade. Por exemplo, se a provisionou um SSD Standard 200 GiB, ele mapeia a oferta de tamanho do disco de E15 (256 GiB). A faturação de qualquer disco provisionado é prostimada de hora em hora, utilizando o preço mensal da oferta de armazenamento. Por exemplo, se aprovisionou um disco E10 e o apagou após 20 horas, é cobrado pela oferta E10, proserendo 20 horas. Isto é independentemente da quantidade de dados reais escritos no disco.

**Snapshots**: As imagens são faturadas com base no tamanho utilizado. Por exemplo, se criar uma imagem instantânea de um disco gerido com capacidade a provisionada de 64 GiB e tamanho real de dados usados de 10 GiB, o instantâneo é faturado apenas para o tamanho de dados usado de 10 GiB.
