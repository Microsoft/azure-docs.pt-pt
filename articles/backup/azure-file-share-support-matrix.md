---
title: Matriz de suporte para cópia de segurança de partilha de ficheiros Azure
description: Fornece um resumo das definições de suporte e limitações ao apoiar as ações de ficheiros Azure.
ms.topic: conceptual
ms.date: 1/26/2020
ms.openlocfilehash: b5bfae9454464bc8d11cef3bd346861e13822046
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838574"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Matriz de suporte para backup de partilha de ficheiros Azure

Pode utilizar o [serviço de backup Azure](https://docs.microsoft.com/azure/backup/backup-overview) para fazer backup as ações de ficheiros Azure. Este artigo resume as definições de suporte quando faz backup as partilhas de ficheiros Azure com a Azure Backup.

## <a name="supported-geos"></a>GEOS suportado

A cópia de segurança para as ações de ficheiros Azure está disponível no seguinte GEOS:

| Regiões de Ga | Regiões apoiadas (como parte da pré-visualização) mas ainda não ga                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Austrália Sudeste (ASE), Canadá Central (CNC), West Central US (WCUS), West US 2 (WUS 2), Índia Sul (INS), North Central US (NCUS), Japão Leste (JPE), Brasil Sul (BRS), Sudeste Asiático (SEA)),Suíça West (SZW), UAE Central (UAC), Noruega Leste (NWE), Índia Oeste (INW), Austrália Central (ACL), Korea Central (KRC), Japão West (JPW), África do Sul Norte (SAN), UK West (UKW) , Coreia do Sul (KRS), Alemanha Norte (GN), Noruega Oeste (NWW), África do Sul Oeste (SAW), Suíça Norte (SZN), Alemanha West Central (GWC), UAE North (UAN), France Central (FRC), Índia Central (INC), Canadá Leste (CNE), Leste Asiático (EA), Austrália Leste (AE), Centro dos EUA (CUS), Oeste DOS EUA (WUS)                                                  |  Leste dos EUA (EUS), Leste dos EUA 2 (EUS2), Norte da Europa (NE), Centro-Sul dos EUA (SCUS), Reino Unido Sul (Reino Unido), Europa Ocidental (WE), EUA Gov Arizona (UGA), EUA Gov Texas (UGT), EUA Gov Virginia (UGV)           |

## <a name="supported-storage-accounts"></a>Contas de armazenamento do Azure

| Detalhes da conta de armazenamento | Suporte                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Tipo de conta            | A Azure Backup suporta as ações de ficheiros Azure presentes nas contas de armazenamento de tipo v1 de uso geral, v2 de propósito geral e tipo de armazenamento de ficheiros |
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
