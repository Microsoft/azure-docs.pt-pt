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
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73412999"
---
**Transferências de dados de saída**: [transferências de dados de saída](https://azure.microsoft.com/pricing/details/bandwidth/) (dados que saem de data centers do Azure) incorrem em cobrança pelo uso de largura de banda.

**Transações**: você será cobrado pelo número de transações executadas em um disco gerenciado padrão. Para SSDs padrão, cada operação de e/s menor ou igual a 256 KiB de taxa de transferência é considerada uma única operação de e/s. Operações de e/s maiores que 256 KiB de taxa de transferência são consideradas várias e/SS de tamanho de 256 KiB. Para HDDs padrão, cada operação de e/s é considerada como uma única transação, independentemente do tamanho de e/s.

Para obter informações detalhadas sobre os preços de Managed Disks, incluindo os custos de transação, consulte [Managed disks preços](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Taxa de reserva de VM de ultra Disk

As VMs do Azure têm a capacidade de indicar se são compatíveis com ultra discos. Uma VM de ultra disco compatível aloca a capacidade de largura de banda dedicada entre a instância de VM de computação e a unidade de escala de armazenamento em bloco para otimizar o desempenho e reduzir a latência. Adicionar esse recurso na VM resultará em um encargo de reserva que só será imposto se você tiver habilitado a capacidade de ultra Disk na VM sem anexar um ultra Disk a ele. Quando um ultra Disk é anexado à VM ultra compatível com disco, esse encargo não seria aplicado. Essa cobrança é por vCPU provisionada na VM. 

> [!Note]
> Para [tamanhos de VM de núcleo restritos](../articles/virtual-machines/linux/constrained-vcpu.md), a taxa de reserva é baseada no número real de vCPUs e não nos núcleos restritos. Para Standard_E32-8s_v3, a taxa de reserva será baseada em núcleos de 32. 

Consulte a [página de preços dos discos do Azure](https://azure.microsoft.com/pricing/details/managed-disks/) para obter detalhes de preços do ultra Disk.

### <a name="azure-disk-reservation"></a>Reserva de disco do Azure

A reserva de disco é a opção de comprar um ano de armazenamento em disco com antecedência a um desconto, reduzindo o custo total. Ao comprar uma reserva de disco, você seleciona um SKU de disco específico em uma região de destino, por exemplo, 10 p30 (1TiB) do SSDs Premium na região leste dos EUA 2 para um termo de um ano. A experiência de reserva é semelhante às instâncias de VM (máquina virtual) reservadas. Você pode agrupar as reservas de VM e disco para maximizar sua economia. Por enquanto, a reserva de discos do Azure oferece um plano de compromisso de um ano para SKUs de SSD Premium de p30 (1TiB) a P80 (32 TiB) em todas as regiões de produção. Para obter mais detalhes sobre os preços dos discos reservados, consulte a [página de preços dos discos do Azure](https://azure.microsoft.com/pricing/details/managed-disks/).