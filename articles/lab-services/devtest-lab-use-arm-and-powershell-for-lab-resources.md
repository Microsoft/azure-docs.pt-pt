---
title: Criar ou modificar laboratórios automaticamente utilizando modelos Azure Resource Manager com o PowerShell | Documentos da Microsoft
description: Saiba como utilizar os modelos Azure Resource Manager com o PowerShell para criar ou modificar laboratórios automaticamente num laboratório DevTest
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: cb5a08730b47cb5df3116aa4a54554ef0ee6f260
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622462"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Criar ou modificar laboratórios automaticamente utilizando modelos Azure Resource Manager e o PowerShell

DevTest Labs fornece muitos modelos Azure Resource Manager e scripts do PowerShell que podem ajudá-lo a rapidamente e criar automaticamente os novos laboratórios ou modificar laboratórios existentes e, em seguida, implementar esses recursos.

Este artigo ajuda a orientar o processo de usar esses modelos e scripts para automatizar a criação, modificação e implementação de seus laboratórios. Este artigo também mostra onde pode encontrar mais informações sobre como utilizar o PowerShell para efetuar algumas tarefas comuns no DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Passo 1: Recolher os seus modelos e scripts
Pode encontrar previamente criados [modelos Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) e [scripts do PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) em nosso público [repositório do GitHub](https://github.com/Azure/azure-devtestlab). Utilizá-los conforme-é, ou personalize-os para as suas necessidades e armazená-los em seus próprios [repositório de Git privado](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Passo 2: Alterar o seu modelo do Azure Resource Manager
Pode seguir os passos indicados em [criar o primeiro modelo do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) se nunca tiver criado um modelo de antes.

Além disso, [melhores práticas para a criação de modelos Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) oferece muitas orientações e sugestões para ajudar a criar modelos Azure Resource Manager que são confiáveis e fáceis de usar. Normalmente, irá utilizar uma variação de um dos exemplos fornecidos ou abordagens e alterar o seu modelo para as suas necessidades.

## <a name="step-3-deploy-resources-with-powershell"></a>Passo 3: Implementar recursos com o PowerShell
Depois de ter personalizada os modelos e os scripts, siga as etapas necessárias para [implementar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). O artigo fornece informações sobre como utilizar o Azure PowerShell com modelos Azure Resource Manager para implementar os seus recursos no Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Tarefas comuns que pode efetuar no DevTest labs com o PowerShell
Existem muitas outras tarefas comuns que podem automatizar com o PowerShell. As secções seguintes da documentação descrevem os passos necessários para executar estas tarefas.

* [Criar uma imagem personalizada a partir de um ficheiro VHD com o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Carregar o ficheiro VHD para a conta de armazenamento do laboratório com o PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Adicionar um utilizador externo a um laboratório com o PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Criar uma função personalizada de laboratório com o PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Passos Seguintes
* Saiba como criar uma [repositório de Git privado](devtest-lab-add-artifact-repo.md) onde irá armazenar seus modelos personalizados ou scripts.
* Explore os [modelos do Azure Resource Manager da Galeria de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).
