---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4b5d2de2e9ccd44517e083a435e127bd5678f002
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564345"
---
# <a name="ultra-disks-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Discos para cargas de trabalho de máquina virtual do Azure gerida dos discos Ultra (pré-visualização)

Discos de ultra do Azure (pré-visualização) fornece débito elevado, o IOPS elevado e o armazenamento de disco de baixa latência consistente para VMs IaaS do Azure. Esta nova oferta fornece a parte superior do desempenho de linha nos níveis de disponibilidade mesmo como nossas ofertas de discos existentes. Benefícios adicionais de discos ultra incluem a capacidade de alterar dinamicamente o desempenho do disco, juntamente com as suas cargas de trabalho sem a necessidade de reiniciar as máquinas virtuais. Discos Ultra são adequados para cargas de trabalho com uso intensivo de dados, como SAP HANA, bases de dados de escalão superior e cargas de trabalho pesado de transação.

## <a name="ultra-disk-features"></a>Recursos de disco Ultra

**Discos geridos**: Discos Ultra só estão disponíveis como discos geridos. Discos Ultra não podem ser implementados como um disco não gerido ou a BLOBs de páginas. Ao criar um disco gerido, especificar o sku de disco escreva como UltraSSD_LRS e indicar o tamanho do disco, o IOPS e débito de que precisa e o Azure cria e gere o disco por si.  

**Máquinas virtuais**: Os discos Ultra foram concebidos para funcionar com todos os Premium SSD SKUs de Máquina Virtual do Azure ativado; No entanto, como se encontra atualmente em pré-visualização, as VMs for dimensionadas como v3 de ES/DS.

**Configuração de desempenho dinâmico**: Discos de ultra permitem-lhe alterar dinamicamente o desempenho (IOPS e débito) do disco, juntamente com suas necessidades de carga de trabalho sem ter de reiniciar as máquinas virtuais.

## <a name="scalability-and-performance-targets"></a>Metas de escalabilidade e desempenho

Quando aprovisiona um discos ultra, terá a opção para configurar de forma independente a capacidade e o desempenho do disco. Discos Ultra vêm em vários tamanhos fixos de 4 GiB até 64 TiB e um modelo de configuração de desempenho flexível que permite que configure independentemente o IOPS e débito de funcionalidade. Ultra discos só podem ser utilizados como discos de dados. Recomendamos que utilize Premium SSDs como discos de SO.

Algumas capacidades chave dos discos ultra são:

- Capacidade do disco: Discos Ultra oferece uma variedade de tamanhos de disco diferente de 4 GiB até 64 TiB.
- IOPS de disco: Os discos Ultra suportam os limites de IOPS de 300 IOPS/GiB até um máximo de mil 160 IOPS por disco. Para atingir o IOPS aprovisionado, certifique-se de que o IOPS de disco selecionado são menos do que o IOPS de VM. O IOPS de disco mínimo é de 100 IOPS.
- Débito de disco: Com o ultra discos, o limite de taxa de transferência de um único disco é 256 KiB/s para cada aprovisionado IOPS, até um máximo de 2000 MBps por disco (em que MBps = 10 ^ 6 Bytes por segundo). O débito de disco mínimo é 1 MiB.

A tabela seguinte resume as diferentes configurações suportadas pelos diferentes tamanhos de disco:  

### <a name="ultra-disks-managed-disk-offerings"></a>Ultra discos geridos ofertas de disco

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

## <a name="pricing-and-billing"></a>Preços e faturação

Quando utilizar discos ultra, serão aplicadas as seguintes considerações de faturas:

- Tamanho do disco de geridos
- Disco gerido aprovisionado IOPS
- Débito de disco gerido
- Taxa de reserva de VM de disco Ultra

### <a name="managed-disk-size"></a>Tamanho do disco de geridos

Discos geridos são faturados sobre os tamanhos VM que escolher durante o aprovisionamento de uma nova VM do Azure. O Azure mapeia o tamanho aprovisionado (arredondado para cima) para a oferta de tamanho de disco mais próxima. Para obter detalhes sobre os tamanhos de disco oferecidos, consulte a tabela na secção de metas de desempenho e escalabilidade acima. Cada disco é mapeado para um tamanho de disco de aprovisionamento suportados e será cobrada em conformidade numa base horária. Por exemplo, se aprovisionar um disco de ultra GiB 200 e eliminado depois de 20 horas, ele será mapeada para a oferta de tamanho de disco de 256gib e será cobrado para o 256gib durante 20 horas. Esta faturação foi baseada no consumo de hora de computação, independentemente do volume de dados, na verdade, escritos no disco.

### <a name="managed-disk-provisioned-iops"></a>Disco gerido aprovisionado IOPS

IOPS são o número de pedidos que a aplicação está a enviar para os discos por segundo. Uma operação de entrada/saída poderia ser de leitura seqüencial ou gravação ou leitura aleatória ou escrever. Com base no tamanho do disco ou o número de discos ligados à VM, o número médio de IOPS é faturado à hora. Para obter detalhes do disco que IOPS oferecidos, consulte a tabela na secção de metas de desempenho e escalabilidade acima.

### <a name="managed-disk-provisioned-throughput"></a>Gerido débito aprovisionado de disco

O débito é a quantidade de dados que a aplicação estiver a enviar para os discos em intervalos especificados, medido em bytes por segundo. Se a sua aplicação está a efetuar operações de entrada/saída grandes, ela exige um débito elevado.  

Existe uma relação entre o débito e IOPS, conforme mostrado na seguinte fórmula:  IOPS x tamanho de e/s = débito

Portanto, é importante determinar os valores IOPS e débito ideal que seu aplicativo requer. Ao testar otimizar um, o outro também obtém afetado. É recomendável iniciar com uma taxa de transferência, correspondente ao tamanho de e/s de KiB 16 e ajustar se necessitar de mais débito.

Para obter detalhes sobre o débito de disco suportadas em discos ultra, consulte a tabela na secção de metas de desempenho e escalabilidade acima. Como o tamanho do disco e IOPS, débito aprovisionado é cobrado numa base horária por MBps aprovisionado.

### <a name="ultra-disk-vm-reservation-fee"></a>Taxa de reserva de VM de disco Ultra

Introduzimos um recurso na VM que indica a que sua VM é disco ultra compatível. Capacidade de largura de banda entre a instância VM de computação e a unidade de escala de armazenamento de bloco para otimizar o desempenho e reduzir a latência de dedicado de um disco de ultra aloca VM compatível. Adicionar esse recurso nos resultados VM numa cobrança de reserva que é imposta apenas se tiver ativado a capacidade de disco ultra na VM sem anexar um disco de ultra a ele. Quando um disco ultra é anexado à VM compatível ultra, esta cobrança não seria aplicada. É cobrado por vCPU aprovisionado na VM.

Consulte a [discos do Azure, página de preços](https://azure.microsoft.com/pricing/details/managed-disks/) para os novo ultra discos detalhes de preço disponíveis em pré-visualização limitada.

### <a name="ultra-disk-preview-scope-and-limitations"></a>Âmbito de pré-visualização do disco Ultra e limitações

Na pré-visualização, ultra discos:

- Vai ser suportado inicialmente na região E.U.A. Leste 2 numa única zona de disponibilidade  
- Só pode ser utilizado com zonas de disponibilidade (conjuntos de disponibilidade e implementações de VM única fora da vontade de zonas não têm a capacidade de anexar um disco de ultra)
- Só são suportadas em VMs de v3 de ES/DS
- Só estão disponíveis como discos de dados e tamanho de setor físico apenas suporte de 4K  
- Só pode ser criado como discos vazios  
- Atualmente só pode ser implementada com modelos do Resource Manager, CLI e SDK de Python.
- Será (ainda não) suporte de disco instantâneos, imagens de VM, os conjuntos de disponibilidade, conjuntos de dimensionamento de Máquina Virtual e o Azure Disk Encryption.
- Não (ainda) suporta a integração com a cópia de segurança do Azure ou Azure Site Recovery.
- Tal como acontece com [a maioria das pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), esta funcionalidade não deve ser utilizada para cargas de trabalho de produção até disponibilidade geral (GA).
