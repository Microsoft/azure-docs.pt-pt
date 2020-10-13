---
title: Registo automático com fornecedor de recursos SQL VM
description: Saiba como permitir que a função de registo automático registe automaticamente todos os VMs do servidor SQL passado e futuro com o fornecedor de recursos SQL VM utilizando o portal Azure.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.openlocfilehash: 75f68a4de2db0c4c9102a58da12d80cc273a6e80
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931177"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Registo automático com fornecedor de recursos SQL VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ative a função de registo automático no portal Azure para registar automaticamente todos os servidors SQL atuais e futuros em VMs Azure com o fornecedor de recursos SQL VM em modo leve.

Este artigo ensina-o a ativar a função de registo automático. Em alternativa, pode [registar um único VM,](sql-vm-resource-provider-register.md)ou [registar os seus VMs a granel](sql-vm-resource-provider-bulk-register.md) com o fornecedor de recursos SQL VM. 

## <a name="overview"></a>Descrição geral

O [fornecedor de recursos SQL VM](sql-vm-resource-provider-register.md#overview) permite-lhe gerir o seu SQL Server VM a partir do portal Azure. Além disso, o fornecedor de recursos permite um conjunto de funcionalidades robusto, incluindo [patching automatizado,](automated-patching.md) [backup automatizado,](automated-backup.md)bem como capacidades de monitorização e gestão. Também desbloqueia a flexibilidade [de licenciamento](licensing-model-azure-hybrid-benefit-ahb-change.md) e [edição.](change-sql-server-edition.md) Anteriormente, estas funcionalidades estavam apenas disponíveis para imagens VM do SQL Server implementadas a partir do Azure Marketplace. 

A função de registo automático permite que os clientes registem automaticamente todos os VMs do servidor SQL atual e futuro na sua assinatura Azure com o fornecedor de recursos SQL VM. Isto é diferente do registo manual, que se foca apenas nos VMs do servidor SQL atuais. 

O registo automático regista os seus VMs sql server em modo leve. Ainda precisa de [fazer um upgrade manual para o modo de gestão completa](sql-vm-resource-provider-register.md#upgrade-to-full) para tirar partido do conjunto completo de funcionalidades. 

## <a name="prerequisites"></a>Pré-requisitos

Para registar o seu SQL Server VM com o fornecedor de recursos, necessitará de: 

- Uma [subscrição do Azure](https://azure.microsoft.com/free/).
- Uma [máquina virtual](../../../virtual-machines/windows/quick-create-portal.md) do Modelo de Recursos Azure Windows com O [Servidor SQL](https://www.microsoft.com/sql-server/sql-server-downloads) implantado para o público ou nuvem do Governo Azure. 


## <a name="enable"></a>Ativar

Para ativar o registo automático dos seus VMs do seu SQL Server no portal Azure, siga os passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para a página de recursos [**de máquinas virtuais SQL.**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 
1. Selecione **o registo automático do SQL Server VM** para abrir a página de registo **automático.** 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="Selecione o registo automático do SQL Server VM para abrir a página de registo automático":::

1. Escolha a sua subscrição a partir do drop-down. 
1. Leia os termos e, se concordar, **selecione eu aceito.** 
1. Selecione **Registar-se** para ativar a funcionalidade e registar automaticamente todos os VMs do servidor SQL atual e futuro com o fornecedor de recursos SQL VM. Isto não reiniciará o serviço SQL Server em nenhum dos VMs. 

## <a name="disable"></a>Desativar

Utilize o [Azure CLI](/cli/azure/install-azure-cli) ou [a Azure PowerShell](/powershell/azure/install-az-ps) para desativar a função de registo automático. Quando a função de registo automático é desativada, os VMs do Servidor SQL adicionados à subscrição precisam de ser registados manualmente junto do fornecedor de recursos SQL VM. Isto não irá desagrear os VMs do servidor SQL existentes que já foram registados.



# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para desativar o registo automático utilizando o Azure CLI, executar o seguinte comando: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para desativar o registo automático utilizando a Azure PowerShell, executar o seguinte comando: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---


## <a name="next-steps"></a>Passos seguintes

Atualize o seu modo de gestão ao [máximo](sql-vm-resource-provider-register.md#upgrade-to-full) para tirar partido do conjunto completo de funcionalidades que lhe foi fornecido pelo fornecedor de recursos SQL VM. 
