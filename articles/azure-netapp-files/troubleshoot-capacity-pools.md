---
title: Problemas de capacidade de resolução de problemas para ficheiros Azure NetApp Microsoft Docs
description: Descreve potenciais problemas que poderá ter ao gerir os pools de capacidade e fornece soluções para os problemas.
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
ms.topic: troubleshooting
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 0b5558501042dd7816202ea05b3a332b23400ff4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91345504"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Problemas de capacidade de resolução de problemas

Este artigo descreve resoluções para questões que poderá ter ao gerir os pools de capacidade. 

## <a name="issues-creating-a-capacity-pool"></a>Problemas que criam um pool de capacidades

Certifique-se de que a contagem de capacidade não excede o limite. Consulte [os limites de recursos para ficheiros Azure NetApp](azure-netapp-files-resource-limits.md).  Se a contagem for inferior ao limite e ainda tiver problemas, preencha um bilhete de apoio e especifique o nome do pool de capacidade.

## <a name="issues-deleting-a-capacity-pool"></a>Problemas eliminando um pool de capacidade

Certifique-se de que removeu todos os volumes e instantâneos do Azure NetApp Files na subscrição onde está a tentar eliminar o pool de capacidade.   

Se já removeu todos os volumes e instantâneos e ainda não consegue apagar o pool de capacidade, ainda podem existir referências aos recursos sem aparecer no portal. Neste caso, preencha um bilhete de apoio e especifique que executou os passos acima recomendados. 

## <a name="volume-creation-or-modification-fails-with-requested-throughput-not-available-error"></a>Criação ou modificação de volume falha com erro de "Reprodução solicitada não disponível"

A produção disponível para um volume é determinada pelo tamanho da sua capacidade e pelo nível de serviço. Se não tiver produção suficiente, deverá aumentar o tamanho da piscina ou ajustar a potência de volume existente.


## <a name="next-steps"></a>Passos seguintes  

* [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Gerir um pool manual de capacidade QoS](manage-manual-qos-capacity-pool.md)
