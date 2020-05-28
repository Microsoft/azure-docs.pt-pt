---
title: Descrição Geral do SQL Server em Máquinas Virtuais do Azure do Windows | Microsoft Docs
description: Saiba como executar edições completas do SQL Server em Máquinas Virtuais do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f9e576599fe1b329807427181d5de3fa37c9adea
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041841"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>O que é o SQL Server nas Máquinas Virtuais do Azure? (Windows)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

O [SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) permite-lhe utilizar versões completas do SQL Server na nuvem sem ter de gerir qualquer hardware no local. As VMs do SQL Server também simplificam os custos de licenciamento quando paga em pay as you go.

As máquinas virtuais do Azure executam em [regiões geográficas](https://azure.microsoft.com/regions/) muito diferentes em todo o mundo. Também oferece uma variedade de [tamanhos de máquina](../../../virtual-machines/windows/sizes.md). A galeria de imagens da máquina virtual permite-lhe criar uma VM do SQL Server com a versão, edição e sistema operativo corretos. Isto faz com que as máquinas virtuais sejam uma boa opção para várias cargas de trabalho do SQL Server.

## <a name="automated-updates"></a>Atualizações automáticas

As VMs do SQL Server do Azure podem utilizar a [ Aplicação Automatizada de Patches](automated-patching.md) para agendar uma janela de manutenção para a instalação automática de atualizações importantes do Windows e do SQL Server.

## <a name="automated-backups"></a>Cópias de segurança automatizadas

As VMs do SQL Server do Azure podem tirar partido da [Cópia de Segurança Automatizada](automated-backup.md), que cria regularmente cópias de segurança da sua base de dados para o armazenamento de blobs. Pode também utilizar esta técnica manualmente. Para obter mais informações, consulte o artigo [Utilizar o Storage do Azure para o Restauro e a Cópia de Segurança do SQL Server](azure-storage-sql-server-backup-restore-use.md).

O Azure também oferece uma solução de backup de classe empresarial para o SQL Server em execução em VMs Azure. Uma solução de backup totalmente gerida, suporta sempre em grupos de disponibilidade, retenção a longo prazo, recuperação pontual ponto-a-longo, gestão e monitorização central. Para mais informações, consulte [o Azure Backup para O Servidor SQL em Azure VM](https://docs.microsoft.com/azure/backup/backup-azure-sql-database).
  

## <a name="high-availability"></a>Elevada disponibilidade

Se necessitar de elevada disponibilidade, considere configurar Grupos de Disponibilidade do SQL Server. Isto envolve várias VMs do SQL Server do Azure numa rede virtual. Pode configurar a sua solução de elevada disponibilidade manualmente ou pode utilizar modelos no portal do Azure para a configuração automática. Para uma vista geral de todas as opções de elevada disponibilidade, consulte o artigo [Elevada Disponibilidade e Recuperação Após Desastre para o SQL Server em Virtual Machines do Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="performance"></a>Desempenho

As máquinas virtuais do Azure oferecem tamanhos de máquina diferentes para satisfazer vários pedidos de carga de trabalho. As VMs de SQL também fornecer configuração de armazenamento automática, que está otimizada para os seus requisitos de desempenho. Para obter mais informações sobre como configurar o armazenamento para as VMs SQL, veja [Configuração de armazenamento para VMs de SQL Server](storage-configuration.md). Para desempenho bem ajustado, veja [Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure (Performance best practices for SQL Server in Azure Virtual Machines)](performance-guidelines-best-practices.md).

## <a name="get-started-with-sql-vms"></a>Introdução às VMs SQL

Para começar a utilizar, escolha uma imagem de máquina virtual do SQL Server com a versão, edição e sistema operativo necessários. As secções seguintes fornecem ligações diretas para o portal do Azure para as imagens da galeria das máquinas virtuais do SQL Server.

> [!TIP]
> Para obter mais informações sobre como compreender os preços das imagens SQL, veja [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços das VMs do Azure do SQL Server)](pricing-guidance.md). 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a>Pague à medida que for.
A tabela seguinte fornece uma matriz de imagens do SQL Server pay as you go.

| Versão | Sistema Operativo | Edição |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Empresa](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web,](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web) [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Programador](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Para ver as imagens de máquina virtual do SQL Server do Linux disponíveis, veja [Overview of SQL Server on Azure Virtual Machines (Linux) (Descrição Geral do SQL Server em Máquinas Virtuais do Azure (Linux))](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md).

> [!NOTE]
> Agora é possível alterar o modelo de licenciamento de um VM de servidor SQL pay-per-use para usar a sua própria licença. Para mais informações, consulte [Como alterar o modelo de licenciamento para um SQL VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> Traga a sua própria licença
Também pode trazer a sua licença (BYOL). Neste cenário, apenas paga a VM sem quaisquer taxas adicionais para o licenciamento do SQL Server.  Colocar a sua própria licença poderá poupar dinheiro ao longo do tempo para cargas de trabalho de produção contínua. Para saber sobre os requisitos necessários para utilizar esta opção, veja [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](pricing-guidance.md#byol).

Para trazer a sua própria licença, pode converter um VM SQL de uso por utilização existente, ou pode implementar uma imagem com o prefixo **{BYOL}**. Para obter mais informações sobre a mudança do seu modelo de licenciamento entre pay-per-use e BYOL, consulte Como alterar o modelo de [licenciamento para um SQL VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 

| Versão | Sistema operativo | Edição |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

É possível implementar uma imagem mais antiga do SQL Server que não está disponível no portal Azure utilizando o PowerShell. Para visualizar todas as imagens disponíveis utilizando o Powershell, utilize o seguinte comando:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Para obter mais informações sobre a implementação de VMs do Servidor SQL utilizando powerShell, veja [como fornecer máquinas virtuais SQL Server com Azure PowerShell](create-sql-vm-powershell.md).


### <a name="connect-to-the-vm"></a>Ligar à VM
Depois de criar a VM do SQL Server, ligue-se à mesma a partir de aplicações ou ferramentas, tais como o SQL Server Management Studio (SSMS). Para instruções, veja [Ligar a uma Máquina Virtual do SQL Server no Azure](ways-to-connect-to-sql.md).

### <a name="migrate-your-data"></a>Migrar os dados
Se tiver uma base de dados existente, deverá movê-la para a VM do SQL recentemente aprovisionada. Para obter uma lista de opções de migração e orientações, consulte o artigo [Migrar uma Base de Dados para o SQL Server numa VM do Azure](migrate-to-vm-from-sql-server.md).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Criar e gerir recursos Azure SQL com o portal Azure

O portal Azure fornece uma única página onde pode gerir [todos os seus recursos Azure SQL,](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) incluindo as suas máquinas virtuais SQL.

Para aceder à página de **recursos Azure SQL,** selecione **Azure SQL** no menu do portal Azure, ou procure e selecione **Azure SQL** em qualquer página.

![Pesquisa por Azure SQL](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> O **Azure SQL** fornece uma forma rápida e fácil de aceder a todas as suas bases de dados SQL, piscinas elásticas, servidores de base de dados, instâncias geridas pela SQL e máquinas virtuais SQL. O Azure SQL não é um serviço ou recurso. 

Para gerir os recursos existentes, selecione o item desejado na lista. Para criar novos recursos Azure SQL, selecione **+ Adicionar**. 

![Criar recurso Azure SQL](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

Depois de selecionar **+ Adicionar,** consulte informações adicionais sobre as diferentes opções selecionando **detalhes do Show** em qualquer azulejo.

![detalhes de azulejos de bases de dados](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

Para obter mais detalhes, veja:

- [Criar uma única base de dados](../../database/single-database-create-quickstart.md)
- [Criar um conjunto elástico](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Criar um caso gerido](../../managed-instance/instance-create-quickstart.md)
- [Criar uma máquina virtual SQL](sql-vm-create-portal-quickstart.md)

## <a name="sql-vm-image-refresh-policy"></a><a id="lifecycle"></a> Política de atualização de imagem de VM do SQL Server
O Azure mantém apenas uma imagem de máquina virtual para cada sistema operativo suportado, a versão e a combinação de edição. Isto significa que ao longo do tempo as imagens são atualizadas e as imagens mais antigas são removidas. Para obter mais informações, veja a secção **Imagens** das [FAQ de VMs do SQL Server](frequently-asked-questions-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Programa de melhoramento da experiência do cliente (PMEC)
O Programa de Melhoramento da Experiência do Cliente (PMEC) está ativado por predefinição. Esta ação envia relatórios periodicamente para a Microsoft, para ajudar a melhorar o SQL Server. Não é precisa nenhuma tarefa de gestão com PMEC, a menos que queira desativá-la após o aprovisionamento. Pode personalizar ou desativar o PMEC ligando-se à VM com o ambiente de trabalho remoto. Em seguida, execute o utilitário **Relatórios de Utilização e Erros do SQL Server**. Siga as instruções para desativar os relatórios. Para obter mais informações sobre a recolha de dados, veja a [Declaração de Privacidade do SQL Server](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement).

## <a name="related-products-and-services"></a>Produtos e serviços relacionados
### <a name="windows-virtual-machines"></a>Máquinas Virtuais do Windows
* [Descrição Geral das Máquinas Virtuais](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>Armazenamento
* [Introdução ao Storage do Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Redes
* [Visão geral da Rede Virtual](../../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Create a Fully Qualified Domain Name in the Azure portal](../../../virtual-machines/linux/portal-create-fqdn.md) (Criar um Nome de Domínio Completamente Qualificado no portal do Azure)

### <a name="sql"></a>SQL
* [Documentação do SQL Server](https://docs.microsoft.com/sql/index)
* [Comparação da Base de Dados SQL do Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Próximos passos

Introdução ao SQL Server em máquinas virtuais do Azure:

* [Criar uma VM do SQL Server no portal do Azure](sql-vm-create-portal-quickstart.md)

Obtenha respostas às perguntas mais comuns sobre as VMs do SQL:

* [FAQ do SQL Server nas Máquinas Virtuais do Azure](frequently-asked-questions-faq.md)

Ver Arquiteturas de Referência para executar aplicações de nível N no Servidor SQL em IaaS

* [Aplicação de nível N do Windows no Azure com servidor SQL](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Executar uma aplicação n-tier em várias regiões de Azure para alta disponibilidade](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
