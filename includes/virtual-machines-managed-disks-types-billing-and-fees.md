---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "73412999"
---
**Transferências de dados de saída**: [Transferências de dados de saída (dados saídos](https://azure.microsoft.com/pricing/details/bandwidth/) dos centros de dados Azure) incorrem na faturação para utilização da largura de banda.

**Transações**: É cobrado o número de transações que realiza num disco gerido padrão. Para os SSDs standard, cada operação de E/S inferior ou igual a 256 KiB de produção é considerada uma única operação de E/S. As operações de I/O maiores do que 256 KiB de produção são consideradas múltiplas I/Os do tamanho 256 KiB. Para os HDDs standard, cada operação IO é considerada como uma única transação, independentemente do tamanho de E/S.

Para obter informações detalhadas sobre os preços dos discos geridos, incluindo os custos de transação, consulte [a fixação dos discos geridos](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Taxa de reserva VM de disco ultra

Os VMs Azure têm a capacidade de indicar se são compatíveis com discos ultra. Um VM ultra compatível com disco atribui capacidade de largura de banda dedicada entre a instância VM compute e a unidade de escala de armazenamento de blocos para otimizar o desempenho e reduzir a latência. A adição desta capacidade no VM resulta numa taxa de reserva que só é imposta se ativar a capacidade do disco ultra no VM sem lhe anexar um disco ultra. Quando um disco ultra é ligado ao VM compatível com disco ultra, esta carga não seria aplicada. Esta taxa é por vCPU provisida no VM. 

> [!Note]
> Para [tamanhos de VM de núcleo limitados,](../articles/virtual-machines/linux/constrained-vcpu.md)a taxa de reserva baseia-se no número real de VCPUs e não nos núcleos constrangidos. Para Standard_E32 8s_v3, a taxa de reserva será baseada em 32 núcleos. 

Consulte a página de preços do [Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) para obter detalhes sobre preços ultra-discos.

### <a name="azure-disk-reservation"></a>Reserva de disco Azure

A reserva em disco é a opção de comprar um ano de armazenamento em disco com desconto, reduzindo o seu custo total. Ao comprar uma reserva de disco, selecione um SKU de disco específico numa região alvo, por exemplo, 10 SSDs premium P30 (1TiB) na região leste dos EUA 2 por um período de um ano. A experiência de reserva é semelhante a instâncias reservadas da máquina virtual (VM). Pode agregar reservas de VM e Disk para maximizar as suas poupanças. Para já, a Azure Disks Reservation oferece um plano de compromisso de um ano para SKUs SSD premium de P30 (1TiB) a P80 (32 TiB) em todas as regiões de produção. Para obter mais detalhes sobre os preços dos Discos Reservados, consulte [a página de preços do Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/).