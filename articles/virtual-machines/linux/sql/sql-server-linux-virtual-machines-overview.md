---
title: Descrição Geral do SQL Server em Máquinas Virtuais do Linux no Azure | Microsoft Docs
description: Saiba como executar edições completas do SQL Server em máquinas virtuais do Linux no Azure. Obtenha ligações diretas para todas as imagens de VMs do SQL Server do Linux e conteúdos relacionados.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c97793dc28b4ef097d8baa34678aeb92bf123809
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778098"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Descrição Geral do SQL Server em Máquinas Virtuais do Azure (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

O SQL Server em máquinas virtuais do Azure permite-lhe utilizar versões completas do SQL Server na cloud sem ter de gerir qualquer hardware no local. As VMs do SQL Server também simplificam os custos de licenciamento quando paga em pay as you go.

As máquinas virtuais do Azure executam em [regiões geográficas](https://azure.microsoft.com/regions/) muito diferentes em todo o mundo. Também oferece uma variedade de [tamanhos de máquina](../sizes.md). A galeria de imagens da máquina virtual permite-lhe criar uma VM do SQL Server com a versão, edição e sistema operativo corretos. Isto faz com que as máquinas virtuais sejam uma boa opção para várias cargas de trabalho do SQL Server.

## <a id="create"></a> Introdução às VMs SQL

Para começar a utilizar, escolha uma imagem de máquina virtual do SQL Server com a versão, edição e sistema operativo necessários. As secções seguintes fornecem ligações diretas para o portal do Azure para as imagens da galeria das máquinas virtuais do SQL Server.

> [!TIP]
> Para obter mais informações sobre como compreender os preços das imagens SQL, veja a [página de preços das VMs do SQL Server do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Versão | Sistema Operativo | Edição |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Programador](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Para ver as imagens de máquina virtual do SQL Server do Windows disponíveis, veja [Descrição Geral do SQL Server em Máquinas Virtuais do Azure (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a id="packages"></a> Pacotes instalados

Quando configurar o SQL Server no Linux, instale o pacote do motor de base de dados e, em seguida, vários pacotes opcionais, consoante os seus requisitos. As imagens de máquina virtual do Linux para o SQL Server instalam automaticamente a maioria dos pacotes por si. A tabela seguinte mostra os pacotes que são instalados para cada distribuição.

| Distribuição | [Motor de Base de Dados](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Ferramentas](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [Agente do SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Pesquisa em Texto Completo](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Suplemento do HA](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![não](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![não](./media/sql-server-linux-virtual-machines-overview/no.png) | ![não](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="related-products-and-services"></a>Produtos e serviços relacionados

### <a name="linux-virtual-machines"></a>Máquinas Virtuais do Linux

* [Descrição Geral das Máquinas Virtuais](../overview.md)

### <a name="storage"></a>Armazenamento

* [Introdução ao Armazenamento do Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Redes

* [Descrição Geral da Rede Virtual](../../../virtual-network/virtual-networks-overview.md)
* [Endereços IP do Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Create a Fully Qualified Domain Name in the Azure portal](../portal-create-fqdn.md) (Criar um Nome de Domínio Completamente Qualificado no portal do Azure)

### <a name="sql"></a>SQL

* [Documentação do SQL Server no Linux](https://docs.microsoft.com/sql/linux)
* [Comparação da Base de Dados SQL do Azure](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Passos seguintes

Introdução ao SQL Server em máquinas virtuais do Linux no Azure:

* [Criar uma VM do SQL Server no portal do Azure](provision-sql-server-linux-virtual-machine.md)

Obtenha respostas às perguntas mais comuns sobre as VMs do SQL no Linux:

* [FAQ do SQL Server nas Máquinas Virtuais do Linux no Azure](sql-server-linux-faq.md)
