---
title: Automatizar tarefas de gestão em VMs do SQL (Resource Manager) | Documentos da Microsoft
description: Este artigo descreve como gerir a extensão de agente do SQL Server, que automatiza tarefas de administração do SQL Server específicas. Estes incluem a cópia de segurança automatizada, a aplicação de patches automatizada e integração do Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d3e9b3b2db4a4adc01ad3b1f348b66496658f0f5
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190938"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatizar tarefas de gestão em máquinas virtuais do Azure com a extensão de agente do SQL Server (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

A Extensão Agende IaaS do SQL Server (SqlIaasExtension) é executada nas máquinas virtuais do Azure para automatizar as tarefas de administração. Este artigo fornece uma descrição geral dos serviços suportados pela extensão, bem como instruções para instalação, o estado e remoção.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para ver a versão clássica deste artigo, consulte [extensão de agente do SQL Server para VMs clássicas do SQL Server](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Serviços suportados
A extensão do agente IaaS do SQL Server suporta as seguintes tarefas de administração:

| Funcionalidade de administração | Descrição |
| --- | --- |
| **Cópia de segurança automatizada do SQL** |Automatiza o agendamento de cópias de segurança para todas as bases de dados por qualquer instância predefinida ou uma [corretamente instalados](virtual-machines-windows-sql-server-iaas-faq.md#administration) com o nome a instância do SQL Server na VM. Para obter mais informações, consulte [cópia de segurança automatizada para SQL Server em máquinas virtuais do Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Aplicação de patches automatizada de SQL** |Configura uma janela de manutenção durante o qual as atualizações importantes do Windows para a VM podem ser feita, para que pode evitar atualizações durante as horas de pico para a sua carga de trabalho. Para obter mais informações, consulte [patching automatizado para SQL Server em máquinas virtuais do Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integração do Cofre de Chaves do Azure** |Permite-lhe automaticamente instalado e configurado o Azure Key Vault na sua VM do SQL Server. Para obter mais informações, consulte [configurar o Azure integração Key Vault para o SQL Server em VMs do Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Depois de instalado e em execução, a extensão do agente IaaS do SQL Server disponibiliza estas funcionalidades de administração no painel do SQL Server da máquina virtual no portal do Azure e através do PowerShell do Azure para imagens do marketplace do SQL Server e através do Azure PowerShell para instalações manuais da extensão. 

## <a name="prerequisites"></a>Pré-requisitos
Requisitos para utilizar a extensão do agente IaaS do SQL Server na sua VM:

**Sistema operativo**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Versões do SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Transferir e configurar os comandos do Azure PowerShell mais recente](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Neste momento, o [extensão de agente IaaS do SQL Server](virtual-machines-windows-sql-server-agent-extension.md) não é suportada para a FCI do servidor SQL no Azure. Recomendamos que desinstale a extensão de VMs que participam de um FCI. As funcionalidades suportadas pela extensão não estão disponíveis para as VMs de SQL após a desinstalação do agente.

## <a name="installation"></a>Instalação
A extensão do agente IaaS do SQL Server é instalado automaticamente quando Aprovisiona uma das imagens de Galeria de máquina virtual do SQL Server. A extensão SQL IaaS oferece a capacidade de gestão para uma única instância na VM do SQL Server. Se houver uma instância predefinida, em seguida, a extensão funcionará com a instância predefinida e que não oferecerá suporte a gestão das outras instâncias. Se não existir nenhuma instância predefinida, mas apenas uma instância nomeada, em seguida, irá gerenciar a instância nomeada. Se não existe nenhuma instância predefinida, e existem várias instâncias com nome, a extensão irá falhar instalar. 



Se tiver de reinstalar a extensão manualmente em um destas VMs do SQL Server, utilize o seguinte comando do PowerShell:

```powershell
Set-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension" -Version "2.0" -Location "East US 2"
```

> [!WARNING]
> Se a extensão já não estiver instalada, instalar a extensão reinicia o serviço SQL Server. No entanto, atualizar a extensão SQL IaaS não reinicia o serviço SQL Server. 

> [!NOTE]
> Embora seja possível instalar a extensão do agente IaaS do SQL Server para imagens personalizadas do SQL Server, a funcionalidade está limitada a [alterar o tipo de licença](virtual-machines-windows-sql-ahb.md). Só funcionam em outros recursos fornecidos pela extensão SQL IaaS [imagens da Galeria VM do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms) (pay as you go ou bring-your-own-license).

### <a name="use-a-single-named-instance"></a>Utilizar uma única instância nomeada
A extensão SQL IaaS funcionará com uma instância nomeada numa imagem do SQL Server se a instância predefinida é desinstalada corretamente e se a extensão de IaaS é reinstalada.

Para utilizar uma instância nomeada do SQL Server, efetue o seguinte:
   1. Implemente uma VM do SQL Server do marketplace. 
   1. Desinstale a extensão de IaaS de dentro do [portal do Azure](https://portal.azure.com).
   1. Desinstale o SQL Server inteiramente no interior da VM do SQL Server.
   1. Instale o SQL Server com uma instância nomeada numa VM do SQL Server. 
   1. Instale a extensão de IaaS a partir do portal do Azure.  

## <a name="status"></a>Estado
É uma forma de verificar se a extensão está instalada ver o estado do agente no portal do Azure. Selecione **todas as definições** na janela de máquina virtual e, em seguida, clique em **extensões**. Deverá ver o **SqlIaasExtension** extensão listado.

![Extensão do agente de IaaS do SQL Server no portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Também pode utilizar o **Get-AzVMSqlServerExtension** cmdlet do PowerShell do Azure.

    Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

O comando anterior confirma que o agente está instalado e fornece informações de estado geral. Também pode obter informações de estado específicos sobre a cópia de segurança automatizada e a aplicação de patches com os seguintes comandos.

    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Remoção
No portal do Azure, pode desinstalar a extensão ao clicar nas reticências no **extensões** janela das suas propriedades de máquina virtual. Em seguida, clique em **Eliminar**.

![Desinstale a extensão de agente IaaS do SQL Server no portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Também pode utilizar o **Remove-AzVMSqlServerExtension** cmdlet do PowerShell.

    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"

## <a name="next-steps"></a>Passos Seguintes
Começar a utilizar um dos serviços suportados pela extensão. Para obter mais detalhes, consulte os artigos referenciados no [serviços suportados](#supported-services) seção deste artigo.

Para obter mais informações sobre a execução do SQL Server em máquinas de virtuais do Azure, consulte [SQL Server em Descrição geral de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

