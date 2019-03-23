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
ms.openlocfilehash: 88a4110d68dc8aa921d647f90de654d2ebb4e17d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395518"
---
## <a name="premium-ssd"></a>SSD Premium

Premium do Azure SSDs fornecer suporte de disco de elevado desempenho e baixa latência para máquinas virtuais (VMs) com entrada/saída (e/s)-cargas de trabalho intensivas. Para tirar partido da velocidade e o desempenho de discos de armazenamento premium, pode migrar os discos de VM existentes para o Premium SSDs. Premium SSDs são adequadas para aplicações de produção de missão crítica.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="standard-ssd"></a>SSD Standard

SSDs padrão do Azure são uma opção de armazenamento económico, otimizada para cargas de trabalho que necessitam de um desempenho consistente em níveis inferiores de IOPS. Standard SSD oferece uma experiência de bom nível de entrada para aqueles que pretende mover para a cloud, especialmente se ocorrerem problemas com a variância de cargas de trabalho em execução em suas soluções HDD no local. Os SSDs Standard proporcionam uma melhor disponibilidade, consistência, fiabilidade e latência em comparação comparada discos HDD. SSDs padrão são adequadas para servidores Web, servidores de aplicações de IOPS reduzidos, aplicações empresariais pouco usada e cargas de trabalho de programador/teste.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

## <a name="standard-hdd"></a>HDD Standard

HDDs de padrão do Azure fornecem suporte de disco fiável e económica para as VMs executar cargas de trabalho insensível à latência. Também suporta blobs, tabelas, filas e ficheiros. Com o armazenamento standard, os dados são armazenados em unidades de disco rígido (HDDs). Ao trabalhar com VMs, pode utilizar os discos standard SSD e HDD para cenários de desenvolvimento/teste e menos cargas de trabalho críticas. Armazenamento Standard está disponível em todas as regiões do Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

## <a name="billing"></a>Faturação

Ao utilizar discos geridos, aplicam-se as seguintes considerações de faturas:

- Tipo de disco
- Tamanho de disco gerido
- Instantâneos
- Transferências de dados de saída
- Número de transações

**Geridos pelo tamanho do disco**: os discos geridos são cobrados no tamanho aprovisionado. Azure mapeia o tamanho aprovisionado (arredondado) para o tamanho de disco oferecidos mais próximo. Para obter detalhes sobre os tamanhos de disco oferecidos, consulte as tabelas anteriores. Cada disco mapeia para uma oferta de tamanho de disco de aprovisionamento suportados e é cobrado em conformidade. Por exemplo, se tiver aprovisionado um 200 GiB SSD padrão, ele mapeia para a oferta de tamanho de disco de E15 (256gib). Para qualquer disco aprovisionado a faturação é contabilizada à hora, utilizando o preço mensal para a oferta de armazenamento Premium. Por exemplo, se aprovisionar um disco de E10 e eliminado depois de 20 horas, é cobrado para a oferta de E10 Rateado para 20 horas. Isto é, independentemente da quantidade de dados reais, escritos no disco.

**Instantâneos**: Os instantâneos são faturados com base no tamanho utilizado. Por exemplo, se criar um instantâneo de um disco gerido com capacidade aprovisionada de 64 GiB e o tamanho de dados utilizados real de 10 GiB, o instantâneo é cobrado apenas para o tamanho de dados utilizados de 10 GiB.