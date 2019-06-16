---
title: Gerir VMs do SQL Server no Azure com o portal do Azure | Documentos da Microsoft
description: Saiba como aceder ao recurso de máquina virtual do SQL no portal do Azure para uma VM do SQL Server alojado no Azure.
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
ms.openlocfilehash: e2a807bbd6baeb2f14a6d36f5d98a28d48725449
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082719"
---
# <a name="manage-sql-server-vms-in-azure-using-the-azure-portal"></a>Gerir VMs do SQL Server no Azure com o portal do Azure

Existe um novo ponto de acesso para gerir a sua VM do SQL Server no Azure com o [portal do Azure](https://portal.azure.com). 

O **máquinas virtuais do SQL** recursos agora são um serviço de gestão independente que permite-lhe ver todas as suas VMs do SQL Server em simultâneo e modificar as definições dedicadas para o SQL Server: 

![Recursos de máquinas virtuais do SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Observações

- O **máquinas virtuais do SQL** recurso é o método recomendado para ver e gerir as suas VMs do SQL Server. No entanto, atualmente, o **máquinas virtuais do SQL** recurso não suporta a gestão dos [fim do suporte (término do suporte ao)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) VMs do SQL Server. Para gerir as definições para as suas VMs do término do suporte ao SQL Server, utilize o preterido [guia de configuração do SQL Server](#access-sql-server-configuration-tab) em vez disso. 
- O **máquinas virtuais do SQL** recurso só está disponível para VMs do SQL Server que têm [registado com o fornecedor de recursos de VM do SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-the-sql-vm-resource-provider). 


## <a name="access-sql-virtual-machine-resource"></a>Recurso de máquina virtual do SQL de acesso
Para aceder ao recurso de máquinas virtuais do SQL, faça o seguinte:

1. Abra o [Portal do Azure](https://portal.azure.com). 
1. Selecione **todos os serviços**. 
1. Tipo de `SQL virtual machines` na caixa de pesquisa.
1. (Opcional): Selecione a estrela junto a **máquinas virtuais do SQL** para adicionar esta opção ao menu Favoritos. 
1. Selecione **máquinas virtuais do SQL**. 

   ![Encontrar máquinas virtuais de VM do SQL em todos os serviços](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Isso listará todas as VMs do SQL Server disponíveis na subscrição. Selecione a que pretende gerir para iniciar o **máquinas virtuais do SQL** recursos. Utilize a caixa de pesquisa se a sua VM do SQL Server não é imediatamente aparente. 

![Todas as VMs de SQL disponível](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

Selecionar a sua VM do SQL Server irá abrir o **máquinas virtuais do SQL** recursos: 


![Recursos de máquinas virtuais do SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

  > [!TIP]
  > O **máquinas virtuais do SQL** recursos destina-se as definições de SQL Server dedicadas. Selecione o nome da VM no **Máquina Virtual** campo para navegar para as definições que são específicas para a VM, mas não exclusivo para o SQL Server. 

## <a name="access-sql-server-configuration-tab"></a>Guia de configuração do SQL Server de acesso
O guia de configuração do SQL Server foi preterido. Neste momento, é o único método para gerenciar [fim do suporte (término do suporte ao)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) VMs do SQL Server e as VMs do SQL Server que não foram [registado com o fornecedor de recursos de VM do SQL](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-the-sql-vm-resource-provider).

Para acessar o separador de configuração de servidor SQL preterido, terá de navegar para o **máquinas virtuais** recursos. Para tal, faça o seguinte:

1. Abra o [Portal do Azure](https://portal.azure.com). 
1. Selecione **todos os serviços**. 
1. Tipo de `virtual machines` na caixa de pesquisa.
1. (Opcional): Selecione a estrela junto a **máquinas virtuais** para adicionar esta opção ao menu Favoritos. 
1. Selecione **máquinas virtuais**. 

   ![Pesquisa para máquinas virtuais](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Isso listará todas as máquinas virtuais na subscrição. Selecione a que pretende gerir para iniciar o **Máquina Virtual** recursos. Utilize a caixa de pesquisa se a sua VM do SQL Server não é imediatamente aparente. 
1. Selecione **configuração do SQL Server** no **definições** painel para gerir o seu SQL Server. 

![Configuração do SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Descrição geral do SQL Server num VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server num FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server numa VM do Windows preços orientações](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server num notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


