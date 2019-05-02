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
ms.openlocfilehash: 42ab8be45d4086589f0793531003700e7552a440
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744459"
---
**Transferências de dados de saída**: [Transferências de dados de saída](https://azure.microsoft.com/pricing/details/bandwidth/) faturação para utilização de largura de banda de incorrer em (dados que saem de datacenters do Azure).

**Transações**: A faturação para o número de transações que executar num disco gerido standard. Para os SSDs padrão, o tamanho da unidade de e/s de 256 KiB é utilizado para o número de transações de gestão de contas. Tamanhos de e/s maiores são contabilizados como várias e/SS de tamanho 256 KiB. Para HDDs padrão, cada operação de e/s é considerada como uma única transação, independentemente do tamanho de e/s.

Para obter informações detalhadas sobre os preços do Managed Disks, incluindo os custos de transação, consulte [preços de discos geridos](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Taxa de reserva de VM de SSD Ultra

VMs do Azure tem a capacidade para indicar se eles são compatíveis com o ultra SSD. Capacidade de largura de banda entre a instância VM de computação e a unidade de escala de armazenamento de bloco para otimizar o desempenho e reduzir a latência de dedicado de um disco de ultra aloca VM compatível. Adicionar esse recurso nos resultados VM numa cobrança de reserva que é imposta apenas se tiver ativado a capacidade de disco ultra na VM sem anexar um disco de ultra a ele. Quando está ligado um disco ultra para o ultra disco VM compatível, não seria possível aplicar esta cobrança. É cobrado por vCPU aprovisionado na VM.

Consulte a [discos do Azure, página de preços](https://azure.microsoft.com/pricing/details/managed-disks/) para ultra discos os detalhes dos preços de disco.