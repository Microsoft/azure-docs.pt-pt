---
title: Erros de recurso não encontrado
description: Descreve como resolver erros quando um recurso não pode ser encontrado ao implantar com um modelo de Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 06/06/2018
ms.openlocfilehash: c6dbe0e494e59017cda166c0785a187ab07c3b54
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150585"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Resolver erros não encontrados para recursos do Azure

Este artigo descreve os erros que você pode ver quando um recurso não pode ser encontrado durante a implantação.

## <a name="symptom"></a>Sintoma

Quando o modelo incluir o nome de um recurso que não pode ser resolvido, você receberá um erro semelhante a:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Se você usar as funções [Reference](resource-group-template-functions-resource.md#reference) ou [listKeys](resource-group-template-functions-resource.md#listkeys) com um recurso que não pode ser resolvido, receberá o seguinte erro:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Causa

O Resource Manager precisa recuperar as propriedades de um recurso, mas não pode identificar o recurso em sua assinatura.

## <a name="solution-1---set-dependencies"></a>Solução 1-definir dependências

Se você estiver tentando implantar o recurso ausente no modelo, verifique se você precisa adicionar uma dependência. O Resource Manager otimiza a implantação criando recursos em paralelo, quando possível. Se um recurso precisar ser implantado após outro recurso, você precisará usar o elemento **depending** em seu modelo. Por exemplo, ao implantar um aplicativo Web, o plano do serviço de aplicativo deve existir. Se você não tiver especificado que o aplicativo Web depende do plano do serviço de aplicativo, o Resource Manager criará os dois recursos ao mesmo tempo. Você receberá um erro informando que o recurso do plano do serviço de aplicativo não pode ser encontrado, pois ele ainda não existe durante a tentativa de definir uma propriedade no aplicativo Web. Você evita esse erro definindo a dependência no aplicativo Web.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Mas, você deseja evitar a definição de dependências que não são necessárias. Quando você tem dependências desnecessárias, prolongar a duração da implantação, impedindo que os recursos que não dependem uns dos outros sejam implantados em paralelo. Além disso, você pode criar dependências circulares que bloqueiam a implantação. As funções Function e [list *](resource-group-template-functions-resource.md#list) de [referência](resource-group-template-functions-resource.md#reference) criam uma dependência implícita do recurso referenciado, quando esse recurso é implantado no mesmo modelo e é referenciado por seu nome (não ID de recurso). Portanto, você pode ter mais dependências do que as dependências especificadas na propriedade **dependy** . A função [ResourceId](resource-group-template-functions-resource.md#resourceid) não cria uma dependência implícita ou valida se o recurso existe. A função de [referência](resource-group-template-functions-resource.md#reference) e as funções de [lista *](resource-group-template-functions-resource.md#list) não criam uma dependência implícita quando o recurso é REFERENCIAdo por sua ID de recurso. Para criar uma dependência implícita, passe o nome do recurso que é implantado no mesmo modelo.

Quando você vir problemas de dependência, precisará obter informações sobre a ordem de implantação de recursos. Para exibir a ordem das operações de implantação:

1. Selecione o histórico de implantação para seu grupo de recursos.

   ![Selecionar histórico de implantação](./media/resource-manager-not-found-errors/select-deployment.png)

2. Selecione uma implantação no histórico e selecione **eventos**.

   ![selecionar eventos de implantação](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. Examine a sequência de eventos para cada recurso. Preste atenção ao status de cada operação. Por exemplo, a imagem a seguir mostra três contas de armazenamento que são implantadas em paralelo. Observe que as três contas de armazenamento são iniciadas ao mesmo tempo.

   ![implantação paralela](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   A próxima imagem mostra três contas de armazenamento que não são implantadas em paralelo. A segunda conta de armazenamento depende da primeira conta de armazenamento e a terceira conta de armazenamento depende da segunda conta de armazenamento. A primeira conta de armazenamento é iniciada, aceita e concluída antes que a próxima seja iniciada.

   ![implantação sequencial](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Solução 2 – obter recurso de um grupo de recursos diferente

Quando o recurso existir em um grupo de recursos diferente daquele que está sendo implantado, use a [função ResourceId](resource-group-template-functions-resource.md#resourceid) para obter o nome totalmente qualificado do recurso.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Solução 3-verificar função de referência

Procure uma expressão que inclua a função de [referência](resource-group-template-functions-resource.md#reference) . Os valores que você fornece variam de acordo com o fato de o recurso estar no mesmo modelo, grupo de recursos e assinatura. Verifique se você está fornecendo os valores de parâmetro necessários para seu cenário. Se o recurso estiver em um grupo de recursos diferente, forneça a ID do recurso completo. Por exemplo, para fazer referência a uma conta de armazenamento em outro grupo de recursos, use:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```