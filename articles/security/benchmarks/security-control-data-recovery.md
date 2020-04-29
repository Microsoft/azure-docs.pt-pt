---
title: Controlo de Segurança Azure - Recuperação de Dados
description: Recuperação de dados de controlo de segurança azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408603"
---
# <a name="security-control-data-recovery"></a>Controlo de Segurança: Recuperação de dados

Certifique-se de que todos os dados, configurações e segredos do sistema são automaticamente apoiados regularmente.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir backups automáticos regulares

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.1 | 10.1 | Cliente |

Ative a Cópia de Segurança Azure e configure a fonte de backup (VMs Azure, SQL Server ou File Shares), bem como o período de frequência e retenção desejado.

- [Como ativar o Backup Azure](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Executar cópias de segurança completas do sistema e fazer cópias de segurança de quaisquer chaves geridas pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.2 | 10.2 | Cliente |

Ativar o Backup Azure e os VM(s) de destino, bem como os períodos de frequência e retenção desejados. O cliente de reserva geria as chaves dentro do Cofre de Chaves Azure.

- [Como ativar o Backup Azure](https://docs.microsoft.com/azure/backup/)

- [Como apoiar as chaves do cofre chave em Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.3 | 10.3 | Cliente |

Garantir a capacidade de realizar periodicamente a restauração de dados dos conteúdos dentro do Azure Backup. Teste a restauração das chaves geridas pelo cliente.

- [Como recuperar ficheiros da cópia de segurança da Máquina Virtual Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [Como restaurar as chaves do cofre chave em Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.4 | 10.4 | Cliente |

Para cópias de segurança no local, a encriptação inativa de dados é fornecida ao utilizar a frase de acesso que forneceu ao fazer cópias de segurança no Azure. Nas VMs do Azure, os dados são encriptados inativamente com a Encriptação do Serviço de Armazenamento (SSE). Utilize o controlo de acesso baseado em papéis para proteger as cópias de segurança e as chaves geridas pelo cliente.  

Ative o Soft-Delete e expurga a proteção no Cofre-Chave para proteger as chaves contra a eliminação acidental ou maliciosa.  Se o Armazenamento Azure for utilizado para armazenar backups, ative a eliminação suave para guardar e recuperar os seus dados quando as bolhas ou as imagens blob ssão eliminada. 

- [Compreender o Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [Como ativar a proteção Soft-Delete e Purga no Cofre-Chave](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)


## <a name="next-steps"></a>Passos seguintes

- Consulte o próximo Controlo de Segurança: Resposta a [Incidentes](security-control-incident-response.md)