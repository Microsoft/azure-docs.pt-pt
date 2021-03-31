---
title: Azure NetApp Files referencias de desempenho para linux | Microsoft Docs
description: Descreve os referenciais de desempenho que os Ficheiros Azure NetApp entregam para o Linux.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: b-juche
ms.openlocfilehash: b763a734866dd5fed5bf0500d4d52b9324c92a79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "82614593"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>Azure NetApp Files referenciais de desempenho para Linux

Este artigo descreve os referenciais de desempenho que a Azure NetApp Files entrega para o Linux.

## <a name="linux-scale-out"></a>Escala de Linux

Esta secção descreve os parâmetros de desempenho da carga de trabalho linux e o IOPS de carga de trabalho.

### <a name="linux-workload-throughput"></a>Produção de carga de trabalho linux  

O gráfico abaixo representa uma carga de trabalho sequencial de 64 kibibytes (KiB) e um conjunto de trabalho de 1-TiB. Mostra que um único volume de Ficheiros Azure NetApp pode lidar entre ~1.600 MiB/s puras escritas sequenciais e ~4.500 leituras sequenciais puras de MiB/s.  

O gráfico ilustra decréscimos em 10% de cada vez, de leitura pura a escrita pura. Demonstra o que se pode esperar quando se utilizam rácios variados de leitura/escrita (100%:0%, 90%:10%, 80%:20%, e assim por diante).

![Produção de carga de trabalho linux](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>IOPS de carga de trabalho linux  

O gráfico a seguir representa uma carga de trabalho aleatória de 4 kibibytes (KiB) e um conjunto de trabalho de 1-TiB. O gráfico mostra que um volume de Ficheiros Azure NetApp pode lidar entre ~130.000 escritas aleatórias puras e ~460.000 leituras aleatórias puras.  

Este gráfico ilustra diminui em 10% de cada vez, de pura leitura a escrita pura. Demonstra o que se pode esperar quando se utilizam rácios variados de leitura/escrita (100%:0%, 90%:10%, 80%:20%, e assim por diante).

![IOPS de carga de trabalho linux](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Escala de Linux  

O núcleo Linux 5.3 permite uma rede de escala de cliente único para `nconnect` NFS. Os gráficos desta secção mostram os resultados dos testes de validação para a opção de montagem do lado do cliente com o NFSv3. A funcionalidade está disponível em SUSE (a começar pelo SLES12SP4) e Ubuntu (a partir do lançamento de 19.10). É semelhante em conceito tanto ao multicanal SMB como ao Oracle Direct NFS.

Os gráficos comparam as vantagens de `nconnect` um volume montado não ligado. Nos gráficos, a FIO gerou a carga de trabalho a partir de um único D32s_v3 instância na região de Azure dos EUA-Oeste2.

### <a name="linux-read-throughput"></a>Linux ler produção  

Os gráficos a seguir mostram leituras sequenciais de ~3.500 MiB/s `nconnect` com, aproximadamente 2,3X non- `nconnect` .

![Linux ler produção](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Linux escrever produção  

Os gráficos que se seguem mostram escritos sequenciais. Indicam que `nconnect` não tem nenhum benefício notável para as gravações sequenciais. 1.500 MiB/s é aproximadamente o limite superior do volume de escrita sequencial e o limite de saída de D32s_v3 caso.

![Linux escrever produção](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Linux ler IOPS  

Os gráficos seguintes mostram leituras aleatórias de ~200.000 leitura IOPS com `nconnect` , aproximadamente 3X non- `nconnect` .

![Linux ler IOPS](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Linux escrever IOPS  

Os gráficos seguintes mostram escritos aleatórios de ~135.000 escrever IOPS com `nconnect` , aproximadamente 3X non- `nconnect` .

![Linux escrever IOPS](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>Passos seguintes

- [Ficheiros Azure NetApp: tirar o máximo partido do seu armazenamento em nuvem](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
