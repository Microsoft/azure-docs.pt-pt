---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 12/12/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3ad66d235e00d9866794ca90be9934e174bf4102
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554139"
---
**Discos geridos de HDD padrão**

| Tipo de disco Standard  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60*             | S70*             | S80*             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Tamanho do disco em GiB          | 32             | 64             | 128            | 256  | 512            | 1,024    | 2,048     | 4,095    | 8,192     | 16,384     | 32,767     |
| IOPs por disco       | Até 500              | Até 500              | Até 500              | Até 500 | Até 500              | Até 500              | Até 500             | Até 500              | Até 1.300              | Até 2000              | Até 2000              |
| Débito por disco | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg | Até 60 MiB/seg| Até 300 MiB/seg | Até 500 MiB/seg | Até 500 MiB/seg |

**Discos geridos de Standard SSD**

| Tipo de disco SSD Standard | E4                | E6                | E10               | E15               | E20             | E30              | E40              | E50              | E60*             | E70*             | E80*             |
|------------------------|-------------------|-------------------|-------------------|-------------------|-----------------|------------------|------------------|------------------|-------------------|-------------------|-------------------|
| Tamanho do disco em GiB       | 32                | 64                | 128               | 256               | 512             | 1,024            | 2,048            | 4,095            | 8,192             | 16,384            | 32,767            |
| IOPs por disco          | Até 120         | Até 240         | Até 500         | Até 500         | Até 500       | Até 500        | Até 500        | Até 500        | Até 1.300       | Até 2000       | Até 2000       |
| Débito por disco    | Até 25 MB/seg   | Até 50 MB/seg   | Até 60 MB/seg   | Até 60 MB/seg   | Até 60 MB/seg | Até 60 MB/seg  | Até 60 MB/seg  | Até 60 MB/seg  | Até 300 MiB/seg | Até 500 MiB/seg | Até 500 MiB/seg |

**Premium SSD discos geridos: Limites de por disco**

| Tipo de disco premium  | P4               | P6               | P10             | P15 | P20              | P30              | P40              | P50              | P60*             | P70*             | P80*             |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Tamanho do disco em GiB           | 32             | 64             | 128            | 256  | 512            | 1,024    | 2,048     | 4,095    | 8,192     | 16,384     | 32,767     |
| IOPs por disco       | Até 120 | Até 240              | Até 500              | Até 1,100 | Até 2,300              | Até 5000              | Até 7.500             | Até 7.500              | Até 12.500              | Máximo de 15 000              | Até 20 000              |
| Débito por disco | Até 25 MiB/seg | Máximo de 50 MiB/seg | Até 100 MiB/seg | Até 125 MiB/seg | Até 150 MiB/seg | Até 200 MiB/seg | Até 250 MiB/seg | Até 250 MiB/seg| Até 480 MiB/seg | Até 750 MiB/seg | Até 750 MiB/seg |

**Premium SSD discos geridos: Limites de por VM**

| Recurso | Limite predefinido |
| --- | --- |
| IOPS máximo por VM |80.000 IOPS com GS5 VM |
| Débito máximo por VM |2.000 MB/s com GS5 VM |
