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
ms.openlocfilehash: 72d87142f9b9c1f7bcb2b02281851bd1e29bc9c8
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262263"
---
Os discos geridos pelo Azure oferecem atualmente quatro tipos de discos, cada tipo direcionado para cenários específicos do cliente.

## <a name="disk-comparison"></a>Comparação de discos

A tabela seguinte proporciona uma comparação de discos ultra, unidades de estado sólido premium (SSD), SSD padrão e discos rígidos padrão (HDD) para discos geridos para ajudá-lo a decidir o que usar.

|   | Disco ultra   | Premium SSD   | SSD padrão   | HDD padrão   |
|---------|---------|---------|---------|---------|
|Tipo de disco   |SSD   |SSD   |SSD   |HDD   |
|Cenário   |Cargas de trabalho intensivas em IO, tais como [SAP HANA,](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)bases de dados de topo (por exemplo, SQL, Oracle) e outras cargas de trabalho pesadas de transações.   |Cargas de trabalho confidenciais de produção e de desempenho   |Servidores web, aplicações empresariais levemente utilizadas e dev/teste   |Backup, acesso não crítico e pouco frequente   |
|Tamanho máximo do disco   |65.536 gibibyte (GiB)    |32.767 GiB    |32.767 GiB   |32.767 GiB   |
|Potência máxima   |2\.000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Max IOPS   |160,000    |20,000   |6,000   |2,000   |

## <a name="ultra-disk"></a>Disco ultra

Os discos ultra Azure fornecem alta entrada, iOPS elevados e armazenamento consistente de disco de baixa latência para VMs Azure IaaS. Alguns benefícios adicionais dos discos ultra incluem a capacidade de alterar dinamicamente o desempenho do disco, juntamente com as suas cargas de trabalho, sem a necessidade de reiniciar as suas máquinas virtuais (VM). Os discos ultra são adequados para cargas de trabalho intensivas de dados, tais como SAP HANA, bases de dados de topo e cargas de trabalho pesadas de transações. Os discos ultra só podem ser utilizados como discos de dados. Recomendamos a utilização de SSDs premium como discos OS.

### <a name="performance"></a>Desempenho

Ao fornecer um disco ultra, pode configurar de forma independente a capacidade e o desempenho do disco. Os discos ultra vêm em vários tamanhos fixos, que variam de 4 GiB até 64 TiB, e apresentam um modelo de configuração de desempenho flexível que lhe permite configurar iOPS e produção de forma independente.

Algumas capacidades-chave dos discos ultra são:

- Capacidade do disco: A capacidade dos discos ultra varia de 4 GiB até 64 TiB.
- IOPS do disco: Os discos ultra suportam os limites iops de 300 IOPS/GiB, até um máximo de 160 K IOPS por disco. Para alcançar o IOPS que forprovisionou, certifique-se de que os IOPS de disco selecionados são inferiores ao limite de IOPS vM. O IOPS mínimo garantido por disco é de 2 IOPS/GiB, com um mínimo global de 100 IOPS. Por exemplo, se tiver um disco ultra 4 GiB, terá um mínimo de 100 IOPS, em vez de oito IOPS.
- Entrada em disco: Com discos ultra, o limite de entrada de um único disco é de 256 KiB/s para cada IOPS provisionado, até um máximo de 2000 MBps por disco (onde mBps = 10^6 Bytes por segundo). O rendimento mínimo garantido por disco é de 4KiB/s para cada IOPS provisionado, com um mínimo global de base de 1 MBps.
- Suporte de discos ultra ajustando os atributos de desempenho do disco (IOPS e entrada) no tempo de execução sem separar o disco da máquina virtual. Uma vez emitida uma operação de redimensionamento de desempenho do disco num disco, pode levar até uma hora para que a alteração realmente faça efeito. Há um limite de quatro operações de redimensionamento de desempenho durante uma janela de 24 horas. É possível que uma operação de redimensionação de desempenho falhe devido à falta de capacidade de largura de banda de desempenho.

### <a name="disk-size"></a>Tamanho do disco

|Tamanho do disco (gib)  |Tampa iOPS  |Tampa de entrada (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1\.024-65.536 (tamanhos nesta gama aumentando em incrementos de 1 TiB)     |160,000         |2,000         |

### <a name="ga-scope-and-limitations"></a>Âmbito e limitações ga

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
