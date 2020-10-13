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
ms.openlocfilehash: e7e3c2eb058d3549f2e1a10ffacf01fd354fbd47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651059"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Problemas de capacidade de resolução de problemas

Este artigo descreve resoluções para questões que poderá ter ao gerir os pools de capacidade. 

## <a name="error-conditions-and-resolutions"></a>Condições e resoluções de erros 

|     Condição de erro    |     Resolução    |
|-|-|
| Problemas que criam um pool de capacidades |  Certifique-se de que a contagem de capacidade não excede o limite. Consulte [os limites de recursos para ficheiros Azure NetApp](azure-netapp-files-resource-limits.md).  Se a contagem for inferior ao limite e ainda tiver problemas, preencha um bilhete de apoio e especifique o nome do pool de capacidade. |
| Problemas eliminando um pool de capacidade  |  Certifique-se de que remove todos os volumes e instantâneos do Azure NetApp Files na subscrição onde está a tentar eliminar o pool de capacidade. <br> Se já removeu todos os volumes e instantâneos e ainda não consegue apagar o pool de capacidade, poderão ainda existir referências aos recursos sem aparecer no portal. Neste caso, preencha um bilhete de apoio e especifique que executou os passos acima recomendados. |
| A criação ou modificação de volume falha com `Requested throughput not available` erro | A produção disponível para um volume é determinada pelo tamanho da sua capacidade e pelo nível de serviço. Se não tiver produção suficiente, deverá aumentar o tamanho da piscina ou ajustar a potência de volume existente. | 

## <a name="next-steps"></a>Passos seguintes  

* [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Gerir um conjunto de capacidade QoS manual](manage-manual-qos-capacity-pool.md)
