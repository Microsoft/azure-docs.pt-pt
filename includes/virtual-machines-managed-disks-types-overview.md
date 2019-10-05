---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3dfc3c309fe3583ddd4307cbfe4e55bf6522ffc3
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955874"
---
# <a name="what-disk-types-are-available-in-azure"></a>Quais tipos de disco estão disponíveis no Azure?

Atualmente, o Azure Managed disks oferece quatro tipos de disco, cada tipo destina-se a cenários de clientes específicos.

## <a name="disk-comparison"></a>Comparação de discos

A tabela a seguir fornece uma comparação de ultra discos, unidades de estado sólido (SSD) Premium, SSD padrão e unidades de disco rígido padrão (HDD) para discos gerenciados para ajudá-lo a decidir o que usar.

|   | Ultra Disk   | Premium SSD   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Tipo de disco   |SSD   |SSD   |SSD   |HDD   |
|Cenário   |Cargas de trabalho com uso intensivo de e/s, como SAP HANA, bancos de dados de camada superior (por exemplo, SQL, Oracle) e outras cargas de trabalho de transações pesadas.   |Cargas de trabalho confidenciais de produção e de desempenho   |Servidores Web, aplicativos empresariais com pouco uso e desenvolvimento/teste   |Backup, não crítico, acesso infrequente   |
|Tamanho máximo do disco   |65.536 Gibibyte (GiB)    |32.767 GiB    |32.767 GiB   |32.767 GiB   |
|Taxa de transferência máxima   |2\.000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|IOPS Máx.   |160.000    |20,000   |6,000   |2\.000   |

## <a name="ultra-disk"></a>Ultra Disk

Os ultra discos do Azure fornecem alta taxa de transferência, IOPS alta e armazenamento de disco consistente de baixa latência para VMs IaaS do Azure. Alguns benefícios adicionais de ultra discos incluem a capacidade de alterar dinamicamente o desempenho do disco, juntamente com suas cargas de trabalho, sem a necessidade de reiniciar suas máquinas virtuais (VM). Ultra discos são adequados para cargas de trabalho com uso intensivo de dados, como SAP HANA, bancos de dados de camada superior e cargas de trabalho com transações pesadas. Ultra discos só podem ser usados como discos de dados. É recomendável usar o SSDs Premium como discos do sistema operacional.

### <a name="performance"></a>Desempenho

Ao provisionar um ultra Disk, você pode configurar independentemente a capacidade e o desempenho do disco. Ultra discos vêm em vários tamanhos fixos, variando de 4 GiB até 64 TiB e apresentam um modelo de configuração de desempenho flexível que permite configurar de forma independente a IOPS e a taxa de transferência.

Alguns dos principais recursos dos ultra discos são:

- Capacidade do disco: O ultra disks Capacity varia de 4 GiB até 64 TiB.
- IOPS de disco: Ultra disks dão suporte a limites de IOPS de 300 IOPS/GiB, até um máximo de 160 K IOPS por disco. Para obter o IOPS que você provisionou, verifique se os IOPS de disco selecionados são menores do que o limite de IOPS de VM. O mínimo de IOPS por disco é 2 IOPS/GiB, com um mínimo de linha de base geral de 100 IOPS. Por exemplo, se você tivesse um ultra GiB de 4 discos, terá um mínimo de 100 IOPS, em vez de oito IOPS.
- Taxa de transferência do disco: Com ultra discos, o limite de taxa de transferência de um único disco é de 256 KiB/s para cada IOPS provisionado, até um máximo de 2000 MBps por disco (em que MBps = 10 ^ 6 bytes por segundo). A taxa de transferência mínima por disco é 4KiB/s para cada IOPS provisionado, com um mínimo de linha de base geral de 1 MBps.
- Ultra discos dão suporte ao ajuste dos atributos de desempenho de disco (IOPS e taxa de transferência) em tempo de execução sem desanexar o disco da máquina virtual. Depois que uma operação de redimensionamento de desempenho do disco tiver sido emitida em um disco, poderá levar até uma hora para que a alteração realmente entre em vigor. Há um limite de quatro operações de redimensionamento de desempenho durante uma janela de 24 horas. É possível que uma operação de redimensionamento de desempenho falhe devido à falta de capacidade de largura de banda de desempenho.

### <a name="disk-size"></a>Tamanho do disco

|Tamanho do disco (GiB)  |Limite de IOPS  |Limite de taxa de transferência (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9\.600         |2\.000         |
|64     |19.200         |2\.000         |
|128     |38.400         |2\.000         |
|256     |76.800         |2\.000         |
|512     |80,000         |2\.000         |
|1024-65536 (tamanhos neste intervalo aumentando em incrementos de 1 TiB)     |160.000         |2\.000         |

### <a name="ga-scope-and-limitations"></a>Limitações e escopo de GA

Por enquanto, ultra discos têm limitações adicionais, como a seguir:

- Têm suporte no leste dos EUA 2, Sudeste Asiático e Europa Setentrional, em duas zonas de disponibilidade por região  
- Só pode ser usado com zonas de disponibilidade (conjuntos de disponibilidade e implantações de VM única fora das zonas não terão a capacidade de anexar um ultra Disk)
- Há suporte apenas em VMs ES/DS v3
- Estão disponíveis somente como discos de dados e só dão suporte ao tamanho de setor físico de 4K  
- Só pode ser criado como discos vazios  
- Ainda não há suporte para instantâneos de disco, imagens de VM, conjuntos de disponibilidade, conjuntos de dimensionamento de máquinas virtuais e Azure Disk Encryption
- Ainda não há suporte para integração com o backup do Azure ou Azure Site Recovery
- O limite máximo atual para IOPS em VMs GA é 80.000.
