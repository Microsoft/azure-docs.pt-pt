---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a355307eef9f5ce1f833cfd7924f5efa234a0cd7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523568"
---
## <a name="premium-ssd"></a>SSD Premium

O SSDs Premium do Azure fornece suporte de disco de alto desempenho e baixa latência para VMs (máquinas virtuais) com cargas de trabalho com uso intensivo de e/s (entrada/saída). Para aproveitar a velocidade e o desempenho dos discos de armazenamento Premium, você pode migrar os discos de VM existentes para o SSDs Premium. O SSDs Premium é adequado para aplicativos de produção de missão crítica. O SSDs Premium só pode ser usado com a série de VMs que são compatíveis com o armazenamento Premium.

Para saber mais sobre os tipos e tamanhos de VM individuais no Azure para Windows, incluindo quais tamanhos são compatíveis com o armazenamento Premium, consulte [tamanhos de VM do Windows](../articles/virtual-machines/windows/sizes.md). Para saber mais sobre os tipos e tamanhos de VM individuais no Azure para Linux, incluindo quais tamanhos são compatíveis com o armazenamento Premium, consulte [tamanhos de VM do Linux](../articles/virtual-machines/linux/sizes.md).

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Ao provisionar um disco de armazenamento Premium, ao contrário do armazenamento Standard, você garante a capacidade, o IOPS e a taxa de transferência desse disco. Por exemplo, se você criar um disco P50, o Azure provisionará a capacidade de armazenamento de 4.095 GB, 7.500 IOPS e taxa de transferência de 250 MB/s para esse disco. Seu aplicativo pode usar toda ou parte da capacidade e do desempenho. SSD Premium discos são projetados para fornecer latências de milissegundos de um dígito baixo e IOPS de destino e taxa de transferência descritas na tabela anterior 99,9% do tempo.

## <a name="bursting-preview"></a>Intermitência (visualização)

SSD Premium tamanhos menores que p30 agora oferecem intermitência de disco (versão prévia) e podem aumentar seu IOPS por disco até 3.500 e sua largura de banda de até 170 Mbps. A intermitência é automatizada e opera com base em um sistema de crédito. Os créditos são acumulados automaticamente em um Bucket de intermitência quando o tráfego de disco está abaixo do destino de desempenho provisionado e os créditos são consumidos automaticamente quando o tráfego ultrapassa o destino, até o limite de intermitência máximo. O limite máximo de intermitência define o teto de IOPS de disco & largura de banda, mesmo se você tiver créditos de intermitência a serem consumidos. A intermitência de disco fornece melhor tolerância a alterações imprevisíveis de padrões de e/s. Você pode aproveitá-lo melhor para inicialização de disco do so e aplicativos com tráfego com picos.    

O suporte a intermitência de discos será habilitado em novas implantações de tamanhos de disco aplicáveis nas [regiões de visualização](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) por padrão, sem a necessidade de ação do usuário. Para discos existentes dos tamanhos aplicáveis, você pode habilitar a intermitência com uma das duas opções: desanexar e anexar novamente o disco ou parar e reiniciar a VM conectada. Todos os tamanhos de disco aplicáveis de intermitência começarão com um Bucket de crédito de intermitência completa quando o disco for anexado a uma máquina virtual que dá suporte a uma duração máxima no limite de pico de intermitência de 30 minutos. Para saber mais sobre como a intermitência funciona nos discos do Azure, confira [SSD Premium intermitênciaing](../articles/virtual-machines/linux/disk-bursting.md). 

### <a name="transactions"></a>Transações

Para o SSDs Premium, cada operação de e/s menor ou igual a 256 KiB de taxa de transferência é considerada uma única operação de e/s. Operações de e/s maiores que 256 KiB de taxa de transferência são consideradas várias e/SS de tamanho de 256 KiB.

## <a name="standard-ssd"></a>SSD Standard

O SSDs do Azure Standard é uma opção de armazenamento econômica, otimizada para cargas de trabalho que precisam de desempenho consistente em níveis de IOPS menores. A SSD Standard oferece uma boa experiência de nível de entrada para aqueles que desejam migrar para a nuvem, especialmente se você tiver problemas com a variação de cargas de trabalho em execução em suas soluções de HDD no local. Em comparação com HDDs padrão, o SSDs padrão oferece melhor disponibilidade, consistência, confiabilidade e latência. O SSDs padrão é adequado para servidores Web, servidores de aplicativos de IOPS baixa, aplicativos empresariais levemente usados e cargas de trabalho de desenvolvimento/teste. Como HDDs padrão, o SSDs padrão está disponível em todas as VMs do Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

O SSDs padrão é projetado para fornecer latências de milissegundos de dígito único e a IOPS e a taxa de transferência até os limites descritos na tabela anterior 99% do tempo. O IOPS e a taxa de transferência reais podem variar às vezes, dependendo dos padrões de tráfego. O SSDs padrão fornecerá um desempenho mais consistente do que os discos de HDD com menor latência.

### <a name="transactions"></a>Transações

Para SSDs padrão, cada operação de e/s menor ou igual a 256 KiB de taxa de transferência é considerada uma única operação de e/s. Operações de e/s maiores que 256 KiB de taxa de transferência são consideradas várias e/SS de tamanho de 256 KiB. Essas transações têm um impacto de cobrança.

## <a name="standard-hdd"></a>HDD Standard

HDDs do Azure Standard fornecem suporte de disco confiável e de baixo custo para VMs que executam cargas de trabalho que não fazem distinção de latência. Com o armazenamento Standard, os dados são armazenados em HDDs (unidades de disco rígido). A latência, o IOPS e a taxa de transferência de discos de HDD Standard podem variar mais amplamente em comparação com discos baseados em SSD. HDD Standard discos são projetados para fornecer latências de gravação em 10 ms e latências de leitura em 20 ms para a maioria das operações de e/s, no entanto, o desempenho real pode variar dependendo do padrão de tamanho de e/s Ao trabalhar com VMs, você pode usar discos HDD padrão para cenários de desenvolvimento/teste e cargas de trabalho menos críticas. Os HDDs padrão estão disponíveis em todas as regiões do Azure e podem ser usados com todas as VMs do Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transações

Para HDDs padrão, cada operação de e/s é considerada como uma única transação, independentemente do tamanho de e/s. Essas transações têm um impacto de cobrança.

## <a name="billing"></a>Faturação

Ao usar o Managed disks, as seguintes considerações de cobrança se aplicam:

- Tipo de disco
- Tamanho do disco gerenciado
- Instantâneos
- Transferências de dados de saída
- Número de transações

**Tamanho do disco gerenciado**: os discos gerenciados são cobrados no tamanho provisionado. O Azure mapeia o tamanho provisionado (arredondado) para o tamanho de disco mais próximo oferecido. Para obter detalhes sobre os tamanhos de disco oferecidos, consulte as tabelas anteriores. Cada disco é mapeado para uma oferta de tamanho de disco provisionado com suporte e é cobrado de acordo. Por exemplo, se você provisionou um SSD Standard GiB de 200, ele é mapeado para a oferta de tamanho de disco de E15 (256 GiB). A cobrança por qualquer disco provisionado é rateada por hora usando o preço mensal da oferta de armazenamento Premium. Por exemplo, se você provisionou um disco E10 e o excluiu após 20 horas, você será cobrado pela oferta de E10 rateada para 20 horas. Isso é independente da quantidade de dados reais gravados no disco.

**Instantâneos**: os instantâneos são cobrados com base no tamanho usado. Por exemplo, se você criar um instantâneo de um disco gerenciado com capacidade provisionada de 64 GiB e tamanho real de dados usados de 10 GiB, o instantâneo será cobrado somente pelo tamanho dos dados usados de 10 GiB.
