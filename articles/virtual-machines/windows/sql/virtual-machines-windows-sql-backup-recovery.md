---
title: Cópia de segurança e restaurar para o SQL Server em VMs do Azure | Documentos da Microsoft
description: Descreve as considerações de cópia de segurança e restauro para bases de dados do SQL Server em execução em máquinas de virtuais do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: ec147355d6f61a282a0e6b0950fe03ba0e2341f5
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530387"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Cópia de Segurança e Restauro para SQL Server em Máquinas Virtuais do Azure

Este artigo fornece orientações sobre as cópia de segurança e restauro opções disponíveis para o SQL Server em execução numa máquina virtual Windows no Azure. O armazenamento do Azure mantém três cópias de cada disco de VM do Azure para garantir a proteção contra perda de dados ou corrupção de dados físicos. Portanto, ao contrário de no local, não precisa se concentrar em falhas de hardware. No entanto, deve ainda de cópia de segurança suas bases de dados do SQL Server para proteger contra erros de aplicações ou utilizadores, tais como inserções de dados acidental ou eliminações. Nesta situação, é importante ser capaz de restaurar para um ponto específico no tempo.

A primeira parte deste artigo fornece uma descrição geral da cópia de segurança disponível e opções de restauro. Ele é seguido por secções fornecem mais informações sobre cada estratégia.

## <a name="backup-and-restore-options"></a>Opções de cópia de segurança e restauro

A tabela seguinte fornece informações sobre várias opções de cópia de segurança e restauro para SQL Server em execução em VMs do Azure:

| Estratégia | Versões do SQL | Descrição |
|---|---|---|---|
| [Cópia de Segurança Automatizada](#automated) | 2014<br/> 2016<br/> 2017 | Cópia de segurança automatizada permite-lhe agendar cópias de segurança regulares para todas as bases de dados numa VM do SQL Server. As cópias de segurança são armazenadas no armazenamento do Azure durante 30 dias. A partir do SQL Server 2016, a cópia de segurança automatizada v2 oferece opções adicionais, como a configuração manual de agendamento e a frequência de completo e backups de log. |
| [Cópia de segurança do Azure para VMs SQL](#azbackup) | 2012<br/> 2014<br/> 2016<br/> 2017 | O Azure Backup fornece uma capacidade de cópia de segurança de classe empresarial para o SQL Server em execução em VMs do Azure. Com este serviço, pode gerenciar centralmente as cópias de segurança para vários servidores e de milhares de bases de dados. Bases de dados podem ser restaurados para um ponto específico no tempo no portal. Ele oferece uma política de retenção personalizável que pode manter cópias de segurança durante anos. Esta funcionalidade está atualmente em pré-visualização pública. |
| [Cópia de segurança manual](#manual) | Tudo | Dependendo da versão do SQL Server, existem várias técnicas para criar cópias de segurança e restaurar o SQL Server em execução numa VM do Azure manualmente. Neste cenário, é responsável por como seus bancos de dados são uma cópia de segurança e a localização de armazenamento e gestão destas cópias de segurança. |

As secções seguintes descrevem cada opção em mais detalhes. A seção final deste artigo fornece um resumo na forma de uma matriz de funcionalidade.

## <a id="automated"></a> Cópia de segurança automatizada

Cópia de segurança automatizada fornece um serviço de cópia de segurança automática para as edições do SQL Server Standard e Enterprise em execução numa VM do Windows no Azure. Este serviço é fornecido pelos [extensão de agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md), que é instalado automaticamente em imagens de máquinas virtuais do SQL Server Windows no portal do Azure.

Todas as bases de dados são uma cópia de segurança para uma conta de armazenamento do Azure que configurar. As cópias de segurança podem ser encriptadas e retidas durante 30 dias.

SQL Server 2016 e VMs superior oferecem mais opções de personalização com o v2 de cópia de segurança automatizada. Esses aprimoramentos incluem:

- Cópias de segurança do sistema da base de dados
- Agenda de cópia de segurança manual e a janela de tempo
- Frequência de cópia de segurança de ficheiro completo e de registo

Para restaurar uma base de dados, tem de localizar os ficheiros de cópia de segurança necessários na conta de armazenamento e efetue um restauro na sua VM de SQL com o SQL Server Management Studio (SSMS) ou comandos Transact-SQL.

Para obter mais informações sobre como configurar a cópia de segurança automatizada para VMs do SQL, consulte um dos seguintes artigos:

- **SQL Server 2016/2017**: [V2 de cópia de segurança automatizada para máquinas virtuais do Azure](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [Cópia de segurança automatizada para máquinas virtuais do SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a> Cópia de segurança do Azure para VMs do SQL (pré-visualização pública)

[O Azure Backup](/azure/backup/) fornece uma capacidade de cópia de segurança de classe empresarial para o SQL Server em execução em VMs do Azure. Todas as cópias de segurança são armazenadas e gerenciadas num cofre dos serviços de recuperação. Existem diversas vantagens que fornece esta solução, especialmente para empresas:

- **Cópia de segurança sem infraestrutura**: Não é necessário que gerir servidores de cópia de segurança ou localizações de armazenamento.
- **Dimensionamento**: Proteger o número de VMs de SQL e de milhares de bases de dados.
- **Pay as you go**: Esta capacidade é um serviço separado fornecido pelo Azure Backup, mas como com todos os serviços do Azure, paga apenas aquilo que utiliza.
- **Monitorização e gestão central**: Gerenciar centralmente todas as cópias de segurança, incluindo outras cargas de trabalho do Azure Backup oferece suporte, a partir de um dashboard único no Azure.
- **Política controlado por cópia de segurança e retenção**: Crie políticas de cópia de segurança padrão para cópias de segurança regulares. Estabeleça políticas de retenção para manter as cópias de segurança durante anos.
- **Suporte para SQL sempre em**: Detetar e proteger uma configuração de SQL Server Always On e irá cumprir a preferência de cópia de segurança do grupo de disponibilidade cópia de segurança.
- **o objetivo de ponto de recuperação de 15 minutos (RPO)**: Configure os backups de log de transação de SQL cada 15 minutos.
- **Ponto de restauro para um tempo**: Utilizar o portal para recuperar bases de dados para um ponto específico no tempo sem ter de restaurar manualmente vários diferenciais completo e backups de log.
- **Alertas de e-mail para falhas consolidados**: Configure notificações de e-mail consolidada para eventuais falhas.
- **Controlo de acesso baseado em funções**: Determine quem pode gerir a cópia de segurança e restaurar as operações através do portal.

Para obter uma descrição geral de como isso funciona, juntamente com uma demonstração, veja o vídeo seguinte:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2dNbw]

Esta solução de cópia de segurança do Azure para VMs de SQL está atualmente em pré-visualização pública. Para obter mais informações, consulte [cópia de segurança da base de dados do SQL Server para o Azure](../../../backup/backup-azure-sql-database.md).

## <a id="manual"></a> Cópia de segurança manual

Se pretender gerir cópias de segurança e restaurar as operações nas suas VMs de SQL manualmente, existem várias opções, dependendo da versão do SQL Server estiver a utilizar. Para obter uma descrição geral da cópia de segurança e restauro, veja um dos seguintes artigos com base na sua versão do SQL Server:

- [Cópia de segurança e restauro para SQL Server 2016 e posterior](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Cópia de segurança e restauro para SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Cópia de segurança e restauro para SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Backup e restauração do SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Cópia de segurança e restauro para SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

As secções seguintes descrevem várias cópia de segurança manual e restaurar opções mais detalhadamente.

### <a name="backup-to-attached-disks"></a>Cópia de segurança para discos anexados

Para o SQL Server em execução em VMs do Azure, pode utilizar a cópia de segurança nativa e restaurar técnicas usando discos ligados na VM para o destino dos ficheiros de cópia de segurança. No entanto, existe um limite para o número de discos pode anexar a máquina virtual do Azure, com base na [tamanho da máquina virtual](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Também há a sobrecarga de gerenciamento de disco a serem considerados.

Para obter um exemplo de como criar manualmente uma cópia de segurança completa da base de dados com o SQL Server Management Studio (SSMS) ou de Transact-SQL, veja [criar uma base de dados cópia de segurança completa](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Cópia de segurança para URL

A partir do SQL Server 2012 SP1 CU2, pode criar cópias de segurança e restaurar diretamente para o armazenamento de Blobs do Microsoft Azure, que também é conhecido como cópia de segurança para URL. SQL Server 2016 também introduziu os seguintes melhoramentos para esta funcionalidade:

| Aprimoramento de 2016 | Detalhes |
| --- | --- |
| **Repartição** |Quando a cópia de segurança para armazenamento de Blobs do Microsoft Azure, SQL Server 2016 suporta o backup vários blobs para ativar o backup de bancos de dados grandes, até um máximo de 12.8 TB. |
| **Cópia de segurança do instantâneo** |Através da utilização de instantâneos do Azure, a cópia de segurança de instantâneos de ficheiros do SQL Server fornece cópias de segurança instantâneas e restauros rápidos para ficheiros de base de dados armazenados com o serviço de armazenamento de Blobs do Azure. Esta capacidade permite-lhe simplificar a cópia de segurança e restaurar as políticas. Cópia de segurança de instantâneos de ficheiros também suporta o ponto no tempo. Para obter mais informações, consulte [cópias de segurança de instantâneos para os ficheiros de base de dados no Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Para obter mais informações, consulte um dos seguintes artigos com base na sua versão do SQL Server:

- **SQL Server 2016/2017**: [Cópia de segurança do SQL Server para URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [Cópia de segurança do SQL Server 2014 para URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [Cópia de segurança do SQL Server 2012 para URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Cópia de segurança gerida

A partir do SQL Server 2014, a cópia de segurança gerida automatiza a criação de cópias de segurança para o armazenamento do Azure. Nos bastidores, a cópia de segurança gerida faz uso da cópia de segurança a funcionalidade de URL descrita na secção anterior deste artigo. Cópia de segurança gerida também é o recurso subjacente que suporta o serviço do SQL Server VM cópia de segurança automatizada.

A partir do SQL Server 2016, a cópia de segurança gerida tem opções adicionais para agendamento, base de dados do sistema de segurança e completa e a frequência de cópia de segurança do registo.

Para obter mais informações, consulte um dos seguintes artigos com base na sua versão do SQL Server:

- [Gerido de cópia de segurança para o Microsoft Azure para SQL Server 2016 e posterior](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Cópia de segurança gerida para o Microsoft Azure para o SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Matriz de decisão

A tabela seguinte resume as capacidades de cada opção de cópia de segurança e restauro para máquinas virtuais do SQL Server no Azure.

|| **Cópia de Segurança Automatizada** | **Cópia de segurança do Azure para SQL** | **Cópia de segurança manual** |
|---|---|---|---|
| Requer o serviço do Azure adicional |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Configurar a política de cópia de segurança no portal do Azure | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Restaurar bases de dados no portal do Azure |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Gerir vários servidores num dashboard |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Restauro para um ponto anterior no tempo | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| o objetivo de ponto de recuperação de 15 minutos (RPO) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Política de retenção de cópia de segurança de curto prazo (dias) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Política de retenção de cópia de segurança de longo prazo (meses, anos) |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Suporte incorporado para o SQL Server Always On |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Cópia de segurança para contas de armazenamento do Azure | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automático) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automático) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(gerida pelo cliente) |
| Gestão de ficheiros de armazenamento e cópia de segurança | | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Cópia de segurança para os discos ligados na VM |   |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Relatórios de cópia de segurança personalizáveis centrais |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Alertas de e-mail consolidado de falhas |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Personalizar a monitorização com base em registos do Azure Monitor |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Monitorizar trabalhos de cópia de segurança com o SSMS ou Transact-SQL scripts | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Restaurar bases de dados com o SSMS ou Transact-SQL scripts | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Passos Seguintes

Se estiver a planear a implementação do SQL Server na VM do Azure, pode encontrar orientações de aprovisionamento no Guia do seguinte: [Como aprovisionar uma máquina virtual do Windows SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

Embora a cópia de segurança e restauro podem ser utilizados para migrar os seus dados, existem caminhos de migração de dados potencialmente mais fácil para o SQL Server numa VM do Azure. Para uma discussão completa sobre as opções de migração e as recomendações, veja [migrar uma base de dados para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md).
