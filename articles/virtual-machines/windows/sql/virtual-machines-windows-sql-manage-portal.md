---
title: Gerenciar VMs SQL Server no Azure usando o portal do Azure | Microsoft Docs
description: Saiba como acessar o recurso de máquina virtual do SQL no portal do Azure para uma VM SQL Server hospedada no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 398eea4b968bb77017415e1dc259004c697b8dda
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846167"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Gerenciar VMs SQL Server no Azure usando o portal do Azure

No [portal do Azure](https://portal.azure.com), o recurso de **máquinas virtuais do SQL** é um serviço de gerenciamento independente. Você pode usá-lo para exibir todas as suas VMs SQL Server simultaneamente e modificar as configurações dedicadas a SQL Server: 

![Recurso de máquinas virtuais do SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Observações

- Recomendamos que você use o recurso de **máquinas virtuais do SQL** para exibir e gerenciar suas VMs SQL Server no Azure. Mas, no momento, o recurso de **máquinas virtuais do SQL** não oferece suporte ao gerenciamento de SQL Server VMs de [fim de suporte](virtual-machines-windows-sql-server-2008-eos-extend-support.md) . Para gerenciar as configurações de SQL Server VMs de fim de suporte, use a [guia Configuração de SQL Server](#access-the-sql-server-configuration-tab) preterida em vez disso. 
- O recurso de **máquinas virtuais do SQL** está disponível somente para VMs SQL Server que foram [registradas com o provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Acessar o recurso de máquinas virtuais do SQL
Para acessar o recurso de **máquinas virtuais do SQL** , faça o seguinte:

1. Abra o [Portal do Azure](https://portal.azure.com). 
1. Selecione **todos os serviços**. 
1. Insira as **máquinas virtuais do SQL** na caixa de pesquisa.
1. (Opcional): Selecione a estrela ao lado de **máquinas virtuais do SQL** para adicionar essa opção ao menu **favoritos** . 
1. Selecione **máquinas virtuais do SQL**. 

   ![Localizar SQL Server máquinas virtuais em todos os serviços](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. O portal lista todas as VMs SQL Server disponíveis na assinatura. Selecione aquele que você deseja gerenciar para abrir o recurso de **máquinas virtuais do SQL** . Use a caixa de pesquisa se sua VM SQL Server não estiver aparecendo. 

   ![Todas as VMs SQL Server disponíveis](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   Selecionar sua VM de SQL Server abre o recurso de **máquinas virtuais do SQL** : 


   ![Recurso de máquinas virtuais do SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> O recurso de **máquinas virtuais do SQL** é para configurações de SQL Server dedicadas. Selecione o nome da VM na caixa **máquina virtual** para abrir as configurações específicas para a VM, mas não exclusivo para SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Acessar a guia de configuração do SQL Server
A guia **configuração de SQL Server** foi preterida. Neste momento, é o único método para gerenciar VMs SQL Server [de fim de suporte](virtual-machines-windows-sql-server-2008-eos-extend-support.md) e SQL Server VMs que não foram [registradas com o provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md).

Para acessar a guia de **configuração de SQL Server** preterida, vá para o recurso de **máquinas virtuais** . Utilize os passos seguintes:

1. Abra o [Portal do Azure](https://portal.azure.com). 
1. Selecione **todos os serviços**. 
1. Insira **máquinas virtuais** na caixa de pesquisa.
1. (Opcional): Selecione a estrela ao lado de **máquinas virtuais** para adicionar essa opção ao menu **favoritos** . 
1. Selecione **máquinas virtuais**. 

   ![Pesquisar máquinas virtuais](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. O portal lista todas as máquinas virtuais na assinatura. Selecione aquele que você deseja gerenciar para abrir o recurso de **máquinas virtuais** . Use a caixa de pesquisa se sua VM SQL Server não estiver aparecendo. 
1. Selecione **configuração de SQL Server** no painel **configurações** para gerenciar sua VM SQL Server. 

   ![Configuração de SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral de SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Perguntas frequentes para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Diretrizes de preços para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notas de versão para SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


