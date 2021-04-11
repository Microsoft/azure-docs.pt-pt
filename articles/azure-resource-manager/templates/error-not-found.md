---
title: Recurso não encontrado erros
description: Descreve como resolver erros quando um recurso não pode ser encontrado. O erro pode ocorrer ao implementar um modelo de Gestor de Recursos Azure ou quando se toma ações de gestão.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: b80c32683190167d5c0d6e0a7f75acce8bbdb833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950880"
---
# <a name="resolve-resource-not-found-errors"></a>Resolver recurso não encontrados erros

Este artigo descreve o erro que vê quando um recurso não pode ser encontrado durante uma operação. Normalmente, vê-se este erro ao utilizar recursos. Você também vê este erro ao fazer tarefas de gestão e O Gestor de Recursos Azure não consegue encontrar o recurso necessário. Por exemplo, se tentar adicionar tags a um recurso que não existe, recebe este erro.

## <a name="symptom"></a>Sintoma

Existem dois códigos de erro que indicam que o recurso não pode ser encontrado. O erro **notFound** devolve um resultado semelhante a:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

O erro **ResourceNotFound** devolve um resultado semelhante ao seguinte:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Causa

O Gestor de Recursos precisa de recuperar as propriedades para um recurso, mas não consegue encontrar o recurso nas suas subscrições.

## <a name="solution-1---check-resource-properties"></a>Solução 1 - verifique as propriedades dos recursos

Quando receber este erro durante uma tarefa de gestão, verifique os valores que fornece para o recurso. Os três valores a verificar são:

* Nome do recurso
* Nome do grupo de recursos
* Subscrição

Se estiver a utilizar o PowerShell ou o Azure CLI, verifique se está a executar o comando na subscrição que contém o recurso. Pode alterar a subscrição com [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) ou [az set](/cli/azure/account#az-account-set). Muitos comandos também fornecem um parâmetro de subscrição que permite especificar uma subscrição diferente do contexto atual.

Se tiver problemas em verificar as propriedades, inscreva-se no [portal](https://portal.azure.com). Encontre o recurso que está a tentar utilizar e examine o nome do recurso, o grupo de recursos e a subscrição.

## <a name="solution-2---set-dependencies"></a>Solução 2 - definir dependências

Se tiver este erro ao implementar um modelo, poderá ter de adicionar uma dependência. O Gestor de Recursos otimiza a implementação criando recursos em paralelo, quando possível. Se um recurso tiver de ser implantado após outro recurso, tem de utilizar o elemento **dependOn** no seu modelo. Por exemplo, ao implementar uma aplicação web, o plano de Serviço de Aplicações deve existir. Se não especificou que a aplicação web depende do plano de Serviço de Aplicações, o Gestor de Recursos cria ambos os recursos ao mesmo tempo. Obtém-se um erro afirmando que o recurso do plano de Serviço de Aplicações não pode ser encontrado, porque ainda não existe quando se tenta definir uma propriedade na aplicação web. Evita este erro definindo a dependência na aplicação web.

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

Mas, queres evitar estabelecer dependências que não são necessárias. Quando se tem dependências desnecessárias, prolonga-se a duração da implantação evitando que recursos que não dependem uns dos outros sejam implantados em paralelo. Além disso, pode criar dependências circulares que bloqueiam a implantação. As funções de função de [referência](template-functions-resource.md#reference) e [lista*](template-functions-resource.md#list) criam uma dependência implícita do recurso referenciado, quando esse recurso é implantado no mesmo modelo e é referenciado pelo seu nome (não ID de recurso). Portanto, você pode ter mais dependências do que as dependências especificadas na propriedade **dependOn.** A função [resourceId](template-functions-resource.md#resourceid) não cria uma dependência implícita ou valida que o recurso existe. As funções [de função](template-functions-resource.md#reference) de referência e [lista*](template-functions-resource.md#list) não criam uma dependência implícita quando o recurso é referido pelo seu ID de recursos. Para criar uma dependência implícita, passe o nome do recurso que é implantado no mesmo modelo.

Quando vê problemas de dependência, precisa de obter informações sobre a ordem de implantação de recursos. Para visualizar a ordem das operações de implantação:

1. Selecione o histórico de implementação para o seu grupo de recursos.

   ![selecionar histórico de implementação](./media/error-not-found/select-deployment.png)

2. Selecione uma implementação a partir da história e selecione **Eventos**.

   ![selecione eventos de implementação](./media/error-not-found/select-deployment-events.png)

3. Examine a sequência de eventos para cada recurso. Preste atenção ao estado de cada operação. Por exemplo, a imagem a seguir mostra três contas de armazenamento que foram implantadas em paralelo. Note que as três contas de armazenamento são iniciadas ao mesmo tempo.

   ![implantação paralela](./media/error-not-found/deployment-events-parallel.png)

   A imagem seguinte mostra três contas de armazenamento que não são implantadas em paralelo. A segunda conta de armazenamento depende da primeira conta de armazenamento, e a terceira conta de armazenamento depende da segunda conta de armazenamento. A primeira conta de armazenamento é iniciada, aceite e concluída antes do início do próximo.

   ![implantação sequencial](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>Solução 3 - obtenha recurso externo

Ao implementar um modelo e necessitar de obter um recurso que exista num grupo de subscrição ou de recursos diferentes, utilize a [função resourceId](template-functions-resource.md#resourceid). Esta função volta a obter o nome totalmente qualificado do recurso.

Os parâmetros de grupo de subscrição e recursos na função resourceid são opcionais. Se não os fornecer, eles estão em incumprimento do grupo de subscrição e recursos atuais. Ao trabalhar com um recurso num grupo de recursos diferente ou subscrição, certifique-se de que fornece esses valores.

O exemplo seguinte obtém o ID de recurso para um recurso que existe num grupo de recursos diferente.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Solução 4 - obtenha identidade gerida a partir de recursos

Se estiver a implementar um recurso que crie implicitamente uma [identidade gerida,](../../active-directory/managed-identities-azure-resources/overview.md)deve esperar até que esse recurso seja implantado antes de recuperar valores na identidade gerida. Se passar o nome de identidade gerido para a função [de referência,](template-functions-resource.md#reference) o Gestor de Recursos tenta resolver a referência antes de o recurso e a identidade serem implantados. Em vez disso, passe o nome do recurso a que a identidade é aplicada. Esta abordagem garante que o recurso e a identidade gerida são implantados antes que o Gestor de Recursos resolva a função de referência.

Na função de referência, utilize `Full` para obter todas as propriedades, incluindo a identidade gerida.

O padrão é:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> Não use o padrão:
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> O seu modelo falhará.

Por exemplo, para obter o ID principal para uma identidade gerida que é aplicada a uma máquina virtual, use:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

Ou, para obter a identificação do inquilino para uma identidade gerida que é aplicada a um conjunto de escala de máquina virtual, use:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>Solução 5 - verificar funções

Ao implementar um modelo, procure expressões que utilizem as funções [de referência](template-functions-resource.md#reference) ou [listKeys.](template-functions-resource.md#listkeys) Os valores que fornece variam em função do facto de o recurso estar no mesmo modelo, grupo de recursos e subscrição. Verifique se está a fornecer os valores de parâmetros necessários para o seu cenário. Se o recurso estiver num grupo de recursos diferente, forneça o ID completo do recurso. Por exemplo, para fazer referência a uma conta de armazenamento noutro grupo de recursos, utilize:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-6---after-deleting-resource"></a>Solução 6 - após a eliminação de recursos

Quando elimina um recurso, pode haver um curto período de tempo quando o recurso ainda aparece no portal, mas não está realmente disponível. Se selecionar o recurso, terá um erro que diz que o recurso não foi encontrado. Refresque o portal para obter a última vista.

Se o problema continuar após uma curta espera, [contacte o suporte](https://azure.microsoft.com/support/options/).
