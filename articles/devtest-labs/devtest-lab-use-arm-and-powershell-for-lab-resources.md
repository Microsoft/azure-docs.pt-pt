---
title: Criar ou modificar laboratórios usando modelos de Gestor de Recursos Azure
description: Saiba como usar modelos do Gestor de Recursos Azure com o PowerShell para criar ou modificar laboratórios automaticamente num laboratório de DevTest
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 645f1d0717514d2c7e7b16844513327127e4e1a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87272645"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Criar ou modificar laboratórios automaticamente utilizando modelos de Gestor de Recursos Azure e PowerShell

A DevTest Labs fornece muitos modelos de Gestor de Recursos Azure e scripts PowerShell que podem ajudá-lo rapidamente e automaticamente a criar novos laboratórios ou modificar laboratórios existentes e, em seguida, implementar esses recursos.

Este artigo ajuda a guiá-lo através do processo de utilização destes modelos e scripts para automatizar a criação, modificação e implementação dos seus laboratórios. Este artigo também mostra onde pode encontrar mais informações sobre como usar o PowerShell para executar algumas tarefas comuns em DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Passo 1: Reúna os seus modelos e scripts
Você pode encontrar [modelos pré-fabricados do Gestor de Recursos Azure](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) e [scripts PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) no nosso [repositório](https://github.com/Azure/azure-devtestlab)público GitHub . Use-os como está, ou personalize-os para as suas necessidades e guarde-os no seu próprio [repo Git privado.](devtest-lab-add-artifact-repo.md)

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Passo 2: Modificar o seu modelo de Gestor de Recursos Azure
Pode seguir os passos no [Criar o seu primeiro modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) se nunca tiver criado um modelo antes.

Além disso, [as melhores práticas para criar modelos de Gestor de Recursos Azure](../azure-resource-manager/templates/template-best-practices.md) oferecem muitas diretrizes e sugestões para ajudá-lo a criar modelos de Gestor de Recursos Azure que são fiáveis e fáceis de usar. Normalmente, você usará uma variação de uma das abordagens ou exemplos fornecidos e modificará o seu modelo para as suas necessidades.

## <a name="step-3-deploy-resources-with-powershell"></a>Passo 3: Mobilizar recursos com PowerShell
Depois de ter personalizado os seus modelos e scripts, siga os passos necessários para [implementar recursos com modelos de Gestor de Recursos e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md). O artigo fornece informações gerais sobre a utilização do Azure PowerShell com modelos do Azure Resource Manager para implantar os seus recursos no Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Tarefas comuns que pode executar em laboratórios DevTest usando PowerShell
Existem muitas outras tarefas comuns que pode automatizar usando o PowerShell. As seguintes secções da documentação descrevem os passos necessários para a realização destas tarefas.

* [Crie uma imagem personalizada a partir de um ficheiro VHD usando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Faça upload do ficheiro VHD para a conta de armazenamento do laboratório usando o PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Adicione um utilizador externo a um laboratório usando o PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Crie um papel personalizado de laboratório usando o PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Passos seguintes
* Saiba como criar um [repositório privado de Git](devtest-lab-add-artifact-repo.md) onde armazenará os seus modelos ou scripts personalizados.
* Explore os [modelos do Gestor de Recursos Azure da galeria de modelos Azure Quickstart.](https://github.com/Azure/azure-quickstart-templates)
