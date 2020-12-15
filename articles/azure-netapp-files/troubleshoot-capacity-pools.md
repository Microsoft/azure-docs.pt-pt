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
ms.date: 11/06/2020
ms.author: b-juche
ms.openlocfilehash: a07898692c8af70fab0d2634ed1513f0f3666a4d
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505465"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Resolver problemas dos conjuntos de capacidade

Este artigo descreve resoluções para problemas que você pode ter ao gerir piscinas de capacidade, incluindo a operação de mudança de piscina. 

## <a name="issues-managing-a-capacity-pool"></a>Problemas de gestão de um pool de capacidades 

|     Condição de erro    |     Resolução    |
|-|-|
| Problemas que criam um pool de capacidades |  Certifique-se de que a contagem de capacidade não excede o limite. Consulte [os limites de recursos para ficheiros Azure NetApp](azure-netapp-files-resource-limits.md).  Se a contagem for inferior ao limite e ainda tiver problemas, preencha um bilhete de apoio e especifique o nome do pool de capacidade. |
| Problemas eliminando um pool de capacidade  |  Certifique-se de que remove todos os volumes e instantâneos do Azure NetApp Files na subscrição onde está a tentar eliminar o pool de capacidade. <br> Se já removeu todos os volumes e instantâneos e ainda não consegue apagar o pool de capacidade, poderão ainda existir referências aos recursos sem aparecer no portal. Neste caso, preencha um bilhete de apoio e especifique que executou os passos acima recomendados. |
| A criação ou modificação de volume falha com `Requested throughput not available` erro | A produção disponível para um volume é determinada pelo tamanho da sua capacidade e pelo nível de serviço. Se não tiver produção suficiente, deverá aumentar o tamanho da piscina ou ajustar a potência de volume existente. | 

## <a name="issues-when-changing-the-capacity-pool-of-a-volume"></a>Problemas ao alterar o pool de capacidade de um volume 

> [!IMPORTANT] 
> O [Dynamically alterar o nível de serviço de um registo de](dynamic-change-volume-service-level.md) pré-visualização pública de volume está suspenso até novo aviso.

|     Condição de erro    |     Resolução    |
|-|-|
| Não é permitida a alteração da capacidade de um volume. | Pode ainda não estar autorizado a utilizar esta funcionalidade. <br> A funcionalidade para mover um volume para outro pool de capacidade está atualmente em pré-visualização. Se estiver a utilizar esta função pela primeira vez, tem de registar a funcionalidade primeiro e definir `-FeatureName ANFTierChange` . Consulte as etapas de registo em [Dinamicamente alterar o nível de serviço de um volume](dynamic-change-volume-service-level.md). |
| O tamanho da piscina é muito pequeno para o tamanho total do volume. |  O erro resulta do facto de o pool de capacidade de destino não ter a capacidade disponível para o volume que está a ser movido.  <br> Aumente o tamanho da piscina de destino, ou escolha outra piscina maior.  Consulte [redimensionar uma piscina de capacidade ou um volume](azure-netapp-files-resize-capacity-pools-or-volumes.md).   |
|  A mudança de piscina não pode ser concluída porque um volume chamado `'{source pool name}'` já existe na piscina alvo `'{target pool name}'` | Este erro ocorre porque o volume com o mesmo nome já existe no pool de capacidade alvo.  Selecione outro pool de capacidade que não tenha um volume com o mesmo nome.   | 

## <a name="next-steps"></a>Passos seguintes  

* [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Gerir um conjunto de capacidade QoS manual](manage-manual-qos-capacity-pool.md)
* [Alterar o nível de serviço de um volume de forma dinâmica](dynamic-change-volume-service-level.md)
* [Redimensionar um conjunto de capacidade ou um volume](azure-netapp-files-resize-capacity-pools-or-volumes.md)
