---
title: Configurar o Cofre de Chaves
description: Como configurar o Cofre chave para utilização com uma máquina virtual.
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: e4bff4d1826d9586495207095eccf8f6c66164a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870012"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurar o cofre chave para máquinas virtuais no Gestor de Recursos Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Na pilha de Gestor de Recursos Azure, segredos/certificados são modelados como recursos fornecidos pelo fornecedor de recursos da Key Vault. Para saber mais sobre o Cofre chave, veja [o que é o Cofre chave Azure?](../../key-vault/general/overview.md)

> [!NOTE]
> 1. Para que o Cofre chave seja utilizado com máquinas virtuais do Gestor de Recursos Azure, a propriedade **EnabledForDeployment** no Key Vault deve ser definida como verdadeira. Pode fazer isso em vários clientes.
> 2. O Cofre chave precisa de ser criado na mesma subscrição e localização que a Máquina Virtual.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Use powerShell para configurar o cofre chave
Para criar um cofre chave utilizando powerShell, consulte [set e recupere um segredo do Cofre de Chaves Azure utilizando powerShell](../../key-vault/secrets/quick-create-powershell.md).

Para novos cofres chave, pode utilizar este cmdlet PowerShell:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Para os cofres-chave existentes, pode utilizar este cmdlet PowerShell:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Use o CLI para configurar o Cofre chave
Para criar um cofre chave utilizando a interface de linha de comando (CLI), consulte Gerir o Cofre de [Chaves utilizando CLI](../../key-vault/general/manage-with-cli2.md#create-a-key-vault).

Para o CLI, tem de criar o cofre-chave antes de atribuir a política de implantação. Pode fazê-lo utilizando o comando seguinte:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Em seguida, para ativar o Cofre chave para utilização com a implementação do modelo, executar o seguinte comando:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>Use modelos para configurar o cofre chave
Enquanto você usa um modelo, `enabledForDeployment` você `true` precisa definir a propriedade para o recurso Key Vault.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Para outras opções que pode configurar quando criar um cofre chave utilizando modelos, consulte [Criar um cofre chave](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
