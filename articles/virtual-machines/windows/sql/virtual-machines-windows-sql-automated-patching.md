---
title: Patching automatizado para VMs do SQL Server (Resource Manager) | Documentos da Microsoft
description: Explica a funcionalidade de aplicação de patches automatizada para SQL Server máquinas virtuais em execução no Azure com o Resource Manager.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 348979a53bff76c85e6d1531bd16cd695145e21b
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59425990"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Aplicação de Patches Automatizada para o SQL Server nas Máquinas Virtuais do Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Clássica](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

A aplicação de patches automatizada estabelece uma janela de manutenção para uma Máquina Virtual do Azure com o SQL Server. As Atualizações Automáticas só podem ser instaladas durante essa janela de manutenção. No SQL Server, esta restrição assegura que as atualizações do sistema e eventuais reinícios associados ocorrem na melhor altura para a base de dados. 

> [!IMPORTANT]
> Só são instaladas as atualizações do Windows assinaladas como **Importantes**. As outras atualizações do SQL Server, como as Atualizações Cumulativas, têm de ser instaladas manualmente. 

A Colocação de Patches Automática depende da [Extensão Agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar a aplicação de patches automatizada, considere os seguintes pré-requisitos:

**Sistema operativo**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versão do SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Instalar os comandos do Azure PowerShell mais recente](/powershell/azure/overview) se quiser configurar a aplicação de patches automatizada com o PowerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> A aplicação de patches automatizada conta com a extensão do agente IaaS do SQL Server. Imagens de Galeria de máquinas virtuais SQL atuais adicione esta extensão por predefinição. Para obter mais informações, consulte [extensão de agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Definições
A tabela seguinte descreve as opções que podem ser configuradas para a aplicação de patches automatizada. Os passos de configuração real variam consoante utilize ou o portal do Azure ou os comandos do Azure Windows PowerShell.

| Definição | Valores possíveis | Descrição |
| --- | --- | --- |
| **Aplicação de Patches Automatizada** |Ativar/desativar (desativado) |Ativa ou desativa a aplicação de patches automatizada para uma máquina virtual do Azure. |
| **Agenda de manutenção** |Todos os dias, segunda-feira, Terça-feira, quarta-feira, Quinta-feira, sexta-feira, Sábado, Domingo |A agenda para baixar e instalar atualizações do Windows, SQL Server e Microsoft para a máquina virtual. |
| **Hora de início da manutenção** |0-24 |A hora de início local para atualizar a máquina virtual. |
| **Duração da janela de manutenção** |30-180 |O número de minutos para ao concluir a transferência e instalação de atualizações. |
| **Categoria de patch** |Importante | A categoria de atualizações do Windows para transferir e instalar.|

## <a name="configuration-in-the-portal"></a>Configuração no Portal
Pode utilizar o portal do Azure para configurar a aplicação de patches automatizada durante o aprovisionamento ou de VMs existentes.

### <a name="new-vms"></a>Novas VMs
Utilize o portal do Azure para configurar a aplicação de patches automatizada quando cria uma nova máquina de Virtual do SQL Server no modelo de implementação do Resource Manager.

Na **definições do SQL Server** painel, selecione **aplicação de patches automatizada**. A captura de ecrã de portal do Azure a seguir mostra a **aplicação de patches de automatizada do SQL** painel.

![SQL aplicação de patches automatizada no portal do Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Para o contexto, consulte o tópico de completo sobre [aprovisionar uma máquina virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes
Para máquinas de virtuais do SQL Server existentes, selecione a máquina virtual do SQL Server. Em seguida, selecione o **configuração do SQL Server** secção a **definições** painel.

![SQL aplicação de patches automática de VMs existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

Na **configuração do SQL Server** painel, clique nas **editar** botão no automatizada secção de aplicação de patches.

![Configurar a aplicação de patches de automatizada do SQL de VMs existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Quando terminar, clique nas **OK** botão na parte inferior dos **configuração do SQL Server** painel para guardar as alterações.

Se pretende ativar a aplicação de patches automatizada pela primeira vez, o Azure configura o agente IaaS do SQL Server em segundo plano. Durante este período, o portal do Azure poderá não mostrar que a aplicação de patches automatizada está configurada. Aguarde alguns minutos até que o agente ser instalado, configurado. Depois que o portal do Azure reflete as novas definições.

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell
Depois de aprovisionar a VM do SQL, utilize o PowerShell para configurar a aplicação de patches automatizada.

No exemplo a seguir, o PowerShell é utilizado para configurar a aplicação de patches automatizada de mensagens em fila numa VM existente do SQL Server. O **New-AzVMSqlServerAutoPatchingConfig** comando configura uma nova janela de manutenção para atualizações automáticas.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Se a extensão já não estiver instalada, instalar a extensão reinicia o serviço SQL Server.

Com base neste exemplo, a tabela seguinte descreve o efeito prático na VM do Azure de destino:

| Parâmetro | Efeito |
| --- | --- |
| **DayOfWeek** |Patches instalados cada Quinta-feira. |
| **MaintenanceWindowStartingHour** |Atualizações de begin em 11:00. |
| **MaintenanceWindowsDuration** |Correções sejam instaladas em 120 minutos. Com base na hora de início, têm de executar por 1 21 horas. |
| **PatchCategory** |A definição apenas possível para este parâmetro é **importante**. Esta ação instala a atualização do Windows marcada como importante; não instala as atualizações do SQL Server que não estão incluídas nesta categoria. |

Pode demorar vários minutos para instalar e configurar o agente IaaS do SQL Server.

Para desativar a aplicação de patches automatizada, execute o mesmo script sem o **-ative** parâmetro para o **New-AzVMSqlServerAutoPatchingConfig**. A ausência do **-ative** parâmetro sinaliza o comando para desabilitar o recurso.

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre outras tarefas de automação disponíveis, consulte [extensão de agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [SQL Server em Descrição geral de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

