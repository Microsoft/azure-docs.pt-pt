---
title: Visão geral do SQL Server em Azure Virtual Machines for Linux Microsoft Docs
description: Saiba como executar edições completas do SQL Server em Azure Virtual Machines para Linux. Obtenha ligações diretas para todas as imagens de VMs do SQL Server do Linux e conteúdos relacionados.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: overview
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c394fe2aa7639d32e5d79bcb22a01151f7666f5b
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324623"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Descrição Geral do SQL Server em Máquinas Virtuais do Azure (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

O SQL Server em Azure Virtual Machines permite-lhe utilizar versões completas do SQL Server na nuvem sem ter de gerir qualquer hardware no local. As VMs do SQL Server também simplificam os custos de licenciamento quando paga em pay as you go.

As máquinas virtuais do Azure executam em [regiões geográficas](https://azure.microsoft.com/regions/) muito diferentes em todo o mundo. Também oferece uma variedade de [tamanhos de máquina](../../../virtual-machines/sizes.md). A galeria de imagens da máquina virtual permite-lhe criar uma VM do SQL Server com a versão, edição e sistema operativo corretos. Isto faz com que as máquinas virtuais sejam uma boa opção para várias cargas de trabalho do SQL Server. 

Se é novo no Azure SQL, consulte o *sql Server no Azure VM Overview* video da nossa série de [vídeo Azure SQL](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)aprofundada:
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a> Começar com VMs do SQL Server

Para começar a utilizar, escolha uma imagem de máquina virtual do SQL Server com a versão, edição e sistema operativo necessários. As secções seguintes fornecem ligações diretas para o portal do Azure para as imagens da galeria das máquinas virtuais do SQL Server.

> [!TIP]
> Para obter mais informações sobre como compreender os preços das imagens do SQL Server, consulte [a página de preços dos VMs linux que executam o SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Versão | Sistema operativo | Edição |
| --- | --- | --- |
| **SQL Server 2019** | Ubuntu 18.04 | [Empresa](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804enterprise-ARM), [Standard,](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804standard-ARM) [Web,](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804web-ARM) [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804sqldev-ARM) | 
| **SQL Server 2019** | Red Hat Enterprise Linux (RHEL) 8 | [Empresa](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8enterprise-ARM), [Standard,](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8standard-ARM) [Web,](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8web-ARM) [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8sqldev-ARM)|
| **SQL Server 2019** | SUSE Linux Enterprise Server (SLES) v12 SP5 | [Empresa](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5enterprise-ARM), [Standard,](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5standard-ARM) [Web,](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5web-ARM) [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5sqldev-ARM)|
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Para ver as imagens de máquinas virtuais do SQL Server disponíveis para Windows, consulte [a visão geral do SQL Server em Azure Virtual Machines (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Pacotes instalados

Ao configurar o SQL Server no Linux, instale o pacote do Motor database e, em seguida, vários pacotes opcionais, dependendo dos seus requisitos. As imagens de máquina virtual do Linux para o SQL Server instalam automaticamente a maioria dos pacotes por si. A tabela seguinte mostra os pacotes que são instalados para cada distribuição.

| Distribuição | [Motor de base de dados](/sql/linux/sql-server-linux-setup) | [Ferramentas](/sql/linux/sql-server-linux-setup-tools) | [Agente do Servidor SQL](/sql/linux/sql-server-linux-setup-sql-agent) | [Pesquisa em texto completo](/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](/sql/linux/sql-server-linux-setup-ssis) | [Suplemento do HA](/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![MOTOR RHEL e base de dados](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e ferramentas](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Agente do SERVIDOR RHEL e SQL](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![REL e pesquisa de texto completo](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Addon RHEL e HA](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |
| SLES | ![SLES e motor de base de dados](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES e ferramentas](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Agente SLES e SQL Server](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES e pesquisa de texto completo](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES e SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![Addon SLES e HA](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png)|
| Ubuntu | ![Ubuntu e motor de base de dados](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e ferramentas](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Agente Ubuntu e SQL Server](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e pesquisa de texto completo](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Addon Ubuntu e HA](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Produtos e serviços relacionados

### <a name="linux-virtual-machines"></a>Máquinas virtuais do Linux

* [Visão geral das máquinas virtuais Azure](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Armazenamento

* [Introdução ao Storage do Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Rede

* [Visão geral da rede virtual](../../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../../virtual-network/public-ip-addresses.md)
* [Create a Fully Qualified Domain Name in the Azure portal](../../../virtual-machines/create-fqdn.md) (Criar um Nome de Domínio Completamente Qualificado no portal do Azure)

### <a name="sql"></a>SQL

* [SqL Server na documentação do Linux](/sql/linux)
* [Comparação da Base de Dados SQL do Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Passos seguintes

Começa com o SQL Server em máquinas virtuais Linux:

* [Criar uma VM do SQL Server no portal do Azure](sql-vm-create-portal-quickstart.md)

Obtenha respostas para perguntas comumente feitas sobre VMs do SQL Server em Linux:

* [FAQ do SQL Server nas Máquinas Virtuais do Azure](frequently-asked-questions-faq.md)