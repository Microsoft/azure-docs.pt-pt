---
title: Backup e restauração para SQL Server em VMs do Azure | Microsoft Docs
description: Descreve as considerações de backup e restauração para SQL Server bancos de dados em execução em máquinas virtuais do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: e4c126bbac73accb984f1040a7fea1740d919233
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100540"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Cópia de Segurança e Restauro para SQL Server em Máquinas Virtuais do Azure

Este artigo fornece orientação sobre as opções de backup e restauração disponíveis para SQL Server em execução em uma máquina virtual do Windows no Azure. O armazenamento do Azure mantém três cópias de cada disco de VM do Azure para garantir a proteção contra perda de dados ou corrupção de dados físicos. Portanto, diferentemente do local, você não precisa se concentrar em falhas de hardware. No entanto, você ainda deve fazer backup de seus bancos de dados SQL Server para proteger contra erros de aplicativo ou de usuário, como inserções ou exclusões inadvertidas. Nessa situação, é importante ser capaz de restaurar para um ponto específico no tempo.

A primeira parte deste artigo fornece uma visão geral das opções de backup e restauração disponíveis. Isso é seguido por seções que fornecem mais informações sobre cada estratégia.

## <a name="backup-and-restore-options"></a>Opções de backup e restauração

A tabela a seguir fornece informações sobre várias opções de backup e restauração para SQL Server em execução em VMs do Azure:

| Estratégia | Versões do SQL | Descrição |
|---|---|---|
| [Cópia de Segurança Automatizada](#automated) | 2014<br/> 2016<br/> 2017 | O backup automatizado permite agendar backups regulares para todos os bancos de dados em uma VM SQL Server. Os backups são armazenados no armazenamento do Azure por até 30 dias. A partir do SQL Server 2016, o backup automatizado v2 oferece opções adicionais, como configurar o agendamento manual e a frequência de backups completos e de log. |
| [Azure Backup for SQL VMs](#azbackup) (Azure Backup para VMs do SQL) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017 | O backup do Azure fornece um recurso de backup de classe empresarial para SQL Server em execução em VMs do Azure. Com esse serviço, você pode gerenciar de forma centralizada os backups para vários servidores e milhares de bancos de dados. Os bancos de dados podem ser restaurados para um ponto no tempo específico no Portal. Ele oferece uma política de retenção personalizável que pode manter backups por anos. |
| [Backup manual](#manual) | Todos | Dependendo da sua versão do SQL Server, há várias técnicas para fazer backup e restaurar manualmente SQL Server em execução em uma VM do Azure. Nesse cenário, você é responsável por como é feito o backup dos bancos de dados e o local de armazenamento e o gerenciamento desses backups. |

As seções a seguir descrevem cada opção mais detalhadamente. A seção final deste artigo fornece um resumo na forma de uma matriz de recursos.

## <a id="automated"></a>Backup automatizado

O backup automatizado fornece um serviço de backup automático para SQL Server Standard e Enterprise Editions em execução em uma VM do Windows no Azure. Esse serviço é fornecido pelo [SQL Server extensão do agente IaaS](virtual-machines-windows-sql-server-agent-extension.md), que é instalado automaticamente em SQL Server imagens de máquina virtual do Windows no portal do Azure.

Todos os bancos de dados são submetidos a backup em uma conta de armazenamento do Azure que você configura. Os backups podem ser criptografados e mantidos por até 30 dias.

SQL Server 2016 e VMs superiores oferecem mais opções de personalização com o backup automatizado v2. Entre esses aprimoramentos estão:

- Backups de banco de dados do sistema
- Agenda de backup manual e janela de tempo
- Frequência de backup de arquivo completo e de log

Para restaurar um banco de dados, você deve localizar os arquivos de backup necessários na conta de armazenamento e executar uma restauração em sua VM do SQL usando comandos SQL Server Management Studio (SSMS) ou Transact-SQL.

Para obter mais informações sobre como configurar o backup automatizado para VMs do SQL, consulte um dos seguintes artigos:

- **SQL Server 2016/2017**: [Backup automatizado v2 para máquinas virtuais do Azure](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [Backup automatizado para máquinas virtuais SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a>Backup do Azure para VMs do SQL

O [backup do Azure](/azure/backup/) fornece um recurso de backup de classe empresarial para SQL Server em execução em VMs do Azure. Todos os backups são armazenados e gerenciados em um cofre dos serviços de recuperação. Há várias vantagens que essa solução fornece, especialmente para empresas:

- **Backup de infraestrutura zero**: Você não precisa gerenciar servidores de backup ou locais de armazenamento.
- **Escala**: Proteja muitas VMs do SQL e milhares de bancos de dados.
- Pré- **pago**: Esse recurso é um serviço separado fornecido pelo backup do Azure, mas como com todos os serviços do Azure, você paga apenas pelo que usar.
- **Monitoramento e gerenciamento central**: Gerencie centralmente todos os seus backups, incluindo outras cargas de trabalho com suporte no backup do Azure, de um único painel no Azure.
- **Backup e retenção orientados por política**: Crie políticas de backup padrão para backups regulares. Estabeleça políticas de retenção para manter backups por anos.
- **Suporte para SQL Always on**: Detecte e proteja um SQL Server Always On configuração e obedeça à preferência de backup do grupo de disponibilidade de backup.
- **objetivo de ponto de recuperação de 15 minutos (RPO)** : Configure backups de log de transações do SQL até a cada 15 minutos.
- **Restauração pontual**: Use o portal para recuperar bancos de dados para um ponto específico no tempo sem precisar restaurar manualmente vários backups completos, diferenciais e de log.
- **Alertas de email consolidados para falhas**: Configure notificações por email consolidadas para quaisquer falhas.
- **Controle de acesso baseado em função**: Determine quem pode gerenciar operações de backup e restauração por meio do Portal.

Para obter uma visão geral rápida de como ela funciona junto com uma demonstração, Assista ao vídeo a seguir:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Essa solução de backup do Azure para VMs do SQL está disponível para o público geral. Para obter mais informações, consulte [fazer backup SQL Server banco de dados no Azure](../../../backup/backup-azure-sql-database.md).

## <a id="manual"></a>Backup manual

Se você quiser gerenciar manualmente as operações de backup e restauração em suas VMs do SQL, há várias opções, dependendo da versão do SQL Server que você está usando. Para obter uma visão geral do backup e da restauração, consulte um dos seguintes artigos com base na sua versão do SQL Server:

- [Backup e restauração para o SQL Server 2016 e posterior](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Backup e restauração para o SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Backup e restauração para o SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Backup e restauração para SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Backup e restauração para o SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

As seções a seguir descrevem várias opções manuais de backup e restauração com mais detalhes.

### <a name="backup-to-attached-disks"></a>Backup em discos anexados

Para SQL Server em execução em VMs do Azure, você pode usar técnicas de backup e restauração nativas usando discos anexados na VM para o destino dos arquivos de backup. No entanto, há um limite para o número de discos que você pode anexar a uma máquina virtual do Azure, com base no [tamanho da máquina virtual](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Também há a sobrecarga do gerenciamento de disco a ser considerada.

Para obter um exemplo de como criar manualmente um backup de banco de dados completo usando o SQL Server Management Studio (SSMS) ou o Transact-SQL, consulte [criar um backup de banco de dados completo](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Backup para URL

A partir do SQL Server 2012 SP1 CU2, você pode fazer backup e restaurar diretamente para Microsoft Azure armazenamento de BLOB, que também é conhecido como backup para URL. SQL Server 2016 também introduziu os seguintes aprimoramentos para esse recurso:

| aprimoramento de 2016 | Detalhes |
| --- | --- |
| **Distribuição** |Ao fazer backup em Microsoft Azure armazenamento de BLOBs, o SQL Server 2016 dá suporte ao backup em vários BLOBs para habilitar o backup de bancos de dados grandes, até um máximo de 12,8 TB. |
| **Backup de instantâneo** |Com o uso de instantâneos do Azure, SQL Server backup de instantâneo de arquivo fornece backups quase instantâneos e restaurações rápidas para arquivos de banco de dados armazenados usando o serviço de armazenamento de BLOBs do Azure. Essa funcionalidade permite simplificar as políticas de backup e restauração. O backup de instantâneo de arquivo também dá suporte à restauração pontual. Para obter mais informações, consulte [backups de instantâneo para arquivos de banco de dados no Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Para obter mais informações, consulte um dos seguintes artigos com base em sua versão do SQL Server:

- **SQL Server 2016/2017**: [SQL Server Backup para URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [Backup do SQL Server 2014 para URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [Backup do SQL Server 2012 para URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Backup gerenciado

A partir do SQL Server 2014, o backup gerenciado automatiza a criação de backups para o armazenamento do Azure. Nos bastidores, o backup gerenciado usa o recurso de backup para URL descrito na seção anterior deste artigo. O backup gerenciado também é o recurso subjacente que dá suporte ao serviço de backup automatizado de VM SQL Server.

A partir do SQL Server 2016, o backup gerenciado obteve opções adicionais para agendamento, backup de banco de dados do sistema e frequência de backup completo e de log.

Para obter mais informações, consulte um dos artigos a seguir com base na sua versão do SQL Server:

- [Backup gerenciado para Microsoft Azure para SQL Server 2016 e posterior](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Backup gerenciado para Microsoft Azure para SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Matriz de decisão

A tabela a seguir resume os recursos de cada opção de backup e restauração para SQL Server máquinas virtuais no Azure.

|| **Cópia de Segurança Automatizada** | **Backup do Azure para SQL** | **Backup manual** |
|---|---|---|---|
| Requer serviço do Azure adicional |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Configurar a política de backup no portal do Azure | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Restaurar bancos de dados no portal do Azure |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Gerenciar vários servidores em um painel |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Restauro para um ponto anterior no tempo | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Objetivo de ponto de recuperação de 15 minutos (RPO) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Política de retenção de backup de curto prazo (dias) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Política de retenção de backup de longo prazo (meses, anos) |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Suporte interno para SQL Server Always On |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Backup para conta (s) de armazenamento do Azure | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png)automática | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png)automática | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(gerenciado pelo cliente) |
| Gerenciamento de arquivos de armazenamento e de backup | | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Backup em discos anexados na VM |   |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Relatórios de backup personalizáveis central |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Alertas de email consolidados para falhas |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Personalizar o monitoramento com base em logs de Azure Monitor |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Monitorar trabalhos de backup com scripts do SSMS ou Transact-SQL | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Restaurar bancos de dados com scripts do SSMS ou Transact-SQL | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Passos seguintes

Se você estiver planejando a implantação do SQL Server em uma VM do Azure, poderá encontrar diretrizes de provisionamento no seguinte guia: [Como provisionar uma máquina virtual do Windows SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

Embora o backup e a restauração possam ser usados para migrar seus dados, há caminhos de migração de dados potencialmente mais fáceis para SQL Server em uma VM do Azure. Para obter uma discussão completa sobre opções de migração e recomendações, consulte [migrando um banco de dados para SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).
