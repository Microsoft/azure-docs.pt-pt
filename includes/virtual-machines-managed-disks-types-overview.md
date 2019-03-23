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
ms.openlocfilehash: 6eae536bd19a2c0e5707d8e0b379774b6eb2707a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395736"
---
# <a name="what-disk-types-are-available-in-azure"></a>Que tipos de disco estão disponíveis no Azure?

Atualmente, o managed disks do Azure oferece quatro tipos de disco, três dos quais estão em disponibilidade geral (GA) e outro que está atualmente em pré-visualização. Esses tipos de quatro disco, de cada têm seus próprios cenários de clientes de destino apropriado.

## <a name="disk-comparison"></a>Comparação de discos

A tabela seguinte fornece uma comparação de ultra solid-state unidades (SSD) (pré-visualização), premium SSD, standard SSD e unidades de disco rígido standard (HDD) para discos geridos para o ajudar a decidir o que utilizar.

|   | SSD Ultra (pré-visualização)   | SSD Premium   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Tipo de disco   |SSD   |SSD   |SSD   |HDD   |
|Cenário   |Cargas de trabalho de e/s intensiva, como SAP HANA, bases de dados de camada superior (por exemplo, SQL, Oracle) e outras cargas de trabalho pesado de transação.   |Cargas de trabalho confidenciais de produção e de desempenho   |Servidores Web, aplicações empresariais pouco usada e desenvolvimento/teste   |Acesso de cópia de segurança, não críticas, pouco frequente   |
|Tamanho do disco   |gibibyte 65.536 (GiB) (pré-visualização)   |32.767 giB    |32.767 giB   |32.767 giB   |
|Débito máximo   |2.000 MiB/s (pré-visualização)   |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|IOPS Máx.   |160,000 (pré-visualização)   |20,000   |6,000   |2.000   |

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
