---
title: Níveis de serviço para ficheiros de NetApp do Azure | Documentos da Microsoft
description: Descreve o desempenho de débito para os níveis de serviço de ficheiros do Azure NetApp.
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 1f9c427045c9d42f6a11cc4bcc798cfc47a4428c
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523108"
---
# <a name="service-levels-for-azure-netapp-files"></a>Níveis de serviços do Azure NetApp Files
Níveis de serviço são um atributo de um conjunto de capacidade. Níveis de serviço são definidos e diferenciados pelo débito máximo permitido para um volume no agrupamento de capacidade com base na quota que é atribuído ao volume.

## <a name="supported-service-levels"></a>Níveis de serviço com suporte

Os ficheiros NetApp do Azure oferece suporte a três níveis de serviço: *Ultra*, *Premium*, e *padrão*. 

* <a name="Ultra"></a>Armazenamento Ultra

    A camada de armazenamento Ultra fornece até 128 MiB/s de débito por 1 TiB de quota de volume atribuído. 

* <a name="Premium"></a>Armazenamento Premium

    A camada de armazenamento Premium fornece até 64 MiB/s de débito por 1 TiB de quota de volume atribuído. 

* <a name="Standard"></a>Armazenamento Standard

    O escalão de armazenamento Standard oferece até 16 MiB/s de débito por 1 TiB de quota de volume atribuído.

## <a name="throughput-limits"></a>Limites de débito

O limite de taxa de transferência para um volume é determinado pela combinação dos seguintes fatores:
* O nível de serviço do conjunto de capacidade à qual pertence o volume
* A quota atribuída ao volume  

Esse conceito é ilustrado no diagrama abaixo:

![Ilustração de nível de serviço](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

No exemplo 1 acima, um volume a partir de um agrupamento de capacidade com o escalão de armazenamento Premium atribuída 2 TiB de quota será atribuído um limite de taxa de transferência de 128 MiB/s (2 TiB * 64 MiB/s). Este cenário aplica-se independentemente do tamanho do conjunto de capacidade ou o consumo de volume real.

No exemplo 2 acima, um volume a partir de um agrupamento de capacidade com o escalão de armazenamento Premium que é atribuído a 100 GiB de quota será atribuído um limite de taxa de transferência de 6,25 MiB/s (0.09765625 TiB * 64 MiB/s). Este cenário aplica-se independentemente do tamanho do conjunto de capacidade ou o consumo de volume real.

## <a name="next-steps"></a>Passos Seguintes

- Consulte a [ficheiros do Azure NetApp página de preços](https://azure.microsoft.com/pricing/details/storage/netapp/) pelo preço de níveis de serviço diferentes
- Ver [modelo de custos para os ficheiros do Azure NetApp](azure-netapp-files-cost-model.md) para o cálculo de consumo de capacidade de um conjunto de capacidade 
- [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)