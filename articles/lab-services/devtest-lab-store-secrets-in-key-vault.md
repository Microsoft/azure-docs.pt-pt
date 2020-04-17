---
title: Guarde segredos num cofre chave em Azure DevTest Labs Microsoft Docs
description: Aprenda a armazenar segredos num Cofre de Chaves Azure e use-os enquanto cria um VM, fórmula ou um ambiente.
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
ms.openlocfilehash: 0ca36a7081aaf70ee2045ee7586184c89591df16
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461519"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Guarde segredos em um cofre chave em Azure DevTest Labs
Poderá ter de introduzir um segredo complexo ao utilizar o Azure DevTest Labs: palavra-passe para o seu Windows VM, chave SSH pública para o seu VM Linux ou ficha de acesso pessoal para clonar o seu repo Git através de um artefacto. Os segredos são geralmente longos e têm personagens aleatórios. Portanto, entrar neles pode ser complicado e inconveniente, especialmente se você usar o mesmo segredo várias vezes.

Para resolver este problema e também manter os seus segredos num local seguro, a DevTest Labs suporta guardar segredos num [cofre de chaves Azure.](../key-vault/general/overview.md) Quando um utilizador guarda um segredo pela primeira vez, o serviço DevTest Labs cria automaticamente um cofre chave no mesmo grupo de recursos que contém o laboratório e armazena o segredo no cofre chave. A DevTest Labs cria um cofre de chave separado para cada utilizador. 

Por favor, note que o utilizador do laboratório terá primeiro de criar uma máquina virtual de laboratório antes de criar um segredo no cofre da chave. Isto porque o serviço DevTest Lab precisa de associar o utilizador do laboratório a um documento de utilizador válido antes de poderem criar e armazenar segredos no seu cofre chave. 


## <a name="save-a-secret-in-azure-key-vault"></a>Salve um segredo no Cofre de Chaves Azure
Para guardar o seu segredo no Cofre de Chaves Azure, faça os seguintes passos:

1. Selecione **os meus segredos** no menu esquerdo.
2. Insira um **nome** para o segredo. Você vê este nome na lista de drop-down ao criar um VM, fórmula ou um ambiente. 
3. Insira o segredo como o **valor.**

    ![Guardar segredo](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Use um segredo do Cofre de Chaves Azure
Quando precisa de introduzir um segredo para criar um VM, fórmula ou um ambiente, pode introduzir um segredo manualmente ou selecionar um segredo guardado do cofre da chave. Para utilizar um segredo armazenado no seu cofre chave, faça as seguintes ações:

1. Selecione **Utilize um segredo guardado**. 
2. Selecione o seu segredo na lista de lançamentos para **Escolher um segredo**. 

    ![Use o segredo em VM](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Use um segredo em um modelo de Gestor de Recursos Azure
Pode especificar o seu nome secreto num modelo de Gestor de Recursos Azure que é usado para criar um VM, como mostra o seguinte exemplo:

![Use segredo em fórmula ou ambiente](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Passos seguintes

- [Criar um VM usando o segredo](devtest-lab-add-vm.md) 
- [Criar uma fórmula usando o segredo](devtest-lab-manage-formulas.md)
- [Criar um ambiente usando o segredo](devtest-lab-create-environment-from-arm.md)
