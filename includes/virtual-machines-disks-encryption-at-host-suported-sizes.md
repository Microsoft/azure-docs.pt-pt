---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e8ec7faf1562381288aeef630bf2076ce413017a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531535"
---
Toda a última geração de tamanhos VM suporta encriptação no hospedeiro:

|Tipo  |Não suportado  |Suportado  |
|---------|---------|---------|
|Fins gerais     | Dv3, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dav4, Dasv4        |
|Otimizado para computação     |         | Fsv2        |
|Otimizada para memória     | Ev3        | DSv2, Esv3, M, Mv2, Eav4, Easv4        |
|Otimizada para armazenamento     |         | Ls, Lsv2 (discos NVMe não encriptados)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (pré-visualização)        |
|Computação de elevado desempenho     | H        | HB, HC, HBv2        |
|Gerações anteriores     | F, A, D, L, G        | DS, GS, Fs, NVv2        |
