---
title: Descrição Geral do SQL Server em Máquinas Virtuais do Linux no Azure | Microsoft Docs
description: Saiba como executar edições completas do SQL Server em máquinas virtuais do Linux no Azure. Obtenha ligações diretas para todas as imagens de VMs do SQL Server do Linux e conteúdos relacionados.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 61b8982868bf14a7b5a5441049cb7fa21cdd9d6d
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266037"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Descrição Geral do SQL Server em Máquinas Virtuais do Azure (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

O SQL Server em máquinas virtuais do Azure permite-lhe utilizar versões completas do SQL Server na cloud sem ter de gerir qualquer hardware no local. As VMs do SQL Server também simplificam os custos de licenciamento quando paga em pay as you go.

As máquinas virtuais do Azure executam em [regiões geográficas](https://azure.microsoft.com/regions/) muito diferentes em todo o mundo. Também oferece uma variedade de [tamanhos de máquina](../../../virtual-machines/windows/sizes.md). A galeria de imagens da máquina virtual permite-lhe criar uma VM do SQL Server com a versão, edição e sistema operativo corretos. Isto faz com que as máquinas virtuais sejam uma boa opção para várias cargas de trabalho do SQL Server. 

## <a name="get-started-with-sql-vms"></a><a id="create"></a>Começar com SQL VMs

Para começar a utilizar, escolha uma imagem de máquina virtual do SQL Server com a versão, edição e sistema operativo necessários. As secções seguintes fornecem ligações diretas para o portal do Azure para as imagens da galeria das máquinas virtuais do SQL Server.

> [!TIP]
> Para obter mais informações sobre como compreender os preços das imagens SQL, veja a [página de preços das VMs do SQL Server do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Versão | Sistema Operativo | Edição |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Para ver as imagens de máquina virtual do SQL Server do Windows disponíveis, veja [Descrição Geral do SQL Server em Máquinas Virtuais do Azure (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Pacotes instalados

Quando configurar o SQL Server no Linux, instale o pacote do motor de base de dados e, em seguida, vários pacotes opcionais, consoante os seus requisitos. As imagens de máquina virtual do Linux para o SQL Server instalam automaticamente a maioria dos pacotes por si. A tabela seguinte mostra os pacotes que são instalados para cada distribuição.

| Distribuição | [Motor de Base de Dados](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Ferramentas](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [Agente do SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Pesquisa completa de texto](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Suplemento do HA](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![não](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) |
| SLES | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![não](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![não](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) |
| Ubuntu | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sim](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Produtos e serviços relacionados

### <a name="linux-virtual-machines"></a>Máquinas Virtuais do Linux

* [Descrição Geral das Máquinas Virtuais](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Armazenamento

* [Introdução ao Storage do Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Redes

* [Visão geral da rede virtual](../../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../../virtual-network/public-ip-addresses.md)
* [Create a Fully Qualified Domain Name in the Azure portal](../../../virtual-machines/windows/portal-create-fqdn.md) (Criar um Nome de Domínio Completamente Qualificado no portal do Azure)

### <a name="sql"></a>SQL

* [SqL Server na documentação do Linux](https://docs.microsoft.com/sql/linux)
* [Comparação da Base de Dados SQL do Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Passos seguintes

Introdução ao SQL Server em máquinas virtuais do Linux no Azure:

* [Criar uma VM do SQL Server no portal do Azure](sql-vm-create-portal-quickstart.md)

Obtenha respostas às perguntas mais comuns sobre as VMs do SQL no Linux:

* [FAQ do SQL Server nas Máquinas Virtuais do Linux no Azure](frequently-asked-questions-faq.md)
