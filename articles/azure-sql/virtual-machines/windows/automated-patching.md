---
title: Patching automatizado para VMs de servidor SQL (Gestor de Recursos) | Microsoft Docs
description: Este artigo explica a função de Patching Automatizado para máquinas virtuais SQL Server que executam no Azure utilizando o Gestor de Recursos.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 429fe39f84a54c22fa97178b85f417d76dc84a8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359477"
---
# <a name="automated-patching-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Patching automatizado para servidor SQL em máquinas virtuais Azure (Gestor de Recursos)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

O Patching Automatizado estabelece uma janela de manutenção para uma máquina virtual Azure que funciona o SQL Server. As Atualizações Automáticas só podem ser instaladas durante essa janela de manutenção. No SQL Server, esta restrição assegura que as atualizações do sistema e eventuais reinícios associados ocorrem na melhor altura para a base de dados. 

> [!IMPORTANT]
> Apenas estão instaladas atualizações do Windows e SQL Server marcadas como **Importante** ou **Críticas.** Outras atualizações do SQL Server, tais como pacotes de serviços e atualizações cumulativas que não estejam marcadas como **Importante** ou **Críticas,** devem ser instaladas manualmente. 

Patching automatizado depende da [infraestrutura do SqL Server como extensão do agente de serviço (IaaS).](sql-server-iaas-agent-extension-automate-management.md)

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar patching automatizado, considere os seguintes pré-requisitos:

**Sistema operativo:**

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019

**Versão SQL Server**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell:**

* [Instale os comandos Azure PowerShell](/powershell/azure/) se planeia configurar patching automatizado com PowerShell.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Patching automatizado baseia-se na extensão do agente IAAS do sql Server. As imagens atuais da galeria de máquinas virtuais SQL adicionam esta extensão por predefinição. Para mais informações, consulte [a extensão do agente IAAS do SqL Server](sql-server-iaas-agent-extension-automate-management.md).
> 
> 

## <a name="settings"></a>Definições
A tabela a seguir descreve as opções que podem ser configuradas para Patching Automatizado. Os passos de configuração reais variam consoante utilize o portal Azure ou os comandos Azure Windows PowerShell.

| Definição | Valores possíveis | Description |
| --- | --- | --- |
| **Aplicação de Patches Automatizada** |Ativar/Desativar (Desativado) |Ativa ou desativa o Patching Automatizado para uma máquina virtual Azure. |
| **Agenda de manutenção** |Todos os dias, segundas, terças, quartas, quintas, sextas, sábados, domingos |O calendário para descarregar e instalar atualizações do Windows, SQL Server e Microsoft para a sua máquina virtual. |
| **Hora de início da manutenção** |0-24 |A hora de início local para atualizar a máquina virtual. |
| **Duração da janela de manutenção** |30-180 |O número de minutos permitidos para completar o download e instalação de atualizações. |
| **Categoria Patch** |Importante | A categoria de atualizações do Windows para descarregar e instalar.|

## <a name="configure-in-the-azure-portal"></a>Configure no portal Azure
Pode utilizar o portal Azure para configurar patching automatizado durante o provisionamento ou para os VM existentes.

### <a name="new-vms"></a>Novos VMs
Utilize o portal Azure para configurar o Patching Automatizado quando criar uma nova máquina virtual SQL Server no modelo de implementação do Gestor de Recursos.

No separador definições do **SQL Server,** selecione **alterar a configuração** sob **remendos automatizados**. A imagem do portal Azure que se segue mostra a lâmina **de remendos automatizado SQL.**

![Patching automatizado SQL no portal Azure](./media/automated-patching/azure-sql-arm-patching.png)

Para obter mais informações, consulte [a Provision a sql server virtual machine on Azure](create-sql-vm-portal.md).

### <a name="existing-vms"></a>VMs existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para as máquinas virtuais do SQL Server existentes, abra o [recurso de máquinas virtuais SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) e selecione **Patching** em **Definições**. 

![Patching Automático SQL para VMs existentes](./media/automated-patching/azure-sql-rm-patching-existing-vms.png)


Quando terminar, clique no botão **OK** na parte inferior da lâmina de configuração do **SQL Server** para guardar as suas alterações.

Se estiver a ativar o Patching Automatizado pela primeira vez, o Azure configura o Agente IAAS do Servidor SQL em segundo plano. Durante este tempo, o portal Azure pode não mostrar que o Patching Automatizado está configurado. Aguarde alguns minutos para que o agente seja instalado e configurado. Depois disso, o portal Azure reflete as novas definições.

## <a name="configure-with-powershell"></a>Configure com PowerShell
Depois de a provisionar o seu SQL VM, utilize o PowerShell para configurar o Patching Automatizado.

No exemplo seguinte, o PowerShell é utilizado para configurar patching automatizado num VM do servidor SQL existente. O comando **New-AzVMSqlServerAutoPatchingConfig** configura uma nova janela de manutenção para atualizações automáticas.

```azurepowershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"
$aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s
Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname
```

> [!IMPORTANT]
> Se a extensão ainda não estiver instalada, a instalação reinicia o SQL Server.

Com base neste exemplo, o quadro que se segue descreve o efeito prático no target Azure VM:

| Parâmetro | Efeito |
| --- | --- |
| **DayOfWeek** |Patches instalados todas as quintas-feiras. |
| **ManutençãoWindowStartingHour** |Inicie as atualizações às 11:00. |
| **ManutençãoWindowsDuration** |As correções devem ser instaladas dentro de 120 minutos. Com base na hora de início, devem estar completos até às 13:00. |
| **PatchCategoria** |A única definição possível para este parâmetro é **importante.** Isto instala a atualização do Windows marcada como Importante; não instala quaisquer atualizações do SQL Server que não estejam incluídas nesta categoria. |

Pode levar vários minutos para instalar e configurar o Agente IAAS do SQL Server.

Para desativar o Patching Automatizado, execute o mesmo script sem o parâmetro **-Ativar** o **novo-AzVMSqlServerAutoPatchingConfig**. A ausência do parâmetro **-Ativar** sinaliza o comando para desativar a função.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre outras tarefas de automatização disponíveis, consulte [a extensão do agente iaaS do servidor SQL.](sql-server-iaas-agent-extension-automate-management.md)

Para obter mais informações sobre a execução do SQL Server em VMs Azure, consulte [o SQL Server na visão geral das máquinas virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).

