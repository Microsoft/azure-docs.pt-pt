---
title: Matriz de suporte para cópia de segurança de partilha de ficheiros Azure
description: Fornece um resumo das definições de suporte e limitações ao apoiar as ações de ficheiros Azure.
ms.topic: conceptual
ms.date: 1/26/2020
ms.openlocfilehash: e74d04cf8ae9010a860b8467d0de771524bd3f3a
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103207"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matriz de suporte para backup de partilha de ficheiros Azure

Pode utilizar o [serviço de backup Azure](https://docs.microsoft.com/azure/backup/backup-overview) para fazer backup as ações de ficheiros Azure. Este artigo resume as definições de suporte quando faz backup as partilhas de ficheiros Azure com a Azure Backup.

## <a name="supported-geos"></a>GEOS suportado

A cópia de segurança para as ações de ficheiros Azure está disponível no seguinte GEOS:

| Regiões de Ga | Regiões apoiadas, mas não GA                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Austrália Leste (AE), Canadá Central (CNC), West Central US (WCUS), Índia Sul (INS), Norte Central EUA (NCUS), Japão Leste (JPE), Brasil Sul (BRS)                                                     |Austrália Sudeste (ASE), Canadá Leste (CE), Centro dos EUA (CUS), Ásia Oriental (EA), Leste dos EUA (EUS2), Japão Oeste (JPW), Índia Central (INC), Coreia Central (KRC), Coreia do Sul (KRS) ) ),North Europe (NE), South Central US (SCUS),South East Asia (SEA), UK South (UKS), UK West (UKW), West Europe (WE),West US (WUS), US Gov Arizona (UGA),US Gov Texas (UGT) (UGV), Austrália Central (ACL), Índia Ocidental (INW), África do Sul Norte (SAN), UAE North (UAN), France Central (FRC), Alemanha Norte (GN), Alemanha West Central (GWC), África Do Sul Oeste (SAW), UAE Central (UAC), Noruega Leste (NWE), Noruega West (NWW), Suíça Norte (SZN), West US 2 (WUS 2)             |

## <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

| Detalhes da conta de armazenamento | Suporte                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Tipo de conta            | A Azure Backup suporta as ações de ficheiros Azure em contas de armazenamento v1 e v2 de uso geral |
| Desempenho              | Azure Backup suporta ações de ficheiros em contas standard e premium de armazenamento |
| Replicação              | As ações do Azure em Contas de Armazenamento com qualquer tipo de replicação são suportadas |

## <a name="supported-file-shares"></a>Partilhas de ficheiros suportadas

| Tipo de partilha de ficheiros                                   | Suporte   |
| -------------------------------------------------- | --------- |
| Standard                                           | Suportado |
| Grande                                              | Suportado |
| Premium                                            | Suportado |
| Partilhas de ficheiros ligadas ao serviço de sincronização do Ficheiro Azure | Suportado |

## <a name="protection-limits"></a>Limites de proteção

| Definição                                                      | Limite |
| ------------------------------------------------------------ | ----- |
| Número máximo de ações de ficheiros que podem ser protegidas por dia por cofre | 200   |
| Número máximo de contas de armazenamento que podem ser registadas por cofre por dia | 50    |

## <a name="backup-limits"></a>Limites de backup

| Definição                                      | Limite |
| -------------------------------------------- | ----- |
| Número máximo de backups a pedido por dia | 4     |
| Número máximo de backups programados por dia | 1     |

## <a name="restore-limits"></a>Restaurar limites

| Definição                                                      | Limite   |
| ------------------------------------------------------------ | ------- |
| Número máximo de restauros por dia                           | 10      |
| Número máximo de ficheiros por restauro                         | 10      |
| Tamanho máximo recomendado de restauro por restauração para grandes partilhas de ficheiros | 15 Tib |

## <a name="retention-limits"></a>Limites de retenção

| Definição                                                      | Limite    |
| ------------------------------------------------------------ | -------- |
| Máximo total de pontos de recuperação por ação de ficheiros em qualquer ponto do tempo | 200      |
| Retenção máxima do ponto de recuperação criado por backup a pedido | 10 anos |
| Retenção máxima dos pontos de recuperação diária (instantâneos) por partilha de ficheiros| 200 dias |
| Retenção máxima dos pontos de recuperação semanais (instantâneos) por ação de ficheiros | 200 semanas |
| Retenção máxima dos pontos de recuperação mensais (instantâneos) por ação de ficheiros | 120 meses |
| Retenção máxima dos pontos de recuperação anual (instantâneos) por ação de ficheiros | 10 anos |

## <a name="supported-restore-methods"></a>Métodos de restauro suportados

| Restore method (Método Restore)     | Detalhes                                                      |
| ------------------ | ------------------------------------------------------------ |
| Restauro total de ações | Pode restaurar a parte completa do ficheiro para o local original ou alternativo |
| Restabelecimento do nível do item | Pode restaurar ficheiros e pastas individuais para a localização original ou alternativa |

## <a name="next-steps"></a>Passos seguintes

* Saiba como [apoiar as ações de ficheiros da Azure](backup-afs.md)
* Saiba como restaurar as ações de [ficheiros azure](restore-afs.md)
* Saiba como gerir cópias de [segurança de ficheiros Azure](manage-afs-backup.md)
