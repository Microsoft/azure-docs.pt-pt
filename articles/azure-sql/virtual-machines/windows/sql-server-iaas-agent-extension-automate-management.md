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
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/30/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 070058eae36bf4f8546cfcf4beb85ac5023e9c79
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286174"
---
# <a name="what-is-the-sql-server-iaas-agent-extension"></a>O que é a extensão do Agente IAAS do SqL Server?
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


A extensão do Agente IAAS do SQL Server (SqlIaasExtension) funciona no SQL Server em Azure Virtual Machines (VMs) para automatizar tarefas de gestão e administração. 

Este artigo fornece uma visão geral da extensão. Para instalar a extensão IAAS do SQL Server ao SQL Server em VMs Azure, consulte os artigos de [instalação automática,](sql-vm-resource-provider-automatic-registration.md) [VMs simples](sql-vm-resource-provider-register.md)ou [VMs a granel](sql-vm-resource-provider-bulk-register.md). 

## <a name="overview"></a>Descrição Geral

A extensão do Agente IAAS do Servidor SQL proporciona uma série de benefícios para o SQL Server em VMs Azure: 

- **Benefícios da funcionalidade** : A extensão desbloqueia uma série de benefícios de funcionalidades de automatização, tais como gestão de portais, flexibilidade de licenças, backup automatizado, patching automatizado e muito mais. Consulte [os benefícios de Recurso](#feature-benefits) mais tarde neste artigo para mais detalhes. 

- **Conformidade** : A extensão oferece um método simplificado de cumprimento da obrigação de notificar a Microsoft de que o Benefício Híbrido Azure foi ativado conforme especificado nos termos do produto. Este processo nega a necessidade de gerir formulários de registo de licenciamento para cada recurso.  

- **Grátis** : A extensão nos três modos de gestão é completamente gratuita. Não existe um custo adicional associado ao fornecedor de recursos, nem com a alteração dos modos de gestão. 

- **Gestão simplificada da licença** : A extensão simplifica a gestão da licença do SQL Server e permite identificar rapidamente os VMs do Servidor SQL com o Benefício Híbrido Azure habilitado através do [portal Azure](manage-sql-vm-portal.md), o Azure CLI ou PowerShell: 

   # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---


> [!IMPORTANT]
> A extensão do Agente IAAS SQL recolhe dados com o propósito expresso de dar aos clientes benefícios opcionais ao utilizar o SQL Server dentro de Máquinas Virtuais Azure. A Microsoft não utilizará estes dados para licenciar auditorias sem o consentimento prévio do cliente. Consulte o [suplemento de privacidade SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data) para obter mais informações.


## <a name="feature-benefits"></a>Benefícios de recurso 

A extensão do Agente IAAS do SqL Server desbloqueia uma série de benefícios de funcionalidade para gerir o seu SQL Server VM. 

Os seguintes dados da tabela são pormenores sobre estes benefícios: 


| Funcionalidade | Descrição |
| --- | --- |
| **Gestão do portal** | Desbloqueia [a gestão no portal](manage-sql-vm-portal.md), para que possa ver todos os VMs do seu SQL Server num só local, e para que possa ativar e desativar funcionalidades específicas do SQL diretamente do portal. 
| **Backup automatizado** |Automatiza o agendamento de backups para todas as bases de dados para a instância predefinida ou para uma instância nomeada [corretamente instalada](frequently-asked-questions-faq.md#administration) do SQL Server no VM. Para obter mais informações, consulte [a cópia de segurança automatizada para o SQL Server em máquinas virtuais Azure (Gestor de Recursos)](automated-backup-sql-2014.md). |
| **Remendação automatizada** |Configura uma janela de manutenção durante a qual podem ocorrer atualizações importantes de segurança do Windows e SQL Server para o seu VM, para que possa evitar atualizações durante os tempos de pico da sua carga de trabalho. Para obter mais informações, consulte [patching automatizado para O Servidor SQL em máquinas virtuais Azure (Gestor de Recursos)](automated-patching.md). |
| **Integração do Cofre de Chaves do Azure** |Permite-lhe instalar e configurar automaticamente o Cofre da Chave Azure no seu SQL Server VM. Para obter mais informações, consulte [a integração do Cofre de Chave Configure para O Servidor SQL em Máquinas Virtuais Azure (Gestor de Recursos)](azure-key-vault-integration-configure.md). |
| **Licenciamento flexível** | Poupe no [custo, transitando perfeitamente](licensing-model-azure-hybrid-benefit-ahb-change.md) da licença de trazer a sua própria licença (também conhecida como Azure Hybrid Benefit) para o modelo de licenciamento pay-as-you-go e de volta. | 
| **Versão /edição flexível** | Se decidir alterar a [versão](change-sql-server-version.md) ou [edição](change-sql-server-edition.md) do SQL Server, pode atualizar os metadados dentro do portal Azure sem ter de recolocar todo o SQL Server VM.  | 


## <a name="management-modes"></a>Modos de gestão

A extensão SQL IaaS tem três modos de gestão:

- **O** modo leve não requer o reinício do SQL Server, mas suporta apenas alterar o tipo de licença e edição do SQL Server. Utilize esta opção para VMs do SERVIDOR SQL com múltiplas instâncias, ou participe num caso de cluster de failover (FCI). Este modo de gestão mantém os binários de extensão SQL IaaS Agent na máquina, mas não instala o Agente. O modo leve é o modo de gestão predefinido quando se utiliza a [função de registo automático](sql-vm-resource-provider-automatic-registration.md) ou quando um tipo de gestão não é especificado durante o registo manual. Não há impacto na memória ou CPU quando se utiliza o modo leve, e não há custos associados. É aconselhável registar primeiro o seu SQL Server VM em modo leve e, em seguida, atualizar para o modo Full durante uma janela de manutenção programada.

- **O** modo completo fornece todas as funcionalidades, mas requer um reinício das permissões do SqL Server e do administrador do sistema. Utilize-o para gerir um SQL Server VM com uma única instância. O modo completo instala dois serviços windows que têm um impacto mínimo na memória e CPU - estes podem ser monitorizados através do gestor de tarefas. Não há qualquer custo associado à utilização do modo de gestão completa. 

- O modo **NoAgent** é dedicado ao SQL Server 2008 e ao SQL Server 2008 R2 instalado no Windows Server 2008. Não há impacto na memória ou CPU quando se utiliza o modo NoAgent. Não há qualquer custo associado à utilização do modo de gestão NoAgent. 

Pode ver o modo atual do seu agente IAAS do Servidor SQL utilizando o Azure PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="installation"></a>Instalação

A extensão do Agente IAAS do Servidor SQL é instalada quando regista os seus VMs do SqL Server com o fornecedor de recursos SQL VM. Registar-se com o fornecedor de recursos cria o _recurso_ **de máquina virtual SQL** dentro da sua subscrição, que é um recurso _separado_ do recurso de máquina virtual. Desregralar o seu SQL Server VM do fornecedor de recursos removerá o _recurso_ **da máquina virtual SQL,** mas não deixará cair a máquina virtual real.

A implementação de uma imagem do SQL Server VM Azure Marketplace através do portal Azure regista automaticamente o SQL Server VM com o fornecedor de recursos. No entanto, se optar por instalar o SQL Server numa máquina virtual Azure ou providenciar uma máquina virtual Azure a partir de um VHD personalizado, então deve registar o seu SQL Server VM com o fornecedor de recursos SQL VM para instalar a extensão sql IaaS Agent. 

Para instalar a extensão, registe o SQL Server VM com o fornecedor de recursos:
- [Automaticamente para todos os VMs atuais e futuros numa subscrição](sql-vm-resource-provider-automatic-registration.md)
- [Para um único VM](sql-vm-resource-provider-register.md)
- [Para vários VMs a granel](sql-vm-resource-provider-bulk-register.md)

### <a name="named-instance-support"></a>Apoio de instância nomeado

A extensão IAAS do SQL Server funcionará com uma instância nomeada do SQL Server se esta for a única instância do SQL Server disponível na máquina virtual. A extensão não será instalada em VMs que tenham várias instâncias sql Server. 

Para utilizar uma instância nomeada do SQL Server, instale uma máquina virtual Azure, instale uma única instância do SQL Server e registe-a com o fornecedor de [recursos SQL VM](sql-vm-resource-provider-register.md) para instalar a extensão.

Em alternativa, para utilizar uma instância nomeada com uma imagem do Servidor SQL do Azure Marketplace, siga estes passos: 

   1. Implementar um SQL Server VM do Azure Marketplace. 
   1. [Desagregar](sql-vm-resource-provider-register.md#unregister-from-rp) o SQL Server VM do fornecedor de recursos SQL VM. 
   1. Desinstale completamente o SQL Server dentro do SQL Server VM.
   1. Instale o SQL Server com uma instância nomeada dentro do SQL Server VM. 
   1. Instale a extensão do Agente IAAS SQL [registando o SQL Server VM com o fornecedor de recursos SQL VM](sql-vm-resource-provider-register.md#register-with-rp). 

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


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes 

**Devo registar o meu SQL Server VM a partir de uma imagem do SQL Server no Azure Marketplace?**

Não. A Microsoft regista automaticamente VMs a partir das imagens do SQL Server no Azure Marketplace. O registo com o fornecedor de recursos SQL VM só é necessário se o VM *não* tiver sido aprovisionado a partir das imagens do SQL Server no Azure Marketplace e no SQL Server.

**O fornecedor de recursos de VM do SQL está disponível para todos os clientes?** 

Sim. Os clientes devem registar os seus VMs sql server com o fornecedor de recursos SQL VM se não utilizarem uma imagem sql Server do Azure Marketplace e, em vez disso, o SqL Server auto-instalado, ou se trouxerem o seu VHD personalizado. Os VMs detidos por todos os tipos de subscrições (Direct, Enterprise Agreement e Cloud Solution Provider) podem registar-se junto do fornecedor de recursos SQL VM.

**Qual é o modo de gestão predefinido ao registar-se com o fornecedor de recursos SQL VM?**

O modo de gestão predefinido quando se regista com o fornecedor de recursos SQL VM é *leve*. Se a propriedade de gestão do SQL Server não estiver definida quando se registar com o fornecedor de recursos SQL VM, o modo será definido como leve e o seu serviço SQL Server não reiniciará. Recomenda-se registar-se primeiro com o fornecedor de recursos SQL VM em modo leve e, em seguida, atualizar para a totalidade durante uma janela de manutenção. Da mesma forma, a gestão predefinitiva também é leve quando se utiliza a [função de registo automático](sql-vm-resource-provider-automatic-registration.md).

**Quais são os pré-requisitos para se registar com o fornecedor de recursos SQL VM?**

Não existem pré-requisitos para se registar com o fornecedor de recursos SQL VM, a não ser ter o SQL Server instalado no VM. Note que se a extensão do agente SQL IaaS for instalada em pleno modo, o serviço SQL Server reiniciará, assim o recomendará durante uma janela de manutenção.

**O registo com o fornecedor de recursos SQL VM vai instalar um agente no meu VM?**

Sim, registar-se com o fornecedor de recursos SQL VM em modo de gestão total instala um agente no VM. Registar-se em modo leve ou NoAgent não. 

Registar-se com o fornecedor de recursos SQL VM em modo leve apenas copia os *binários* de extensão sql IaaS Agent para o VM, não instala o agente. Estes binários são então utilizados para instalar o agente quando o modo de gestão é atualizado para o máximo.


**O registo com o fornecedor de recursos SQL VM reiniciará o SQL Server no meu VM?**

Depende do modo especificado durante o registo. Se for especificado o modo "NoAgent" leve ou noAgent, o serviço SQL Server não será reiniciado. No entanto, especificar o modo de gestão como completo fará com que o serviço SQL Server reinicie. A função de registo automático regista os seus VMs do SQL Server em modo leve, a menos que a versão do Windows Server seja 2008, caso em que o SQL Server VM será registado no modo NoAgent. 

**Qual é a diferença entre os modos de gestão leve e NoAgent ao registar-se com o fornecedor de recursos SQL VM?** 

O modo de gestão NoAgent é o único modo de gestão disponível para SQL Server 2008 e SQL Server 2008 R2 no Windows Server 2008. Para todas as versões posteriores do Windows Server, os dois modos de gestão disponíveis são leves e cheios. 

O modo NoAgent requer que a versão e as propriedades de edição do SQL Server sejam definidas pelo cliente. O modo leve consulta o VM para encontrar a versão e edição da instância SQL Server.

**Posso registar-me com o fornecedor de recursos SQL VM sem especificar o tipo de licença SQL Server?**

Não. O tipo de licença SQL Server não é uma propriedade opcional quando se está a registar com o fornecedor de recursos SQL VM. Tem de definir o tipo de licença SQL Server como pay-as-you-go ou Azure Hybrid Benefit ao registar-se com o fornecedor de recursos SQL VM em todos os modos de gestão (NoAgent, leve e cheio). Se tiver alguma das versões gratuitas do SQL Server instaladas, como a edição de Desenvolvimento ou Avaliação, deve registar-se com o licenciamento pay-as-you-go. O Azure Hybrid Benefit só está disponível para versões pagas do SQL Server, como edições Enterprise e Standard.

**Posso atualizar a extensão IAAS do SQL Server do modo NoAgent para o modo completo?**

Não. A atualização do modo de gestão para o modo de gestão para o modo NoAgent ou para o modo NoAgent não está disponível para o modo NoAgent. Esta é uma limitação técnica do Windows Server 2008. Terá de atualizar o SISTEMA primeiro para o Windows Server 2008 R2 ou superior, e depois poderá fazer o upgrade para o modo de gestão completa. 

**Posso atualizar a extensão IAAS do SQL Server do modo leve para o modo completo?**

Sim. A atualização do modo de gestão de peso para cheio é suportada através do Azure PowerShell ou do portal Azure. Isto irá desencadear um reinício do serviço SQL Server.

**Posso reduzir a extensão IAAS do SQL Server de modo completo para modo NoAgent ou modo de gestão leve?**

Não. A redução do modo de gestão de extensão SQL Server IaaS não é suportada. O modo de gestão não pode ser desclassificado do modo completo para o modo leve ou noAgent, e não pode ser desclassificado do modo leve para o modo NoAgent. 

Para alterar o modo de gestão da plena gestibilidade, [não registe](sql-vm-resource-provider-register.md#unregister-from-rp) o SQL Server VM do fornecedor de recursos SQL VM, largando o _recurso_ da máquina virtual SQL e reregistando novamente o SQL Server VM com o fornecedor de recursos SQL VM num modo de gestão diferente.

**Posso inscrever-me no fornecedor de recursos SQL VM do portal Azure?**

Não. O registo com o fornecedor de recursos SQL VM não está disponível no portal Azure. O registo junto do fornecedor de recursos SQL VM só é suportado com o Azure CLI ou a Azure PowerShell. 

**Posso registar um VM com o fornecedor de recursos SQL VM antes da instalação do SQL Server?**

Não. Um VM deve ter pelo menos uma instância SQL Server (Database Engine) para registar com sucesso com o fornecedor de recursos SQL VM. Se não houver nenhuma instância do SQL Server no VM, o novo recurso Microsoft.SqlVirtualMachine estará num estado falhado.

**Posso registar um VM com o fornecedor de recursos SQL VM se existirem várias instâncias do SQL Server?**

Sim. O fornecedor de recursos SQL VM registará apenas uma instância sql Server (Database Engine). O fornecedor de recursos SQL VM registará a instância padrão do SQL Server no caso de múltiplas instâncias. Se não houver uma instância predefinida, apenas é suportado o registo em modo leve. Para atualizar do modo de gestão leve para o modo de gestão completa, ou a instância padrão do SQL Server deve existir ou o VM deve ter apenas uma instância chamada SQL Server.

**Posso registar um cluster de falha do SQL Server com o fornecedor de recursos SQL VM?**

Sim. Sql Server falha casos de cluster num Azure VM pode ser registado com o fornecedor de recursos SQL VM em modo leve. No entanto, as instâncias de cluster de failover do SQL Server não podem ser atualizadas para o modo de gestão completa.

**Posso registar o meu VM com o fornecedor de recursos SQL VM se um grupo de disponibilidade Always On estiver configurado?**

Sim. Não existem restrições ao registo de uma instância do SQL Server num VM Azure com o fornecedor de recursos SQL VM se estiver a participar numa configuração do grupo de disponibilidade Always On.

**Qual o custo para o registo com o fornecedor de recursos SQL VM, ou com a atualização para o modo de gestão completa?**

Nenhum. Não existe qualquer taxa associada ao registo com o fornecedor de recursos SQL VM, ou à utilização de qualquer um dos três modos de gestão. Gerir o seu SQL Server VM com o fornecedor de recursos é completamente gratuito. 

**Qual é o impacto de desempenho da utilização dos diferentes modos de gestão?**

Não há impacto na utilização dos modos de gestão *NoAgent* e *leves.* Existe um impacto mínimo ao utilizar o modo de gestão *total* de dois serviços instalados no SISTEMA. Estes podem ser monitorizados através do gestor de tarefas e vistos na consola de Serviços Windows incorporada. 

Os dois nomes de serviço são:
- `SqlIaaSExtensionQuery` (Nome de exibição - `Microsoft SQL Server IaaS Query Service` )
- `SQLIaaSExtension` (Nome de exibição - `Microsoft SQL Server IaaS Agent` )

**Como retiro a extensão?**

Remova a extensão [não registrando](sql-vm-resource-provider-register.md#unregister-from-rp) o SQL Server VM do fornecedor de recursos SQL VM. 

## <a name="next-steps"></a>Passos seguintes

Para instalar a extensão IAAS do SQL Server ao SQL Server em VMs Azure, consulte os artigos de [instalação automática,](sql-vm-resource-provider-automatic-registration.md) [VMs simples](sql-vm-resource-provider-register.md)ou [VMs a granel](sql-vm-resource-provider-bulk-register.md).

Para obter mais informações sobre a execução do SQL Server em Máquinas Virtuais Azure, consulte o [Servidor SQL em Máquinas Virtuais Azure?](sql-server-on-azure-vm-iaas-what-is-overview.md)
