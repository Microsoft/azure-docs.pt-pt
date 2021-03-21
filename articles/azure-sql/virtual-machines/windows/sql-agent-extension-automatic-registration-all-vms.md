---
title: Inscrição automática com extensão do Agente SQL IaaS
description: Saiba como permitir que a função de registo automático registe automaticamente todos os VMs do servidor SQL passado e futuro com a extensão SQL IaaS Agent utilizando o portal Azure.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.openlocfilehash: 139852949a3744fd603cb197b2e27fa32679aae0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042427"
---
# <a name="automatic-registration-with-sql-iaas-agent-extension"></a>Inscrição automática com extensão do Agente SQL IaaS
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ative a função de registo automático no portal Azure para registar automaticamente todos os servidors SQL atuais e futuros em Azure Virtual Machines (VMs) com a [extensão SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) em modo leve. 

Este artigo ensina-o a ativar a função de registo automático. Em alternativa, pode [registar um único VM,](sql-agent-extension-manually-register-single-vm.md)ou [registar os seus VMs a granel](sql-agent-extension-manually-register-vms-bulk.md) com a extensão sql IaaS Agent. 

## <a name="overview"></a>Descrição geral

Registar o seu SQL Server VM com a [extensão SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md) para desbloquear um conjunto completo de benefícios. 

Quando o registo automático está ativado, um trabalho é executado diariamente para detetar se o SQL Server está ou não instalado em todos os VMs não registados na subscrição. Isto é feito copiando os binários de extensão do agente SQL IaaS para o VM, executando então um utilitário único que verifica a colmeia de registo sql Server. Se for detetada a colmeia SQL Server, a máquina virtual é registada com a extensão em modo leve. Se não existir colmeia SQL Server no registo, os binários são removidos.

Uma vez que o registo automático esteja ativado para uma subscrição, todos os VMs atuais e futuros que tenham o SQL Server instalado serão registados com a extensão sql IaaS Agent **em modo leve sem tempo de inatividade, e sem reiniciar o serviço SQL Server**. Ainda precisa de [fazer um upgrade manual para o modo de gestão completa](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) para tirar partido do conjunto completo de funcionalidades. 

> [!IMPORTANT]
> A extensão do Agente IAAS SQL recolhe dados com o propósito expresso de dar aos clientes benefícios opcionais ao utilizar o SQL Server dentro de Máquinas Virtuais Azure. A Microsoft não utilizará estes dados para licenciar auditorias sem o consentimento prévio do cliente. Consulte o [suplemento de privacidade SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

Para registar o seu SQL Server VM com a extensão, necessitará de: 

- Uma [subscrição Azure](https://azure.microsoft.com/free/) e, no mínimo, permissões [de função de contribuinte.](../../../role-based-access-control/built-in-roles.md#all)
- Uma máquina virtual do Azure Resource Model [Windows Server 2008 R2 (ou posterior)](../../../virtual-machines/windows/quick-create-portal.md) com [o SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) implantado na nuvem do governo do Azure ou do Governo Azure. O Windows Server 2008 não é suportado. 


## <a name="enable"></a>Ativar

Para ativar o registo automático dos seus VMs do seu SQL Server no portal Azure, siga estes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Navegue para a página de recursos [**de máquinas virtuais SQL.**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 
1. Selecione **o registo automático do SQL Server VM** para abrir a página de registo **automático.** 

   :::image type="content" source="media/sql-agent-extension-automatic-registration-all-vms/automatic-registration.png" alt-text="Selecione o registo automático do SQL Server VM para abrir a página de registo automático":::

1. Escolha a sua subscrição a partir do drop-down. 
1. Leia os termos e, se concordar, **selecione eu aceito.** 
1. Selecione **Registar-se** para ativar a funcionalidade e registar automaticamente todos os VMs do servidor SQL atual e futuro com a extensão sql IaaS Agent. Isto não reiniciará o serviço SQL Server em nenhum dos VMs. 

## <a name="disable"></a>Desativar

Utilize o [Azure CLI](/cli/azure/install-azure-cli) ou [a Azure PowerShell](/powershell/azure/install-az-ps) para desativar a função de registo automático. Quando a função de registo automático é desativada, os VMs do Servidor SQL adicionados à subscrição precisam de ser registados manualmente com a extensão do Agente IAAS SQL. Isto não irá desagrear os VMs do servidor SQL existentes que já foram registados.



# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para desativar o registo automático utilizando o Azure CLI, executar o seguinte comando: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Para desativar o registo automático utilizando a Azure PowerShell, executar o seguinte comando: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Ativar para várias subscrições

Pode ativar a função de registo automático para várias subscrições Azure utilizando o PowerShell. 

Para tal, siga estes passos:

1. Guarde [este script](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) para um `.ps1` ficheiro, como `EnableBySubscription.ps1` . 
1. Navegue para onde guardou o script utilizando uma posição de comando administrativa ou uma janela PowerShell. 
1. Ligue-se a Azure ( `az login` ).
1. Execute o script, passando em Subscrições como parâmetros como   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   Por exemplo: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

Erros de registo falhados são armazenados `RegistrationErrors.csv` no mesmo diretório onde guardou e executou o `.ps1` script. 

## <a name="next-steps"></a>Passos seguintes

Atualize o seu modo de gestão ao [máximo](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) para tirar partido do conjunto completo de funcionalidades que lhe é fornecida pela extensão sql IaaS Agent. 
