---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5a811620de8336abd3e0df6d72db761ce18b2b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86230995"
---
Toda a última geração de tamanhos VM suporta encriptação no hospedeiro:

|Tipo  |Não suportado  |Suportado  |
|---------|---------|---------|
|Fins gerais     | Dv3, Dav4, Dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dasv4        |
|Com otimização de computação     |         | Fsv2        |
|Com otimização de memória     | Ev3        | DSv2, Esv3, M, Mv2, Easv4        |
|Com otimização de armazenamento     |         | Ls, Lsv2 (discos NVMe não encriptados)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (pré-visualização)        |
|Computação de elevado desempenho     | H        | HB, HC, HBv2        |
|Gerações anteriores     | F, A, D, L, G        | DS, GS, Fs, NVv2        |

A atualização do tamanho do VM resultará em validação para verificar se o novo tamanho VM suporta a funcionalidade EncryptionAtHost.
