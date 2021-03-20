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
ms.openlocfilehash: e5a811620de8336abd3e0df6d72db761ce18b2b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86230995"
---
Toda a última geração de tamanhos VM suporta encriptação no hospedeiro:

|Tipo  |Não suportado  |Suportado  |
|---------|---------|---------|
|Fins gerais     | Dv3, Dav4, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dasv4        |
|Otimizado para computação     |         | Fsv2        |
|Otimizada para memória     | Ev3        | DSv2, Esv3, M, Mv2, Easv4        |
|Otimizada para armazenamento     |         | Ls, Lsv2 (discos NVMe não encriptados)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (pré-visualização)        |
|Computação de elevado desempenho     | H        | HB, HC, HBv2        |
|Gerações anteriores     | F, A, D, L, G        | DS, GS, Fs, NVv2        |

A atualização do tamanho do VM resultará em validação para verificar se o novo tamanho VM suporta a funcionalidade EncryptionAtHost.
