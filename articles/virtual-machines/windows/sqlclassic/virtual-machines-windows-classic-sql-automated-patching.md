---
title: Patching automatizado para VMs do SQL Server (clássico) | Documentos da Microsoft
description: Explica a funcionalidade de aplicação de patches automatizada para SQL Server máquinas virtuais em execução no Azure com o modo de implementação clássica.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aa912e3eb76d72e7a79c83d7e51d493310bd36b3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362140"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Patching automatizado para SQL Server em máquinas virtuais do Azure (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Clássico](../classic/sql-automated-patching.md)
> 
> 

A aplicação de patches automatizada estabelece uma janela de manutenção para uma Máquina Virtual do Azure com o SQL Server. As Atualizações Automáticas só podem ser instaladas durante essa janela de manutenção. Para o SQL Server, isto garante que as atualizações do sistema e os reinícios de associados ocorrerem no momento melhor possível para a base de dados. 

> [!IMPORTANT]
> Só são instaladas as atualizações do Windows assinaladas como **Importantes**. As outras atualizações do SQL Server, como as Atualizações Cumulativas, têm de ser instaladas manualmente. 

A Colocação de Patches Automática depende da [Extensão Agente IaaS do SQL Server](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para ver a versão do Resource Manager deste artigo, consulte [aplicação de patches automatizada para SQL Server no Gestor de recursos de máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-automated-patching.md).

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

* [Instalar os comandos do Azure PowerShell mais recente](/powershell/azure/overview).

**Extensão de IaaS do SQL Server**:

* [Instalar a extensão de IaaS do SQL Server](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Definições
A tabela seguinte descreve as opções que podem ser configuradas para a aplicação de patches automatizada. Para VMs clássicas, tem de utilizar o PowerShell para configurar estas definições.

| Definição | Valores possíveis | Descrição |
| --- | --- | --- |
| **Aplicação de Patches Automatizada** |Ativar/desativar (desativado) |Ativa ou desativa a aplicação de patches automatizada para uma máquina virtual do Azure. |
| **Agenda de manutenção** |Todos os dias, segunda-feira, Terça-feira, quarta-feira, Quinta-feira, sexta-feira, Sábado, Domingo |A agenda para baixar e instalar atualizações do Windows, SQL Server e Microsoft para a máquina virtual. |
| **Hora de início da manutenção** |0-24 |A hora de início local para atualizar a máquina virtual. |
| **Duração da janela de manutenção** |30-180 |O número de minutos para ao concluir a transferência e instalação de atualizações. |
| **Categoria de patch** |Importante |A categoria de atualizações para transferir e instalar. |

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell
No exemplo a seguir, o PowerShell é utilizado para configurar a aplicação de patches automatizada de mensagens em fila numa VM existente do SQL Server. O **New-AzureVMSqlServerAutoPatchingConfig** comando configura uma nova janela de manutenção para atualizações automáticas.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Com base neste exemplo, a tabela seguinte descreve o efeito prático na VM do Azure de destino:

| Parâmetro | Efeito |
| --- | --- |
| **DayOfWeek** |Patches instalados cada Quinta-feira. |
| **MaintenanceWindowStartingHour** |Atualizações de begin em 11:00. |
| **MaintenanceWindowDuration** |Correções sejam instaladas em 120 minutos. Com base na hora de início, têm de executar por 1 21 horas. |
| **PatchCategory** |A definição apenas possível para este parâmetro é "Importante". |

Pode demorar vários minutos para instalar e configurar o agente IaaS do SQL Server.

Para desativar a aplicação de patches automatizada, execute o mesmo script sem o - parâmetro de Enable para New-AzureVMSqlServerAutoPatchingConfig. Tal como acontece com a instalação, pode demorar vários minutos para desativar a aplicação de patches automatizada.

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre outras tarefas de automação disponíveis, consulte [extensão de agente IaaS do SQL Server](../classic/sql-server-agent-extension.md).

Para obter mais informações sobre como executar o SQL Server em VMs do Azure, consulte [SQL Server em Descrição geral de máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

