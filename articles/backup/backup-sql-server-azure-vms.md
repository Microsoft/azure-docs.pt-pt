---
title: Sobre o SQL Server VM do Azure pela cópia de segurança do Azure | Documentos da Microsoft
description: Saiba mais sobre bases de dados do SQL Server na VM do Azure e a funcionalidade de consideração desta funcionalidade.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sachdevaswati
ms.openlocfilehash: a57b52b3b0cc493fdde60e9bddfb0125ff2ce3e4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175969"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Sobre a cópia de segurança do SQL Server em VMs do Azure

Bases de dados do SQL Server são cargas de trabalho críticas que exigem um objetivo de ponto de recuperação (RPO) e a retenção de longa duração. Pode criar cópias de segurança bases de dados do SQL Server em execução em VMs do Azure utilizando [cópia de segurança do Azure](backup-overview.md).

## <a name="backup-process"></a>Processo de cópia de segurança

Esta solução tira partido das APIs de nativas de SQL para efetuar cópias de segurança das bases de dados SQL.

* Depois de especificar a VM do SQL Server que pretende proteger e consultar para as bases de dados no departamento de TI, o serviço de cópia de segurança do Azure irá instalar uma extensão de cópia de segurança da carga de trabalho na VM com o nome `AzureBackupWindowsWorkload`  extensão.
* Esta extensão é constituído por um coordenador e um plug-in do SQL. Embora o coordenador é responsável por acionar fluxos de trabalho para várias operações como configurar a cópia de segurança, cópia de segurança e restauro, o plug-in é responsável por fluxo de dados real.
* Para poder detetar as bases de dados nesta VM, o Azure Backup cria a conta `NT SERVICE\AzureWLBackupPluginSvc`. Esta conta é utilizada para cópia de segurança e restauro e necessita de permissões de administrador do sistema do SQL. Tira partido de cópia de segurança do Azure a `NT AUTHORITY\SYSTEM` de conta para deteção/consulta de base de dados, para que esta conta tem de ser um início de sessão público no SQL. Se não criar a VM do SQL Server no Azure Marketplace, poderá receber um erro **UserErrorSQLNoSysadminMembership**. Se isto ocorrer [siga estas instruções](backup-azure-sql-database.md).
* Assim que o acionador de configurar a proteção em bases de dados selecionadas, o serviço de cópia de segurança configura o coordenador com as agendas de cópia de segurança e outros detalhes da política, que a extensão coloca em cache localmente na VM 
* Na hora agendada, o coordenador de comunica com o plug-in e ele começa a transmissão em fluxo os dados de cópia de segurança do SQL server a utilizar a VDI.  
* O plug-in envia os dados diretamente para o Cofre de serviços de recuperação, eliminando a necessidade de uma localização de transição. Os dados são encriptados e armazenados pelo serviço de cópia de segurança do Azure em contas de armazenamento.
* Quando a transferência de dados estiver concluída, o coordenador confirma a consolidação com o serviço de cópia de segurança.

  ![Arquitetura de cópia de segurança SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Antes de começar

Antes de começar, verifique o seguinte:

1. Certifique-se de que tem uma instância do SQL Server em execução no Azure. Pode [rapidamente a criar uma instância do SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) no marketplace.
2. Reveja os [consideração de funcionalidades](#feature-consideration) e [suporte a cenários](#scenario_support).
3. [Reveja as perguntas mais comuns](faq-backup-sql-server.md) sobre este cenário.


## <a name="feature-consideration-and-limitations"></a>Funcionalidade considerações e limitações

- A VM a executar o SQL Server necessita de conectividade de internet para acessar os endereços IP públicos do Azure.
- Cópias de segurança de [distribuído grupos de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) grupos totalmente não funcionam.
- Sempre na ativação pós-falha Cluster instâncias do SQL Server (FCIs) não são suportadas para cópia de segurança.
- Cópia de segurança do SQL Server deve ser configurada no portal ou através do PowerShell.
- Operações de cópia de segurança e restauro de bases de dados de espelho FCI, instantâneos de base de dados e bases de dados não são suportadas.
- Não não possível proteger bases de dados com grande número de ficheiros. O número máximo de ficheiros é 1000.
- Pode criar até ~ 2000 bancos de dados SQL num cofre. Se tiver mais, crie outro cofre.
- Pode configurar a cópia de segurança para até 50 bases de dados de uma vez; Esta restrição ajuda a otimizar cargas de cópia de segurança.
- Não é possível proteger bases de dados com mais de 1000 arquivos.
- Tamanho de base de dados recomendada para garantir um melhor desempenho é 2TB.
- Protege bases de dados até 300 por servidor, se tiver configurados para a cada 15 minutos de backups de log. O número de bases de dados pode aumentar se a frequência de cópia de segurança é menos freqüente. Podemos irá partilhar uma forma detalhada para calcular isso em breve.


## <a name="scenario-support"></a>Suporte a cenários

**Suporte** | **Detalhes**
--- | ---
**Implementações suportadas** | As VMs do Azure do SQL Marketplace e externas (SQL Server instalado manualmente) são suportadas VMs.
**Áreas geográficas suportadas** | Sudeste da Austrália (ASE), leste da Austrália (AE) <br> Sul do Brasil (BRS)<br> Canada Central (CNC), Canada East (CE)<br> South East Asia (SEA), East Asia (EA) <br> EUA Leste (EUS), este dos E.U.A. 2 (EUS2), EUA Centro-Oeste (WCUS), E.U.A. oeste (WUS); E.U.A. oeste 2 (WUS 2) Norte dos E.U.A. (NCUS) dos EUA Central (CUS) Centro-Sul dos E.U.A. (SCUS) <br> India Central (INC), India South (INS) <br> Leste do Japão (JPE), oeste do Japão (JPW) <br> Coreia Central (KRC), Sul da Coreia (KRS) <br> Europa do Norte (m), Europa Ocidental <br> Norte da África do Sul (SAN), África do Sul Ocidental (SAW) <br> EAU Central (UAC), Emirados Árabes Unidos norte (UAN) <br> Sul do Reino Unido (UKS), do Reino Unido oeste (UKW)
**Sistemas operativos suportados** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> Linux não é atualmente suportado.
**Versões suportadas do SQL Server** | SQL Server 2017; SQL Server 2016, SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Versões suportadas do .NET** | .NET framework 4.5.2 e superior instalado na VM



## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais sobre](backup-sql-server-database-azure-vms.md) backup bancos de dados do SQL Server.
- [Saiba mais sobre](restore-sql-database-azure-vm.md) restaurar cópia de segurança de bases de dados do SQL Server.
- [Saiba mais sobre](manage-monitor-sql-database-backup.md) gerir cópia de segurança de bases de dados do SQL Server.
