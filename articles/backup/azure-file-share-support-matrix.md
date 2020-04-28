---
title: Matriz de suporte para cópia de segurança de partilha de ficheiros Azure
description: Fornece um resumo das definições de suporte e limitações ao apoiar as ações de ficheiros Azure.
ms.topic: conceptual
ms.date: 1/26/2020
ms.openlocfilehash: 03190c6b83fceec88442583a57ff46756cfe6221
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195694"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matriz de suporte para backup de partilha de ficheiros Azure

Pode utilizar o [serviço de backup Azure](https://docs.microsoft.com/azure/backup/backup-overview) para fazer backup as ações de ficheiros Azure. Este artigo resume as definições de suporte quando faz backup as partilhas de ficheiros Azure com a Azure Backup.

## <a name="supported-geos"></a>GEOS suportado

A cópia de segurança para as ações de ficheiros Azure está disponível no seguinte GEOS:

| Regiões de Ga | Regiões apoiadas, mas não GA                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Austrália Sudeste (ASE), Canadá Central (CNC), West Central US (WCUS), West US 2 (WUS 2), Índia Sul (INS), North Central US (NCUS), Japão Leste (JPE), Brasil Sul (BRS), Sudeste Asiático (SEA)                                                     |Austrália Leste (AE), Canadá Leste (CE), Leste asiático (EA), Leste dos EUA (EUS2), Leste dos EUA 2 (EUS2), Japão Oeste (JPW), Índia Central (INC), Coreia Central (KRC), Coreia do Sul (KRS), Europa do Norte (NE)), South Central US (SCUS), UK South (UKS), UK West (UKW), West Europe (WE), West US (WUS), US Gov Arizona (UGA), EUA Gov Texas (UGT), EUA Gov Virginia (UGV), Australia Central (ACL) , Índia Oeste (INW), África do Sul Norte (SAN), UAE North (UAN), France Central (FRC), Alemanha Norte (GN), Alemanha West Central (GWC), África do Sul Oeste (SAW), UAE Central (UAC), Noruega Leste (NWE), Noruega Oeste (NWW), Suíça Norte (SZN), Centro dos EUA (CUS)           |

## <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

| Detalhes da conta de armazenamento | Suporte                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Tipo de conta            | A Azure Backup suporta as ações de ficheiros Azure presentes em contas de armazenamento de tipo v1 de uso geral, v2 de uso geral e tipo de partilha de ficheiros |
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
