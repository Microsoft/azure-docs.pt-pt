---
title: O que é a extensão do Agente IAAS do SqL Server?
description: Este artigo descreve como a extensão do Agente IAAS do SQL Server ajuda a automatizar tarefas de administração específicas de gestão do SQL Server em VMs Azure. Estes incluem funcionalidades como backup automatizado, patching automatizado, integração Azure Key Vault, gestão de licenciamento, configuração de armazenamento e gestão central de todas as instâncias SQL Server VM.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: fdff3f6144f7099f3f61cfe57186357e17136e9f
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225494"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>Automatizar a gestão com a extensão do Agente IAAS do SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


A extensão do Agente IAAS do SQL Server (SqlIaasExtension) funciona no SQL Server em Azure Virtual Machines (VMs) para automatizar tarefas de gestão e administração. 

Este artigo fornece uma visão geral da extensão. Para instalar a extensão IAAS do SQL Server ao SQL Server em VMs Azure, consulte os artigos de [instalação automática,](sql-agent-extension-automatic-registration-all-vms.md) [VMs simples](sql-agent-extension-manually-register-single-vm.md)ou [VMs a granel](sql-agent-extension-manually-register-vms-bulk.md). 

## <a name="overview"></a>Descrição Geral

A extensão do Agente IAAS do SQL Server permite a integração com o portal Azure, e dependendo do modo de gestão, desbloqueia uma série de benefícios de funcionalidade para o SQL Server em VMs Azure: 

- **Benefícios da funcionalidade**: A extensão desbloqueia uma série de benefícios de funcionalidades de automatização, tais como gestão de portais, flexibilidade de licenças, backup automatizado, patching automatizado e muito mais. Consulte [os benefícios de Recurso](#feature-benefits) mais tarde neste artigo para mais detalhes. 

- **Conformidade**: A extensão oferece um método simplificado de cumprimento da obrigação de notificar a Microsoft de que o Benefício Híbrido Azure foi ativado conforme especificado nos termos do produto. Este processo nega a necessidade de gerir formulários de registo de licenciamento para cada recurso.  

- **Grátis**: A extensão nos três modos de gestão é completamente gratuita. Não há custo adicional associado à extensão, ou à alteração dos modos de gestão. 

- **Gestão simplificada da licença**: A extensão simplifica a gestão da licença do SQL Server e permite identificar rapidamente os VMs do Servidor SQL com o Benefício Híbrido Azure habilitado utilizando o [portal Azure](manage-sql-vm-portal.md), PowerShell ou o Azure CLI: 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---


> [!IMPORTANT]
> A extensão do Agente IAAS SQL recolhe dados com o propósito expresso de dar aos clientes benefícios opcionais ao utilizar o SQL Server dentro de Máquinas Virtuais Azure. A Microsoft não utilizará estes dados para licenciar auditorias sem o consentimento prévio do cliente. Consulte o [suplemento de privacidade SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data) para obter mais informações.


## <a name="feature-benefits"></a>Benefícios de recurso 

A extensão do Agente IAAS do SqL Server desbloqueia uma série de benefícios de funcionalidade para gerir o seu SQL Server VM. 

Os seguintes dados da tabela são pormenores sobre estes benefícios: 


| Funcionalidade | Descrição |
| --- | --- |
| **Gestão do portal** | Desbloqueia [a gestão no portal](manage-sql-vm-portal.md), para que possa ver todos os VMs do seu SQL Server num só local, e para que possa ativar e desativar funcionalidades específicas do SQL diretamente do portal. <br/> Modo de gestão: & leves cheios|  
| **Backup automatizado** |Automatiza o agendamento de backups para todas as bases de dados para a instância predefinida ou para uma instância nomeada [corretamente instalada](frequently-asked-questions-faq.md#administration) do SQL Server no VM. Para obter mais informações, consulte [a cópia de segurança automatizada para o SQL Server em máquinas virtuais Azure (Gestor de Recursos)](automated-backup-sql-2014.md). <br/> Modo de gestão: Completo|
| **Remendação automatizada** |Configura uma janela de manutenção durante a qual podem ocorrer atualizações importantes de segurança do Windows e SQL Server para o seu VM, para que possa evitar atualizações durante os tempos de pico da sua carga de trabalho. Para obter mais informações, consulte [patching automatizado para O Servidor SQL em máquinas virtuais Azure (Gestor de Recursos)](automated-patching.md). <br/> Modo de gestão: Completo|
| **Integração do Cofre chave Azure** |Permite-lhe instalar e configurar automaticamente o Cofre da Chave Azure no seu SQL Server VM. Para obter mais informações, consulte [a integração do Cofre de Chave Configure para O Servidor SQL em Máquinas Virtuais Azure (Gestor de Recursos)](azure-key-vault-integration-configure.md). <br/> Modo de gestão: Completo|
| **Ver utilização do disco no portal** | Permite-lhe visualizar uma representação gráfica da utilização do disco dos seus ficheiros de dados SQL no portal Azure.  <br/> Modo de gestão: Completo | 
| **Licenciamento flexível** | Poupe no [custo, transitando perfeitamente](licensing-model-azure-hybrid-benefit-ahb-change.md) da licença de trazer a sua própria licença (também conhecida como Azure Hybrid Benefit) para o modelo de licenciamento pay-as-you-go e de volta. <br/> Modo de gestão: & leves cheios| 
| **Versão /edição flexível** | Se decidir alterar a [versão](change-sql-server-version.md) ou [edição](change-sql-server-edition.md) do SQL Server, pode atualizar os metadados dentro do portal Azure sem ter de recolocar todo o SQL Server VM.  <br/> Modo de gestão: & leves cheios| 


## <a name="management-modes"></a>Modos de gestão

Pode optar por registar a sua extensão SQL IaaS em três modos de gestão: 

- **O** modo leve copia binários de extensão para o VM, mas não instala o agente, e não reinicia o serviço SQL Server. O modo leve só suporta alterar o tipo de licença e edição do SQL Server e proporciona uma gestão limitada do portal. Utilize esta opção para VMs do SERVIDOR SQL com múltiplas instâncias, ou aqueles que participam num caso de cluster de failover (FCI). O modo leve é o modo de gestão predefinido quando se utiliza a [função de registo automático](sql-agent-extension-automatic-registration-all-vms.md) ou quando um tipo de gestão não é especificado durante o registo manual. Não há impacto na memória ou CPU quando se utiliza o modo leve, e não há custos associados. É aconselhável registar primeiro o seu SQL Server VM em modo leve e, em seguida, atualizar para o modo Full durante uma janela de manutenção programada. 

- **O** modo completo instala o SqL IaaS Agent para o VM para fornecer todas as funcionalidades, mas requer um reinício das permissões de serviço e administrador do sistema SQL Server. Utilize-o para gerir um SQL Server VM com uma única instância. O modo completo instala dois serviços windows que têm um impacto mínimo na memória e CPU - estes podem ser monitorizados através do gestor de tarefas. Não há qualquer custo associado à utilização do modo de gestão completa. 

- O modo **NoAgent** é dedicado ao SQL Server 2008 e ao SQL Server 2008 R2 instalado no Windows Server 2008. Não há impacto na memória ou CPU quando se utiliza o modo NoAgent. Não existe qualquer custo associado à utilização do modo de gestão NoAgent, o SQL Server não é reiniciado e um agente não é instalado no VM. 

Pode ver o modo atual do seu agente IAAS do Servidor SQL utilizando o Azure PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>Instalação

Registe o seu SQL Server VM com a extensão SQL Server IaaS Agent para criar o _recurso_ **de máquina virtual SQL** dentro da sua subscrição, que é um recurso _separado_ do recurso da máquina virtual. Desregralar o seu SQL Server VM da extensão removerá o _recurso_ **da máquina virtual SQL,** mas não deixará cair a máquina virtual real.

A implementação de uma imagem do SQL Server VM Azure Marketplace através do portal Azure regista automaticamente o SQL Server VM com a extensão. No entanto, se optar por instalar o SQL Server numa máquina virtual Azure ou providenciar uma máquina virtual Azure a partir de um VHD personalizado, então deve registar o seu SQL Server VM com a extensão SQL IaaS para desbloquear os benefícios da funcionalidade. 

Registar a extensão em modo leve copiará os binários, mas não instalará o agente no VM. O agente é instalado no VM quando a extensão é atualizada para o modo de gestão completa. 

Há três formas de registar com a extensão: 
- [Automaticamente para todos os VMs atuais e futuros numa subscrição](sql-agent-extension-automatic-registration-all-vms.md)
- [Manualmente para um único VM](sql-agent-extension-manually-register-single-vm.md)
- [Manualmente para vários VMs a granel](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>Apoio de instância nomeado

A extensão do Agente IAAS do SQL Server funciona com uma instância nomeada do SQL Server se for a única instância do SQL Server disponível na máquina virtual. A extensão não é instalada em VMs que têm várias instâncias nomeadas sql Server se não houver instância padrão no VM. 

Para utilizar uma instância nomeada do SQL Server, instale uma máquina virtual Azure, instale uma única instância do SQL Server e registe-a com a [Extensão SQL IaaS](sql-agent-extension-manually-register-single-vm.md).

Em alternativa, para utilizar uma instância nomeada com uma imagem do Servidor SQL do Azure Marketplace, siga estes passos: 

   1. Implementar um SQL Server VM do Azure Marketplace. 
   1. [Desativar](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) o SQL Server VM a partir da extensão do Agente IAAS SQL. 
   1. Desinstale completamente o SQL Server dentro do SQL Server VM.
   1. Instale o SQL Server com uma instância nomeada dentro do SQL Server VM. 
   1. [Registe o VM com a Extensão do Agente SQL IaaS](sql-agent-extension-manually-register-single-vm.md#register-with-extension). 

## <a name="verify-status-of-extension"></a>Verificar estado de extensão

Utilize o portal Azure ou a Azure PowerShell para verificar o estado da extensão. 

### <a name="azure-portal"></a>Portal do Azure

Verifique se a extensão está instalada no portal Azure. 

Selecione **todas as definições** no painel de máquinas virtual e, em seguida, selecione **Extensões**. Deve ver a extensão **sqlIaasExtension** listada.

![Estado da extensão do Agente IAAS do SqL Server no portal Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

Também pode utilizar o **cmdlet Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

O comando anterior confirma que o agente está instalado e fornece informações gerais sobre o estado. Pode obter informações específicas sobre a cópia de segurança e remendos automatizados utilizando os seguintes comandos:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>Limitações

A extensão do Agente IAAS SQL apenas suporta: 

- VMs do servidor SQL implantados através do Gestor de Recursos Azure. Os VMs do Servidor SQL implantados através do modelo clássico não são suportados. 
- VMs do servidor SQL implantados para o público ou nuvem do Governo Azure. As deslocações para outras nuvens privadas ou governamentais não são apoiadas. 


## <a name="in-region-data-residency"></a>Residência de dados na região
A máquina virtual Azure SQL e a Extensão do Agente SQL IaaS não movem nem armazenam os dados dos clientes para fora da região em que são implantados.

## <a name="next-steps"></a>Passos seguintes

Para instalar a extensão IAAS do SQL Server ao SQL Server em VMs Azure, consulte os artigos de [instalação automática,](sql-agent-extension-automatic-registration-all-vms.md) [VMs simples](sql-agent-extension-manually-register-single-vm.md)ou [VMs a granel](sql-agent-extension-manually-register-vms-bulk.md).

Para obter mais informações sobre a execução do SQL Server em Máquinas Virtuais Azure, consulte o [Servidor SQL em Máquinas Virtuais Azure?](sql-server-on-azure-vm-iaas-what-is-overview.md)

Para saber mais, consulte [perguntas frequentes.](frequently-asked-questions-faq.md) 
