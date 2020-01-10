---
title: Armazenar segredos em um cofre de chaves no Azure DevTest Labs | Microsoft Docs
description: Saiba como armazenar segredos em um Azure Key Vault e usá-los ao criar uma VM, fórmula ou um ambiente.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: 3f16d84f66f2da6094054d161f286070fc86a73b
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720128"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Armazenar segredos em um cofre de chaves no Azure DevTest Labs
Talvez seja necessário inserir um segredo complexo ao usar Azure DevTest Labs: senha para sua VM do Windows, chave SSH pública para sua VM Linux ou token de acesso pessoal para clonar seu repositório git por meio de um artefato. Os segredos são geralmente longos e têm caracteres aleatórios. Portanto, inseri-los pode ser complicado e inconveniente, especialmente se você usar o mesmo segredo várias vezes.

Para resolver esse problema e também manter seus segredos em um local seguro, o DevTest Labs dá suporte ao armazenamento de segredos em um [cofre de chaves do Azure](../key-vault/key-vault-overview.md). Quando um usuário salva um segredo pela primeira vez, o serviço do DevTest Labs cria automaticamente um cofre de chaves no mesmo grupo de recursos que contém o laboratório e armazena o segredo no cofre de chaves. O DevTest Labs cria um cofre de chaves separado para cada usuário. 

Observe que o usuário do laboratório precisará primeiro criar uma máquina virtual de laboratório antes de poder criar um segredo no cofre de chaves. Isso ocorre porque o DevTest Lab Service precisa associar o usuário do laboratório a um documento de usuário válido antes de poder criar e armazenar segredos em seu cofre de chaves. 


## <a name="save-a-secret-in-azure-key-vault"></a>Salvar um segredo no Azure Key Vault
Para salvar seu segredo em Azure Key Vault, execute as seguintes etapas:

1. Selecione **meus segredos** no menu à esquerda.
2. Insira um **nome** para o segredo. Você vê esse nome na lista suspensa ao criar uma VM, fórmula ou um ambiente. 
3. Insira o segredo como o **valor**.

    ![Segredo do repositório](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Usar um segredo de Azure Key Vault
Quando precisar inserir um segredo para criar uma VM, fórmula ou um ambiente, você pode inserir um segredo manualmente ou selecionar um segredo salvo no cofre de chaves. Para usar um segredo armazenado em seu cofre de chaves, execute as seguintes ações:

1. Selecione **usar um segredo salvo**. 
2. Selecione seu segredo na lista suspensa para **escolher um segredo**. 

    ![Usar segredo na VM](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Usar um segredo em um modelo de Azure Resource Manager
Você pode especificar o nome do segredo em um modelo de Azure Resource Manager que é usado para criar uma VM, conforme mostrado no exemplo a seguir:

![Usar segredo em fórmula ou ambiente](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Passos seguintes

- [Criar uma VM usando o segredo](devtest-lab-add-vm.md) 
- [Criar uma fórmula usando o segredo](devtest-lab-manage-formulas.md)
- [Criar um ambiente usando o segredo](devtest-lab-create-environment-from-arm.md)
