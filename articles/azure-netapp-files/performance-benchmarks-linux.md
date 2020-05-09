---
title: Azure NetApp Files referências de desempenho para Linux / Microsoft Docs
description: Descreve os benchmarks de desempenho que o Azure NetApp Files entrega para o Linux.
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
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614593"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>Referências de desempenho do Azure NetApp Files para linux

Este artigo descreve os benchmarks de desempenho que os Ficheiros Azure NetApp entrega para o Linux.

## <a name="linux-scale-out"></a>Linux scale-out

Esta secção descreve os parâmetros de desempenho da carga de trabalho do Linux e do IOPS de carga de trabalho.

### <a name="linux-workload-throughput"></a>Entrada de carga de trabalho linux  

O gráfico abaixo representa uma carga de trabalho sequencial de 64 kibibyte (KiB) e um conjunto de trabalho de 1-TiB. Mostra que um único volume de Ficheiros Azure NetApp pode lidar entre ~1.600 MiB/s escritas sequenciais puras e ~4.500 Leituras sequenciais puras mib/s.  

O gráfico ilustra que diminui em 10% de cada vez, desde a leitura pura à escrita pura. Demonstra o que se pode esperar quando se utilizam variados rácios de leitura/escrita (100%:0%, 90%:10%, 80%:20%, e assim por diante).

![Entrada de carga de trabalho linux](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>IOPS de carga de trabalho linux  

O gráfico seguinte representa uma carga de trabalho aleatória de 4 kibibyte (KiB) e um conjunto de trabalho de 1-TiB. O gráfico mostra que um volume de Ficheiros Azure NetApp pode lidar entre ~130.000 escritas aleatórias puras e ~460.000 leituras aleatórias puras.  

Este gráfico ilustra diminui em 10% de cada vez, desde a leitura pura à escrita pura. Demonstra o que se pode esperar quando se utilizam variados rácios de leitura/escrita (100%:0%, 90%:10%, 80%:20%, e assim por diante).

![IOPS de carga de trabalho linux](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Escala linux  

O kernel Linux 5.3 permite uma rede de escala`nconnect`de um cliente para NFS- . Os gráficos desta secção mostram os resultados dos testes de validação para a opção de montagem do lado do cliente com nFSv3. A funcionalidade está disponível em SUSE (a partir de SLES12SP4) e Ubuntu (a partir do lançamento de 19.10). É semelhante em conceito tanto ao multicanal SMB como ao Oracle Direct NFS.

Os gráficos comparam `nconnect` as vantagens de um volume montado não ligado. Nos gráficos, o FIO gerou a carga de trabalho de um único D32s_v3 caso na região de Us-West2 Azure.

### <a name="linux-read-throughput"></a>Linux ler a entrada  

Os gráficos seguintes mostram leituras sequenciais de ~3.500 MiB/s com `nconnect``nconnect`, aproximadamente 2,3X não- .

![Linux ler a entrada](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Linux escrever a entrada  

Os gráficos seguintes mostram escritos sequenciais. Indicam que `nconnect` não tem benefícios visíveis para as escritas sequenciais. 1.500 MiB/s é aproximadamente o limite superior do volume de escrita sequencial e o limite de D32s_v3 instância de saída.

![Linux escrever a entrada](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Linux leu IOPS  

Os gráficos seguintes mostram leituras aleatórias de ~200.000 lêem IOPS com `nconnect`, aproximadamente 3X non-`nconnect`.

![Linux leu IOPS](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Linux escrever IOPS  

Os gráficos seguintes mostram escritas aleatórias de ~135.000 escritos IOPS com `nconnect`, aproximadamente 3X non-`nconnect`.

![Linux escrever IOPS](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>Passos seguintes

- [Ficheiros Azure NetApp: Tirar o máximo partido do seu armazenamento em nuvem](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
