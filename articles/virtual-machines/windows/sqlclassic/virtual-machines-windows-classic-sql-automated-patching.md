---
title: Aplicação de patch automatizada para VMs SQL Servers (clássicas) | Microsoft Docs
description: Explica o recurso de aplicação de patch automatizado para SQL Server máquinas virtuais em execução no Azure usando o modo de implantação clássico.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: efc6d0c25c5186b391deb08ee0e41dcb8ae6edf0
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978087"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Aplicação de patch automatizada para SQL Server em máquinas virtuais do Azure (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Clássico](../classic/sql-automated-patching.md)
> 
> 

A aplicação de patch automatizada estabelece uma janela de manutenção para uma máquina virtual do Azure que executa o SQL Server. As Atualizações Automáticas só podem ser instaladas durante essa janela de manutenção. Por SQL Server, isso garante que as atualizações do sistema e quaisquer reinicializações associadas ocorram no melhor momento possível para o banco de dados. 

> [!IMPORTANT]
> Só são instaladas as atualizações do Windows assinaladas como **Importantes**. As outras atualizações do SQL Server, como as Atualizações Cumulativas, têm de ser instaladas manualmente. 

A Colocação de Patches Automática depende da [Extensão Agente IaaS do SQL Server](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos e clássico](../../../azure-resource-manager/management/deployment-models.md). Este artigo aborda o uso do modelo de implantação clássico. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para exibir a versão do Resource Manager deste artigo, consulte [aplicação de patch automatizada para SQL Server no Gerenciador de recursos de máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Pré-requisitos
Para usar a aplicação de patch automatizada, considere os seguintes pré-requisitos:

**Sistema operacional**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versão do SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Instale os comandos mais recentes do Azure PowerShell](/powershell/azure/overview).

**SQL Server extensão IaaS**:

* [Instale a extensão de IaaS SQL Server](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Definições
A tabela a seguir descreve as opções que podem ser configuradas para aplicação de patch automatizada. Para VMs clássicas, você deve usar o PowerShell para definir essas configurações.

| Definição | Valores possíveis | Descrição |
| --- | --- | --- |
| **Aplicação de Patches Automatizada** |Habilitar/desabilitar (desabilitado) |Habilita ou desabilita a aplicação de patch automatizada para uma máquina virtual do Azure. |
| **Agendamento de manutenção** |Todos os dias, segunda-feira, terça-feira, quarta-feira, sexta-feira, sábado, domingo |O agendamento para baixar e instalar o Windows, SQL Server e atualizações da Microsoft para sua máquina virtual. |
| **Hora de início da manutenção** |0-24 |A hora de início local para atualizar a máquina virtual. |
| **Duração da janela de manutenção** |30-180 |O número de minutos permitido para concluir o download e a instalação de atualizações. |
| **Categoria do patch** |Importante |A categoria de atualizações a serem baixadas e instaladas. |

## <a name="configuration-with-powershell"></a>Configuração com o PowerShell
No exemplo a seguir, o PowerShell é usado para configurar a aplicação de patch automatizada em uma VM de SQL Server existente. O comando **New-AzureVMSqlServerAutoPatchingConfig** configura uma nova janela de manutenção para atualizações automáticas.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Com base neste exemplo, a tabela a seguir descreve o efeito prático na VM do Azure de destino:

| Parâmetro | Efeito |
| --- | --- |
| **DayOfWeek** |Patches instalados toda quinta-feira. |
| **MaintenanceWindowStartingHour** |Inicie as atualizações às 11:10:00. |
| **MaintenanceWindowDuration** |Os patches devem ser instalados em até 120 minutos. Com base na hora de início, eles devem ser concluídos em 1:13h. |
| **PatchCategory** |A única configuração possível para esse parâmetro é "importante". |

Pode levar vários minutos para instalar e configurar o agente IaaS SQL Server.

Para desabilitar a aplicação de patch automatizada, execute o mesmo script sem o parâmetro-Enable para o New-AzureVMSqlServerAutoPatchingConfig. Assim como acontece com a instalação, pode levar vários minutos para desabilitar a aplicação de patch automatizada.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre outras tarefas de automação disponíveis, consulte [SQL Server extensão do agente IaaS](../classic/sql-server-agent-extension.md).

Para obter mais informações sobre como executar SQL Server em VMs do Azure, consulte [visão geral de SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

