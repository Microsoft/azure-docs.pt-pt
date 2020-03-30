---
title: Configurar o cofre chave para VMs do Windows em Gestor de Recursos Azure
description: Como configurar o Cofre chave para utilização com uma máquina virtual do Gestor de Recursos Azure.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: a64163da1dee2bceb567436dc18ba0fa5274cfcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243149"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurar o cofre chave para máquinas virtuais no Gestor de Recursos Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Na pilha de Gestor de Recursos Azure, segredos/certificados são modelados como recursos fornecidos pelo fornecedor de recursos da Key Vault. Para saber mais sobre o Cofre chave, veja [o que é o Cofre chave Azure?](../../key-vault/key-vault-overview.md)

> [!NOTE]
> 1. Para que o Cofre chave seja utilizado com máquinas virtuais do Gestor de Recursos Azure, a propriedade **EnabledForDeployment** no Key Vault deve ser definida como verdadeira. Pode fazer isso em vários clientes.
> 2. O Cofre chave precisa de ser criado na mesma subscrição e localização que a Máquina Virtual.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Use powerShell para configurar o cofre chave
Para criar um cofre chave utilizando powerShell, consulte [set e recupere um segredo do Cofre de Chaves Azure utilizando powerShell](../../key-vault/quick-create-powershell.md).

Para novos cofres chave, pode utilizar este cmdlet PowerShell:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Para os cofres-chave existentes, pode utilizar este cmdlet PowerShell:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Use o CLI para configurar o Cofre chave
Para criar um cofre chave utilizando a interface de linha de comando (CLI), consulte Gerir o Cofre de [Chaves utilizando CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

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
