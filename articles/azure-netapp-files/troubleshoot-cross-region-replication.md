---
title: Resolução de problemas de replicação transversal para ficheiros Azure NetApp Microsoft Docs
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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 3aaa5d2bc6fdbda0d1db212539c719aa65cae61b
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90709013"
---
# <a name="troubleshoot-cross-region-replication"></a>Resolução de problemas replicação transversal

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

## <a name="errors-resyncing-volume"></a>Erros no volume de ressíduo

|     Mensagem de Erro    |     Resolução    |
|-|-|
|     `Volume Replication is in invalid status: (Mirrored|Uninitialized) for operation: 'ResyncReplication'`     |     Validar que a replicação do volume está no estado "quebrado".    |

## <a name="errors-deleting-snapshot"></a>Erros que apagam o instantâneo 

|     Mensagem de Erro    |     Resolução    |
|-|-|
|     `Snapshot   cannot be deleted, parent volume is a Data Protection volume with a   replication object`    |     Valide que quebrou a replicação do volume se quiser eliminar esta imagem.    |
|     `Cannot delete   volume replication generated snapshot`    |     Não é permitida a eliminação de instantâneos de linha de base de replicação.    |

## <a name="next-steps"></a>Passos seguintes  

* [Replicação inter-região](cross-region-replication-introduction.md)
* [Requisitos e considerações para a utilização da replicação entre regiões](cross-region-replication-requirements-considerations.md)
* [Criar olhando de replicação](cross-region-replication-create-peering.md)
* [Mostrar estado de saúde da relação de replicação](cross-region-replication-display-health-status.md)
* [Gerir a recuperação após desastre](cross-region-replication-manage-disaster-recovery.md)
* [Resolução de problemas replicação transversal](troubleshoot-cross-region-replication.md)
