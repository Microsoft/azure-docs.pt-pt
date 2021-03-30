---
title: Guarde segredos num cofre chave em Azure DevTest Labs | Microsoft Docs
description: Aprenda a armazenar segredos num Cofre de Chaves Azure e use-os enquanto cria um VM, fórmula ou um ambiente.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5714279ef183cb930d643575466dae3d6cb69bba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85481651"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Guarde segredos em um cofre chave em Azure DevTest Labs
Poderá ter de introduzir um segredo complexo ao utilizar o Azure DevTest Labs: palavra-passe para o seu Windows VM, chave SSH pública para o seu VM Linux ou token de acesso pessoal para clonar o seu Git repo através de um artefacto. Os segredos são geralmente longos e têm personagens aleatórios. Portanto, inseri-los pode ser complicado e inconveniente, especialmente se usar o mesmo segredo várias vezes.

Para resolver este problema e também manter os seus segredos num local seguro, a DevTest Labs suporta guardar segredos num [cofre de chaves Azure.](../key-vault/general/overview.md) Quando um utilizador guarda um segredo pela primeira vez, o serviço DevTest Labs cria automaticamente um cofre chave no mesmo grupo de recursos que contém o laboratório e armazena o segredo no cofre das chaves. A DevTest Labs cria um cofre de chaves separado para cada utilizador. 

Por favor, note que o utilizador do laboratório terá primeiro de criar uma máquina virtual de laboratório antes de criar um segredo no cofre das chaves. Isto porque o serviço DevTest Lab precisa de associar o utilizador do laboratório a um documento de utilizador válido antes de ser autorizado a criar e armazenar segredos no seu cofre chave. 


## <a name="save-a-secret-in-azure-key-vault"></a>Guarde um segredo em Azure Key Vault
Para guardar o seu segredo no Cofre da Chave Azure, faça os seguintes passos:

1. Selecione **os meus segredos** no menu esquerdo.
2. Insira um **nome** para o segredo. Você vê este nome na lista de drop-down ao criar um VM, fórmula ou um ambiente. 
3. Insira o segredo como **o valor.**

    ![Segredo da loja](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Use um segredo do Cofre da Chave Azure
Quando precisa de inserir um segredo para criar um VM, fórmula ou um ambiente, pode introduzir um segredo manualmente ou selecionar um segredo guardado a partir do cofre da chave. Para utilizar um segredo armazenado no seu cofre, faça as seguintes ações:

1. **Selecione Utilize um segredo guardado**. 
2. Selecione o seu segredo da lista de drop-down para **Escolher um segredo**. 

    ![Use segredo em VM](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Use um segredo em um modelo de gestor de recursos Azure
Pode especificar o seu nome secreto num modelo do Gestor de Recursos Azure que é utilizado para criar um VM como mostrado no seguinte exemplo:

![Use segredo em fórmula ou ambiente](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Passos seguintes

- [Criar um VM usando o segredo](devtest-lab-add-vm.md) 
- [Criar uma fórmula usando o segredo](devtest-lab-manage-formulas.md)
- [Criar um ambiente usando o segredo](devtest-lab-create-environment-from-arm.md)
