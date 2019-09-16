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
ms.openlocfilehash: a416d1c6e813be558f034e15576c57efa6073788
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968606"
---
**Transferências de dados de saída**: [Transferências de dados de saída](https://azure.microsoft.com/pricing/details/bandwidth/) (os dados que saem dos data centers do Azure) incorrem na cobrança do uso de largura de banda.

**Transações**: Você será cobrado pelo número de transações executadas em um disco gerenciado padrão. Para SSDs padrão, cada operação de e/s menor ou igual a 256 KiB de taxa de transferência é considerada uma única operação de e/s. Operações de e/s maiores que 256 KiB de taxa de transferência são consideradas várias e/SS de tamanho de 256 KiB. Para HDDs padrão, cada operação de e/s é considerada como uma única transação, independentemente do tamanho de e/s.

Para obter informações detalhadas sobre os preços de Managed Disks, incluindo os custos de transação, consulte [Managed disks preços](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Taxa de reserva de VM de ultra Disk

As VMs do Azure têm a capacidade de indicar se são compatíveis com ultra discos. Uma VM de ultra disco compatível aloca a capacidade de largura de banda dedicada entre a instância de VM de computação e a unidade de escala de armazenamento em bloco para otimizar o desempenho e reduzir a latência. Adicionar esse recurso na VM resultará em um encargo de reserva que só será imposto se você tiver habilitado a capacidade de ultra Disk na VM sem anexar um ultra Disk a ele. Quando um ultra Disk é anexado à VM ultra compatível com disco, esse encargo não seria aplicado. Essa cobrança é por vCPU provisionada na VM. 

> [!Note]
> Para [tamanhos de VM de núcleo restritos](../articles/virtual-machines/linux/constrained-vcpu.md), a taxa de reserva é baseada no número real de vCPUs e não nos núcleos restritos. Para Standard_E32-8s_v3, a taxa de reserva será baseada em núcleos de 32. 

Consulte a [página de preços dos discos do Azure](https://azure.microsoft.com/pricing/details/managed-disks/) para obter detalhes de preços do ultra Disk.
