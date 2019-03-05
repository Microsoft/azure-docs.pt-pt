---
title: Erros de modelo inválido do Azure | Documentos da Microsoft
description: Descreve como resolver erros de modelo inválido.
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
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: ee4bce38e0fcde93ba0417617ae90dab2eefda67
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316205"
---
# <a name="resolve-errors-for-invalid-template"></a>Resolver erros de modelo inválido

Este artigo descreve como resolver erros de modelo inválido.

## <a name="symptom"></a>Sintoma

Quando implementar um modelo, receberá um erro que indica:

```
Code=InvalidTemplate
Message=<varies>
```

A mensagem de erro depende do tipo de erro.

## <a name="cause"></a>Causa

Este erro pode resultar de vários tipos diferentes de erros. Geralmente envolvem um erro de sintaxe ou estrutural no modelo.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Solução 1 - erro de sintaxe

Se receber uma mensagem de erro que indica a validação de modelo falhou, poderá ter um problema de sintaxe no seu modelo.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Este erro é fácil fazer como expressões de modelo podem ser complexas. Por exemplo, a atribuição de nome seguinte para uma conta de armazenamento tem um conjunto de parênteses Retos, três funções, três conjuntos de parênteses, um conjunto de aspas simples e uma propriedade:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Se não fornecer a sintaxe correspondente, o modelo produz um valor que é diferente de sua intenção.

Quando receber este tipo de erro, reveja com cuidado a sintaxe da expressão. Considere a utilização de um editor de JSON, como [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) ou [Visual Studio Code](resource-manager-vs-code.md), que pode avisá-lo de erros de sintaxe.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Solução 2 - comprimentos de segmento incorretos

Outro erro de modelo inválido ocorre quando o nome do recurso não está no formato correto.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Um recurso de nível de raiz tem de ter um segmento de menos o nome do que no tipo de recurso. Cada segmento é diferenciado por uma barra. No exemplo a seguir, o tipo tem dois segmentos e o nome tem um segmento, para que ele tem um **nome válido**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Mas o exemplo seguinte é **não é um nome válido** porque tem o mesmo número de segmentos que o tipo.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Para obter recursos filho, o tipo e o nome tem o mesmo número de segmentos. Este número de segmentos faz sentido, porque o nome completo e o tipo para o menor inclui o nome do principal e o tipo. Por conseguinte, o nome completo ainda tem um segmento menos do que o tipo completo.

```json
"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "name": "contosokeyvault",
        ...
        "resources": [
            {
                "type": "secrets",
                "name": "appPassword",
                ...
            }
        ]
    }
]
```

Obter os segmentos correto pode ser complicado com tipos de Gestor de recursos que são aplicados em fornecedores de recursos. Por exemplo, a aplicação de um bloqueio de recursos para um web site requer um tipo com quatro segmentos. Por conseguinte, o nome é três segmentos:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Solução de 3 - o parâmetro não é válido

Se fornecer um valor de parâmetro que não é um dos valores permitidos, receberá uma mensagem semelhante ao seguinte erro:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Double Verifique os valores permitidos no modelo e forneça um durante a implementação. Para obter mais informações sobre os valores de parâmetro permitidos, consulte [secção de parâmetros de modelos Azure Resource Manager](resource-group-authoring-templates.md#parameters).

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Solução 4 - demasiados grupos de recursos de destino

Se especificar mais do que cinco grupos de recursos de destino numa única implementação, recebe este erro. Considere a consolidação o número de grupos de recursos na sua implementação, ou a implementação de alguns dos modelos como implementações separadas. Para obter mais informações, consulte [recursos do Azure implementar a mais de uma subscrição ou grupo de recursos](resource-manager-cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Solução 5 - dependência circular detetada

Recebe este erro quando os recursos são dependentes entre si de forma que impede a implementação de iniciar. Uma combinação de interdependências faz duas ou mais recursos esperar por outros recursos que também estão a aguardar. Por exemplo, resource1 depende resource3 resource2 depende resource1 e resource3 depende resource2. Normalmente, pode resolver esse problema ao remover dependências desnecessárias.

Para resolver uma dependência circular:

1. No seu modelo, localize o recurso identificado na dependência circular. 
2. Para esse recurso, examine o **dependsOn** propriedade e quaisquer utilizações da **referência** função para ver quais os recursos que ele depende. 
3. Examine esses recursos para ver que recursos eles dependem. Siga as dependências, até que observe um recurso que depende o recurso original.
5. Para os recursos envolvidos na dependência circular, examinar cuidadosamente todas as utilizações dos **dependsOn** propriedade para identificar quaisquer dependências que não são necessários. Remova essas dependências. Se tiver a certeza de que é necessária uma dependência, tente removê-lo. 
6. Reimplemente o modelo.

Remover valores a partir da **dependsOn** propriedade pode provocar erros ao implementar o modelo. Se obtiver um erro, adicione a dependência novamente no modelo. 

Se essa abordagem não resolve a dependência circular, considere a transferência de parte de sua lógica de implementação em recursos subordinados (como extensões ou definições de configuração). Configure os recursos subordinados de implementar após os recursos envolvidos na dependência circular. Por exemplo, suponha que estiver implantando duas máquinas virtuais, mas tem de definir as propriedades em cada um que se referem a outra. Pode implementá-las na seguinte ordem:

1. vm1
2. vm2
3. Extensão na vm1 depende da vm1 e vm2. A extensão define os valores na vm1 que obtém a partir do vm2.
4. Extensão na vm2 depende da vm1 e vm2. A extensão define os valores de vm2 que ele obtém da vm1.

A mesma abordagem funciona para aplicações de serviço de aplicações. Considere mover os valores de configuração para um recurso de subordinados do recurso de aplicação. Pode implantar duas aplicações web na seguinte ordem:

1. webapp1
2. webapp2
3. configuração para webapp1 depende webapp1 e webapp2. Contém definições de aplicação com os valores do webapp2.
4. configuração para webapp2 depende webapp1 e webapp2. Contém definições de aplicação com os valores do webapp1.
