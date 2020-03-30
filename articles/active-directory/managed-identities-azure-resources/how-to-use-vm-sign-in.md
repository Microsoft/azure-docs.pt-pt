---
title: Use identidades geridas num Azure VM para iniciar sessão - Azure AD
description: Instruções passo a passo e exemplos para a utilização de um Azure VM gerido identidades para o serviço de recursos Azure principal para o log ingestão de clientes e acesso a recursos.
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
ms.openlocfilehash: 34f4dc749c0254b5aa4e9ff018d2a869832de3f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547393"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Como usar identidades geridas para recursos Azure em um VM Azure para iniciar sessão 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Este artigo fornece exemplos de scripts PowerShell e CLI para iniciar o acesso a identidades geridas para o principal de serviço de recursos do Azure, e orientação sobre tópicos importantes como o tratamento de erros.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se planeia utilizar os exemplos Azure PowerShell ou Azure CLI neste artigo, certifique-se de instalar a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps) ou [do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Todo o script de amostraneste artigo pressupõe que o cliente da linha de comando está a executar num VM com identidades geridas para recursos Azure habilitados. Utilize a função VM "Connect" no portal Azure, para ligar remotamente ao seu VM. Para mais detalhes sobre a possibilidade de permitir identidades geridas para os recursos Azure num VM, consulte [as identidades geridas pela Configure para os recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md), ou um dos artigos variantes (utilizando PowerShell, CLI, um modelo ou um Azure SDK). 
> - Para evitar erros durante o acesso aos recursos, a identidade gerida do VM deve ter acesso pelo menos ao "Leitor" no âmbito apropriado (o VM ou superior) para permitir operações do Gestor de Recursos Azure no VM. Consulte as identidades geridas para o acesso dos [recursos do Azure a um recurso utilizando o portal Azure](howto-assign-access-portal.md) para mais detalhes.

## <a name="overview"></a>Descrição geral

Identidades geridas para os recursos Azure fornecem um [objeto principal](../develop/developer-glossary.md#service-principal-object) de serviço , que é criado para [permitir identidades geridas para recursos Azure](overview.md#how-does-the-managed-identities-for-azure-resources-work) no VM. O diretor de serviço pode ter acesso aos recursos do Azure, e utilizado como identidade por clientes de script/linha de comando para iniciar sessão e acesso a recursos. Tradicionalmente, para aceder a recursos seguros sob a sua própria identidade, um cliente script teria de:  

   - ser registado e consentido com a AD Azure como uma aplicação cliente confidencial/web
   - Inscreva-se sob o seu principal de serviço, utilizando as credenciais da app (que provavelmente estão incorporadas no script)

Com identidades geridas para recursos Azure, o seu cliente script já não precisa de fazer, pois pode assinar sob as identidades geridas para o serviço de recursos Azure principal. 

## <a name="azure-cli"></a>CLI do Azure

O seguinte guião demonstra como:

1. Inscreva-se na Azure AD sob a identidade gerida pela VM para o principal de serviço de recursos do Azure  
2. Ligue para o Gestor de Recursos Azure e obtenha o PRINCIPAL ID do serviço da VM. O CLI cuida de gerir automaticamente a aquisição/utilização de tokens para si. Certifique-se de substituir o `<VM-NAME>`seu nome de máquina virtual por .  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

O seguinte guião demonstra como:

1. Inscreva-se na Azure AD sob a identidade gerida pela VM para o principal de serviço de recursos do Azure  
2. Ligue para um gestor de recursos azure cmdlet para obter informações sobre o VM. A PowerShell cuida de gerir automaticamente o uso do token.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>IDs de recursos para serviços Azure

Consulte [os serviços Azure que suportam a autenticação Azure AD](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) para uma lista de recursos que suportam a Azure AD e foram testados com identidades geridas para recursos Azure, e seus respetivos IDs de recursos.

## <a name="error-handling-guidance"></a>Orientação de manipulação de erros 

Respostas como as seguintes podem indicar que a identidade gerida pelo VM para os recursos Azure não foi corretamente configurada:

- PowerShell: *Invoke-WebRequest : Incapaz de ligar ao servidor remoto*
- CLI: *MSI: Não conseguiu recuperar `http://localhost:50342/oauth2/token` um símbolo com um erro de 'HTTPConnectionPool (host='hosthost', porta=50342)* 

Se receber um desses erros, volte ao Azure VM no [portal Azure](https://portal.azure.com) e:

- Vá à página **identidade** e certifique-se de que o **Sistema atribuído** está definido para "Sim".
- Vá à página **de Extensões** e garanta que as identidades geridas para extensão de recursos Do Azure **(prevista para a depreciação em janeiro de 2019)** foram implementadas com sucesso.

Se um dos dois estiver incorreto, poderá ter de recolocar as identidades geridas dos recursos Azure no seu recurso novamente, ou resolver a falha de implantação. Consulte [as identidades geridas para os recursos Do Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md) se precisar de assistência com a configuração VM.

## <a name="next-steps"></a>Passos seguintes

- Para permitir identidades geridas para recursos Azure num Azure VM, consulte [as identidades geridas pela Configure para os recursos Azure num Azure VM utilizando powerShell](qs-configure-powershell-windows-vm.md), ou [Configure identidades geridas para recursos Azure em um Azure VM usando O ClI Azure](qs-configure-cli-windows-vm.md)






