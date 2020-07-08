---
title: Patching Automatizado para VMs do servidor SQL (Clássico) Microsoft Docs
description: Explica a função de patching automatizado para máquinas virtuais do servidor SQL que estão a funcionar em Azure utilizando o modo de implementação clássico.
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
ms.openlocfilehash: 3b977c0900178d64f202d4faa122f5d0ef90187c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84014660"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Patching automatizado para servidor SQL em máquinas virtuais Azure (Clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/automated-patching.md)
> * [Clássico](../classic/sql-automated-patching.md)
> 
> 

A colocação de patches automática estabelece uma janela de manutenção para uma Máquina Virtual do Azure a executar o SQL Server. As Atualizações Automáticas só podem ser instaladas durante essa janela de manutenção. Para o SQL Server, isto garante que as atualizações do sistema e quaisquer reinícios associados ocorram na melhor altura possível para a base de dados. 

> [!IMPORTANT]
> Só são instaladas as atualizações do Windows assinaladas como **Importantes**. As outras atualizações do SQL Server, como as Atualizações Cumulativas, têm de ser instaladas manualmente. 

A Colocação de Patches Automática depende da [Extensão Agente IaaS do SQL Server](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> A Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Classic.](../../../azure-resource-manager/management/deployment-models.md) Este artigo abrange utilizando o modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para ver a versão do Gestor de Recursos deste artigo, consulte [Patching Automatizado para O Servidor SQL no Azure Virtual Machines Resource Manager](../../../azure-sql/virtual-machines/windows/automated-patching.md).

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar patching automatizado, considere os seguintes pré-requisitos:

**Sistema operativo:**

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versão SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell:**

* [Instale os mais recentes comandos Azure PowerShell](/powershell/azure/overview).

**Extensão IAAS do servidor SQL:**

* [Instale a extensão IAAS do servidor SQL](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Definições
A tabela a seguir descreve as opções que podem ser configuradas para Patching Automatizado. Para os VM clássicos, deve utilizar o PowerShell para configurar estas definições.

| Definição | Valores possíveis | Descrição |
| --- | --- | --- |
| **Aplicação de Patches Automatizada** |Ativar/Desativar (Desativado) |Ativa ou desativa o Patching Automatizado para uma máquina virtual Azure. |
| **Agenda de manutenção** |Todos os dias, segundas, terças, quartas, quintas, sextas, sábados, domingos |O calendário para descarregar e instalar atualizações do Windows, SQL Server e Microsoft para a sua máquina virtual. |
| **Hora de início da manutenção** |0-24 |A hora de início local para atualizar a máquina virtual. |
| **Duração da janela de manutenção** |30-180 |O número de minutos permitidos para completar o download e instalação de atualizações. |
| **Categoria Patch** |Importante |A categoria de atualizações para descarregar e instalar. |

## <a name="configuration-with-powershell"></a>Configuração com PowerShell
No exemplo seguinte, o PowerShell é utilizado para configurar patching automatizado num VM do servidor SQL existente. O comando **New-AzureVMSqlServerAutoPatching Configurar** configura uma nova janela de manutenção para atualizações automáticas.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Com base neste exemplo, o quadro que se segue descreve o efeito prático no target Azure VM:

| Parâmetro | Efeito |
| --- | --- |
| **DayOfWeek** |Patches instalados todas as quintas-feiras. |
| **ManutençãoWindowStartingHour** |Inicie as atualizações às 11:00. |
| **ManutençãoWindowDuration** |As correções devem ser instaladas dentro de 120 minutos. Com base na hora de início, devem estar completos até às 13:00. |
| **PatchCategoria** |A única definição possível para este parâmetro é "Importante". |

Pode levar vários minutos para instalar e configurar o Agente IAAS do SQL Server.

Para desativar o Patching Automatizado, execute o mesmo script sem o parâmetro -Ativar o novo-AzureVMSqlServerAutoPatchingConfig. Tal como na instalação, pode levar vários minutos a desativar o Patching Automatizado.

## <a name="next-steps"></a>Próximos passos
Para obter informações sobre outras tarefas de automatização disponíveis, consulte [a extensão do agente iaaS do servidor SQL.](../classic/sql-server-agent-extension.md)

Para obter mais informações sobre a execução do SQL Server em VMs Azure, consulte [o SQL Server na visão geral das Máquinas Virtuais Azure](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

