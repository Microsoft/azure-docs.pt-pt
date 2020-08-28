---
title: Utilize identidades geridas num Azure VM para iniciar sação - Azure ADV
description: Instruções passo a passo e exemplos para a utilização de identidades geridas por Azure VM para o principal do serviço de recursos Azure para o login do cliente do script e acesso a recursos.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 02815003bf5167a73c1e2362d9270deb867c84b7
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88999415"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Como utilizar identidades geridas para recursos Azure num VM Azure para o sign-in 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Este artigo fornece exemplos de scripts PowerShell e CLI para iniciar sing-in usando identidades geridas para o principal do serviço de recursos Azure, e orientação sobre tópicos importantes, como o tratamento de erros.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se pretender utilizar os exemplos Azure PowerShell ou Azure CLI neste artigo, certifique-se de instalar a versão mais recente da [Azure PowerShell](/powershell/azure/install-az-ps) ou [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Todo o script de amostra deste artigo pressupõe que o cliente da linha de comando está a funcionar num VM com identidades geridas para recursos Azure ativados. Utilize a função VM "Connect" no portal Azure, para ligar remotamente ao seu VM. Para obter detalhes sobre como permitir identidades geridas para recursos Azure num VM, consulte [identidades geridas configure para recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md), ou um dos artigos variantes (utilizando PowerShell, CLI, um modelo ou um Azure SDK). 
> - Para evitar erros durante o acesso aos recursos, a identidade gerida do VM deve ser dada pelo menos ao "Leitor" no âmbito apropriado (o VM ou superior) para permitir operações do Azure Resource Manager no VM. Consulte [atribuir identidades geridas para acesso de recursos Azure a um recurso que utilize o portal Azure](howto-assign-access-portal.md) para obter mais detalhes.

## <a name="overview"></a>Descrição geral

Identidades geridas para recursos Azure fornecem um [objeto principal de serviço](../develop/developer-glossary.md#service-principal-object) , que é criado ao permitir [identidades geridas para recursos Azure](overview.md) no VM. O principal de serviço pode ter acesso aos recursos da Azure, e usado como identidade pelos clientes script/linha de comando para acesso de login e recursos. Tradicionalmente, para aceder a recursos seguros sob a sua própria identidade, um cliente de script teria de:  

   - ser registado e consentido com a Azure AD como uma aplicação confidencial/cliente web
   - iniciar s-sign no seu principal serviço, usando as credenciais da app (que provavelmente estão incorporadas no script)

Com identidades geridas para recursos Azure, o seu cliente script já não precisa de fazer nenhum dos dois, uma vez que pode entrar sob as identidades geridas para o principal do serviço de recursos Azure. 

## <a name="azure-cli"></a>CLI do Azure

O seguinte guião demonstra como:

1. Inscreva-se na Azure AD sob a identidade gerida da VM para o diretor de serviço de recursos Azure  
2. Ligue para o Gestor de Recursos Azure e obtenha o ID principal de serviço da VM. O CLI cuida de gerir automaticamente a aquisição/utilização de tokens. Certifique-se de substituir o nome da máquina virtual por `<VM-NAME>` .  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

O seguinte guião demonstra como:

1. Inscreva-se na Azure AD sob a identidade gerida da VM para o diretor de serviço de recursos Azure  
2. Ligue para um cmdlet do Azure Resource Manager para obter informações sobre o VM. A PowerShell cuida de gerir o uso de fichas para si automaticamente.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>IDs de recursos para serviços Azure

Consulte [os serviços Azure que suportam a autenticação Azure AD](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) para uma lista de recursos que suportam a Azure AD e foram testados com identidades geridas para recursos Azure, e respetivas identidades de recursos.

## <a name="error-handling-guidance"></a>Orientação de manuseamento de erros 

Respostas como as seguintes podem indicar que a identidade gerida pelo VM para os recursos Azure não foi corretamente configurada:

- PowerShell: *Invoke-WebRequest : Incapaz de ligar ao servidor remoto*
- CLI: *MSI: Falhou na recuperação de um token `http://localhost:50342/oauth2/token` com um erro de 'HTTPConnectionPool (host='localhost', porta=50342)* 

Se receber um destes erros, volte ao Azure VM no [portal Azure](https://portal.azure.com) e:

- Vá à página **identidade** e certifique-se de que o **Sistema atribuído** está definido como "Sim".
- Vá à página **extensões** e garanta que as identidades geridas para a extensão dos recursos do Azure **(prevista para a depreciação em janeiro de 2019)** foram implementadas com sucesso.

Se algum deles estiver incorreto, poderá ter de recolocar as identidades geridas para os recursos Azure no seu recurso novamente, ou resolver problemas com a falha de implantação. Consulte [identidades geridas para configurar recursos Azure num VM utilizando o portal Azure](qs-configure-portal-windows-vm.md) se precisar de assistência com a configuração VM.

## <a name="next-steps"></a>Passos seguintes

- Para permitir identidades geridas para recursos Azure num Azure VM, consulte [identidades geridas configure para recursos Azure em um Azure VM usando PowerShell](qs-configure-powershell-windows-vm.md), ou [Configure identidades geridas para recursos Azure em um VM Azure usando Azure CLI](qs-configure-cli-windows-vm.md)






