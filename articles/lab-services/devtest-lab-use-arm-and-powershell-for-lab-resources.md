---
title: Criar ou modificar laboratórios usando modelos de Azure Resource Manager
description: Saiba como usar modelos de Azure Resource Manager com o PowerShell para criar ou modificar laboratórios automaticamente em um laboratório de DevTest
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 180081829387afeb7e63defe7a22378aa8d417d6
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170298"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Criar ou modificar laboratórios automaticamente usando modelos de Azure Resource Manager e o PowerShell

O DevTest Labs fornece muitos modelos de Azure Resource Manager e scripts do PowerShell que podem ajudá-lo a criar rapidamente novos laboratórios ou modificar os laboratórios existentes e, em seguida, implantar esses recursos.

Este artigo ajuda você a orientá-lo pelo processo de usar esses modelos e scripts para automatizar a criação, modificação e implantação de seus laboratórios. Este artigo também mostra onde você pode encontrar mais informações sobre como usar o PowerShell para executar algumas tarefas comuns no DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Etapa 1: reunir seus modelos e scripts
Você pode encontrar modelos predefinidos do [Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) e [scripts do PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) em nosso [repositório GitHub](https://github.com/Azure/azure-devtestlab)público. Use-os como estão ou personalize-os para suas necessidades e armazene-os em seu próprio [repositório git privado](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Etapa 2: modificar seu modelo de Azure Resource Manager
Você pode seguir as etapas em [criar seu primeiro modelo de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) se você nunca criou um modelo antes.

Além disso, [as práticas recomendadas para a criação de modelos de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) oferecem muitas diretrizes e sugestões para ajudá-lo a criar modelos de Azure Resource Manager confiáveis e fáceis de usar. Normalmente, você usará uma variação de uma das abordagens ou exemplos fornecidos e modificará seu modelo para suas necessidades.

## <a name="step-3-deploy-resources-with-powershell"></a>Etapa 3: implantar recursos com o PowerShell
Depois de personalizar seus modelos e scripts, siga as etapas necessárias para [implantar recursos com modelos e Azure PowerShell do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). O artigo fornece informações gerais sobre como usar Azure PowerShell com Azure Resource Manager modelos para implantar seus recursos no Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Tarefas comuns que você pode executar no DevTest Labs usando o PowerShell
Há muitas outras tarefas comuns que você pode automatizar usando o PowerShell. As seções a seguir da documentação descrevem as etapas necessárias para executar essas tarefas.

* [Criar uma imagem personalizada de um arquivo VHD usando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Carregar o arquivo VHD na conta de armazenamento do laboratório usando o PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Adicionar um usuário externo a um laboratório usando o PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Criar uma função personalizada de laboratório usando o PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Passos seguintes
* Saiba como criar um [repositório git privado](devtest-lab-add-artifact-repo.md) em que você armazenará seus modelos ou scripts personalizados.
* Explore os [modelos de Azure Resource Manager da Galeria de modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).
