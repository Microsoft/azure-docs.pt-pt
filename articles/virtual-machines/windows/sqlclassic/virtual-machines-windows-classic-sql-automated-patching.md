---
title: Patching automatizado para VMs de servidor SQL (Clássico) / Microsoft Docs
description: Explica a função de Patching Automatizado para máquinas virtuais do Servidor SQL que estão a funcionar em Azure utilizando o modo de implementação clássico.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978087"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Patching automatizado para servidor SQL em máquinas virtuais azure (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [Clássica](../classic/sql-automated-patching.md)
> 
> 

A colocação de patches automática estabelece uma janela de manutenção para uma Máquina Virtual do Azure a executar o SQL Server. As Atualizações Automáticas só podem ser instaladas durante essa janela de manutenção. Para o SQL Server, isto garante que as atualizações do sistema e quaisquer reinícios associados ocorrem na melhor altura possível para a base de dados. 

> [!IMPORTANT]
> Só são instaladas as atualizações do Windows assinaladas como **Importantes**. As outras atualizações do SQL Server, como as Atualizações Cumulativas, têm de ser instaladas manualmente. 

A Colocação de Patches Automática depende da [Extensão Agente IaaS do SQL Server](../classic/sql-server-agent-extension.md).

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Clássico.](../../../azure-resource-manager/management/deployment-models.md) Este artigo cobre a utilização do modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para ver a versão do Gestor de Recursos deste artigo, consulte [Patching Automatizado para Servidor SQL em Gestor](../sql/virtual-machines-windows-sql-automated-patching.md)de Recursos de Máquinas Virtuais Azure .

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

**Extensão SQL Server IaaS**:

* [Instale a extensão SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Definições
A tabela seguinte descreve as opções que podem ser configuradas para Patching Automatizado. Para VMs clássicos, deve utilizar o PowerShell para configurar estas definições.

| Definição | Valores possíveis | Descrição |
| --- | --- | --- |
| **Aplicação de Patches Automatizada** |Ativar/Desativar (Desativado) |Ativa ou desativa Patching Automatizado para uma máquina virtual Azure. |
| **Agenda de manutenção** |Todos os dias, segunda, terça, quarta, quinta, sexta, sábado, domingo |A programação para descarregar e instalar as atualizações do Windows, SQL Server e Microsoft para a sua máquina virtual. |
| **Hora de início da manutenção** |0-24 |A hora de início local para atualizar a máquina virtual. |
| **Duração da janela de manutenção** |30-180 |O número de minutos permitidos para completar o download e instalação de atualizações. |
| **Categoria Patch** |Importante |A categoria de atualizações para descarregar e instalar. |

## <a name="configuration-with-powershell"></a>Configuração com PowerShell
No exemplo seguinte, o PowerShell é usado para configurar patching automatizado num VM de servidor SQL existente. O comando **New-AzureVMSqlServerAutoPatchingConfig** configura uma nova janela de manutenção para atualizações automáticas.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Com base neste exemplo, a tabela seguinte descreve o efeito prático no vitelo-alvo:

| Parâmetro | Efeito |
| --- | --- |
| **DayofWeek** |Patches instalados todas as quintas-feiras. |
| **ManutençãoJanelaHora de Início** |Comece as atualizações às 11:00. |
| **ManutençãoDuração da janela** |As manchas devem ser instaladas dentro de 120 minutos. Com base na hora de início, devem estar concluídos até às 13h. |
| **Categoria Patch** |A única configuração possível para este parâmetro é "Importante". |

Pode levar vários minutos para instalar e configurar o Agente IaaS do Servidor SQL.

Para desativar o Patching Automatizado, execute o mesmo script sem o parâmetro -Ativar o Novo AzureVMSqlServerAutoPatchingConfig. Tal como acontece com a instalação, pode levar vários minutos a desativar o Patching Automatizado.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre outras tarefas de automatização disponíveis, consulte a extensão do [agente SQL Server IaaS](../classic/sql-server-agent-extension.md).

Para obter mais informações sobre a execução do Servidor SQL em VMs Azure, consulte [o SQL Server na visão geral das Máquinas Virtuais Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

