---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cdfc858522c5b331c90259de0fb5ad84fa54bc5d
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57251961"
---
# <a name="what-disk-types-are-available-in-azure"></a>Que tipos de disco estão disponíveis no Azure?

Atualmente, o managed disks do Azure oferece quatro tipos de disco, três dos quais estão em disponibilidade geral (GA) e outro que está atualmente em pré-visualização. Esses tipos de quatro disco, de cada têm seus próprios cenários de clientes de destino apropriado.

## <a name="disk-comparison"></a>Comparação de discos

A tabela seguinte fornece uma comparação de ultra solid-state unidades (SSD) (pré-visualização), premium SSD, standard SSD e unidades de disco rígido standard (HDD) para discos geridos para o ajudar a decidir o que utilizar.

|   | SSD Ultra (pré-visualização)   | SSD Premium   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Tipo de disco   |SSD   |SSD   |SSD   |HDD   |
|Cenário   |Cargas de trabalho de e/s intensiva, como SAP HANA, bases de dados de camada superior (por exemplo, SQL, Oracle) e outras cargas de trabalho pesado de transação.   |Cargas de trabalho confidenciais de produção e de desempenho   |Servidores Web, aplicações empresariais pouco usada e desenvolvimento/teste   |Acesso de cópia de segurança, não críticas, pouco frequente   |
|Tamanho do disco   |gibibyte 65.536 (GiB) (pré-visualização)   |4095 giB (GA), 32.767 GiB (pré-visualização)    |4095 GiB de (GA), 32.767 GiB (pré-visualização)   |4095 giB (GA), 32.767 GiB (pré-visualização)   |
|Débito máximo   |2.000 MiB/s (pré-visualização)   |250 (GA) MiB/s, 750 MiB/s (pré-visualização)   |60 MiB/s (GA), 500 MiB/s (pré-visualização)   |60 Mib/s (GA), 500 MiB/s (pré-visualização)   |
|IOPS Máx.   |160,000 (pré-visualização)   |7500 (GA), 20 000 (pré-visualização)   |500 (GA), 2.000 (pré-visualização)   |500 (GA), 2.000 (pré-visualização)   |

## <a name="ultra-ssd-preview"></a>SSD Ultra (pré-visualização)

SSD ultra do Azure (pré-visualização) fornecer alto débito e armazenamento de disco de baixa latência consistente IOPS elevado para VMs IaaS do Azure. Alguns dos benefícios adicionais de ultra SSD incluem a capacidade de alterar dinamicamente o desempenho do disco, juntamente com as suas cargas de trabalho, sem a necessidade de reiniciar as máquinas virtuais. Ultra SSD são adequados para cargas de trabalho com uso intensivo de dados, como SAP HANA, bases de dados de escalão superior e cargas de trabalho pesado de transação. Ultra SSD só pode ser utilizado como discos de dados. Recomendamos que utilize premium SSDs como discos de SO.

### <a name="performance"></a>Desempenho

Quando aprovisiona um disco ultra, pode configurar a forma independente a capacidade e o desempenho do disco. Ultra SSD são fornecidos em vários tamanhos fixos, que vão desde GiB 4 até 64 TiB e um modelo de configuração de desempenho flexível que permite que configure independentemente o IOPS e débito de funcionalidade.

Alguns recursos principais do Ultra SSD são:

- Capacidade do disco: Ultra intervalos de capacidade SSD de 4 GiB até 64 TiB.
- IOPS de disco: Ultra SSD suporta limites de IOPS de 300 IOPS/GiB até um máximo de mil 160 IOPS por disco. Para atingir o IOPS aprovisionado, certifique-se de que o IOPS de disco selecionado são menos do que o IOPS de VM. O IOPS mínimo do disco são 100 IOPS.
- Débito de disco: Com o ultra SSD, o limite de taxa de transferência de um único disco é 256 KiB/s para cada aprovisionado IOPS, até um máximo de 2000 MBps por disco (em que MBps = 10 ^ 6 Bytes por segundo). O débito de disco mínimo é 1 MiB.

### <a name="disk-size"></a>Tamanho do disco

|Tamanho do disco (GiB)  |Limites IOPS  |Limite de débito (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2.000         |
|64     |19,200         |2.000         |
|128     |38,400         |2.000         |
|256     |76,800         |2.000         |
|512     |80,000         |2.000         |
|1.024-65,536 (tamanhos neste intervalo de aumento em incrementos de 1 TiB)     |160,000         |2.000         |

### <a name="preview-scope-and-limitations"></a>Âmbito de pré-visualização e limitações

Durante a pré-visualização, ultra SSD:

- São suportadas na região E.U.A. Leste 2 numa zona de disponibilidade única  
- Só pode ser utilizado com zonas de disponibilidade (conjuntos de disponibilidade e únicas implementações de VM fora da vontade de zonas não têm a capacidade de anexar um disco de ultra)
- Só são suportadas em VMs de v3 de ES/DS
- Só estão disponíveis como discos de dados e tamanho de setor físico apenas suporte de 4K  
- Só pode ser criado como discos vazios  
- Atualmente só pode ser implementada com modelos do Azure Resource Manager, CLI e SDK de python.
- Ainda não suporta instantâneos de disco, imagens VM, conjuntos de disponibilidade, conjuntos de dimensionamento de máquina virtual e encriptação de disco do Azure.
- Ainda não suporta a integração com a cópia de segurança do Azure ou Azure Site Recovery.
- Tal como acontece com [a maioria das pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), esta funcionalidade não deve ser utilizada para cargas de trabalho de produção até disponibilidade geral (GA).

## <a name="premium-ssd"></a>SSD Premium

Premium do Azure SSDs fornecer suporte de disco de elevado desempenho e baixa latência para máquinas virtuais (VMs) com entrada/saída (e/s)-cargas de trabalho intensivas. Para tirar partido da velocidade e o desempenho de discos de armazenamento premium, pode migrar os discos de VM existentes para o Premium SSDs. Premium SSDs são adequadas para aplicações de produção de missão crítica.

### <a name="disk-size"></a>Tamanho do disco

Tamanhos marcados com asterisco estão atualmente em pré-visualização.

| Tamanhos SSD Premium  | P4               | P6               | P10             | P15 | P20              | P30              | P40              | P50              | P60*              | P70*              | P80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Tamanho do disco em GiB           | 32             | 64             | 128            | 256  | 512            | 1,024    | 2,048     | 4,095    | 8,192     | 16,384     | 32,767     |
| IOPs por disco       | Até 120 | Até 240              | Até 500              | Até 1,100 | Até 2,300              | Até 5000              | Até 7.500             | Até 7.500              | Até 12.500              | Máximo de 15 000              | Até 20 000              |
| Débito por disco | Até 25 MiB/seg | Máximo de 50 MiB/seg | Até 100 MiB/seg | Até 125 MiB/seg | Até 150 MiB/seg | Até 200 MiB/seg | Até 250 MiB/seg | Até 250 MiB/seg| Até 480 MiB/seg | Até 750 MiB/seg | Até 750 MiB/seg |

## <a name="standard-ssd"></a>SSD Standard

SSDs padrão do Azure são uma opção de armazenamento económico, otimizada para cargas de trabalho que necessitam de um desempenho consistente em níveis inferiores de IOPS. Standard SSD oferece uma experiência de bom nível de entrada para aqueles que pretende mover para a cloud, especialmente se ocorrerem problemas com a variância de cargas de trabalho em execução em suas soluções HDD no local. Os SSDs Standard proporcionam uma melhor disponibilidade, consistência, fiabilidade e latência em comparação comparada discos HDD. SSDs padrão são adequadas para servidores Web, servidores de aplicações de IOPS reduzidos, aplicações empresariais pouco usada e cargas de trabalho de programador/teste.

### <a name="disk-size"></a>Tamanho do disco

Tamanhos marcados com asterisco estão atualmente em pré-visualização.

| Tamanhos SSD Standard  | E4                   | E6                   | E10               | E15               | E20             | E30 | E40              | E50              | E60*              | E70*              | E80*              |
|---------------------|---------------------|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Tamanho do disco em GiB           | 32             | 64             | 128             | 256             | 512            | 1,024  | 2,048            | 4,095     | 8,192     | 16,384     | 32,767    |
| IOPs por disco       | Até 120              | Até 240              | Até 500              | Até 500              | Até 500              | Até 500 | Até 500              | Até 500              | Até 500             | Até 500              | Até 1.300              | Até 2000              | Até 2000              |
| Débito por disco |  Até 25 MiB/seg  |  Máximo de 50 MiB/seg  |  Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg| Até 300 MiB/seg |  Até 500 MiB/seg | Até 500 MiB/seg |

## <a name="standard-hdd"></a>HDD Standard

HDDs de padrão do Azure fornecem suporte de disco fiável e económica para as VMs executar cargas de trabalho insensível à latência. Também suporta blobs, tabelas, filas e ficheiros. Com o armazenamento standard, os dados são armazenados em unidades de disco rígido (HDDs). Ao trabalhar com VMs, pode utilizar os discos standard SSD e HDD para cenários de desenvolvimento/teste e menos cargas de trabalho críticas. Armazenamento Standard está disponível em todas as regiões do Azure.

### <a name="disk-size"></a>Tamanho do disco

Tamanhos marcados com asterisco estão atualmente em pré-visualização.

| Tipo de Disco Standard  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60*              | S70*              | S80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Tamanho do disco em GiB          | 32             | 64             | 128            | 256  | 512            | 1,024    | 2,048     | 4,095    | 8,192     | 16,384     | 32,767     |
| IOPs por disco       | Até 500              | Até 500              | Até 500              | Até 500 | Até 500              | Até 500              | Até 500             | Até 500              | Até 1.300              | Até 2000              | Até 2000              |
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
