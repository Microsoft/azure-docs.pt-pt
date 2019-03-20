---
title: Como utilizar identidades geridas para recursos do Azure numa VM do Azure para início de sessão
description: Instruções passo a passo instruções e exemplos de utilização de uma VM do Azure geridos identidades para o principal de serviço de recursos do Azure para o início de sessão de cliente de script e acesso a recursos.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dfbb618bafd9ae3cbb0c691e63a243ab2c2c31e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57996532"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Como utilizar identidades geridas para recursos do Azure numa VM do Azure para início de sessão 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Este artigo fornece exemplos de script do PowerShell e a CLI para início de sessão com identidades geridas para o principal de serviço de recursos do Azure e obter orientações sobre tópicos importantes, como o tratamento de erros.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se planeja usar os exemplos do Azure PowerShell ou a CLI do Azure neste artigo, certifique-se de que instala a versão mais recente do [do Azure PowerShell](/powershell/azure/install-az-ps) ou [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Todos os script de exemplo neste artigo supõe que o cliente de linha de comando está em execução numa VM com identidades geridas para recursos do Azure ativados. Utilize a funcionalidade de "Ligar" de VM no portal do Azure, para ligar remotamente à VM. Para obter detalhes sobre como ativar identidades geridas para recursos do Azure numa VM, consulte [configurar geridos identidades para recursos do Azure numa VM com o portal do Azure](qs-configure-portal-windows-vm.md), ou um dos artigos variantes (com o PowerShell, CLI, um modelo ou um Azure SDK). 
> - Para evitar erros durante o acesso a recursos, identidade gerida da VM tem de obter, pelo menos, "Leitor de" acesso no âmbito do apropriado (a VM ou superior) para permitir operações do Azure Resource Manager na VM. Ver [atribuir geridos identidades para acesso de recursos do Azure a um recurso no portal do Azure](howto-assign-access-portal.md) para obter detalhes.

## <a name="overview"></a>Descrição geral

Identidades geridas para recursos do Azure fornece um [objeto principal do serviço](../develop/developer-glossary.md#service-principal-object) , que é [criada após a ativação de identidades geridas para recursos do Azure](overview.md#how-does-it-work) na VM. O principal de serviço pode ser concedido acesso aos recursos do Azure e utilizado como uma identidade por clientes de script/linha de comandos para início de sessão e acesso a recursos. Tradicionalmente, para aceder a recursos protegidos na sua própria identidade do utilizador, um cliente de script precisaria:  

   - ser registrado e dar o seu consentimento no Azure AD como uma aplicação de cliente confidencial/web
   - Inicie sessão em seu principal de serviço, com as credenciais da aplicação (o que são provável que incorporado no script)

Com identidades geridas para recursos do Azure, o cliente de script já não precisa fazer qualquer um, como ele pode iniciar sessão sob as identidades geridas para o principal de serviço de recursos do Azure. 

## <a name="azure-cli"></a>CLI do Azure

O script a seguir demonstra como:

1. Inicie sessão no Azure AD em identidade gerida da VM para o principal de serviço de recursos do Azure  
2. Chamar o Azure Resource Manager e obter o ID principal de serviço. da VM CLI cuida do gerenciamento de aquisição/utilizar token para automaticamente. Certifique-se de que substitua o nome de máquina virtual de `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

O script a seguir demonstra como:

1. Inicie sessão no Azure AD em identidade gerida da VM para o principal de serviço de recursos do Azure  
2. Chame um cmdlet do Azure Resource Manager para obter informações sobre a VM. PowerShell se encarrega de gerir a utilização de token para automaticamente.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure

Ver [que o suporte do Azure AD a autenticação dos serviços Azure](services-support-msi.md#azure-services-that-support-azure-ad-authentication) para obter uma lista de recursos que oferecem suporte ao AD do Azure e foram testados com identidades geridas para recursos do Azure e os respetivos IDs de recurso.

## <a name="error-handling-guidance"></a>Documentação de orientação de manipulação de erro 

As respostas, como a seguir podem indicar a identidade gerida da VM para recursos do Azure não foi configurada corretamente:

- PowerShell: *Invoke-WebRequest: Não é possível ligar ao servidor remoto*
- CLI: *MSI: Falha ao obter um token da `http://localhost:50342/oauth2/token` com um erro de "HTTPConnectionPool (host = 'localhost', porta = 50342)* 

Se receber um desses erros, regresse à VM do Azure no [portal do Azure](https://portal.azure.com) e:

- Vá para o **identidade** página e certifique-se **sistema atribuído** está definido como "Sim".
- Vá para o **extensões** página e certifique-se a identidades geridas para a extensão de recursos do Azure **(planeada para preterição em Janeiro de 2019)** implementado com êxito.

Se uma for incorreta, terá de voltar a implementar as identidades geridas para recursos do Azure no seu recurso, ou a falha de implementação de resolução de problemas. Ver [identidades geridas por um configurar recursos do Azure numa VM com o portal do Azure](qs-configure-portal-windows-vm.md) se precisar de assistência com a configuração da VM.

## <a name="next-steps"></a>Passos Seguintes

- Para ativar identidades geridas para recursos do Azure numa VM do Azure, consulte [configurar geridos identidades para recursos do Azure na VM do Azure com o PowerShell](qs-configure-powershell-windows-vm.md), ou [configurar geridos identidades para recursos do Azure numa VM do Azure com a CLI do Azure](qs-configure-cli-windows-vm.md)






