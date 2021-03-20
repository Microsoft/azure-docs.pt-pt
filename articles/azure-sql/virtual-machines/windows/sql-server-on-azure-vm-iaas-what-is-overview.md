---
title: Descrição Geral do SQL Server em Máquinas Virtuais do Azure do Windows | Microsoft Docs
description: Aprenda a executar edições completas do SQL Server em Azure Virtual Machines na nuvem sem ter que gerir qualquer hardware no local.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4b1234b88eafedbd03b78241baaa40231a6cbd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97356808"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>O que é o SQL Server em Azure Virtual Machines (Windows)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

[O SQL Server em Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) permite-lhe utilizar versões completas do SQL Server na nuvem sem ter de gerir qualquer hardware no local. As máquinas virtuais SQL Server (VMs) também simplificam os custos de licenciamento quando paga à medida que vai.

As máquinas virtuais do Azure executam em [regiões geográficas](https://azure.microsoft.com/regions/) muito diferentes em todo o mundo. Também oferece uma variedade de [tamanhos de máquina](../../../virtual-machines/sizes.md). A galeria de imagens da máquina virtual permite-lhe criar uma VM do SQL Server com a versão, edição e sistema operativo corretos. Isto torna as máquinas virtuais uma boa opção para muitas cargas de trabalho diferentes do SQL Server.

Se é novo no SQL Server em VMs Azure, consulte o *sql Server no Azure VM Overview* video da nossa série de [vídeo Azure SQL](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)aprofundada:
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="automated-updates"></a>Atualizações automáticas

O SQL Server em Azure Virtual Machines pode utilizar [patching automatizado](automated-patching.md) para agendar uma janela de manutenção para instalar janelas importantes e atualizações do SQL Server automaticamente.

## <a name="automated-backups"></a>Cópias de segurança automatizadas

O SQL Server em Azure Virtual Machines pode tirar partido da Cópia de [Segurança Automatizada,](automated-backup.md)que cria regularmente cópias de segurança da sua base de dados para o armazenamento de bolhas. Pode também utilizar esta técnica manualmente. Para obter mais informações, consulte o artigo [Utilizar o Storage do Azure para o Restauro e a Cópia de Segurança do SQL Server](azure-storage-sql-server-backup-restore-use.md).

O Azure também oferece uma solução de backup de classe empresarial para o SQL Server em execução em VMs Azure. Uma solução de backup totalmente gerida, suporta grupos de disponibilidade Always On, retenção a longo prazo, recuperação pontual e gestão e monitorização centrais. Para obter mais informações, consulte [Azure Backup para SQL Server em VMs Azure](../../../backup/backup-azure-sql-database.md).
  

## <a name="high-availability"></a>Elevada disponibilidade

Se necessitar de elevada disponibilidade, considere configurar Grupos de Disponibilidade do SQL Server. Isto envolve múltiplas instâncias do SQL Server em Azure Virtual Machines numa rede virtual. Pode configurar manualmente a sua solução de alta disponibilidade ou utilizar modelos no portal Azure para configuração automática. Para obter uma visão geral de todas as opções de alta disponibilidade, consulte [Alta Disponibilidade e Recuperação de Desastres para O SQL Server em Máquinas Virtuais Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="performance"></a>Desempenho

As máquinas virtuais do Azure oferecem tamanhos de máquina diferentes para satisfazer vários pedidos de carga de trabalho. Os VMs do SqL Server também fornecem uma configuração de armazenamento automatizada, que é otimizada para os seus requisitos de desempenho. Para obter mais informações sobre a configuração do armazenamento para VMs do sql server, consulte [a configuração de armazenamento para VMs do servidor SQL](storage-configuration.md). Para afinar o desempenho, consulte as [melhores práticas de desempenho para SQL Server em Azure Virtual Machines](performance-guidelines-best-practices.md).

## <a name="get-started-with-sql-server-vms"></a>Começar com VMs do SQL Server

Para começar a utilizar, escolha uma imagem de máquina virtual do SQL Server com a versão, edição e sistema operativo necessários. As secções seguintes fornecem ligações diretas para o portal do Azure para as imagens da galeria das máquinas virtuais do SQL Server.

> [!TIP]
> Para obter mais informações sobre como compreender os preços das imagens do SQL Server, consulte [a orientação de preços do SQL Server em Máquinas Virtuais Azure](pricing-guidance.md). 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a> Pague à medida que vai.
A tabela seguinte fornece uma matriz de imagens do SQL Server pay as you go.

| Versão | Sistema operativo | Edição |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Empresa](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standard,](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard) [Web,](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web) [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Programador](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Para ver o Servidor SQL disponível nas imagens da máquina virtual Linux, consulte [a visão geral do SQL Server em Azure Virtual Machines (Linux)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md).

> [!NOTE]
> Agora é possível alterar o modelo de licenciamento de um SQL Server VM pay-per-use para usar a sua própria licença. Para obter mais informações, consulte [como alterar o modelo de licenciamento para um SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> Traga a sua própria licença.
Também pode trazer a sua licença (BYOL). Neste cenário, apenas paga a VM sem quaisquer taxas adicionais para o licenciamento do SQL Server.  Colocar a sua própria licença poderá poupar dinheiro ao longo do tempo para cargas de trabalho de produção contínua. Para saber sobre os requisitos necessários para utilizar esta opção, veja [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](pricing-guidance.md#byol).

Para obter a sua própria licença, pode converter um VM do SqL Server VM existente ou pode implantar uma imagem com o prefixo **{BYOL}**. Para obter mais informações sobre a mudança do seu modelo de licenciamento entre pay-per-use e BYOL, consulte [como alterar o modelo de licenciamento para um SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 

| Versão | Sistema operativo | Edição |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

É possível implementar uma imagem mais antiga do SQL Server que não está disponível no portal Azure utilizando o PowerShell. Para ver todas as imagens disponíveis utilizando o PowerShell, utilize o seguinte comando:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Para obter mais informações sobre a implementação de VMs do Servidor SQL utilizando o PowerShell, consulte [Como providenciar máquinas virtuais SQL Server com Azure PowerShell](create-sql-vm-powershell.md).


### <a name="connect-to-the-vm"></a>Ligar à VM
Depois de criar a VM do SQL Server, ligue-se à mesma a partir de aplicações ou ferramentas, tais como o SQL Server Management Studio (SSMS). Para obter instruções, consulte [Ligar a uma máquina virtual SQL Server no Azure](ways-to-connect-to-sql.md).

### <a name="migrate-your-data"></a>Migrar os dados
Se tiver uma base de dados existente, deverá movê-la para o recém-atado SQL Server VM. Para obter uma lista de opções de migração e orientações, consulte o artigo [Migrar uma Base de Dados para o SQL Server numa VM do Azure](migrate-to-vm-from-sql-server.md).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Criar e gerir recursos Azure SQL com o portal Azure

O portal Azure fornece uma única página onde pode gerir [todos os seus recursos Azure SQL,](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) incluindo as suas máquinas virtuais SQL.

Para aceder à página **de recursos Azure SQL,** selecione **Azure SQL** no menu do portal Azure ou procure e selecione **Azure SQL** a partir de qualquer página.

![Pesquisa por Azure SQL](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> O Azure SQL fornece uma forma rápida e fácil de aceder a todas as suas bases de dados Azure SQL, piscinas elásticas, servidores lógicos, instâncias geridas e máquinas virtuais. O Azure SQL não é um serviço ou recurso. 

Para gerir os recursos existentes, selecione o item desejado na lista. Para criar novos recursos Azure SQL, selecione **+ Adicionar**. 

![Criar recurso Azure SQL](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

Depois de selecionar **+ Adicionar,** consulte informações adicionais sobre as diferentes opções selecionando **detalhes do Show** em qualquer azulejo.

![dados de azulejos de azulejos](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

Para obter mais detalhes, veja:

- [Criar uma única base de dados](../../database/single-database-create-quickstart.md)
- [Criar um conjunto elástico](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Criar um caso gerido](../../managed-instance/instance-create-quickstart.md)
- [Criar uma máquina virtual SQL Server](sql-vm-create-portal-quickstart.md)

## <a name="sql-server-vm-image-refresh-policy"></a><a id="lifecycle"></a> Política de atualização de imagem do SQL Server VM
O Azure mantém apenas uma imagem de máquina virtual para cada sistema operativo suportado, a versão e a combinação de edição. Isto significa que ao longo do tempo as imagens são atualizadas e as imagens mais antigas são removidas. Para obter mais informações, veja a secção **Imagens** das [FAQ de VMs do SQL Server](frequently-asked-questions-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Programa de melhoramento da experiência do cliente (PMEC)
O Programa de Melhoramento da Experiência do Cliente (PMEC) está ativado por predefinição. Esta ação envia relatórios periodicamente para a Microsoft, para ajudar a melhorar o SQL Server. Não é precisa nenhuma tarefa de gestão com PMEC, a menos que queira desativá-la após o aprovisionamento. Pode personalizar ou desativar o PMEC ligando-se à VM com o ambiente de trabalho remoto. Em seguida, execute o utilitário **Relatórios de Utilização e Erros do SQL Server**. Siga as instruções para desativar os relatórios. Para obter mais informações sobre a recolha de dados, veja a [Declaração de Privacidade do SQL Server](/sql/sql-server/sql-server-privacy).

## <a name="related-products-and-services"></a>Produtos e serviços relacionados
### <a name="windows-virtual-machines"></a>Máquinas virtuais do Windows
* [Visão geral das máquinas virtuais Azure](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>Armazenamento
* [Introdução ao Storage do Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Rede
* [Visão geral da rede virtual](../../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../../virtual-network/public-ip-addresses.md)
* [Create a Fully Qualified Domain Name in the Azure portal](../../../virtual-machines/create-fqdn.md) (Criar um Nome de Domínio Completamente Qualificado no portal do Azure)

### <a name="sql"></a>SQL
* [Documentação do Servidor SQL](/sql/index)
* [Comparação da Base de Dados SQL do Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Passos seguintes

Começa com o SQL Server em Azure Virtual Machines:

* [Criar uma VM do SQL Server no portal do Azure](sql-vm-create-portal-quickstart.md)

Obtenha respostas para perguntas comumente feitas sobre VMs do SERVIDOR SQL:

* [FAQ do SQL Server nas Máquinas Virtuais do Azure](frequently-asked-questions-faq.md)

Ver Arquiteturas de Referência para executar aplicações n-tier no SQL Server em IaaS

* [Aplicação de nível N do Windows no Azure com o SQL Server](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Executar uma aplicação de nível N em várias regiões do Azure para alta disponibilidade](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)