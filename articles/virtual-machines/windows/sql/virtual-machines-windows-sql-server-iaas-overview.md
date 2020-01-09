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
ms.openlocfilehash: 7d8d1505a268976161636abd0ed2d24398978284
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75374315"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>O que é o SQL Server nas Máquinas Virtuais do Azure? (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

O [SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) permite-lhe utilizar versões completas do SQL Server na nuvem sem ter de gerir qualquer hardware no local. As VMs do SQL Server também simplificam os custos de licenciamento quando paga em pay as you go.

As máquinas virtuais do Azure executam em [regiões geográficas](https://azure.microsoft.com/regions/) muito diferentes em todo o mundo. Também oferece uma variedade de [tamanhos de máquina](../sizes.md). A galeria de imagens da máquina virtual permite-lhe criar uma VM do SQL Server com a versão, edição e sistema operativo corretos. Isto faz com que as máquinas virtuais sejam uma boa opção para várias cargas de trabalho do SQL Server.

## <a name="automated-updates"></a>Atualizações automáticas

As VMs do SQL Server do Azure podem utilizar a [ Aplicação Automatizada de Patches](virtual-machines-windows-sql-automated-patching.md) para agendar uma janela de manutenção para a instalação automática de atualizações importantes do Windows e do SQL Server.

## <a name="automated-backups"></a>Cópias de segurança automatizadas

As VMs do SQL Server do Azure podem tirar partido da [Cópia de Segurança Automatizada](virtual-machines-windows-sql-automated-backup-v2.md), que cria regularmente cópias de segurança da sua base de dados para o armazenamento de blobs. Pode também utilizar esta técnica manualmente. Para obter mais informações, consulte o artigo [Utilizar o Storage do Azure para o Restauro e a Cópia de Segurança do SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md).

## <a name="high-availability"></a>Elevada disponibilidade

Se necessitar de elevada disponibilidade, considere configurar Grupos de Disponibilidade do SQL Server. Isto envolve várias VMs do SQL Server do Azure numa rede virtual. Pode configurar a sua solução de elevada disponibilidade manualmente ou pode utilizar modelos no portal do Azure para a configuração automática. Para uma vista geral de todas as opções de elevada disponibilidade, consulte o artigo [Elevada Disponibilidade e Recuperação Após Desastre para o SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="performance"></a>Desempenho

As máquinas virtuais do Azure oferecem tamanhos de máquina diferentes para satisfazer vários pedidos de carga de trabalho. As VMs de SQL também fornecer configuração de armazenamento automática, que está otimizada para os seus requisitos de desempenho. Para obter mais informações sobre como configurar o armazenamento para as VMs SQL, veja [Configuração de armazenamento para VMs de SQL Server](virtual-machines-windows-sql-server-storage-configuration.md). Para desempenho bem ajustado, veja [Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure (Performance best practices for SQL Server in Azure Virtual Machines)](virtual-machines-windows-sql-performance.md).

## <a name="get-started-with-sql-vms"></a>Introdução às VMs SQL

Para começar a utilizar, escolha uma imagem de máquina virtual do SQL Server com a versão, edição e sistema operativo necessários. As secções seguintes fornecem ligações diretas para o portal do Azure para as imagens da galeria das máquinas virtuais do SQL Server.

> [!TIP]
> Para obter mais informações sobre como compreender os preços das imagens SQL, veja [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços das VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md). 

### <a id="payasyougo"></a> Pay as you go
A tabela seguinte fornece uma matriz de imagens do SQL Server pay as you go.

| Versão | Sistema Operativo | Edição |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [desenvolvedor](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Programador](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Para ver as imagens de máquina virtual do SQL Server do Linux disponíveis, veja [Overview of SQL Server on Azure Virtual Machines (Linux) (Descrição Geral do SQL Server em Máquinas Virtuais do Azure (Linux))](../../linux/sql/sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Agora é possível alterar o modelo de licenciamento de uma VM de SQL Server de pagamento por uso para usar sua própria licença. Para obter mais informações, consulte [como alterar o modelo de licenciamento para uma VM do SQL](virtual-machines-windows-sql-ahb.md). 

### <a id="BYOL"></a> Traga a sua própria licença
Também pode trazer a sua licença (BYOL). Neste cenário, apenas paga a VM sem quaisquer taxas adicionais para o licenciamento do SQL Server.  Colocar a sua própria licença poderá poupar dinheiro ao longo do tempo para cargas de trabalho de produção contínua. Para saber sobre os requisitos necessários para utilizar esta opção, veja [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md#byol).

Para trazer sua própria licença, você pode converter uma VM SQL de pagamento por uso existente ou pode implantar uma imagem com o prefixo **{BYOL}** . Para obter mais informações sobre como alternar o modelo de licenciamento entre pagamento por uso e BYOL, consulte [como alterar o modelo de licenciamento para uma VM do SQL](virtual-machines-windows-sql-ahb.md). 

| Versão | Sistema operativo | Edição |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

É possível implantar uma imagem mais antiga do SQL Server que não está disponível no portal do Azure usando o PowerShell. Para exibir todas as imagens disponíveis usando o PowerShell, use o seguinte comando:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Para obter mais informações sobre como implantar VMs SQL Server usando o PowerShell, veja [como provisionar SQL Server máquinas virtuais com Azure PowerShell](virtual-machines-windows-ps-sql-create.md).


### <a name="connect-to-the-vm"></a>Ligar à VM
Depois de criar a VM do SQL Server, ligue-se à mesma a partir de aplicações ou ferramentas, tais como o SQL Server Management Studio (SSMS). Para instruções, veja [Ligar a uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrar os seus dados
Se tiver uma base de dados existente, deverá movê-la para a VM do SQL recentemente aprovisionada. Para obter uma lista de opções de migração e orientações, consulte o artigo [Migrar uma Base de Dados para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Criar e gerenciar recursos do SQL Azure com o portal do Azure

O portal do Azure fornece uma única página onde você pode gerenciar [todos os seus recursos SQL do Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) , incluindo suas máquinas virtuais do SQL.

Para acessar a página **recursos do SQL do Azure** , selecione **SQL do azure** no menu portal do Azure ou pesquise e selecione **SQL do Azure** em qualquer página.

![Pesquisar SQL do Azure](./media/quickstart-sql-vm-create-portal/search-for-azure-sql.png)

> [!NOTE]
> O **SQL do Azure** fornece uma maneira rápida e fácil de acessar todos os seus bancos de dados SQL, pools elásticos, servidores de banco de dados, instâncias gerenciadas do SQL e máquinas virtuais do SQL. O SQL do Azure não é um serviço ou recurso. 

Para gerenciar os recursos existentes, selecione o item desejado na lista. Para criar novos recursos do SQL Azure, selecione **+ Adicionar**. 

![Criar recurso SQL do Azure](./media/quickstart-sql-vm-create-portal/create-azure-sql-resource.png)

Depois de selecionar **+ Adicionar**, exiba informações adicionais sobre as diferentes opções selecionando **Mostrar detalhes** em qualquer bloco.

![detalhes do bloco bancos de dados](./media/quickstart-sql-vm-create-portal/sql-vm-details.png)

Para obter mais detalhes, veja:

- [Criar um banco de dados individual](../../../sql-database/sql-database-single-database-get-started.md)
- [Criar um conjunto elástico](../../../sql-database/sql-database-elastic-pool.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Criar uma instância gerenciada](../../../sql-database/sql-database-managed-instance-get-started.md)
- [Criar uma máquina virtual do SQL](quickstart-sql-vm-create-portal.md)

## <a id="lifecycle"></a> Política de atualização de imagem de VM do SQL Server
O Azure mantém apenas uma imagem de máquina virtual para cada sistema operativo suportado, a versão e a combinação de edição. Isto significa que ao longo do tempo as imagens são atualizadas e as imagens mais antigas são removidas. Para obter mais informações, veja a secção **Imagens** das [FAQ de VMs do SQL Server](virtual-machines-windows-sql-server-iaas-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Programa de melhoramento da experiência do cliente (PMEC)
O Programa de Melhoramento da Experiência do Cliente (PMEC) está ativado por predefinição. Esta ação envia relatórios periodicamente para a Microsoft, para ajudar a melhorar o SQL Server. Não é precisa nenhuma tarefa de gestão com PMEC, a menos que queira desativá-la após o aprovisionamento. Pode personalizar ou desativar o PMEC ligando-se à VM com o ambiente de trabalho remoto. Em seguida, execute o utilitário **Relatórios de Utilização e Erros do SQL Server**. Siga as instruções para desativar os relatórios. Para obter mais informações sobre a recolha de dados, veja a [Declaração de Privacidade do SQL Server](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement).

## <a name="related-products-and-services"></a>Produtos e serviços relacionados
### <a name="windows-virtual-machines"></a>Máquinas Virtuais do Windows
* [Descrição Geral das Máquinas Virtuais](../overview.md)

### <a name="storage"></a>Armazenamento
* [Introdução ao Armazenamento do Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Funcionamento em Rede
* [Descrição Geral da Rede Virtual](../../../virtual-network/virtual-networks-overview.md)
* [Endereços IP do Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Create a Fully Qualified Domain Name in the Azure portal](../portal-create-fqdn.md) (Criar um Nome de Domínio Completamente Qualificado no portal do Azure)

### <a name="sql"></a>SQL
* [Documentação do SQL Server](https://docs.microsoft.com/sql/index)
* [Comparação da Base de Dados SQL do Azure](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Passos seguintes

Introdução ao SQL Server em máquinas virtuais do Azure:

* [Criar uma VM do SQL Server no portal do Azure](quickstart-sql-vm-create-portal.md)

Obtenha respostas às perguntas mais comuns sobre as VMs do SQL:

* [FAQ do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-faq.md)

Exibir arquiteturas de referência para executar aplicativos de N camadas no SQL Server no IaaS

* [Aplicativo de N camadas do Windows no Azure com SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Executar um aplicativo de N camadas em várias regiões do Azure para alta disponibilidade](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
