---
title: Patching automatizado para VMs de servidor SQL (Gestor de Recursos) [ Microsoft Docs
description: Explica a função de Patching Automatizado para máquinas virtuais do Servidor SQL que estão a funcionar em Azure utilizando o Gestor de Recursos.
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
ms.openlocfilehash: 292f3e8819f6f9f4b2989423814e02dfcfb4bfdb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127678"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Aplicação de Patches Automatizada para o SQL Server nas Máquinas Virtuais do Azure (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [Clássica](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

A colocação de patches automática estabelece uma janela de manutenção para uma Máquina Virtual do Azure a executar o SQL Server. As Atualizações Automáticas só podem ser instaladas durante essa janela de manutenção. No SQL Server, esta restrição assegura que as atualizações do sistema e eventuais reinícios associados ocorrem na melhor altura para a base de dados. 

> [!IMPORTANT]
> Apenas as atualizações do Windows e do SQL Server marcadas como **importantes** ou **críticas** são instaladas. Outras atualizações do SQL Server, tais como pacotes de serviços e atualizações cumulativas que não estejam marcadas como **Importantes** ou **Críticas,** devem ser instaladas manualmente. 

A Colocação de Patches Automática depende da [Extensão Agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar patching automatizado, considere os seguintes pré-requisitos:

**Sistema operativo:**

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versão SQL Server**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell:**

* [Instale os mais recentes comandos Azure PowerShell](/powershell/azure/overview) se pretender configurar Patching Automatizado com powerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Patching automatizado depende da extensão do agente SQL Server IaaS. As imagens atuais da galeria de máquinas virtuais SQL adicionam esta extensão por padrão. Para mais informações, consulte a extensão do [agente SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).
> 
> 

## <a name="settings"></a>Definições
A tabela seguinte descreve as opções que podem ser configuradas para Patching Automatizado. Os passos reais de configuração variam consoante utilize o portal Azure ou os comandos Azure Windows PowerShell.

| Definição | Valores possíveis | Descrição |
| --- | --- | --- |
| **Aplicação de Patches Automatizada** |Ativar/Desativar (Desativado) |Ativa ou desativa Patching Automatizado para uma máquina virtual Azure. |
| **Agenda de manutenção** |Todos os dias, segunda, terça, quarta, quinta, sexta, sábado, domingo |A programação para descarregar e instalar as atualizações do Windows, SQL Server e Microsoft para a sua máquina virtual. |
| **Hora de início da manutenção** |0-24 |A hora de início local para atualizar a máquina virtual. |
| **Duração da janela de manutenção** |30-180 |O número de minutos permitidos para completar o download e instalação de atualizações. |
| **Categoria Patch** |Importante | A categoria de atualizações do Windows para descarregar e instalar.|

## <a name="configuration-in-the-portal"></a>Configuração no Portal
Pode utilizar o portal Azure para configurar patching automatizado durante o provisionamento ou para VMs existentes.

### <a name="new-vms"></a>Novos VMs
Utilize o portal Azure para configurar patching automatizado quando criar uma nova máquina virtual do Servidor SQL no modelo de implementação do Gestor de Recursos.

No separador de definições do **Servidor SQL,** selecione a **configuração De alterar** sob **patching automatizado**. A seguinte imagem do portal Azure mostra a lâmina **de patching automatizada SQL.**

![Patching Automatizado SQL no portal Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Para o contexto, consulte o tópico completo sobre o fornecimento de [uma máquina virtual SQL Server em Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para as máquinas virtuais existentes do SQL Server, abra o recurso das [máquinas virtuais SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) e selecione **Patching** em **Definições**. 

![Patching Automático SQL para VMs existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


Quando terminar, clique no botão **OK** na parte inferior da lâmina de configuração do **Servidor SQL** para guardar as alterações.

Se estiver a permitir patching automatizado pela primeira vez, o Azure confunde o Agente IaaS do Servidor SQL em segundo plano. Durante este tempo, o portal Azure pode não mostrar que o Patching Automatizado está configurado. Aguarde vários minutos para que o agente seja instalado, configurado. Depois disso, o portal Azure reflete as novas definições.

## <a name="configuration-with-powershell"></a>Configuração com PowerShell
Depois de fornecer o seu VM SQL, utilize o PowerShell para configurar patching automatizado.

No exemplo seguinte, o PowerShell é usado para configurar patching automatizado num VM de servidor SQL existente. O comando **New-AzVMSqlServerAutoPatchingConfig** configura uma nova janela de manutenção para atualizações automáticas.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> Se a extensão ainda não estiver instalada, a instalação da extensão reinicia o serviço SQL Server.

Com base neste exemplo, a tabela seguinte descreve o efeito prático no vitelo-alvo:

| Parâmetro | Efeito |
| --- | --- |
| **DayofWeek** |Patches instalados todas as quintas-feiras. |
| **ManutençãoJanelaHora de Início** |Comece as atualizações às 11:00. |
| **ManutençãoWindowsDuração** |As manchas devem ser instaladas dentro de 120 minutos. Com base na hora de início, devem estar concluídos até às 13h. |
| **Categoria Patch** |A única definição possível para este parâmetro é **importante**. Isto instala a atualização do Windows marcada como Importante; não instala nenhuma atualização do SQL Server que não esteja incluída nesta categoria. |

Pode levar vários minutos para instalar e configurar o Agente IaaS do Servidor SQL.

Para desativar o Patching Automatizado, execute o mesmo script sem o **parâmetro -Ativar** o **Novo AzVMSqlServerAutoPatchingConfig**. A ausência do parâmetro **-Ativar** sinaliza o comando para desativar a função.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre outras tarefas de automatização disponíveis, consulte a extensão do [agente SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Para obter mais informações sobre a execução do Servidor SQL em VMs Azure, consulte [o SQL Server na visão geral das Máquinas Virtuais Azure](virtual-machines-windows-sql-server-iaas-overview.md).

