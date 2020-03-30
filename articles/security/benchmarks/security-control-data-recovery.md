---
title: Controlo de Segurança Azure - Recuperação de Dados
description: Recuperação de dados de controlo de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934498"
---
# <a name="security-control-data-recovery"></a>Controlo de Segurança: Recuperação de dados

Certifique-se de que todos os dados, configurações e segredos do sistema são automaticamente apoiados regularmente.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automáticos regulares

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.1 | 10.1 | Cliente |

Ative a Cópia de Segurança Azure e configure a fonte de backup (VMs Azure, SQL Server ou File Shares), bem como o período de frequência e retenção desejado.

Como ativar o Backup Azure:

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer cópias de segurança de quaisquer chaves geridas pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.2 | 10.2 | Cliente |

Ativar o Backup Azure e os VM(s) de destino, bem como os períodos de frequência e retenção desejados. O cliente de reserva geria as chaves dentro do Cofre de Chaves Azure.

Como ativar o Backup Azure:

https://docs.microsoft.com/azure/backup/

Como apoiar as chaves do cofre em Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.3 | 10.3 | Cliente |

Garantir a capacidade de realizar periodicamente a restauração de dados dos conteúdos dentro do Azure Backup. Se necessário, o teste restaurar a um VLAN isolado. Teste a restauração das chaves geridas pelo cliente.

Como recuperar ficheiros da cópia de segurança da Máquina Virtual Azure:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Como restaurar as chaves do cofre chave em Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.4 | 10.4 | Cliente |

Para cópias de segurança no local, a encriptação inativa de dados é fornecida ao utilizar a frase de acesso que forneceu ao fazer cópias de segurança no Azure. Nas VMs do Azure, os dados são encriptados inativamente com a Encriptação do Serviço de Armazenamento (SSE). Pode ativar o Soft-Delete no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.

Como ativar o Soft-Delete no Cofre de Chaves:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Passos seguintes

Consulte o próximo controlo de segurança: Resposta a [incidentes](security-control-incident-response.md)
