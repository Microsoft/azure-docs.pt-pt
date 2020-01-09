---
title: Controle de segurança do Azure-recuperação de dados
description: Recuperação de dados de controle de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 1cd3e39fd504b5095a83192a4a6314c71d3ca980
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564299"
---
# <a name="security-control-data-recovery"></a>Controle de segurança: recuperação de dados

Certifique-se de que todos os dados do sistema, configurações e segredos sejam automaticamente submetidos a backup regularmente.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantir backups automatizados regulares

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 9,1 | 10.1 | Cliente |

Habilite o backup do Azure e configure a fonte de backup (VMs do Azure, SQL Server ou compartilhamentos de arquivos), bem como a frequência e o período de retenção desejados.

Como habilitar o backup do Azure: https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: executar backups completos do sistema e fazer backup de qualquer chave gerenciada pelo cliente

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 9.2 | 10.2 | Cliente |

Habilite o backup do Azure e as VMs de destino, bem como os períodos de frequência e retenção desejados. Fazer backup de chaves gerenciadas pelo cliente dentro do Azure Key Vault.

Como habilitar o backup do Azure: https://docs.microsoft.com/azure/backup/

Como fazer backup de chaves do cofre de chaves no Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validar todos os backups, incluindo chaves gerenciadas pelo cliente

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 9,3 | 10,3 | Cliente |

Garanta a capacidade de executar periodicamente a restauração de dados de conteúdo no backup do Azure. Se necessário, teste a restauração em uma VLAN isolada. Teste a restauração de chaves de backup gerenciadas pelo cliente.

Como recuperar arquivos do backup de máquina virtual do Azure:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Como restaurar chaves do Key Vault no Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantir a proteção de backups e chaves gerenciadas pelo cliente

| ID do Azure | IDs de CIS | Responsabilidade |
|--|--|--|
| 9.4 | 10,4 | Cliente |

Para o backup local, a criptografia em repouso é fornecida usando a senha que você fornece ao fazer backup no Azure. Para VMs do Azure, os dados são criptografados em repouso usando o Criptografia do Serviço de Armazenamento (SSE). Você pode habilitar a exclusão reversível em Key Vault para proteger as chaves contra exclusão acidental ou mal-intencionada.

Como habilitar a exclusão reversível no Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Passos seguintes

Consulte o próximo controle de segurança: [resposta a incidentes](security-control-incident-response.md)
