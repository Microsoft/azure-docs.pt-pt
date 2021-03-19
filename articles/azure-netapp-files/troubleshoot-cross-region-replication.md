---
title: Resolução de problemas de replicação de regiões para ficheiros Azure NetApp | Microsoft Docs
description: Descreve mensagens de erro e resoluções que podem ajudá-lo a resolver problemas de replicação de regiões cruzadas para ficheiros Azure NetApp.
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
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: d3d944646689e9e6189b0343e8bf67c8fb0abcbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590930"
---
# <a name="troubleshoot-cross-region-replication"></a>Resolver problemas da replicação entre regiões

Este artigo descreve mensagens de erro e resoluções que podem ajudá-lo a resolver problemas de replicação de regiões cruzadas para ficheiros Azure NetApp. 

## <a name="errors-creating-replication"></a>Erros que criam replicação  

|     Mensagem de Erro    |     Resolução    |
|-|-|
|     `Volume {0} cannot   be used as source because it is already in replication`    |     Não é possível criar uma replicação com um volume de origem que já se encontra numa relação de replicação de dados.    |
|     `Peered region   '{0}' is not accepted`    |     Está a tentar criar uma réplica entre regiões não espreitadas.    |
|     `RemoteVolumeResource   '{0}' of wrong type '{1}'`    |     Validar que o ID de recurso remoto é um ID de recursos de volume.    |

## <a name="errors-authorizing-volume"></a>Erros que autorizam o volume  

|     Mensagem de Erro    |     Resolução    |
|-|-|
|     `Missing value   for 'AuthorizeSourceReplication'`    |     O   `RemoteResourceID` que está em falta ou é inválido do pedido de UI ou API (corrigir mensagem de erro).    |
|     `Missing value   for 'RemoteVolumeResourceId'`    |     O   `RemoteResourceID` pedido de UI ou API está em falta ou é inválido.    |
|     `Data   Protection volume not found for RemoteVolumeResourceId: {remoteResourceId}`    |     Validar se   `RemoteResourceID` estiver correto ou existir para o utilizador.    |
|     `Remote volume   '{0}' is not configured for replication`    |     O volume de destino não é um volume de proteção de dados.    |
|     `Remote volume   '{0}' does not have source volume '{1}' as RemoteVolumeResourceId`    |     O volume de proteção de dados não tem este volume de origem no seu ID de recursos remotos (foi introduzido o ID de origem errada).    |
|     `The   destination volume replication creation failed (message: {0})`    |     Este erro indica um erro do servidor. Suporte de contato.    |

## <a name="errors-deleting-replication"></a>Erros que apagam a replicação

|     Mensagem de Erro    |     Resolução    |
|-|-|
|     `Replication   cannot be deleted, mirror state needs to be in status: Broken before deleting`    |     Validar que a replicação foi quebrada ou não é insinibida e inativa (inicialização falhada).    |
|     `Cannot delete   source replication`    |     Não é permitido eliminar a replicação do lado da fonte. Certifique-se de que está a apagar a replicação do lado do destino.    |

## <a name="errors-deleting-volume"></a>Erros que apagam o volume

|     Mensagem de Erro    |     Resolução    |
|-|-|
| `Volume is a member of an active volume replication relationship`  |  Elimine a replicação antes de apagar o volume. Ver [Eliminar replicações](cross-region-replication-delete.md). Esta operação requer que quebre o espreitamento antes de apagar a replicação para o volume. |
| `Volume with replication cannot be deleted`  |  Elimine a replicação antes de apagar o volume. Ver [Eliminar replicações](cross-region-replication-delete.md). Esta operação requer que quebre o espreitamento antes de apagar a replicação para o volume. 

## <a name="errors-resyncing-volume"></a>Erros no volume de ressíduo

|     Mensagem de Erro    |     Resolução    |
|-|-|
|     `Volume Replication is in invalid status: (Mirrored|Uninitialized) for operation: 'ResyncReplication'`     |     Validar que a replicação do volume está no estado "quebrado".    |

## <a name="errors-deleting-snapshot"></a>Erros que apagam o instantâneo 

|     Mensagem de Erro    |     Resolução    |
|-|-|
|     `Snapshot   cannot be deleted, parent volume is a Data Protection volume with a   replication object`    |     Valide que quebrou a replicação do volume se quiser eliminar esta imagem.    |
|     `Cannot delete   volume replication generated snapshot`    |     Não é permitida a eliminação de instantâneos de linha de base de replicação.    |

## <a name="errors-resizing-volumes"></a>Erros de redimensionamento volumes

|     Mensagem de Erro    |     Resolução    |
|-|-|
|   Tentar redimensionar um volume de origem está a falhar com o erro `"PoolSizeTooSmall","message":"Pool size too small for total volume size."`  |  Certifique-se de que tem espaço suficiente nas piscinas de capacidade tanto para a fonte como para os volumes de destino da replicação entre regiões. Quando redimensiona o volume de origem, o volume de destino é automaticamente redimensionado. Mas se a capacidade de alojamento do volume de destino não tiver espaço suficiente, o redimensionamento da fonte e dos volumes de destino falhará. Consulte [o Redimensionar um volume de destino de replicação transversal](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume) para obter mais detalhes.   |

## <a name="next-steps"></a>Passos seguintes  

* [Replicação entre regiões](cross-region-replication-introduction.md)
* [Requisitos e considerações para a utilização da replicação entre regiões](cross-region-replication-requirements-considerations.md)
* [Criar replicação de volume](cross-region-replication-create-peering.md)
* [Apresentar o estado de funcionamento da relação de replicação](cross-region-replication-display-health-status.md)
* [Gerir a recuperação após desastre](cross-region-replication-manage-disaster-recovery.md)
* [Redimensionar um volume de destino de replicação transversal](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Resolver problemas da replicação entre regiões](troubleshoot-cross-region-replication.md)
