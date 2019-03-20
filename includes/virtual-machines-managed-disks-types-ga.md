---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0201776914610ddaca50a670fc500156a65cd734
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2019
ms.locfileid: "58124687"
---
## <a name="premium-ssd"></a>SSD Premium

Premium do Azure SSDs fornecer suporte de disco de elevado desempenho e baixa latência para máquinas virtuais (VMs) com entrada/saída (e/s)-cargas de trabalho intensivas. Para tirar partido da velocidade e o desempenho de discos de armazenamento premium, pode migrar os discos de VM existentes para o Premium SSDs. Premium SSDs são adequadas para aplicações de produção de missão crítica.

### <a name="disk-size"></a>Tamanho do disco

Tamanhos marcados com asterisco estão atualmente em pré-visualização.

| Tamanhos SSD Premium | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60* | P70* | P80* |
|-------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Tamanho do disco em GiB | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8,192 | 16,384 | 32,767 |
| IOPs por disco | Até 120 | Até 240 | Até 500 | Até 1,100 | Até 2,300 | Até 5000 | Até 7.500 | Até 7.500 | Até 12.500 | Máximo de 15 000 | Até 20 000 |
| Débito por disco | Até 25 MiB/seg | Máximo de 50 MiB/seg | Até 100 MiB/seg | Até 125 MiB/seg | Até 150 MiB/seg | Até 200 MiB/seg | Até 250 MiB/seg | Até 250 MiB/seg| Até 480 MiB/seg | Até 750 MiB/seg | Até 750 MiB/seg |

## <a name="standard-ssd"></a>SSD Standard

SSDs padrão do Azure são uma opção de armazenamento económico, otimizada para cargas de trabalho que necessitam de um desempenho consistente em níveis inferiores de IOPS. Standard SSD oferece uma experiência de bom nível de entrada para aqueles que pretende mover para a cloud, especialmente se ocorrerem problemas com a variância de cargas de trabalho em execução em suas soluções HDD no local. Os SSDs Standard proporcionam uma melhor disponibilidade, consistência, fiabilidade e latência em comparação comparada discos HDD. SSDs padrão são adequadas para servidores Web, servidores de aplicações de IOPS reduzidos, aplicações empresariais pouco usada e cargas de trabalho de programador/teste.

### <a name="disk-size"></a>Tamanho do disco

Tamanhos marcados com asterisco estão atualmente em pré-visualização.

| Tamanhos SSD Standard | E4 | E6 | E10 | E15 | E20 | E30 | E40 | E50 | E60* | E70* | E80* |
|--------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Tamanho do disco em GiB | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8,192 | 16,384 | 32,767 |
| IOPs por disco | Até 120 | Até 240 | Até 500 | Até 500 | Até 500 | Até 500 | Até 500 | Até 500 | Até 1.300 | Até 2000 | Até 2000 |
| Débito por disco |  Até 25 MiB/seg |  Máximo de 50 MiB/seg  |  Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg| Até 300 MiB/seg |  Até 500 MiB/seg | Até 500 MiB/seg |

## <a name="standard-hdd"></a>HDD Standard

HDDs de padrão do Azure fornecem suporte de disco fiável e económica para as VMs executar cargas de trabalho insensível à latência. Também suporta blobs, tabelas, filas e ficheiros. Com o armazenamento standard, os dados são armazenados em unidades de disco rígido (HDDs). Ao trabalhar com VMs, pode utilizar os discos standard SSD e HDD para cenários de desenvolvimento/teste e menos cargas de trabalho críticas. Armazenamento Standard está disponível em todas as regiões do Azure.

### <a name="disk-size"></a>Tamanho do disco

Tamanhos marcados com asterisco estão atualmente em pré-visualização.

| Tipo de Disco Standard | S4 | S6 | S10 | S15 | S20 | S30 | S40 | S50 | S60* | S70* | S80* |
|--------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Tamanho do disco em GiB | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8,192 | 16,384 | 32,767 |
| IOPs por disco | Até 500 | Até 500 | Até 500 | Até 500 | Até 500 | Até 500 | Até 500 | Até 500 | Até 1.300 | Até 2000 | Até 2000 |
| Débito por disco | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg| Até 300 MiB/seg | Até 500 MiB/seg | Até 500 MiB/seg |

## <a name="billing"></a>Faturação

Ao utilizar discos geridos, aplicam-se as seguintes considerações de faturas:

- Tipo de disco
- Tamanho de disco gerido
- Instantâneos
- Transferências de dados de saída
- Número de transações

**Geridos pelo tamanho do disco**: os discos geridos são cobrados no tamanho aprovisionado. Azure mapeia o tamanho aprovisionado (arredondado) para o tamanho de disco oferecidos mais próximo. Para obter detalhes sobre os tamanhos de disco oferecidos, consulte as tabelas anteriores. Cada disco mapeia para uma oferta de tamanho de disco de aprovisionamento suportados e é cobrado em conformidade. Por exemplo, se tiver aprovisionado um 200 GiB SSD padrão, ele mapeia para a oferta de tamanho de disco de E15 (256gib). Para qualquer disco aprovisionado a faturação é contabilizada à hora, utilizando o preço mensal para a oferta de armazenamento Premium. Por exemplo, se aprovisionar um disco de E10 e eliminado depois de 20 horas, é cobrado para a oferta de E10 Rateado para 20 horas. Isto é, independentemente da quantidade de dados reais, escritos no disco.

**Instantâneos**: Os instantâneos são faturados com base no tamanho utilizado. Por exemplo, se criar um instantâneo de um disco gerido com capacidade aprovisionada de 64 GiB e o tamanho de dados utilizados real de 10 GiB, o instantâneo é cobrado apenas para o tamanho de dados utilizados de 10 GiB.