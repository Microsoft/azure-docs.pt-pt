---
title: Implementar ambientes de modelos aninhados em Azure DevTest Labs
description: Aprenda a implementar modelos de Gestor de Recursos Azure aninhados para fornecer ambientes com Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 39002e286fafd4f813333a14ed86256a517897e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481345"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Implementar modelos aninhados do Gestor de Recursos Azure para ambientes de teste
Uma implementação aninhada permite-lhe executar outros modelos do Gestor de Recursos Azure dentro de um modelo principal de Gestor de Recursos. Permite-lhe decompor a sua implantação num conjunto de modelos específicos e direcionados. Proporciona benefícios em termos de teste, reutilização e legibilidade. O artigo [Utilizando modelos ligados ao implementar recursos Azure](../azure-resource-manager/templates/linked-templates.md) fornece uma boa visão geral desta solução com várias amostras de código. Este artigo fornece um exemplo específico da Azure DevTest Labs. 

## <a name="key-parameters"></a>Parâmetros-chave
Embora possa criar o seu próprio modelo de Gestor de Recursos de raiz, recomendamos que utilize o [projeto Azure Resource Group](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) em Visual Studio, o que facilita o desenvolvimento e depuração de modelos. Quando adiciona um recurso de implementação aninhado para azuredeploy.jsligado, o Visual Studio adiciona vários itens para tornar o modelo mais flexível. Estes itens incluem a sub-dobragem com o modelo secundário e o ficheiro de parâmetros, nomes variáveis dentro do ficheiro do modelo principal e dois parâmetros para a localização de armazenamento para os novos ficheiros. Os **_artifactsLocation** e **_artifactsLocationSasToken** são os parâmetros-chave que os DevTest Labs usam. 

Se não está familiarizado com o funcionamento dos Laboratórios DevTest com ambientes, consulte [criar ambientes multi-VM e recursos PaaS com modelos de Gestor de Recursos Azure.](devtest-lab-create-environment-from-arm.md) Os seus modelos estão armazenados no repositório ligado ao laboratório em DevTest Labs. Quando cria um novo ambiente com esses modelos, os ficheiros são transferidos para um recipiente de armazenamento Azure no laboratório. Para poder identificar e copiar os ficheiros aninhados, a DevTest Labs identifica os parâmetros _artifactsLocation e _artifactsLocationSasToken e copia as sub-dobradinhas até ao recipiente de armazenamento. Em seguida, insere automaticamente a localização e o token assinatura de acesso partilhado (SaS) em parâmetros. 

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

A pasta do repositório que contém este modelo tem uma sub-dobragem `nestedtemplates` com os ficheiros **NestOne.js** e **NestOne.parameters.js.** No **azuredeploy.jsem**, URI para o modelo é construído usando a localização dos artefactos, pasta de modelo aninhado, nome de arquivo de modelo aninhado. Da mesma forma, URI para os parâmetros é construído usando a localização dos artefactos, pasta de modelo aninhado e arquivo de parâmetros para o modelo aninhado. 

Aqui está a imagem da mesma estrutura do projeto no Visual Studio: 

![Estrutura do projeto no Estúdio Visual](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Pode adicionar pastas adicionais na pasta primária, mas não mais profundas do que um único nível. 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos para mais detalhes sobre ambientes: 

- [Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configure e use ambientes públicos em Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Ligue um ambiente à rede virtual do seu laboratório em Azure DevTest Labs](connect-environment-lab-virtual-network.md)