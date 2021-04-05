---
title: Matriz de suporte para a azure partilha de ficheiros
description: Fornece um resumo das configurações e limitações de suporte ao fazer o backup das ações de ficheiros Azure.
ms.topic: conceptual
ms.date: 5/07/2020
ms.custom: references_regions
ms.openlocfilehash: 38c066c20399b39d676fb0c25aa158993258b979
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370993"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matriz de suporte para cópia de segurança da partilha de ficheiros Azure

Pode utilizar o [serviço Azure Backup](./backup-overview.md) para fazer backup das ações de ficheiros Azure. Este artigo resume as definições de suporte quando faz backup de ações de ficheiros Azure com a Azure Backup.

> [!NOTE]
> A Azure Backup não suporta ações da NFS.

## <a name="supported-regions"></a>Regiões suportadas

### <a name="ga-regions-for-azure-file-shares-backup"></a>Regiões de GA para ações de ficheiros Azure ações de backup

A azure file shares backup está disponível em todas as **regiões, exceto:** Alemanha Central (Soberano), Alemanha Nordeste (Soberano), China Leste, China Leste 2, China Norte, China Norte 2, EUA Gov Iowa

## <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

| Detalhes da conta de armazenamento | Suporte                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Tipo de Conta            | A Azure Backup suporta ações de ficheiros Azure presentes em v1, v2 de uso geral e contas de armazenamento de tipo de armazenamento de ficheiros |
| Desempenho              | A Azure Backup suporta ações de ficheiros em contas standard e Premium Storage |
| Replicação              | As ações de ficheiros Azure em contas de armazenamento com qualquer tipo de replicação são suportadas |
| Firewall ativada         | As ações de ficheiros Azure em contas de armazenamento com regras de Firewall que permitem ao Microsoft Azure Services aceder à conta de armazenamento são suportadas|

## <a name="supported-file-shares"></a>Ações de ficheiros suportadas

| Tipo de partilha de ficheiros                                   | Suporte   |
| -------------------------------------------------- | --------- |
| Standard                                           | Suportado |
| Grande                                              | Suportado |
| Premium                                            | Suportado |
| Ações de ficheiros ligadas ao serviço de sincronização de ficheiros Azure | Suportado |

## <a name="protection-limits"></a>Limites de proteção

| Definição                                                      | Limite |
| ------------------------------------------------------------ | ----- |
| Número máximo de ações de ficheiros que podem ser protegidas por abóbada por dia| 200   |
| Número máximo de contas de armazenamento que podem ser registadas por cofre por dia | 50    |
| Número máximo de ações de ficheiros que podem ser protegidas por cofre | 2000   |
| Número máximo de contas de armazenamento que podem ser registadas por cofre | 200   |

## <a name="backup-limits"></a>Limites de backup

| Definição                                      | Limite |
| -------------------------------------------- | ----- |
| Número máximo de backups a pedido por dia | 10   |
| Número máximo de backups programados por dia | 1     |

## <a name="restore-limits"></a>Restaurar limites

| Definição                                                      | Limite   |
| ------------------------------------------------------------ | ------- |
| Número máximo de restauros por dia                           | 10      |
| Número máximo de ficheiros por restauro                         | 99      |
| Tamanho máximo recomendado de restauração por restauro para grandes partilhas de ficheiros | 15 TiB |

## <a name="retention-limits"></a>Limites de retenção

| Definição                                                      | Limite    |
| ------------------------------------------------------------ | -------- |
| Máximo total de pontos de recuperação por partilha de ficheiros em qualquer ponto do tempo | 200      |
| Retenção máxima do ponto de recuperação criado por backup a pedido | 10 anos |
| Retenção máxima de pontos de recuperação diários (instantâneos) por partilha de ficheiros| 200 dias |
| Retenção máxima de pontos de recuperação semanais (instantâneos) por partilha de ficheiros | 200 semanas |
| Retenção máxima de pontos de recuperação mensais (snapshots) por partilha de ficheiros | 120 meses |
| Retenção máxima de pontos de recuperação anual (snapshots) por partilha de ficheiros | 10 anos |

## <a name="supported-restore-methods"></a>Métodos de restauro apoiados

| Restore method (Método Restore)     | Detalhes                                                      |
| ------------------ | ------------------------------------------------------------ |
| Restauro de ações completas | Pode restaurar a partilha completa do ficheiro para a localização original ou alternativa |
| Restauro do nível do item | Pode restaurar ficheiros e pastas individuais para a localização original ou alternativa |

## <a name="next-steps"></a>Passos seguintes

* Saiba como [apoiar as ações de ficheiros da Azure](backup-afs.md)
* Saiba como restaurar as [ações de ficheiros Azure](restore-afs.md)
* Saiba como gerir as [cópias de segurança de partilha de ficheiros Azure](manage-afs-backup.md)
