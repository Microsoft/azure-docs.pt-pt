---
title: Backup e Restauro para Servidor SQL em VMs Azure [ Microsoft Docs
description: Descreve considerações de backup e restauro para bases de dados do SQL Server em execução em Máquinas Virtuais Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79249779"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Cópia de Segurança e Restauro para SQL Server em Máquinas Virtuais do Azure

Este artigo fornece orientações sobre as opções de backup e restauro disponíveis para o SQL Server que funciona numa máquina virtual do Windows em Azure. O Azure Storage mantém três cópias de cada disco Azure VM para garantir proteção contra perda de dados ou corrupção de dados físicos. Assim, ao contrário das instalações, não é preciso focar-se em falhas de hardware. No entanto, deve ainda fazer backup nas bases de dados do SQL Server para proteger contra erros de aplicação ou de utilizador, tais como inserções ou supressões inadvertidas de dados. Nesta situação, é importante poder restabelecer um determinado ponto no tempo.

A primeira parte deste artigo fornece uma visão geral das opções de backup e restauro disponíveis. Segue-se secções que fornecem mais informações sobre cada estratégia.

## <a name="backup-and-restore-options"></a>Opções de backup e restauro

A tabela seguinte fornece informações sobre várias opções de backup e restauro para o Servidor SQL em execução em VMs Azure:

| Estratégia | Versões SQL | Descrição |
|---|---|---|
| [Backup automatizado](#automated) | 2014<br/> 2016<br/> 2017 | A Cópia de Segurança Automatizada permite-lhe agendar cópias de segurança regulares para todas as bases de dados de um VM do Servidor SQL. As cópias de segurança são armazenadas no armazenamento azure por um período de até 30 dias. Começando com o SQL Server 2016, o Backup Automático v2 oferece opções adicionais, tais como configurar o agendamento manual e a frequência de backups completos e de registo. |
| [Azure Backup for SQL VMs](#azbackup) (Azure Backup para VMs do SQL) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017 | O Azure Backup fornece uma capacidade de backup da classe Enterprise para o Servidor SQL em execução em VMs Azure. Com este serviço, pode gerir centralmente backups para vários servidores e milhares de bases de dados. As bases de dados podem ser restauradas a um ponto específico no portal. Oferece uma política de retenção personalizável que pode manter backups durante anos. |
| [Backup manual](#manual) | Todos | Dependendo da sua versão do SQL Server, existem várias técnicas para fazer backup manual e restaurar o Servidor SQL em funcionamento num VM Azure. Neste cenário, é responsável pela forma como as suas bases de dados estão apoiadas e pela localização de armazenamento e gestão destas cópias de segurança. |

As seguintes secções descrevem cada opção com mais detalhes. A secção final deste artigo fornece um resumo sob a forma de uma matriz de características.

## <a name="automated-backup"></a><a id="automated"></a>Backup automatizado

A Backup Automatizada fornece um serviço de backup automático para edições SQL Server Standard e Enterprise em execução num Windows VM em Azure. Este serviço é fornecido pela extensão do [agente IaaS do Servidor SQL,](virtual-machines-windows-sql-server-agent-extension.md)que é automaticamente instalada no portal SQL Server Windows no portal Azure.

Todas as bases de dados são apoiadas numa conta de armazenamento Azure que configura. As cópias de segurança podem ser encriptadas e retidas até 30 dias.

O SQL Server 2016 e os VMs mais elevados oferecem mais opções de personalização com backup automático v2. Estas melhorias incluem:

- Backups de base de dados do sistema
- Horário manual de backup e janela de tempo
- Frequência de backup de ficheiros completo e de registo

Para restaurar uma base de dados, deve localizar os ficheiros de backup necessários na conta de armazenamento e efetuar um restauro no seu SQL VM utilizando o SQL Server Management Studio (SSMS) ou os comandos Transact-SQL.

Para obter mais informações sobre como configurar backup automatizado para VMs SQL, consulte um dos seguintes artigos:

- **SQL Server 2016/2017**: [Backup automático v2 para máquinas virtuais Azure](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [Backup automatizado para Máquinas Virtuais SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)

## <a name="azure-backup-for-sql-vms"></a><a id="azbackup"></a>Backup Azure para VMs SQL

[O Azure Backup](/azure/backup/) fornece uma capacidade de backup da classe Enterprise para o Servidor SQL em execução em VMs Azure. Todos os backups são armazenados e geridos num cofre dos Serviços de Recuperação. Existem várias vantagens que esta solução proporciona, especialmente para as Empresas:

- **Backup de infraestrutura zero:** Não tem de gerir servidores de reserva ou locais de armazenamento.
- **Escala**: Proteja muitos VMs SQL e milhares de bases de dados.
- **Pay-As-You-Go**: Esta capacidade é um serviço separado fornecido pela Azure Backup, mas como em todos os serviços Azure, você só paga pelo que você usa.
- **Gestão e monitorização central**: Gere centralmente todas as suas cópias de segurança, incluindo outras cargas de trabalho que o Azure Backup suporta, a partir de um único dashboard em Azure.
- **Backup e retenção orientados pela política**: Crie políticas padrão de backup para backups regulares. Estabeleça políticas de retenção para manter os backups durante anos.
- **Suporte para SQL Always On**: Detete e proteja um Servidor SQL sempre na configuração e honre a preferência de backup do Grupo de Disponibilidade de reserva.
- **Objetivo do Ponto de Recuperação de 15 minutos (RPO)**: Configure as cópias de segurança do registo de transações SQL até cada 15 minutos.
- **Restabelecimento do tempo**: Utilize o portal para recuperar bases de dados num determinado momento sem ter de restaurar manualmente várias cópias de segurança completas, diferenciais e de registo.
- **Alertas de e-mail consolidados para falhas**: Configure notificações de email consolidadas para eventuais falhas.
- **Controlo de acesso baseado em funções**: Determine quem pode gerir as operações de backup e restaurar as operações através do portal.

Para uma visão geral rápida de como funciona juntamente com uma demonstração, veja o seguinte vídeo:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Esta solução de backup Azure para VMs SQL está geralmente disponível. Para mais informações, consulte a base de [dados do SQL Server para o Azure](../../../backup/backup-azure-sql-database.md).

## <a name="manual-backup"></a><a id="manual"></a>Backup manual

Se pretender gerir manualmente as operações de backup e restaurar as operações nos seus VMs SQL, existem várias opções dependendo da versão do SQL Server que está a utilizar. Para obter uma visão geral da cópia de segurança e restaurar, consulte um dos seguintes artigos com base na sua versão do SQL Server:

- [Backup e Restauro para SQL Server 2016 e mais tarde](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Backup e Restauro para SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Backup e Restauro para SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Backup e Restauro para SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Backup e Restauro para SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

As seguintes secções descrevem várias opções de backup manual e restaurar mais detalhadamente.

### <a name="backup-to-attached-disks"></a>Cópia de segurança para discos anexados

Para o Servidor SQL em execução em VMs Azure, pode utilizar técnicas de backup nativas e restaurar utilizando discos anexados no VM para o destino dos ficheiros de backup. No entanto, existe um limite para o número de discos que pode ligar a uma máquina virtual Azure, com base no [tamanho da máquina virtual](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Há também a sobrecarga da gestão do disco a considerar.

Para um exemplo de como criar manualmente uma cópia de segurança de base de dados completa utilizando o SQL Server Management Studio (SSMS) ou o Transact-SQL, consulte [Criar uma cópia](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server)de segurança completa da base de dados .

### <a name="backup-to-url"></a>Backup para URL

Começando pelo SQL Server 2012 SP1 CU2, pode fazer backup e restaurar diretamente o armazenamento Microsoft Azure Blob, que também é conhecido como backup para URL. O SQL Server 2016 também introduziu as seguintes melhorias para esta funcionalidade:

| Melhoria de 2016 | Detalhes |
| --- | --- |
| **Striping** |Ao apoiar o armazenamento de blob do Microsoft Azure, o SQL Server 2016 suporta o backup de várias bolhas para permitir o backup de grandes bases de dados, até um máximo de 12,8 TB. |
| **Snapshot Backup** |Através da utilização de instantâneos Azure, a Cópia de Segurança instantânea do SQL Server fornece cópias de segurança quase instantâneas e restauros rápidos para ficheiros de base de dados armazenados utilizando o serviço de armazenamento Azure Blob. Esta capacidade permite-lhe simplificar as suas políticas de backup e restaurar. A cópia de segurança do instantâneo também suporta a restauração do tempo. Para mais informações, consulte as cópias de [segurança instantâneas para ficheiros de base de dados no Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Para mais informações, consulte um dos seguintes artigos com base na sua versão do SQL Server:

- **SQL Server 2016/2017**: [SQL Server Backup to URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014 Backup to URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [SQL Server 2012 Backup to URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Backup gerido

Começando com o SQL Server 2014, o Managed Backup automatiza a criação de backups para o armazenamento Azure. Nos bastidores, a Backup Gerida utiliza a funcionalidade Backup to URL descrita na secção anterior deste artigo. Backup gerido é também a funcionalidade subjacente que suporta o serviço de backup automatizado SQL Server VM.

A partir do SQL Server 2016, a cópia de segurança gerida obteve opções adicionais para agendamento, backup de base de dados do sistema e frequência de backup completa e log.

Para mais informações, consulte um dos seguintes artigos com base na sua versão do SQL Server:

- [Backup gerido para microsoft Azure para SQL Server 2016 e mais tarde](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Backup gerido para microsoft Azure para SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Matriz de decisão

A tabela seguinte resume as capacidades de cada backup e restaure a opção de restauro para máquinas virtuais SQL Server em Azure.

|| **Backup automatizado** | **Backup Azure para SQL** | **Backup manual** |
|---|---|---|---|
| Requer serviço Azure adicional |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Configure a política de backup no portal Azure | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Restaurar bases de dados no portal Azure |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Gerir vários servidores num painel de instrumentos |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Restauro para um ponto anterior no tempo | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Objetivo do Ponto de Recuperação de 15 minutos (RPO) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Política de retenção de backup a curto prazo (dias) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Política de retenção de backup a longo prazo (meses, anos) |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Suporte incorporado para SQL Server Always On |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Backup para a conta de armazenamento azure(s) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automática) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automática) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(gerido pelo cliente) |
| Gestão de ficheiros de armazenamento e cópia de segurança | | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Backup para discos anexados no VM |   |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Relatórios de backup personalizáveis centrais |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Alertas de e-mail consolidados para falhas |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Personalizar monitorização com base nos registos do Monitor Azure |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Monitorize trabalhos de backup com scripts SSMS ou Transact-SQL | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Restaurar bases de dados com scripts SSMS ou Transact-SQL | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Passos seguintes

Se estiver a planear a sua implementação do SQL Server num VM Azure, pode encontrar orientação de provisionamento no seguinte guia: [Como fornecer uma máquina virtual do Windows SQL Server no portal Azure](virtual-machines-windows-portal-sql-server-provision.md).

Embora a cópia de segurança e restauro possam ser usadas para migrar os seus dados, existem caminhos de migração de dados potencialmente mais fáceis para o SQL Server num VM Azure. Para uma discussão completa sobre opções e recomendações de migração, consulte [Migrating a Database to SQL Server em um VM Azure](virtual-machines-windows-migrate-sql.md).
