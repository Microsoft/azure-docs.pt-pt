---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 16a0fa29c067262f1794528b16abfce662d05605
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748992"
---
<!-- F-series, Fs-series* -->

Os tamanhos de VM otimizados para computação têm uma alta taxa de CPU para memória. Esses tamanhos são bons para servidores Web de tráfego médio, dispositivos de rede, processos em lote e servidores de aplicativos. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs. Ele também inclui informações sobre a taxa de transferência de armazenamento e a largura de banda de rede para cada tamanho neste agrupamento.

A série Fsv2 é baseada no processador Intel® Xeon® Platinum 8168. Ele apresenta uma velocidade de clock de Turbo principal de 3,4 GHz e uma frequência máxima de Turbo de núcleo único de 3,7 GHz. As instruções Intel® AVX-512 são novas em processadores escalonáveis da Intel. Essas instruções fornecem um aumento de desempenho 2X para cargas de trabalho de processamento de vetores em operações de ponto flutuante de precisão única e dupla. Em outras palavras, eles são realmente rápidos para qualquer carga de trabalho computacional.

Em um preço de lista inferior por hora, a série Fsv2 é o melhor valor em preço-desempenho no portfólio do Azure com base na ACU (unidade de computação do Azure) por vCPU.

## <a name="fsv2-series-sup1sup"></a>Série Fsv2 <sup>1</sup>

ACU: 195 - 210

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

| Tamanho             | vCPUs | Memória: GiB | Armazenamento (SSD) temporário em GiB | Máximo de discos de dados | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000/31 (32)           | 3200/47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000/63 (64)           | 6400/95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800 / 190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000/255 (256)        | 25600 / 380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000/512 (512)        | 51200/750              | 8 / 14000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96000/768 (768)        | 76800 / 1100             | 8 / 21000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | 80000 / 1100             | 8 / 28000              |
| <sup>Standard_F72s_v2 2,&nbsp;3</sup> | 72 | 144 | 576         | 32             | 144000 / 1152 (1520)     | 80000 / 1100             | 8 / 30000              |

<sup>1</sup> o recurso de VMs da série Fsv2 Intel® tecnologia Hyper-Threading.

<sup>2</sup> o uso de mais de 64 vCPU exige um destes sistemas operacionais convidados com suporte:
- Windows Server 2016 ou posterior
- Ubuntu 16, 4 LTS ou posterior, com kernel ajustado do Azure (kernel 4,15 ou posterior)
- SLES 12 SP2 ou posterior
- RHEL ou CentOS versão 6.7 a 6.10, com o pacote LIS fornecido pela Microsoft 4.3.1 (ou mais tarde) instalado
- RHEL ou CentOS versão 7,3, com o pacote LIS fornecido pela Microsoft 4.2.1 (ou posterior) instalado
- RHEL ou CentOS versão 7,6 ou posterior
- Oracle Linux com UEK4 ou posterior
- Debian 9 com o kernel de backports, Debian 10 ou posterior
- CoreOS com um kernel 4,14 ou posterior

<sup>3</sup> a instância é isolada em hardware dedicado a um único cliente.
