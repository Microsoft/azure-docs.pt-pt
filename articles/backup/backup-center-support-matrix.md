---
title: Matriz de suporte para backup center
description: Este artigo resume os cenários que o Backup Center suporta para cada tipo de carga de trabalho
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 8effc2514abf1cac55abc28b625b869810536baf
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997003"
---
# <a name="support-matrix-for-backup-center"></a>Matriz de suporte para backup center

O Backup Center fornece um único painel de vidro para as empresas [governarem, monitorizarem, operarem e analisarem backups à escala.](backup-center-overview.md) Este artigo resume os cenários que o Backup Center suporta para cada tipo de carga de trabalho.

## <a name="supported-scenarios"></a>Cenários suportados

| **Categoria** | **Cenário**  | **Cargas de trabalho suportadas**  | **Limites** |
| -------------| ------------- | ----------------------- |------------|
| Monitorização   | Ver todos os trabalhos | <li> Máquina Virtual do Azure <br><br> <li> Base de Dados Azure para servidor PostgreSQL | <li> 7 dias de empregos disponíveis fora da caixa. <br> <li> Cada filtro/drop-down suporta um máximo de 1000 itens. Assim, o Backup Center pode ser usado para monitorizar um máximo de 1000 assinaturas e 1000 cofres através dos inquilinos. |
| Monitorização | Ver todas as instâncias de backup | <li> Máquina Virtual do Azure <br><br> <li> Base de Dados Azure para servidor PostgreSQL | Mesmo que acima |
| Monitorização | Ver todas as políticas de backup | <li> Máquina Virtual do Azure <br><br> <li> Base de Dados Azure para servidor PostgreSQL | Mesmo que acima |
| Monitorização | Ver todos os cofres | <li> Máquina Virtual do Azure <br><br> <li> Base de Dados Azure para servidor PostgreSQL | Mesmo que acima |
| Ações | Configurar a cópia de segurança | <li> Máquina Virtual do Azure <br><br> <li> Base de Dados Azure para servidor PostgreSQL | Consulte as matrizes de suporte para cópia de [segurança Azure VM](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) e [Base de Dados Azure para cópia de segurança do Servidor PostgreSQL](backup-azure-database-postgresql.md#support-matrix) |
| Ações | Restaurar a instância de backup | <li> Máquina Virtual do Azure <br><br> <li> Base de Dados Azure para servidor PostgreSQL | Consulte as matrizes de suporte para cópia de [segurança Azure VM](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) e [Base de Dados Azure para cópia de segurança do Servidor PostgreSQL](backup-azure-database-postgresql.md#support-matrix) |
| Ações | Criar cofre | <li> Máquina Virtual do Azure <br><br> <li> Base de Dados Azure para servidor PostgreSQL | Consulte as matrizes de suporte para [o cofre dos Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-support-matrix#vault-support) |
| Ações | Criar política de backup | <li> Máquina Virtual do Azure <br><br> <li> Base de Dados Azure para servidor PostgreSQL | Consulte as matrizes de suporte para [o cofre dos Serviços de Recuperação](https://docs.microsoft.com/azure/backup/backup-support-matrix#vault-support) |
| Ações | Execute backup a pedido para uma instância de backup | <li> Máquina Virtual do Azure <br><br> <li> Base de Dados Azure para servidor PostgreSQL | Consulte as matrizes de suporte para cópia de [segurança Azure VM](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) e [Base de Dados Azure para cópia de segurança do Servidor PostgreSQL](backup-azure-database-postgresql.md#support-matrix) |
| Ações | Pare o backup para uma instância de backup | <li> Máquina Virtual do Azure <br><br> <li> Base de Dados Azure para servidor PostgreSQL | Consulte as matrizes de suporte para cópia de [segurança Azure VM](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas) e [Base de Dados Azure para cópia de segurança do Servidor PostgreSQL](backup-azure-database-postgresql.md#support-matrix) |
| Informações | Ver Relatórios de Backup | <li> Máquina Virtual do Azure <br><br> <li> SQL em Azure Virtual Machine <br><br> <li> SAP HANA em Azure Virtual Machine <br><br> <li> Ficheiros do Azure <br><br> <li> System Center Data Protection Manager <br><br> <li> Agente de reserva Azure (MARS) <br><br> <li> Servidor do Backup do Azure (MABS) | Consulte [os cenários suportados para relatórios de backup](https://docs.microsoft.com/azure/backup/configure-reports#supported-scenarios) |
| Governação | Ver e atribuir políticas de Azure integradas e personalizadas na categoria 'Backup' | N/D | N/D |
| Governação | Ver fontes de dados não configuradas para cópia de segurança | <li> Máquina Virtual do Azure <br><br> <li> Base de Dados Azure para servidor PostgreSQL | N/D |

## <a name="unsupported-scenarios"></a>Cenários não suportados

| **Categoria** | **Cenário**  |
|--------------|---------------|
| Monitorização | Ver alertas em escala |
| Ações | Configurar as definições do cofre em escala |
| Ações | Execute o trabalho de restauro de região cruzada do Backup Center |

## <a name="next-steps"></a>Passos seguintes

* [Reveja a matriz de suporte para Azure Backup](https://docs.microsoft.com/azure/backup/backup-support-matrix)
* [Reveja a matriz de suporte para backup Azure VM](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas)
* [Reveja a matriz de suporte para Azure Database para cópia de segurança do Servidor PostgreSQL](backup-azure-database-postgresql.md#support-matrix)
