---
title: Controlo de Segurança Azure - Recuperação de Dados
description: Recuperação de dados de controlo de segurança da Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 835e4f681d514bb6b92caa5ee076e3794ed59236
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698823"
---
# <a name="security-control-data-recovery"></a>Controlo de Segurança: Recuperação de Dados

Certifique-se de que todos os dados, configurações e segredos do sistema são automaticamente apoiados regularmente.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Garantir back ups automáticos regulares

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.1 | 10.1 | Cliente |

Ativar o Azure Backup e configurar a fonte de backup (Azure VMs, SQL Server ou File Shares), bem como o período de frequência e retenção pretendido.

- [Como ativar o backup do Azure](../../backup/index.yml)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Execute cópias de segurança completas do sistema e faça backups de backups de qualquer tecla gerida pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.2 | 10.2 | Cliente |

Ativar o Azure Backup e os VM(s) alvo, bem como os períodos de frequência e retenção pretendidos. O cliente de reserva geriu as chaves dentro do Cofre da Chave Azure.

- [Como ativar o backup do Azure](../../backup/index.yml)

- [Como backup chaves chave cofre em Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.3 | 10.3 | Cliente |

Garantir a capacidade de realizar periodicamente a restauração de dados de conteúdos dentro da Cópia de Segurança Azure. Teste de restauração de chaves geridas pelo cliente.

- [Como recuperar ficheiros da cópia de segurança da Azure Virtual Machine](../../backup/backup-azure-restore-files-from-vm.md)

- [Como restaurar chaves chave do cofre em Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Garantir a proteção das cópias de segurança e das chaves geridas pelo cliente

| Azure ID | CIS IDs | Responsabilidade |
|--|--|--|
| 9.4 | 10.4 | Cliente |

Para cópias de segurança no local, a encriptação inativa de dados é fornecida ao utilizar a frase de acesso que forneceu ao fazer cópias de segurança no Azure. Nas VMs do Azure, os dados são encriptados inativamente com a Encriptação do Serviço de Armazenamento (SSE). Utilize o controlo de acesso baseado em funções Azure para proteger cópias de segurança e chaves geridas pelo cliente.  

Ativar Soft-Delete e limpar a proteção no Cofre de Chaves para proteger as chaves contra a eliminação acidental ou maliciosa.  Se o Azure Storage for utilizado para armazenar cópias de segurança, ative a eliminação suave para guardar e recuperar os seus dados quando as bolhas ou as imagens blob forem eliminadas. 

- [Compreender Azure RBAC](../../role-based-access-control/overview.md)

- [Como permitir a proteção de Soft-Delete e purga no Cofre de Chaves](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)


## <a name="next-steps"></a>Passos seguintes

- Ver o próximo Controlo de Segurança:  [Resposta a incidentes](security-control-incident-response.md)