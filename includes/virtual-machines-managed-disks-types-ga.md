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
ms.openlocfilehash: 30df480eb314594cbc4d949302aff11e6d764b6f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65815580"
---
## <a name="premium-ssd"></a>Premium SSD

Premium do Azure SSDs fornecer suporte de disco de elevado desempenho e baixa latência para máquinas virtuais (VMs) com entrada/saída (e/s)-cargas de trabalho intensivas. Para tirar partido da velocidade e o desempenho de discos de armazenamento premium, pode migrar os discos de VM existentes para o Premium SSDs. Premium SSDs são adequadas para aplicações de produção de missão crítica. SSD Premium só pode ser utilizado com a série VM que estão premium compatível com o armazenamento.

Para saber mais sobre os tipos VM individuais e os tamanhos no Azure para Windows, incluindo a que os tamanhos estão premium compatível com o armazenamento, consulte [tamanhos de VM do Windows](../articles/virtual-machines/windows/sizes.md). Para saber mais sobre tipos de VM individuais e os tamanhos no Azure para Linux, incluindo a que os tamanhos estão premium compatível com o armazenamento, consulte [tamanhos de VM do Linux](../articles/virtual-machines/linux/sizes.md).

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Quando aprovisiona um disco de armazenamento premium, ao contrário do armazenamento standard, haverá a garantia de capacidade, IOPS e débito desse disco. Por exemplo, se criar um disco de P50, Azure Aprovisiona a capacidade de armazenamento de 4095 GB, 7.500 IOPS e débito de 250-MB/s desse disco. Seu aplicativo pode usar toda ou parte da capacidade e desempenho. Os discos SSD Premium foram concebidos para fornecer o destino desempenho 99,9% do tempo.

### <a name="transactions"></a>Transações

Para os premium SSDs, cada operação e/s inferior ou igual a 256 KiB de débito é considerada uma única operação de e/s. Operações de e/s superiores a 256 KiB de débito são consideradas várias e/SS de tamanho 256 KiB.

## <a name="standard-ssd"></a>SSD Standard

SSDs padrão do Azure são uma opção de armazenamento económico, otimizada para cargas de trabalho que necessitam de um desempenho consistente em níveis inferiores de IOPS. Standard SSD oferece uma experiência de bom nível de entrada para aqueles que pretende mover para a cloud, especialmente se ocorrerem problemas com a variância de cargas de trabalho em execução em suas soluções HDD no local. Em comparação com HDDs padrão, o standard SSDs proporcionam uma melhor disponibilidade, consistência, fiabilidade e latência. SSDs padrão são adequadas para servidores Web, servidores de aplicações de IOPS reduzidos, aplicações empresariais pouco usada e cargas de trabalho de programador/teste. Como HDDs padrão, o standard SSDs estão disponíveis em todas as VMs do Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

SSDs padrão foram concebidos para fornecer latências de milissegundo de dígito na maioria das operações de e/s e para disponibilizar o IOPS e débito até aos limites descrito na tabela anterior 99% do tempo. IOPS e o débito real podem variar, às vezes, consoante os padrões de tráfego. SSDs padrão irão proporcionar um desempenho mais consistente que os discos HDD com menor latência.

### <a name="transactions"></a>Transações

Para os SSDs padrão, cada operação e/s inferior ou igual a 256 KiB de débito é considerada uma única operação de e/s. Operações de e/s superiores a 256 KiB de débito são consideradas várias e/SS de tamanho 256 KiB. Essas transações tem um impacto de faturação.

## <a name="standard-hdd"></a>HDD Standard

HDDs de padrão do Azure fornecem suporte de disco fiável e económica para as VMs executar cargas de trabalho insensível à latência. Também suporta blobs, tabelas, filas e ficheiros. Com o armazenamento standard, os dados são armazenados em unidades de disco rígido (HDDs). Ao trabalhar com VMs, pode utilizar os discos standard SSD e HDD para cenários de desenvolvimento/teste e menos cargas de trabalho críticas. Standard HDDs estão disponíveis em todas as regiões do Azure e podem ser utilizados com todas as VMs do Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transações

Para HDDs padrão, cada operação de e/s é considerada como uma única transação, independentemente do tamanho de e/s. Essas transações tem um impacto de faturação.

## <a name="billing"></a>Faturação

Ao utilizar discos geridos, aplicam-se as seguintes considerações de faturas:

- Tipo de disco
- Tamanho de disco gerido
- Instantâneos
- Transferências de dados de saída
- Número de transações

**Geridos pelo tamanho do disco**: os discos geridos são cobrados no tamanho aprovisionado. Azure mapeia o tamanho aprovisionado (arredondado) para o tamanho de disco oferecidos mais próximo. Para obter detalhes sobre os tamanhos de disco oferecidos, consulte as tabelas anteriores. Cada disco mapeia para uma oferta de tamanho de disco de aprovisionamento suportados e é cobrado em conformidade. Por exemplo, se tiver aprovisionado um 200 GiB SSD padrão, ele mapeia para a oferta de tamanho de disco de E15 (256gib). Para qualquer disco aprovisionado a faturação é contabilizada à hora, utilizando o preço mensal para a oferta de armazenamento Premium. Por exemplo, se aprovisionar um disco de E10 e eliminado depois de 20 horas, é cobrado para a oferta de E10 Rateado para 20 horas. Isto é, independentemente da quantidade de dados reais, escritos no disco.

**Instantâneos**: Os instantâneos são faturados com base no tamanho utilizado. Por exemplo, se criar um instantâneo de um disco gerido com capacidade aprovisionada de 64 GiB e o tamanho de dados utilizados real de 10 GiB, o instantâneo é cobrado apenas para o tamanho de dados utilizados de 10 GiB.