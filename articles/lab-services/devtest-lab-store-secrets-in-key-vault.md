---
title: Store segredos num cofre de chaves no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como armazenar segredos num cofre de chaves do Azure e utilizá-los ao criar uma VM, fórmula, ou um ambiente.
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
ms.openlocfilehash: 17469d3602935715d570a496e12b6680269ff465
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622945"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Store segredos num cofre de chaves no Azure DevTest Labs
Poderá ter de introduzir um segredo complexo, ao utilizar o Azure DevTest Labs: palavra-passe para a sua VM do Windows, chave pública de SSH para a sua VM do Linux ou token de acesso pessoal clonar o repositório do Git através de um artefacto. Segredos são normalmente longos e tem carateres aleatórios. Por conseguinte, a introduzir pode ser difícil e inconveniente, especialmente se usar o mesmo segredo várias vezes.

Para resolver o problema e também manter seus segredos num local seguro, DevTest Labs dá suporte a armazenar segredos numa [do Azure key vault](../key-vault/key-vault-overview.md). Quando um utilizador guarda um segredo pela primeira vez, o serviço de DevTest Labs cria automaticamente um cofre de chaves no mesmo grupo de recursos que contém o laboratório e armazena o segredo no Cofre de chaves. DevTest Labs cria um cofre de chaves separado para cada utilizador. 

## <a name="save-a-secret-in-azure-key-vault"></a>Guardar um segredo no Cofre de chaves do Azure
Para guardar o seu segredo no Cofre de chaves do Azure, siga os passos abaixo:

1. Selecione **Moje tajné kódy** no menu da esquerda.
2. Introduza um **nome** para o segredo. Verá este nome na lista pendente ao criar uma VM, fórmula, ou um ambiente. 
3. Introduza o segredo como o **valor**.

    ![Segredo de Store](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Utilize um segredo no Azure Key Vault
Quando tem de introduzir um segredo para criar uma VM, fórmula, nem num ambiente, pode introduzir um segredo manualmente ou selecione um segredo guardado a partir do Cofre de chave. Para utilizar um segredo armazenado no seu Cofre de chaves, faça as seguintes ações:

1. Selecione **utilizar um segredo guardado**. 
2. Selecione o seu segredo da lista pendente para **escolher um segredo**. 

    ![Utilize o segredo numa VM](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Utilizar um segredo num modelo do Azure Resource Manager
Pode especificar o nome do segredo num modelo do Azure Resource Manager que é utilizado para criar uma VM, conforme mostrado no exemplo a seguir:

![Utilizar o segredo no ambiente ou de fórmula](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma VM com o segredo](devtest-lab-add-vm.md) 
- [Criar uma fórmula com o segredo](devtest-lab-manage-formulas.md)
- [Criar um ambiente com o segredo](devtest-lab-create-environment-from-arm.md)
