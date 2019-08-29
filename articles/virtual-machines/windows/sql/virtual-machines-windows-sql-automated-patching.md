---
title: Aplicação de patch automatizada para VMs SQL Server (Resource Manager) | Microsoft Docs
description: Explica o recurso de aplicação de patch automatizado para máquinas virtuais SQL Server em execução no Azure usando o Gerenciador de recursos.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 0900dd1809ecb1e93906b57483f334d0f12f6582
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102083"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Aplicação de Patches Automatizada para o SQL Server nas Máquinas Virtuais do Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Clássico](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

A aplicação de patch automatizada estabelece uma janela de manutenção para uma máquina virtual do Azure que executa o SQL Server. As Atualizações Automáticas só podem ser instaladas durante essa janela de manutenção. No SQL Server, esta restrição assegura que as atualizações do sistema e eventuais reinícios associados ocorrem na melhor altura para a base de dados. 

> [!IMPORTANT]
> Só são instaladas as atualizações do Windows assinaladas como **Importantes**. As outras atualizações do SQL Server, como as Atualizações Cumulativas, têm de ser instaladas manualmente. 

A Colocação de Patches Automática depende da [Extensão Agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Pré-requisitos
Para usar a aplicação de patch automatizada, considere os seguintes pré-requisitos:

**Sistema operacional**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versão do SQL Server**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Instale os comandos de Azure PowerShell mais recentes](/powershell/azure/overview) se você planeja configurar a aplicação de patch automatizada com o PowerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> A aplicação de patch automatizada depende da extensão do agente IaaS SQL Server. As imagens atuais da Galeria de máquinas virtuais do SQL adicionam essa extensão por padrão. Para obter mais informações, consulte [SQL Server extensão do agente IaaS](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Definições
A tabela a seguir descreve as opções que podem ser configuradas para aplicação de patch automatizada. As etapas de configuração reais variam dependendo se você usar o portal do Azure ou os comandos do Windows PowerShell do Azure.

| Definição | Valores possíveis | Descrição |
| --- | --- | --- |
| **Aplicação de Patches Automatizada** |Habilitar/desabilitar (desabilitado) |Habilita ou desabilita a aplicação de patch automatizada para uma máquina virtual do Azure. |
| **Agendamento de manutenção** |Todos os dias, segunda-feira, terça-feira, quarta-feira, sexta-feira, sábado, domingo |O agendamento para baixar e instalar o Windows, SQL Server e atualizações da Microsoft para sua máquina virtual. |
| **Hora de início da manutenção** |0-24 |A hora de início local para atualizar a máquina virtual. |
| **Duração da janela de manutenção** |30-180 |O número de minutos permitido para concluir o download e a instalação de atualizações. |
| **Categoria do patch** |Importante | A categoria de atualizações do Windows a serem baixadas e instaladas.|

## <a name="configuration-in-the-portal"></a>Configuração no portal
Você pode usar o portal do Azure para configurar a aplicação de patch automatizada durante o provisionamento ou para VMs existentes.

### <a name="new-vms"></a>Novas VMs
Use o portal do Azure para configurar a aplicação de patch automatizada ao criar uma nova máquina virtual SQL Server no modelo de implantação do Gerenciador de recursos.

Na guia **configurações de SQL Server** , selecione **Alterar configuração** em **aplicação de patch automatizada**. A captura de tela a seguir portal do Azure mostra a folha de **aplicação de patch automatizada do SQL** .

![Aplicação de patch automatizada do SQL no portal do Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Para o contexto, consulte o tópico completo sobre o [provisionamento de uma máquina virtual SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para máquinas virtuais SQL Server existentes, abra o [recurso de máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) e selecione **aplicação de patch** em **configurações**. 

![Aplicação de patch automática do SQL para VMs existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


Quando terminar, clique no botão **OK** na parte inferior da folha de **configuração de SQL Server** para salvar as alterações.

Se você estiver habilitando a aplicação de patch automatizada pela primeira vez, o Azure configurará o SQL Server agente IaaS em segundo plano. Durante esse tempo, o portal do Azure pode não mostrar que a aplicação de patch automatizada está configurada. Aguarde alguns minutos para que o agente seja instalado, configurado. Depois disso, o portal do Azure reflete as novas configurações.

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell
Depois de provisionar sua VM do SQL, use o PowerShell para configurar a aplicação de patch automatizada.

No exemplo a seguir, o PowerShell é usado para configurar a aplicação de patch automatizada em uma VM de SQL Server existente. O comando **New-AzVMSqlServerAutoPatchingConfig** configura uma nova janela de manutenção para atualizações automáticas.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtension-AutoPatchingSettings $aps-VMName $vmname-ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Se a extensão ainda não estiver instalada, a instalação da extensão reiniciará o serviço de SQL Server.

Com base neste exemplo, a tabela a seguir descreve o efeito prático na VM do Azure de destino:

| Parâmetro | Efeito |
| --- | --- |
| **DayOfWeek** |Patches instalados toda quinta-feira. |
| **MaintenanceWindowStartingHour** |Inicie as atualizações às 11:10:00. |
| **MaintenanceWindowsDuration** |Os patches devem ser instalados em até 120 minutos. Com base na hora de início, eles devem ser concluídos em 1:13h. |
| **PatchCategory** |A única configuração possível para esse parâmetro é **importante**. Isso instala o Windows Update marcado como importante; Ele não instala nenhuma atualização de SQL Server que não esteja incluída nessa categoria. |

Pode levar vários minutos para instalar e configurar o agente IaaS SQL Server.

Para desabilitar a aplicação de patch automatizada, execute o mesmo script sem o parâmetro **-Enable** para o **New-AzVMSqlServerAutoPatchingConfig**. A ausência do parâmetro **-Enable** sinaliza o comando para desabilitar o recurso.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre outras tarefas de automação disponíveis, consulte [SQL Server extensão do agente IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre como executar SQL Server em VMs do Azure, consulte [visão geral de SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

