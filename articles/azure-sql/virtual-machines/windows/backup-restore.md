---
title: Backup e restauro para SQL Server em VMs Azure / Microsoft Docs
description: Descreve considerações de backup e restauro para bases de dados do SQL Server em execução em Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: db270224a753f815a2d94e6a1fa79ebbedf49278
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94991575"
---
# <a name="backup-and-restore-for-sql-server-on-azure-vms"></a>Backup e restauro para SQL Server em VMs Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo fornece orientações sobre as opções de backup e restauro disponíveis para o SQL Server em execução numa máquina virtual Windows (VM) em Azure. O Azure Storage mantém três cópias de cada disco Azure VM para garantir a proteção contra a perda de dados ou corrupção de dados físicos. Assim, ao contrário do SQL Server no local, não precisa de se concentrar em falhas de hardware. No entanto, deverá ainda fazer o back up das bases de dados do SQL Server para proteger contra aplicações ou erros do utilizador, tais como inserções de dados inadvertidas ou eliminações. Nesta situação, é importante poder restabelecer um determinado ponto no tempo.

A primeira parte deste artigo fornece uma visão geral das opções de backup e restauro disponíveis. Seguem-se secções que fornecem mais informações sobre cada estratégia.

## <a name="backup-and-restore-options"></a>Opções de backup e restauro

A tabela seguinte fornece informações sobre várias opções de backup e restauro para SQL Server em VMs Azure:

| Estratégia | Versões SQL | Descrição |
|---|---|---|
| [Cópia de Segurança Automatizada](#automated) | 2014<br/> 2016<br/> 2017 | A Cópia de Segurança Automatizada permite-lhe agendar cópias de segurança regulares para todas as bases de dados num SQL Server VM. As cópias de segurança são armazenadas no armazém da Azure por um tempo até 30 dias. Começando pelo SQL Server 2016, o Backup Automático v2 oferece opções adicionais, como configurar o agendamento manual e a frequência de cópias de segurança completas e de registo. |
| [Azure Backup for SQL VMs](#azbackup) (Azure Backup para VMs do SQL) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017 | O Azure Backup fornece uma capacidade de backup da classe Enterprise para O SQL Server em VMs Azure. Com este serviço, pode gerir centralmente backups para vários servidores e milhares de bases de dados. As bases de dados podem ser restauradas a um ponto específico no tempo no portal. Oferece uma política de retenção personalizável que pode manter backups durante anos. |
| [Backup manual](#manual) | Todos | Dependendo da sua versão do SQL Server, existem várias técnicas para fazer backup manual e restaurar o SQL Server no Azure VM. Neste cenário, é responsável pela forma como as suas bases de dados são apoiadas e pela localização e gestão de armazenamento e gestão destas cópias de segurança. |

As seguintes secções descrevem cada opção com mais detalhes. A secção final deste artigo fornece um resumo sob a forma de uma matriz de recurso.

## <a name="automated-backup"></a><a id="automated"></a> Backup automatizado

O Backup Automatizado fornece um serviço de backup automático para as edições SQL Server Standard e Enterprise em execução num Windows VM em Azure. Este serviço é fornecido pela Extensão do [Agente IAAS do SQL Server](sql-server-iaas-agent-extension-automate-management.md), que é automaticamente instalada em imagens de máquinas virtuais SQL Server Windows no portal Azure.

Todas as bases de dados estão ressarçadas até uma conta de armazenamento Azure que configura. As cópias de segurança podem ser encriptadas e retidas até 30 dias.

O SQL Server 2016 e os VMs mais elevados oferecem mais opções de personalização com backup automatizado v2. Estas melhorias incluem:

- Backups de bases de dados do sistema
- Horário de backup manual e janela de tempo
- Frequência de backup de ficheiros completos e de registo

Para restaurar uma base de dados, tem de localizar os ficheiros de cópia de segurança necessários na conta de armazenamento e efetuar uma restauração no seu SQL VM utilizando comandos SQL Server Management Studio (SSMS) ou Transact-SQL.

Para obter mais informações sobre como configurar a Cópia de Segurança Automatizada para VMS SQL, consulte um dos seguintes artigos:

- **SQL Server 2016/2017**: [Cópia de segurança automatizada v2 para máquinas virtuais Azure](automated-backup.md)
- **SQL Server 2014**: [Cópia de segurança automatizada para máquinas virtuais SQL Server 2014](automated-backup-sql-2014.md)

## <a name="azure-backup-for-sql-vms"></a><a id="azbackup"></a> Backup Azure para VMs SQL

[O Azure Backup](../../../backup/index.yml) fornece uma capacidade de backup da classe Enterprise para O SQL Server em VMs Azure. Todos os backups são armazenados e geridos num cofre dos Serviços de Recuperação. Existem várias vantagens que esta solução proporciona, especialmente para as Empresas:

- **Backup de infraestrutura zero**: Não é preciso gerir servidores de backup ou locais de armazenamento.
- **Escala**: Proteja muitos SQL VMs e milhares de bases de dados.
- **Pay-As-You-Go**: Esta capacidade é um serviço separado fornecido pela Azure Backup, mas como em todos os serviços Azure, você só paga pelo que usa.
- **Gestão e monitorização** central : Gerencie centralmente todos os seus backups, incluindo outras cargas de trabalho que a Azure Backup suporta, a partir de um único dashboard em Azure.
- **Backup e retenção orientados por políticas**: Crie políticas de backup padrão para cópias de segurança regulares. Estabeleça políticas de retenção para manter os backups durante anos.
- **Suporte para SQL Always On**: Detete e proteja um servidor SQL Sempre Na configuração e honre a preferência de backup do Grupo de Disponibilidade.
- **Objetivo do ponto de recuperação de 15 minutos (RPO)**: Configurar os registos de transações SQL até 15 minutos.
- **Ponto de restauração do tempo**: Utilize o portal para recuperar bases de dados a um ponto específico no tempo sem ter de restaurar manualmente múltiplas cópias de segurança completas, diferenciais e de registo.
- **Alertas de e-mail consolidados para falhas**: Configure notificações de email consolidadas para eventuais falhas.
- **Controlo de acesso baseado em funções Azure**: Determine quem pode gerir as operações de backup e restaurar as operações através do portal.

Para uma visão geral rápida de como funciona juntamente com uma demonstração, veja o seguinte vídeo:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Esta solução Azure Backup para SQL VMs está geralmente disponível. Para obter mais informações, consulte [a base de dados do SQL Server para Azure](../../../backup/backup-azure-sql-database.md).

## <a name="manual-backup"></a><a id="manual"></a> Backup manual

Se pretender gerir manualmente as operações de backup e restauro nos seus VMs SQL, existem várias opções dependendo da versão do SQL Server que está a utilizar. Para obter uma visão geral da cópia de segurança e restauro, consulte um dos seguintes artigos com base na sua versão do SQL Server:

- [Backup e restauro para SQL Server 2016 e mais tarde](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Backup e restauro para SQL Server 2014](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases?viewFallbackFrom=sql-server-2014)
- [Backup e restauro para SQL Server 2012](/previous-versions/sql/sql-server-2012/ms187048(v=sql.110))
- [Backup e restauro para SQL Server SQL Server 2008 R2](/previous-versions/sql/sql-server-2008-r2/ms187048(v=sql.105))
- [Backup e restauro para SQL Server 2008](/previous-versions/sql/sql-server-2008/ms187048(v=sql.100))

As secções seguintes descrevem várias opções de backup manual e restauro em mais detalhes.

### <a name="backup-to-attached-disks"></a>Backup para discos anexados

Para o SQL Server em VMs Azure, pode utilizar técnicas de backup e restauro nativas utilizando discos anexados no VM para o destino dos ficheiros de cópia de segurança. No entanto, existe um limite para o número de discos que pode anexar a uma máquina virtual Azure, com base no [tamanho da máquina virtual](../../../virtual-machines/sizes.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json). Há também a sobrecarga da gestão do disco a considerar.

Para obter um exemplo de como criar manualmente uma cópia de segurança completa da base de dados utilizando o SQL Server Management Studio (SSMS) ou o Transact-SQL, consulte [Criar uma cópia de segurança completa da base de dados](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Backup para URL

Começando com o SQL Server 2012 SP1 CU2, pode fazer backup e restaurar diretamente para o armazenamento do Microsoft Azure Blob, que também é conhecido como backup para URL. O SQL Server 2016 também introduziu as seguintes melhorias para esta funcionalidade:

| Melhoria de 2016 | Detalhes |
| --- | --- |
| **Striping** |Ao fazer o backup para o armazenamento de blob do Microsoft Azure, o SQL Server 2016 suporta o backup de várias bolhas para permitir o backup de grandes bases de dados, até um máximo de 12.8 TB. |
| **Backup instantâneo** |Através da utilização de instantâneos Azure, o SQL Server File-Snapshot Backup fornece cópias de segurança quase instantâneas e restauros rápidos para ficheiros de base de dados armazenados utilizando o serviço de armazenamento Azure Blob. Esta capacidade permite-lhe simplificar as suas políticas de backup e restaurar as suas políticas. A cópia de segurança do snapshot do ficheiro também suporta o ponto de restauração do tempo. Para obter mais informações, consulte [cópias de segurança instantâneas para ficheiros de base de dados em Azure](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Para mais informações, consulte um dos seguintes artigos com base na sua versão do SQL Server:

- **SQL Server 2016/2017**: [Backup do servidor SQL para URL](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014 Backup para URL](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service?viewFallbackFrom=sql-server-2014)
- **SQL Server 2012**: [SQL Server 2012 Backup para URL](/previous-versions/sql/sql-server-2012/jj919148(v=sql.110))

### <a name="managed-backup"></a>Cópia de Segurança Gerida

Começando pelo SQL Server 2014, o Managed Backup automatiza a criação de backups para o armazenamento Azure. Nos bastidores, o Managed Backup utiliza a funcionalidade backup para URL descrita na secção anterior deste artigo. O Backup Gerido é também a funcionalidade subjacente que suporta o serviço de Backup Automatizado SQL Server VM.

A partir de SQL Server 2016, Managed Backup obteve opções adicionais para agendamento, backup de base de dados do sistema e frequência de backup completa e log.

Para mais informações, consulte um dos seguintes artigos com base na sua versão do SQL Server:

- [Conseguiu backup para o Microsoft Azure para SQL Server 2016 e mais tarde](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Cópia de segurança gerida para o Microsoft Azure para SQL Server 2014](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?viewFallbackFrom=sql-server-2014)

## <a name="decision-matrix"></a>Matriz de decisão

A tabela seguinte resume as capacidades de cada opção de backup e restauro para máquinas virtuais SQL Server em Azure.

| Opção | Cópia de Segurança Automatizada | Backup Azure para SQL | Backup manual |
|---|---|---|---|
| Requer serviço Azure adicional |   | ![Marca verde.](./media/backup-restore/yes.png) |   |
| Configure a política de backup no portal Azure | ![Marca verde.](./media/backup-restore/yes.png) | ![Marca verde.](./media/backup-restore/yes.png) |   |
| Restaurar bases de dados no portal Azure |   | ![Marca verde.](./media/backup-restore/yes.png) |   |
| Gerir vários servidores num só dashboard |   | ![Marca verde.](./media/backup-restore/yes.png) |   |
| Restauro para um ponto anterior no tempo | ![Marca verde.](./media/backup-restore/yes.png) | ![Marca verde.](./media/backup-restore/yes.png) | ![Marca verde.](./media/backup-restore/yes.png) |
| Objetivo do ponto de recuperação de 15 minutos (RPO) | ![Marca verde.](./media/backup-restore/yes.png) | ![Marca verde.](./media/backup-restore/yes.png) | ![Marca verde.](./media/backup-restore/yes.png) |
| Política de retenção de backup de curto prazo (dias) | ![Marca verde.](./media/backup-restore/yes.png) | ![Marca verde.](./media/backup-restore/yes.png) |   |
| Política de retenção de apoio a longo prazo (meses, anos) |   | ![Marca verde.](./media/backup-restore/yes.png) |   |
| Suporte incorporado para SQL Server Always On |   | ![Marca verde.](./media/backup-restore/yes.png) |   |
| Cópia de segurança para a conta de armazenamento Azure | ![Marca verde.](./media/backup-restore/yes.png)(automático) | ![Marca verde.](./media/backup-restore/yes.png)(automático) | ![Marca verde.](./media/backup-restore/yes.png)(gerido pelo cliente) |
| Gestão de ficheiros de armazenamento e backup | | ![Marca verde.](./media/backup-restore/yes.png) |  |
| Backup para discos anexados no VM |   |   | ![Marca verde.](./media/backup-restore/yes.png) |
| Relatórios centrais de backup personalizáveis |   | ![Marca verde.](./media/backup-restore/yes.png) |   |
| Alertas de e-mail consolidados para falhas |   | ![Marca verde.](./media/backup-restore/yes.png) |   |
| Personalizar a monitorização com base nos registos do Azure Monitor |   | ![Marca verde.](./media/backup-restore/yes.png) |   |
| Monitorize trabalhos de backup com scripts SSMS ou Transact-SQL | ![Marca verde.](./media/backup-restore/yes.png) | ![Marca verde.](./media/backup-restore/yes.png) | ![Marca verde.](./media/backup-restore/yes.png) |
| Restaurar bases de dados com scripts SSMS ou Transact-SQL | ![Marca verde.](./media/backup-restore/yes.png) |   | ![Marca verde.](./media/backup-restore/yes.png) |

## <a name="next-steps"></a>Próximos passos

Se estiver a planear a sua implantação do SQL Server em Azure VM, pode encontrar orientações de provisionamento no seguinte guia: [Como providenciar uma máquina virtual do Windows SQL Server no portal Azure](create-sql-vm-portal.md).

Embora a cópia de segurança e restauro possa ser usada para migrar os seus dados, existem caminhos de migração de dados potencialmente mais fáceis para o SQL Server em VM. Para uma discussão completa sobre opções e recomendações de [migração, consulte a Migração de uma Base de Dados para O SQL Server em Azure VM](migrate-to-vm-from-sql-server.md).