---
title: Azure Security Benchmark V2 - Backup and Recovery
description: Backup e recuperação V2 de referência de segurança Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fe6861a3319b9d9c0e6535ee3303c90f0a0f26c8
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059266"
---
# <a name="security-control-backup-and-recovery"></a>Controlo de Segurança: Backup e Recuperação

A Cópia de Segurança e Recuperação abrange os controlos para garantir que as cópias de segurança de dados e de configuração nos diferentes níveis de serviço sejam executadas, validadas e protegidas.

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Garantir backups automáticos regulares

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Certifique-se de que está a fazer backup de sistemas e dados para manter a continuidade do negócio após um evento inesperado. Esta orientação deve ser orientado por quaisquer objetivos para o Objetivo do Ponto de Recuperação (RPO) e para o Objetivo do Tempo de Recuperação (RTO).

Ativar a Azure Backup e configurar a fonte de backup (por exemplo, VMs Azure, SQL Server, bases de dados HANA ou Partilhas de Ficheiros), bem como o período de frequência e retenção pretendido.  

Para um nível mais elevado de redundância, pode permitir a opção de armazenamento geo-redundante replicar dados de backup para uma região secundária e recuperar usando a restauração da região transversal.

- [Como ativar o backup do Azure](/azure/backup/)

- [Como permitir a restauração da região transversal](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Política e normas](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: Encriptar dados de backup

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| BR-2 | 10.2 | CP-9 |

Certifique-se de que os seus backups estão protegidos contra ataques. Isto deve incluir encriptação das cópias de segurança para proteger contra a perda de confidencialidade.   

Para a cópia de segurança no local utilizando o Azure Backup, a encriptação em repouso é fornecida utilizando a palavra-passe que fornece. Para a cópia de segurança regular do serviço Azure, os dados de backup são automaticamente encriptados utilizando as teclas geridas pela plataforma Azure. Pode optar por encriptar a cópia de segurança utilizando a chave gerida pelo cliente. Neste caso, certifique-se de que esta chave gerida pelo cliente no cofre chave também está no âmbito de backup. 

Utilize o controlo de acesso baseado em funções em Azure Backup, Azure Key Vault ou outros recursos para proteger cópias de segurança e chaves geridas pelo cliente. Além disso, pode ativar funcionalidades de segurança avançadas para exigir MFA antes que as cópias de segurança possam ser alteradas ou eliminadas.

- [Visão geral das funcionalidades de segurança no Azure Backup](/azure/backup/security-overview)

- [Encriptação de dados de backup usando chaves geridas pelo cliente](/azure/backup/encryption-at-rest-with-cmk) 

- [Como apoiar chaves do Cofre chave em Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Preparação de incidentes](/) azure/cloud-adopt-framework/organize/cloud-security-incident-preparation

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Validar todas as cópias de segurança, incluindo chaves geridas pelo cliente

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| BR-3 | 10.3 | CP-4, CP-9 |

Efetue periodicamente a restauração de dados da sua cópia de segurança. Certifique-se de que pode restaurar as chaves geridas pelo cliente.

- [Como recuperar ficheiros da cópia de segurança da Azure Virtual Machine](/azure/backup/backup-azure-restore-files-from-vm)

- [Como restaurar chaves do Cofre chave em Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Mitigar o risco de chaves perdidas

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| BR-4 | 10.4 | CP-9 |

Certifique-se de que tem medidas para prevenir e recuperar da perda de chaves. Ativar a proteção de eliminação e purga suave no Cofre da Chave Azure para proteger as chaves contra a eliminação acidental ou maliciosa.  

- [Como permitir a eliminação suave e a proteção de purga no Cofre de Chaves](/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Segurança de Dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

