---
title: Registrar SQL Server máquina virtual no Azure com o provedor de recursos de VM do SQL | Microsoft Docs
description: Registre sua VM do SQL Server com o provedor de recursos da VM do SQL para melhorar a capacidade de gerenciamento.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 95a167cbc8fde4488e3f46ffd850f0619cf1b651
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305874"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrar SQL Server máquina virtual no Azure com o provedor de recursos de VM do SQL

Este artigo descreve como registrar sua VM (máquina virtual) SQL Server do Azure com o provedor de recursos de VM do SQL. 

A implantação de uma imagem do Marketplace de VM SQL Server por meio do portal do Azure registra automaticamente a VM SQL Server com o provedor de recursos. No entanto, se você optar por instalar automaticamente o SQL Server em uma máquina virtual do Azure em vez de escolher uma imagem do Azure Marketplace, deverá registrar sua VM do SQL Server com o provedor de recursos hoje para:

-  **Conformidade** – de acordo com os termos de produto da Microsoft, os clientes que usam o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) devem indicar à Microsoft ao usar o benefício híbrido do Azure-e para fazer isso, devem se registrar com o provedor de recursos de VM do SQL. 

-  **Benefícios do recurso** -registrar seu SQL Server VM com o provedor de recursos desbloqueia a [aplicação automática](virtual-machines-windows-sql-automated-patching.md)de patches, [backup automático](virtual-machines-windows-sql-automated-backup-v2.md), monitoramento e capacidade de gerenciamento, bem como flexibilidade de [Licenciamento](virtual-machines-windows-sql-ahb.md) e [edição](virtual-machines-windows-sql-change-edition.md) . Anteriormente, elas só estavam disponíveis para SQL Server imagens de VM do Azure Marketplace.

A instalação automática de SQL Server em uma VM do Azure ou o provisionamento de uma VM do Azure de um VHD personalizado com SQL Server é compatível por meio de Benefício Híbrido do Azure para SQL Server com a condição que os clientes indicam que usam para a Microsoft registrando-se com o recurso de VM do SQL operador. 

Para utilizar o provedor de recursos da VM do SQL, você também deve registrar o provedor de recursos da VM do SQL com sua assinatura. Isso pode ser feito com o portal do Azure, o CLI do Azure e o PowerShell. 

## <a name="prerequisites"></a>Pré-requisitos

Para registrar sua VM SQL Server com o provedor de recursos, você precisará do seguinte: 

- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Uma [VM SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [CLI do Azure](/cli/azure/install-azure-cli) e [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>Registrar com provedor de recursos de VM do SQL
Se a [extensão de IaaS do SQL](virtual-machines-windows-sql-server-agent-extension.md) já estiver instalada na VM, o registro com o provedor de recursos de VM do SQL estará simplesmente criando um recurso de metadados do tipo Microsoft. SqlVirtualMachine/SqlVirtualMachines. Abaixo está o trecho de código para se registrar no provedor de recursos da VM do SQL se a extensão IaaS do SQL já estiver instalada na VM. Você precisa fornecer o tipo de licença de SQL Server desejado ao registrar com o provedor de recursos de VM do SQL como ' PAYG ' ou ' AHUB '. 

Registre SQL Server VM usando o PowerShell com o seguinte trecho de código:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

Se a extensão de IaaS do SQL não estiver instalada na VM, você poderá se registrar com o provedor de recursos da VM do SQL especificando o modo de gerenciamento leve do SQL. No modo de gerenciamento leve do SQL, o provedor de recursos da VM do SQL instalará automaticamente a extensão de IaaS do SQL no [modo leve](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) e verificará SQL Server metadados da instância; Isso não reiniciará SQL Server serviço. Você precisa fornecer o tipo de licença de SQL Server desejado ao registrar com o provedor de recursos de VM do SQL como ' PAYG ' ou ' AHUB '. 

Registre SQL Server VM no modo de gerenciamento leve do SQL usando o PowerShell com o seguinte trecho de código:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

O registro com o provedor de recursos de VM do SQL no [modo leve](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) garantirá a conformidade e habilitará o licenciamento flexível, bem como as atualizações de edição in-loco SQL Server. As instâncias de cluster de failover e as implantações de várias instâncias podem ser registradas com o provedor de recursos de VM do SQL somente no modo leve. Você pode seguir as instruções encontradas em portal do Azure para atualizar para o [modo completo](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) e habilitar o conjunto de recursos de gerenciamento abrangente com uma SQL Server reinicialização a qualquer momento. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Registrar SQL Server 2008/R2 em VMs do Windows Server 2008

SQL Server 2008 e 2008 R2 instalados no Windows Server 2008 podem ser registrados com o recurso de VM do SQL fornecido [](virtual-machines-windows-sql-server-agent-extension.md) no modo noagent. Essa opção garante a conformidade e permite que a VM SQL Server seja monitorada no portal do Azure com funcionalidade limitada.

A tabela a seguir detalha os valores aceitáveis para os parâmetros fornecidos durante o registro:

| Parâmetro | Valores aceitáveis                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`ou`'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` ou `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Para registrar seu SQL Server 2008 ou 2008 R2 na instância do Windows Server 2008, use o seguinte trecho de código do PowerShell:  

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>Verificar status do registro
Você pode verificar se o SQL Server já foi registrado com o provedor de recursos de VM do SQL usando o portal do Azure, CLI do Azure ou o PowerShell. 

### <a name="azure-portal"></a>Portal do Azure 
Para verificar o status do registro usando o portal do Azure, faça o seguinte.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
1. Navegue até suas [máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md).
1. Selecione seu SQL Server VM na lista. Se sua VM de SQL Server não estiver listada aqui, é provável que sua VM SQL Server não tenha sido registrada com o provedor de recursos de VM do SQL. 
1. Exiba o valor em `Status`. Se `Status = Succeeded`, em seguida, a VM de SQL Server foi registrada com êxito no provedor de recursos de VM do SQL. 

    ![Verificar o status com o registro do SQL RP](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>AZ CLI

Verifique o status atual de registro da VM SQL Server com o comando AZ CLI a seguir. `ProvisioningState`mostrará `Succeeded` se o registro foi bem-sucedido. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

Verifique o status atual de registro da VM SQL Server com o seguinte cmdlet do PowerShell. `ProvisioningState`mostrará `Succeeded` se o registro foi bem-sucedido. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
Um erro indica que a VM de SQL Server não foi registrada com o provedor de recursos. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Registrar provedor de recursos da VM do SQL com assinatura 

Para registrar sua VM SQL Server com o provedor de recursos de VM do SQL, você deve registrar o provedor de recursos em sua assinatura. Você pode fazer isso com o portal do Azure ou CLI do Azure.

### <a name="azure-portal"></a>Portal do Azure

As etapas a seguir registrarão o provedor de recursos da VM do SQL em sua assinatura do Azure usando o portal do Azure. 

1. Abra o portal do Azure e navegue até **todos os serviços**. 
1. Navegue até **assinaturas** e selecione a assinatura de interesse.  
1. Na página **assinaturas** , navegue até **provedores de recursos**. 
1. Digite `sql` o filtro para abrir os provedores de recursos relacionados ao SQL. 
1. Selecione *registrar*, *registrar novamente*ou cancelar o *registro* para o provedor **Microsoft. SqlVirtualMachine** dependendo da ação desejada. 

   ![Modificar o provedor](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>AZ CLI
O trecho de código a seguir registrará o provedor de recursos da VM do SQL em sua assinatura do Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

O trecho de código do PowerShell a seguir registrará o provedor de recursos da VM do SQL em sua assinatura do Azure.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Observações

 - O provedor de recursos de VM do SQL dá suporte apenas a VMs SQL Server implantadas usando o ' Resource Manager '. Não há suporte para VMs SQL Server implantadas usando o ' modelo clássico '. 
 - O provedor de recursos de VM do SQL dá suporte apenas a VMs SQL Server implantadas na nuvem pública. Não há suporte para implantações na nuvem privada ou governamental. 
 
## <a name="frequently-asked-questions"></a>Perguntas mais frequentes 

**Devo registrar minha VM SQL Server provisionada de uma imagem SQL no Azure Marketplace?**

Não. O Microsoft autoregistra as VMs provisionadas a partir das imagens SQL no Azure Marketplace. O registro com o provedor de recursos da VM do SQL será necessário somente se a VM não tiver sido provisionada a partir das imagens do SQL no Azure Marketplace e o SQL Server tiver sido instalado automaticamente.

**O fornecedor de recursos de VM do SQL está disponível para todos os clientes?** 

Sim. Os clientes devem registrar sua VM SQL Server com o provedor de recursos de VM do SQL se não usavam uma imagem SQL Server do Azure Marketplace e de SQL Server autoinstalados ou trouxeram seu VHD personalizado. As VMs de propriedade de todos os tipos de assinaturas (direta, EA e CSP) podem se registrar com o provedor de recursos de VM do SQL.

**Devo me registrar com o provedor de recursos de VM do SQL se minha VM de SQL Server já tiver a extensão IaaS do SQL instalada?**

Se sua VM SQL Server for autoinstalada, não provisionada das imagens SQL no Azure Marketplace, você deverá se registrar com o provedor de recursos de VM do SQL mesmo que tenha instalado a extensão SQL IaaS. O registro com o provedor de recursos de VM do SQL cria um novo recurso do tipo Microsoft. SqlVirtualMachines. A instalação da extensão IaaS do SQL não cria esse recurso.

**Qual é o modo de gerenciamento SQL padrão ao se registrar com o provedor de recursos de VM do SQL?**

O modo de gerenciamento SQL padrão ao se registrar com a VM do SQL RP está _cheio_. Se a propriedade de gerenciamento do SQL não for definida durante o registro com o provedor de recursos de VM do SQL, o modo será definido como capacidade de gerenciamento completa. Ter a extensão de IaaS do SQL instalada na VM é o pré-requisito para registrar-se com o provedor de recursos de VM do SQL no modo de gerenciamento completo.

**Quais são os pré-requisitos para se registrar com o provedor de recursos de VM do SQL?**

Não há nenhum pré-requisito para registrar com o provedor de recursos de VM do SQL no modo leve ou no modo sem agente. Os pré-requisitos para se registrar com o provedor de recursos de VM do SQL no modo completo são ter a extensão IaaS do SQL instalada na VM.

**Posso registrar com o provedor de recursos de VM do SQL se eu não tiver a extensão IaaS do SQL instalada na VM?**

Sim, você pode registrar com o provedor de recursos de VM do SQL no modo de gerenciamento leve se não tiver a extensão IaaS do SQL instalada na VM. No modo leve, o provedor de recursos de VM do SQL usará um aplicativo de console para verificar a versão e a edição da instância do SQL. O aplicativo de console será desligado depois de verificar se há pelo menos uma instância do SQL em execução na VM. O registro com o provedor de recursos de VM do SQL no modo leve não será reiniciado SQL Server e não criará um agente na VM.

**O registro com o provedor de recursos da VM do SQL instalará um agente em minha VM?**

Não. O registro com o provedor de recursos de VM do SQL criará apenas um novo recurso de metadados e não instalará um agente na VM. A extensão de IaaS do SQL é necessária apenas para habilitar a capacidade de gerenciamento total, portanto, atualizar o modo de gerenciamento de Lightweight para Full instalará a extensão SQL IaaS e reiniciará SQL Server.

**Será registrado com a reinicialização do provedor de recursos da VM do SQL SQL Server em minha VM?**

Não. O registro com o provedor de recursos de VM do SQL criará apenas um novo recurso de metadados e não será reiniciado SQL Server na VM. A reinicialização de SQL Server só é necessária para instalar a extensão SQL IaaS; e a extensão de IaaS do SQL é necessária para habilitar a capacidade de gerenciamento total. A atualização do modo de gerenciamento de Lightweight para Full instalará a extensão IaaS do SQL e será reiniciada SQL Server.

**Qual é a diferença entre os modos de gerenciamento Lightweight e no-Agent ao se registrar com o provedor de recursos de VM do SQL?** 

O modo de gerenciamento no-Agent só está disponível para o SQL Server 2008/R2 no Windows Server 2008; e é o único modo de gerenciamento disponível para essas versões. Para todas as outras versões do SQL Server, os dois modos de gerenciamento disponíveis são leves e completos. O modo no-Agent requer SQL Server Propriedades de versão e edição a serem definidas pelo cliente; o modo leve consulta a VM para localizar a versão e a edição da instância do SQL.

**Posso registrar com o provedor de recursos de VM do SQL no modo leve ou sem agente com CLI do Azure?**

Não. A propriedade modo de gerenciamento do SQL só está disponível ao registrar com o provedor de recursos de VM do SQL com Azure PowerShell. CLI do Azure não dá suporte à definição da propriedade de gerenciamento do SQL e sempre registra com o provedor de recursos da VM do SQL no modo padrão – capacidade de gerenciamento total.

**Posso registrar com o provedor de recursos da VM do SQL sem especificar o tipo de licença do SQL?**

Não. O tipo de licença do SQL não é uma propriedade opcional ao se registrar com a VM do SQL RP. Você precisa definir o tipo de licença do SQL como PAYG ou AHUB ao registrar com o provedor de recursos da VM do SQL em todos os modos de gerenciamento (sem agente, leve e completo) com AZ CLI e PowerShell.

**Posso atualizar a extensão de IaaS do SQL do modo sem agente para o modo completo?**

Não. A atualização do modo de gerenciamento do SQL para completo ou leve não está disponível para o modo no-Agent. Essa é uma limitação técnica do Windows Server 2008.

**Posso atualizar a extensão de IaaS do SQL do modo leve para o modo completo?**

Sim. A atualização do modo de gerenciamento do SQL de Lightweight para Full tem suporte por meio do PowerShell ou do portal do Azure; e isso requer a reinicialização de SQL Server.

**Posso fazer downgrade da extensão de IaaS do SQL do modo completo para os modos de gerenciamento sem agente ou leve?**

Não. Não há suporte para o downgrade do modo de gerenciamento de extensão de IaaS do SQL. Não é possível fazer downgrade do modo de gerenciamento do SQL do modo completo para o modo lightweight ou no-Agent; e não é possível fazer downgrade do modo leve para o modo sem agente. Para alterar o modo de gerenciamento da capacidade de gerenciamento total; remover a extensão de IaaS do SQL; Remova o recurso Microsoft. SqlVirtualMachine e registre novamente SQL Server VM com o provedor de recursos de VM do SQL.

**Posso registrar com o provedor de recursos de VM do SQL da portal do Azure?**

Não. O registro com o provedor de recursos de VM do SQL não está disponível no portal do Azure. O registro com o provedor de recursos de VM do SQL no modo de gerenciamento completo tem suporte com CLI do Azure ou PowerShell; e o registro com o provedor de recursos de VM do SQL nos modos de gerenciamento leve ou sem agente tem suporte apenas nas APIs Azure PowerShell.

**Posso registrar uma VM com o provedor de recursos da VM do SQL antes que o SQL Server seja instalado?**

Não. A VM deve ter pelo menos uma instância do SQL para se registrar com êxito com o provedor de recursos da VM do SQL. Se não houver nenhuma instância do SQL na VM, o novo recurso Microsoft. SqlVirtualMachine estará em um estado de falha.

**Posso registrar uma VM com o recurso de VM do SQL se houver várias instâncias do SQL?**

Sim. O provedor de recursos da VM do SQL registrará apenas uma instância do SQL. O provedor de recursos da VM do SQL registrará a instância padrão do SQL no caso de várias instâncias. Se não houver nenhuma instância padrão, haverá suporte apenas para o registro no modo leve. Para atualizar do modo de gerenciamento leve para o completo, a instância do SQL padrão deve existir ou a VM deve ter apenas uma instância do SQL nomeada.

**Posso registrar uma instância de cluster de failover SQL Server com o provedor de recursos de VM do SQL?**

Sim. As instâncias do SQL FCI em uma VM do Azure podem ser registradas com o provedor de recursos de VM do SQL no modo leve. No entanto, as instâncias do SQL FCI não podem ser atualizadas para o modo de gerenciamento completo.

**Posso registrar minha VM com a VM do SQL RP se o grupo de disponibilidade AlwaysOn estiver configurado?**

Sim. Não há restrições para registrar uma instância de SQL Server em uma VM do Azure com o provedor de recursos de VM do SQL se estiver participando de uma configuração de grupo de disponibilidade AlwaysOn.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja os artigos seguintes: 

* [Visão geral de SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Perguntas frequentes sobre o SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Diretrizes de preços do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server em notas de versão da VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
