---
title: Implementar ambientes de modelo do Resource Manager aninhada no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como implementar modelos aninhados do Gestor de recursos do Azure para fornecer ambientes com o Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: spelluru
ms.openlocfilehash: eec0cde4a36449f85998bfb04d16f1d52c68bb19
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835289"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Implementar modelos aninhados do Gestor de recursos do Azure para ambientes de teste
Uma implementação aninhada permite-lhe executar outros modelos de Azure Resource Manager a partir de um modelo do Resource Manager principal. Permite-lhe para decompor a sua implantação num conjunto de modelos de destinados e a finalidade específica. Ele fornece benefícios em termos de teste, reutilização e legibilidade. O artigo [utilizar modelos ligados durante a implantação de recursos do Azure](../azure-resource-manager/resource-group-linked-templates.md) fornece uma boa visão geral dessa solução com vários exemplos de código. Este artigo fornece um exemplo que é específico para o Azure DevTest Labs. 

## <a name="key-parameters"></a>Parâmetros de chave
Embora possa criar seu próprio modelo do Resource Manager a partir do zero, recomendamos que utilize o [projeto do grupo de recursos do Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) no Visual Studio, que torna mais fácil desenvolver e depurar modelos. Quando adiciona um recurso de implementação aninhada para azuredeploy. JSON, o Visual Studio adiciona vários itens a fazer com que o modelo mais flexível. Esses itens incluem a subpasta com o ficheiro de modelo e parâmetros secundário, os nomes de variáveis dentro do arquivo de modelo principal e dois parâmetros para a localização de armazenamento para os novos ficheiros. O **artifactslocation** e **artifactslocationsastoken** são os parâmetros de chave que utiliza o DevTest Labs. 

Se não estiver familiarizado com o funcionamento de laboratórios DevTest com ambientes, veja [criar ambientes multi-VM e recursos PaaS com modelos Azure Resource Manager](devtest-lab-create-environment-from-arm.md). Os modelos são armazenados no repositório associado ao laboratório no DevTest Labs. Quando cria um novo ambiente com esses modelos, os ficheiros são movidos para um contentor de armazenamento do Azure no laboratório. Para conseguir identificar e copie os ficheiros aninhados, o DevTest Labs identifica os parâmetros artifactslocation e artifactslocationsastoken e copia as subpastas até o contentor de armazenamento. Em seguida, ele automaticamente insere a localização e o token de assinatura de acesso partilhado (SaS) em parâmetros. 

## <a name="nested-deployment-example"></a>Exemplo de implementação aninhada
Eis um exemplo simples de uma implementação aninhada:

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

A pasta no repositório que contém este modelo tem uma subpasta `nestedtemplates` com os arquivos **NestOne.json** e **NestOne.parameters.json**. Na **azuredeploy. JSON**, URI para o modelo é criado com a localização de artefactos, a pasta de modelos aninhados, aninhada de nome de ficheiro de modelo. Da mesma forma, o URI para os parâmetros é compilada com a localização de artefactos, a pasta de modelo aninhado e o ficheiro de parâmetros para o modelo aninhado. 

Segue-se a imagem da mesma estrutura de projeto no Visual Studio: 

![Estrutura do projeto no Visual Studio](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Pode adicionar pastas adicionais na pasta principal, mas não aprofundar mais do que um único nível. 

## <a name="next-steps"></a>Passos Seguintes
Veja os artigos seguintes para obter detalhes sobre os ambientes: 

- [Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurar e utilizar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Ligar um ambiente a rede virtual do seu laboratório no Azure DevTest Labs](connect-environment-lab-virtual-network.md)