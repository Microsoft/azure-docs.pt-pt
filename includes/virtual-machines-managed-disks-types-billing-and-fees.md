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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73412999"
---
**Transferências**de dados de saída : [Transferências](https://azure.microsoft.com/pricing/details/bandwidth/) de dados de saída (dados que saem dos centros de dados do Azure) incorrem na faturação para uso da largura de banda.

**Transações**: É cobrado pelo número de transações que executa num disco gerido padrão. Para os SSDs padrão, cada operação de I/S inferior ou igual a 256 KiB de entrada é considerada uma única operação de I/S. As operações de I/O superiores a 256 KiB de entrada são consideradas múltiplas I/Os de tamanho 256 KiB. Para os HDDs Standard, cada operação IO é considerada como uma única transação, independentemente do tamanho de I/O.

Para obter informações detalhadas sobre os preços dos Discos Geridos, incluindo os custos de transação, consulte o [Preço dos Discos Geridos](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Taxa de reserva VM de disco ultra

Os VMs azure têm a capacidade de indicar se são compatíveis com discos ultra. Um VM compatível com disco ultra atribui uma capacidade de largura de banda dedicada entre a instância De Computação VM e a unidade de escala de armazenamento de blocos para otimizar o desempenho e reduzir a latência. A adição desta capacidade no VM resulta numa carga de reserva que só é imposta se ativar a capacidade de disco ultra no VM sem lhe ligar um disco ultra. Quando um disco ultra é ligado ao VM compatível com o disco ultra, esta carga não seria aplicada. Esta taxa é por vCPU provisionada no VM. 

> [!Note]
> Para [tamanhos de VM de núcleo limitados,](../articles/virtual-machines/linux/constrained-vcpu.md)a taxa de reserva baseia-se no número real de vCPUs e não nos núcleos limitados. Para Standard_E32-8s_v3, a taxa de reserva será baseada em 32 núcleos. 

Consulte a página de preços do [Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) para obter detalhes de preços ultra discos.

### <a name="azure-disk-reservation"></a>Reserva de disco azure

A reserva de disco é a opção de comprar um ano de armazenamento de disco com desconto, reduzindo o seu custo total. Ao comprar uma reserva de disco, você seleciona um SKU de disco específico numa região-alvo, por exemplo, 10 SSDs premium P30 (1TiB) na região leste dos EUA 2 por um período de um ano. A experiência de reserva é semelhante às instâncias reservadas da máquina virtual (VM). Pode embalar reservas vm e disco para maximizar as suas poupanças. Para já, a Azure Disks Reservation oferece um plano de compromisso de um ano para sKUs premium de P30 (1TiB) para P80 (32 TiB) em todas as regiões de produção. Para mais detalhes sobre os preços dos Discos Reservados, consulte a página de preços dos [Discos Azure](https://azure.microsoft.com/pricing/details/managed-disks/).