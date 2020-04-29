---
title: Recurso não encontrado erros
description: Descreve como resolver erros quando um recurso não pode ser encontrado ao ser implantado com um modelo de Gestor de Recursos Azure.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76773251"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Resolver erros não encontrados para os recursos do Azure

Este artigo descreve os erros que pode ver quando um recurso não pode ser encontrado durante a implementação.

## <a name="symptom"></a>Sintoma

Quando o seu modelo inclui o nome de um recurso que não pode ser resolvido, recebe um erro semelhante ao:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Se utilizar as funções de [referência](template-functions-resource.md#reference) ou [listaKeys](template-functions-resource.md#listkeys) com um recurso que não pode ser resolvido, recebe o seguinte erro:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Causa

O Gestor de Recursos precisa de recuperar as propriedades para um recurso, mas não consegue identificar o recurso na sua subscrição.

## <a name="solution-1---set-dependencies"></a>Solução 1 - conjunto de dependências

Se estiver a tentar implantar o recurso em falta no modelo, verifique se precisa de adicionar uma dependência. O Gestor de Recursos otimiza a implantação criando recursos em paralelo, quando possível. Se um recurso tiver de ser implantado após outro recurso, terá de utilizar o elemento **dependsOn** no seu modelo. Por exemplo, ao implementar uma aplicação web, o plano de Serviço de Aplicações deve existir. Se ainda não especificou que a aplicação da Web depende do plano de Serviço de Aplicações, o Gestor de Recursos cria ambos os recursos ao mesmo tempo. Obtém um erro afirmando que o recurso do plano do App Service não pode ser encontrado, porque ainda não existe ao tentar definir uma propriedade na aplicação web. Evita este erro definindo a dependência na aplicação web.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Mas, queres evitar estabelecer dependências que não são necessárias. Quando se tem dependências desnecessárias, prolonga-se a duração da implantação evitando que recursos que não dependam uns dos outros sejam implantados em paralelo. Além disso, pode criar dependências circulares que bloqueiam a implantação. As funções de função [de referência](template-functions-resource.md#reference) e [lista*](template-functions-resource.md#list) criam uma dependência implícita do recurso referenciado, quando esse recurso é implantado no mesmo modelo e é referenciado pelo seu nome (não id de recurso). Portanto, você pode ter mais dependências do que as dependências especificadas na propriedade **dependsOn.** A função [resourceId](template-functions-resource.md#resourceid) não cria uma dependência implícita ou valida que o recurso existe. As funções de função [de referência](template-functions-resource.md#reference) e [lista*](template-functions-resource.md#list) não criam uma dependência implícita quando o recurso é referido pelo seu ID de recurso. Para criar uma dependência implícita, passe o nome do recurso que é implantado no mesmo modelo.

Quando vês problemas de dependência, precisas de ter uma visão da ordem de implantação de recursos. Para visualizar a ordem das operações de implantação:

1. Selecione o histórico de implementação para o seu grupo de recursos.

   ![selecionar histórico de implantação](./media/error-not-found/select-deployment.png)

2. Selecione uma implementação a partir do histórico e selecione **Eventos**.

   ![selecionar eventos de implantação](./media/error-not-found/select-deployment-events.png)

3. Examine a sequência de eventos para cada recurso. Preste atenção ao estado de cada operação. Por exemplo, a imagem seguinte mostra três contas de armazenamento que foram implantadas em paralelo. Note que as três contas de armazenamento são iniciadas ao mesmo tempo.

   ![implantação paralela](./media/error-not-found/deployment-events-parallel.png)

   A imagem seguinte mostra três contas de armazenamento que não são implantadas em paralelo. A segunda conta de armazenamento depende da primeira conta de armazenamento, e a terceira conta de armazenamento depende da segunda conta de armazenamento. A primeira conta de armazenamento é iniciada, aceite e concluída antes do início da próxima.

   ![implantação sequencial](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Solução 2 - obtenha recursos de diferentes grupos de recursos

Quando o recurso existe num grupo de recursos diferente daquele para o que está a ser implantado, utilize a [função resourceId](template-functions-resource.md#resourceid) para obter o nome totalmente qualificado do recurso.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Solução 3 - verificar função de referência

Procure uma expressão que inclua a função [de referência.](template-functions-resource.md#reference) Os valores que fornece variam em função de se o recurso está no mesmo modelo, grupo de recursos e subscrição. Verifique duas vezes se está a fornecer os valores de parâmetro necessários para o seu cenário. Se o recurso estiver num grupo de recursos diferente, forneça o ID completo do recurso. Por exemplo, para fazer referência a uma conta de armazenamento noutro grupo de recursos, utilize:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Solução 4 - obter identidade gerida a partir de recurso

Se estiver a implantar um recurso que crie implicitamente uma [identidade gerida,](../../active-directory/managed-identities-azure-resources/overview.md)deve esperar até que esse recurso seja implantado antes de recuperar valores sobre a identidade gerida. Se passar o nome de identidade gerido para a função [de referência,](template-functions-resource.md#reference) o Gestor de Recursos tenta resolver a referência antes de o recurso e a identidade serem implementados. Em vez disso, passe o nome do recurso a que a identidade é aplicada. Esta abordagem garante que o recurso e a identidade gerida são implantados antes que o Gestor de Recursos resolva a função de referência.

Na função de `Full` referência, utilize para obter todas as propriedades, incluindo a identidade gerida.

Por exemplo, para obter o ID do inquilino para uma identidade gerida que é aplicada a um conjunto de escala de máquina virtual, use:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```