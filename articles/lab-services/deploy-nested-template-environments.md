---
title: Implementar ambientes de modelos aninhados em Laboratórios Azure DevTest
description: Aprenda a implementar modelos de Gestor de Recursos Azure aninhados para fornecer ambientes com Laboratórios Azure DevTest.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e83bc4e77a44f20d55fa3b56bc81aefd1d25bb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168828"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Implementar modelos de Gestor de Recursos Azure aninhados para testar ambientes
Uma implementação aninhada permite-lhe executar outros modelos do Gestor de Recursos Azure a partir de um modelo principal do Gestor de Recursos. Permite-lhe decompor a sua implantação num conjunto de modelos específicos e específicos para o propósito. Proporciona benefícios em termos de testes, reutilização e legibilidade. O artigo [Utilizando modelos ligados ao implantar recursos Azure](../azure-resource-manager/templates/linked-templates.md) fornece uma boa visão geral desta solução com várias amostras de código. Este artigo fornece um exemplo específico para o Azure DevTest Labs. 

## <a name="key-parameters"></a>Parâmetros-chave
Embora possa criar o seu próprio modelo de Gestor de Recursos do zero, recomendamos que utilize o projeto do Grupo de [Recursos Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) no Estúdio Visual, o que facilita o desenvolvimento e depuração de modelos. Quando adiciona um recurso de implantação aninhada ao azuredeploy.json, o Visual Studio adiciona vários itens para tornar o modelo mais flexível. Estes itens incluem a subpasta com o modelo secundário e o ficheiro de parâmetros, nomes variáveis dentro do ficheiro principal do modelo, e dois parâmetros para a localização de armazenamento para os novos ficheiros. Os **_artifactsLocation** e **_artifactsLocationSasToken** são os parâmetros-chave que os Laboratórios DevTest usam. 

Se não está familiarizado com a forma como os DevTest Labs funcionam com ambientes, consulte [Create multi-VM ambientes e recursos PaaS com modelos de Gestor de Recursos Azure](devtest-lab-create-environment-from-arm.md). Os seus modelos estão armazenados no repositório ligado ao laboratório em DevTest Labs. Quando se cria um novo ambiente com esses modelos, os ficheiros são transferidos para um contentor de Armazenamento Azure no laboratório. Para poder identificar e copiar os ficheiros aninhados, o DevTest Labs identifica os parâmetros _artifactsLocation e _artifactsLocationSasToken e copia as subpastas até ao recipiente de armazenamento. Em seguida, insere automaticamente a localização e a assinatura de acesso partilhado (SaS) em parâmetros. 

## <a name="nested-deployment-example"></a>Exemplo de implantação aninhada
Aqui está um exemplo simples de uma implantação aninhada:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

A pasta no repositório que contém `nestedtemplates` este modelo tem uma subpasta com os ficheiros **NestOne.json** e **NestOne.parameters.json**. No **azuredeploy.json**, URI para o modelo é construído usando a localização dos artefactos, pasta de modelo aninhado, nome de ficheiro de modelo aninhado. Da mesma forma, o URI para os parâmetros é construído usando a localização dos artefactos, pasta de modelo aninhado e arquivo de parâmetro para o modelo aninhado. 

Aqui está a imagem da mesma estrutura de projeto no Estúdio Visual: 

![Estrutura de projeto em Estúdio Visual](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Pode adicionar pastas adicionais na pasta principal, mas não mais profunda do que um único nível. 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos para obter detalhes sobre ambientes: 

- [Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configure e use ambientes públicos em Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Ligue um ambiente à rede virtual do seu laboratório em Azure DevTest Labs](connect-environment-lab-virtual-network.md)