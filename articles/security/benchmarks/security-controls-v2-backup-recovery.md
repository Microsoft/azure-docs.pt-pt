---
title: Azure Security Benchmark V2 - Backup and Recovery
description: Backup e recuperação V2 de referência de segurança Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fd9a01868230efd9e9078171359d81302b472cd3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724426"
---
# <a name="security-control-v2-backup-and-recovery"></a>Controlo de Segurança V2: Backup e Recuperação

A Cópia de Segurança e Recuperação abrange os controlos para garantir que as cópias de segurança de dados e de configuração nos diferentes níveis de serviço sejam executadas, validadas e protegidas.

Para ver a política de Azure incorporada aplicável, consulte [detalhes da iniciativa de conformidade regulamentar de referência de segurança Azure: Backup and Recovery](../../governance/policy/samples/azure-security-benchmark#backup-and-recovery)

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Garantir backups automáticos regulares

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| BR-1 | 10.1 | CP-2, CP4, CP-6, CP-9 |

Certifique-se de que está a fazer backup de sistemas e dados para manter a continuidade do negócio após um evento inesperado. Isto deve ser definido por quaisquer objetivos para o Objetivo do Ponto de Recuperação (RPO) e para o Objetivo do Tempo de Recuperação (RTO).

Ativar o Azure Backup e configurar a fonte de backup (tais como VMs Azure, SQL Server, bases de dados HANA ou Partilhas de Ficheiros), bem como o período de frequência e retenção pretendido.

Para um nível de proteção mais elevado, pode permitir que a opção de armazenamento geo-redundante reproduza dados de backup para uma região secundária e recupere usando a restauração da região transversal.

- [Continuidade de negócio e recuperação após desastre de escala empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [Como ativar o backup do Azure](../../backup/index.yml)

- [Como ativar o restauro entre regiões](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Política e normas](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2: Encriptar dados de backup

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| BR-2 | 10.2 | CP-9 |

Certifique-se de que os seus backups estão protegidos contra ataques. Isto deve incluir encriptação das cópias de segurança para proteger contra a perda de confidencialidade.

Para cópias de segurança no local que utilizem O Backup Azure, a encriptação em repouso é fornecida utilizando a palavra-passe que fornece. Para cópias de segurança regulares do serviço Azure, os dados de backup são automaticamente encriptados utilizando as teclas geridas pela plataforma Azure. Pode optar por encriptar as cópias de segurança utilizando a chave gerida pelo cliente. Neste caso, certifique-se de que esta chave gerida pelo cliente no cofre chave também está no âmbito de backup.

Utilize o controlo de acesso baseado em funções Azure em Azure Backup, Azure Key Vault ou outros recursos para proteger cópias de segurança e chaves geridas pelo cliente. Além disso, pode ativar funcionalidades de segurança avançadas para exigir MFA antes que as cópias de segurança possam ser alteradas ou eliminadas.

- [Visão geral das funcionalidades de segurança no Azure Backup](../../backup/security-overview.md)

- [Encriptação de dados de cópias de segurança com chaves geridas pelo cliente](../../backup/encryption-at-rest-with-cmk.md) 

- [Como apoiar chaves do Cofre chave em Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

- [Funcionalidades de segurança para ajudar a proteger os backups híbridos de ataques](../../backup/backup-azure-security-feature.md#prevent-attacks)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| BR-3 | 10,3 | CP-4, CP-9 |

Efetue periodicamente a restauração de dados da sua cópia de segurança. Certifique-se de que pode restaurar as chaves geridas pelo cliente.

- [Como recuperar ficheiros da cópia de segurança da Azure Virtual Machine](../../backup/backup-azure-restore-files-from-vm.md)

- [Como restaurar chaves do Cofre chave em Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Mitigar o risco de perda de chaves

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| BR-4 | 10.4 | CP-9 |

Certifique-se de que dispõe de medidas para prevenir e recuperar da perda de chaves. Ative a eliminação recuperável e a proteção contra remoção no Azure Key Vault para proteger as chaves contra a eliminação maliciosa ou acidental.

- [Como ativar a eliminação recuperável e a proteção contra remoção no Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Arquitetura de segurança](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Preparação de incidentes](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Segurança de Dados](/azure/cloud-adoption-framework/organize/cloud-security-data-security)