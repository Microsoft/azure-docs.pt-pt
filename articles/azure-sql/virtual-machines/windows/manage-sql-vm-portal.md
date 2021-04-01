---
title: Gerir máquinas virtuais SQL Server em Azure utilizando o portal Azure | Microsoft Docs
description: Saiba como aceder ao recurso de máquina virtual SQL no portal Azure para um SQL Server VM hospedado no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 68c7805136a7361a64a6ff6dfbb9e7d910b2ea9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97357947"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Gerir VMs de servidor SQL em Azure utilizando o portal Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

No [portal Azure,](https://portal.azure.com)o recurso [**de máquinas virtuais SQL**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) é um serviço de gestão independente para gerir o SQL Server em VMs Azure. Pode usá-lo para visualizar simultaneamente todos os seus VMs do seu SQL Server e modificar as definições dedicadas ao SQL Server: 

![Recurso de máquinas virtuais SQL](./media/manage-sql-vm-portal/sql-vm-manage.png)


## <a name="remarks"></a>Observações

- Recomendamos que utilize o recurso [**de máquinas virtuais SQL**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) para visualizar e gerir os seus VMs sql server em Azure. Mas atualmente, o recurso **de máquinas virtuais SQL** não suporta a gestão de VMs de servidor SQL [de suporte final.](sql-server-2008-extend-end-of-support.md) Para gerir as definições para os VMs do seu servidor SQL de ponta de suporte, utilize o separador de configuração do [SQL Server](#access-the-sql-server-configuration-tab) precedido. 
- O recurso **de máquinas virtuais SQL** está disponível apenas para VMs sql servidor que tenham [registado com a extensão SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Aceda ao recurso de máquinas virtuais SQL
Para aceder ao recurso **de máquinas virtuais SQL,** faça o seguinte:

1. Abra o [portal do Azure](https://portal.azure.com). 
1. Selecione **Todos os serviços**. 
1. Introduza **máquinas virtuais SQL** na caixa de pesquisa.
1. (Opcional): Selecione a estrela ao lado **de máquinas virtuais SQL** para adicionar esta opção ao menu **Favoritos.** 
1. Selecione **máquinas virtuais SQL**. 

   ![Encontre máquinas virtuais SQL Server em todos os serviços](./media/manage-sql-vm-portal/sql-vm-search.png)

1. O portal lista todos os VMs do Servidor SQL disponíveis dentro da subscrição. Selecione o que pretende conseguir abrir o recurso **de máquinas virtuais SQL.** Utilize a caixa de pesquisa se o seu SQL Server VM não estiver a aparecer. 

   ![Todos os VMs do Servidor SQL disponíveis](./media/manage-sql-vm-portal/all-sql-vms.png)

   A seleção do seu SQL Server VM abre o recurso **de máquinas virtuais SQL:** 


   ![Ver o recurso de máquinas virtuais SQL](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> O recurso **de máquinas virtuais SQL** destina-se a configurações dedicadas do SQL Server. Selecione o nome do VM na **caixa de máquinas Virtual** para abrir definições específicas do VM, mas não exclusivas do SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Aceda ao separador de configuração do SQL Server
O **separador de configuração do SQL Server** foi depreciado. Neste momento, é o único método para gerir VMs de servidor SQL [de suporte em fim de suporte](sql-server-2008-extend-end-of-support.md) e VMs do Servidor SQL que não foram [registados com a extensão do Agente IAAS SQL](sql-agent-extension-manually-register-single-vm.md).

Para aceder ao separador de configuração do **SQL Server** predetado, aceda ao recurso **de máquinas Virtuais.** Utilize os passos seguintes:

1. Abra o [portal do Azure](https://portal.azure.com). 
1. Selecione **Todos os serviços**. 
1. Introduza **máquinas virtuais** na caixa de pesquisa.
1. (Opcional): Selecione a estrela ao lado **de máquinas Virtuais** para adicionar esta opção ao menu **Favoritos.** 
1. Selecione **Máquinas virtuais**. 

   ![Pesquisa de máquinas virtuais](./media/manage-sql-vm-portal/vm-search.png)

1. O portal lista todas as máquinas virtuais na subscrição. Selecione o que pretende conseguir abrir o recurso **das máquinas Virtuais.** Utilize a caixa de pesquisa se o seu SQL Server VM não estiver a aparecer. 
1. Selecione **a configuração do Servidor SQL** no painel **de Definições** para gerir o seu VM do servidor SQL. 

   ![Configuração do SQL Server](./media/manage-sql-vm-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, veja os seguintes artigos: 

* [Visão geral do SQL Server num VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [FAQ para SQL Server em um VM Windows](frequently-asked-questions-faq.md)
* [Orientação de preços para o SQL Server num VM do Windows](pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](doc-changes-updates-release-notes.md)


