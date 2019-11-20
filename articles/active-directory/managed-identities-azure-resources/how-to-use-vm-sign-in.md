---
title: Usar identidades gerenciadas em uma VM do Azure para entrar-Azure AD
description: Instruções e exemplos passo a passo para usar uma identidade gerenciada de VM do Azure para a entidade de serviço de recursos do Azure para entrada de cliente de script e acesso a recursos.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3d6d128677d2e82f4750a7771885474bf284fb1
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184223"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Como usar identidades gerenciadas para recursos do Azure em uma VM do Azure para entrar 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Este artigo fornece exemplos de script do PowerShell e da CLI para entrar usando identidades gerenciadas para a entidade de serviço de recursos do Azure e orientação sobre tópicos importantes, como tratamento de erros.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se você planeja usar os exemplos de Azure PowerShell ou CLI do Azure neste artigo, certifique-se de instalar a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps) ou [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Todo o script de exemplo neste artigo pressupõe que o cliente de linha de comando está sendo executado em uma VM com identidades gerenciadas para recursos do Azure habilitados. Use o recurso "conectar" da VM no portal do Azure para conectar-se remotamente à sua VM. Para obter detalhes sobre como habilitar identidades gerenciadas para recursos do Azure em uma VM, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md), ou um dos artigos variantes (usando o PowerShell, a CLI, um modelo ou um SDK do Azure). 
> - Para evitar erros durante o acesso a recursos, a identidade gerenciada da VM deve receber pelo menos o acesso "leitor" no escopo apropriado (a VM ou superior) para permitir operações de Azure Resource Manager na VM. Consulte [atribuir identidades gerenciadas para o acesso de recursos do Azure a um recurso usando o portal do Azure](howto-assign-access-portal.md) para obter detalhes.

## <a name="overview"></a>Descrição geral

Identidades gerenciadas para recursos do Azure fornece um [objeto de entidade de serviço](../develop/developer-glossary.md#service-principal-object) , que é [criado na habilitação de identidades gerenciadas para recursos do Azure](overview.md#how-does-it-work) na VM. A entidade de serviço pode receber acesso aos recursos do Azure e usada como uma identidade por clientes de linha de comando/script para entrada e acesso a recursos. Tradicionalmente, para acessar recursos protegidos em sua própria identidade, um cliente de script precisaria:  

   - ser registrado e consentido com o Azure AD como um aplicativo cliente confidencial/Web
   - entre em sua entidade de serviço, usando as credenciais do aplicativo (que provavelmente são inseridas no script)

Com identidades gerenciadas para recursos do Azure, o cliente de script não precisa mais fazer nenhuma das duas, pois pode entrar na entidade de serviço identidades gerenciadas para recursos do Azure. 

## <a name="azure-cli"></a>CLI do Azure

O script a seguir demonstra como:

1. Entre no Azure AD na identidade gerenciada da VM para entidade de serviço de recursos do Azure  
2. Chame Azure Resource Manager e obtenha a ID da entidade de serviço da VM. A CLI cuida do gerenciamento de aquisição/uso de tokens automaticamente. Certifique-se de substituir o nome da máquina virtual por `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

O script a seguir demonstra como:

1. Entre no Azure AD na identidade gerenciada da VM para entidade de serviço de recursos do Azure  
2. Chame um cmdlet Azure Resource Manager para obter informações sobre a VM. O PowerShell cuida do gerenciamento do uso de token para você automaticamente.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure

Consulte [Serviços do Azure que dão suporte à autenticação do Azure ad](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) para obter uma lista de recursos que dão suporte ao Azure AD e foram testados com identidades gerenciadas para recursos do Azure e suas respectivas IDs de recurso.

## <a name="error-handling-guidance"></a>Diretrizes para tratamento de erros 

Respostas como as seguintes podem indicar que a identidade gerenciada da VM para recursos do Azure não foi configurada corretamente:

- PowerShell: *Invoke-WebRequest: não é possível conectar-se ao servidor remoto*
- CLI: *MSI: falha ao recuperar um token do `http://localhost:50342/oauth2/token` com um erro de ' HTTPConnectionPool (host = ' localhost ', Port = 50342)* 

Se você receber um desses erros, retorne para a VM do Azure na [portal do Azure](https://portal.azure.com) e:

- Acesse a página **identidade** e verifique se o **sistema atribuído** está definido como "Sim".
- Vá para a página **extensões** e verifique se as identidades gerenciadas para a extensão de recursos do Azure **(planejada para substituição em janeiro de 2019)** implantadas com êxito.

Se algum estiver incorreto, talvez seja necessário reimplantar as identidades gerenciadas dos recursos do Azure no recurso novamente ou solucionar a falha de implantação. Consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](qs-configure-portal-windows-vm.md) se você precisar de assistência com a configuração da VM.

## <a name="next-steps"></a>Passos seguintes

- Para habilitar identidades gerenciadas para recursos do Azure em uma VM do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando o PowerShell](qs-configure-powershell-windows-vm.md)ou [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando CLI do Azure](qs-configure-cli-windows-vm.md)






