---
title: Não foram encontrados erros de recursos do Azure | Documentos da Microsoft
description: Descreve como resolver erros quando não é possível encontrar um recurso.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/06/2018
ms.author: tomfitz
ms.openlocfilehash: 0dfbfa73009b5fa47dd7bb0804b612d0c2ad0a5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61062302"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Resolva os erros não encontrados para recursos do Azure

Este artigo descreve os erros que poderá ver quando um recurso não for encontrado durante a implementação.

## <a name="symptom"></a>Sintoma

Quando o seu modelo inclui o nome de um recurso que não pode ser resolvido, receberá um erro semelhante a:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Se utilizar o [referência](resource-group-template-functions-resource.md#reference) ou [listKeys](resource-group-template-functions-resource.md#listkeys) funções com um recurso que não podem ser resolvidos, receberá o erro seguinte:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Causa

Gestor de recursos tem de recuperar as propriedades de um recurso, mas não é possível identificar o recurso na sua subscrição.

## <a name="solution-1---set-dependencies"></a>Solução 1 - dependências de conjunto

Se estiver a tentar implementar o recurso em falta no modelo, verifique se tem de adicionar uma dependência. Gestor de recursos otimiza a implementação através da criação de recursos em paralelo, sempre que possível. Se um recurso tem de ser implementado depois de outro recurso, tem de utilizar o **dependsOn** elemento no seu modelo. Por exemplo, quando implementar uma aplicação web, o plano do serviço de aplicações tem de existir. Se ainda não especificou que a aplicação web que depende o plano do serviço de aplicações, o Resource Manager cria os dois recursos ao mesmo tempo. Receberá um erro a indicar que o serviço de aplicações não é possível localizar o recurso do plano, porque ele ainda não existe durante a tentativa de definir uma propriedade na aplicação web. Impedir que este erro ao definir a dependência na aplicação web.

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

No entanto, deseja evitar a definição de dependências que não são necessários. Quando tiver dependências desnecessárias, prolongar a duração da implementação ao impedir que os recursos que não são dependentes entre si de serem implementadas em paralelo. Além disso, pode criar dependências circulares que bloquear a implementação. O [referência](resource-group-template-functions-resource.md#reference) função e [lista *](resource-group-template-functions-resource.md#list) funções cria uma dependência implícita no recurso referenciado, quando esse recurso está implementado no mesmo modelo e é referenciado pelo respetivo nome (não ID de recurso ). Portanto, pode ter dependências mais que as dependências especificadas na **dependsOn** propriedade. O [resourceId](resource-group-template-functions-resource.md#resourceid) função não criar uma dependência implícita ou validar que o recurso existe. O [referência](resource-group-template-functions-resource.md#reference) função e [lista *](resource-group-template-functions-resource.md#list) funções não criar uma dependência implícita, quando o recurso é referido pelo seu ID de recurso. Para criar uma dependência implícita, passe o nome do recurso que é implementado no mesmo modelo.

Quando vir os problemas de dependência, precisa obter informações sobre a ordem de implementação de recursos. Para ver a ordem das operações de implementação:

1. Selecione o histórico de implementação para o grupo de recursos.

   ![Selecione o histórico de implementação](./media/resource-manager-not-found-errors/select-deployment.png)

2. Selecione uma implementação do histórico e selecione **eventos**.

   ![Selecionar eventos de implementação](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. Examine a seqüência de eventos para cada recurso. Preste atenção para o estado de cada operação. Por exemplo, a imagem seguinte mostra três contas de armazenamento implementada em paralelo. Tenha em atenção que as três contas de armazenamento são iniciadas ao mesmo tempo.

   ![implementação paralela](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   A imagem seguinte mostra três contas de armazenamento que não são implementadas em paralelo. A segunda conta de armazenamento depende da primeira conta de armazenamento e a terceira conta de armazenamento depende a segunda conta de armazenamento. A primeira conta de armazenamento é iniciada, aceite e concluída antes da próxima é iniciada.

   ![implementação seqüencial](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Solução 2 – obter recursos do grupo de recursos diferente

Quando o recurso existe no grupo de recursos diferente daquele que está a ser implementada, utilize o [resourceId função](resource-group-template-functions-resource.md#resourceid) para obter o nome completamente qualificado do recurso.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Solução de 3 - função de referência de verificação

Procurar por uma expressão que inclui a [referência](resource-group-template-functions-resource.md#reference) função. Os valores que fornecer variam consoante se o recurso está no mesmo modelo, grupo de recursos e subscrição. Volte a verificar que está a fornecer os valores dos parâmetros necessários para o seu cenário. Se o recurso está no grupo de recursos diferente, forneça o ID de recurso completo. Por exemplo, para fazer referência a uma conta de armazenamento no outro grupo de recursos, utilize:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```