---
title: Configurar a chave de cofre para Windows de VMs no Azure Resource Manager | Documentos da Microsoft
description: Como configurar o Cofre de chaves para utilização com uma máquina virtual do Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: a8c29f015b6b3652361a886585cb4ccc3f3b7293
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519957"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurar o Cofre de chaves para máquinas virtuais no Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Na pilha do Azure Resource Manager, os segredos/certificados são modelados como recursos que são fornecidos pelo fornecedor de recursos do Cofre de chaves. Para saber mais sobre o Key Vault, consulte [o que é o Azure Key Vault?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. Para que o Cofre de chaves para serem utilizadas com as máquinas virtuais do Azure Resource Manager, o **EnabledForDeployment** propriedade no Cofre de chaves tem de ser definida como true. Pode fazê-lo em vários clientes.
> 2. O Cofre de chaves tem de ser criada na mesma subscrição e localização que a Máquina Virtual.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Utilize o PowerShell para configurar o Cofre de chaves
Para criar um cofre de chaves com o PowerShell, veja [conjunto e obter um segredo no Azure Key Vault com o PowerShell](../../key-vault/quick-create-powershell.md).

Para obter cofres de chaves novo, pode utilizar este cmdlet do PowerShell:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Para obter cofres de chaves existentes, pode utilizar este cmdlet do PowerShell:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Utilizar a CLI para configurar o Cofre de chaves
Para criar um cofre de chaves utilizando a interface de linha de comandos (CLI), veja [gerir Key Vault com a CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Para a CLI, terá de criar o Cofre de chaves, antes de atribuir a política de implementação. Pode fazê-lo utilizando o comando seguinte:

    az keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Utilize modelos para configurar o Cofre de chaves
Apesar de utilizar um modelo, tem de definir o `enabledForDeployment` propriedade `true` para o recurso do Key Vault.

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

Para outras opções que pode configurar quando cria um cofre de chaves com os modelos, veja [criar um cofre de chaves](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
