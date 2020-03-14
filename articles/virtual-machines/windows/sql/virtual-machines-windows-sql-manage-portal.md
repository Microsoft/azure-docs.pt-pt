---
title: Gerir VMs do Servidor SQL em Azure utilizando o portal Azure  Microsoft Docs
description: Saiba como aceder ao recurso de máquina virtual SQL no portal Azure para um VM SQL Server hospedado no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 244ad7d079fd7baf25f8079557576c42d25ca785
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243214"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Gerencie os VMs do Servidor SQL em Azure utilizando o portal Azure

No [portal Azure,](https://portal.azure.com)o recurso **sql virtual machines** é um serviço de gestão independente. Pode usá-lo para visualizar todos os seus VMs do Servidor SQL simultaneamente e modificar as definições dedicadas ao Servidor SQL: 

![Recurso de máquinas virtuais SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Observações

- Recomendamos que utilize o recurso das **máquinas virtuais SQL** para visualizar e gerir os seus VMs sQL Server em Azure. Mas atualmente, o recurso **sql virtual machines** não suporta a gestão de VMs de servidor SQL de [fim de suporte.](virtual-machines-windows-sql-server-2008-eos-extend-support.md) Para gerir as definições para os vMs do servidor SQL, utilize o separador de [configuração do Servidor SQL](#access-the-sql-server-configuration-tab) deprecatado. 
- O recurso **sql de máquinas virtuais está** disponível apenas para VMs de servidor SQL que [se registaram com o fornecedor de recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Aceda ao recurso das máquinas virtuais SQL
Para aceder ao recurso das **máquinas virtuais SQL,** faça o seguinte:

1. Abra o [Portal do Azure](https://portal.azure.com). 
1. Selecione **todos os Serviços**. 
1. Introduza **as máquinas virtuais SQL** na caixa de pesquisa.
1. (Opcional): Selecione a estrela ao lado de **máquinas virtuais SQL** para adicionar esta opção ao seu menu **Favoritos.** 
1. Selecione **máquinas virtuais SQL**. 

   ![Encontre máquinas virtuais Do Servidor SQL em todos os serviços](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. O portal lista todos os VMs do Servidor SQL disponíveis na subscrição. Selecione o que pretende conseguir para abrir o recurso das **máquinas virtuais SQL.** Utilize a caixa de pesquisa se o seu VM do Servidor SQL não aparecer. 

   ![Todos os VMs de Servidor SQL disponíveis](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   A seleção do seu VM do Servidor SQL abre o recurso das **máquinas virtuais SQL:** 


   ![Recurso de máquinas virtuais SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> O recurso **das máquinas virtuais SQL** destina-se a configurações dedicadas do Servidor SQL. Selecione o nome do VM na caixa **de máquinas Virtual** para abrir definições específicas para o VM, mas não exclusiva do Servidor SQL. 

## <a name="access-the-sql-server-configuration-tab"></a>Aceda ao separador de configuração do Servidor SQL
O separador de **configuração do Servidor SQL** foi depreciado. Neste momento, é o único método para gerir VMs de servidor SQL de [suporte](virtual-machines-windows-sql-server-2008-eos-extend-support.md) final e VMs de servidor SQL que não foram registados no fornecedor de [recursos SQL VM](virtual-machines-windows-sql-register-with-resource-provider.md).

Para aceder ao separador de configuração do **Servidor SQL** deprecatado, aceda ao recurso das **máquinas Virtuais.** Utilize os passos seguintes:

1. Abra o [Portal do Azure](https://portal.azure.com). 
1. Selecione **todos os Serviços**. 
1. Introduza **máquinas virtuais** na caixa de pesquisa.
1. (Opcional): Selecione a estrela ao lado de **máquinas virtuais** para adicionar esta opção ao seu menu **Favoritos.** 
1. Selecione **máquinas Virtuais**. 

   ![Pesquisa de máquinas virtuais](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. O portal lista todas as máquinas virtuais na subscrição. Selecione o que pretende conseguir abrir o recurso das **máquinas Virtuais.** Utilize a caixa de pesquisa se o seu VM do Servidor SQL não aparecer. 
1. Selecione **a configuração do Servidor SQL** no painel **definições** para gerir o vM do servidor SQL. 

   ![Configuração do Servidor SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral do Servidor SQL num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [FAQ para Servidor SQL em um VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientação de preços para O Servidor SQL num VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de lançamento para SQL Server num VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


