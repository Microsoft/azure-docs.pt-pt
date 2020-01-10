---
title: Erros de modelo inválidos
description: Descreve como resolver erros de modelo inválido ao implantar modelos de Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.openlocfilehash: 9337812152dac7948afc7471760f3dc14443f549
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484574"
---
# <a name="resolve-errors-for-invalid-template"></a>Resolver erros de modelo inválido

Este artigo descreve como resolver erros de modelo inválidos.

## <a name="symptom"></a>Sintoma

Ao implantar um modelo, você receberá um erro indicando:

```
Code=InvalidTemplate
Message=<varies>
```

A mensagem de erro depende do tipo de erro.

## <a name="cause"></a>Causa

Esse erro pode resultar de vários tipos diferentes de erros. Em geral, eles envolvem uma sintaxe ou um erro estrutural no modelo.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Solução 1-erro de sintaxe

Se você receber uma mensagem de erro indicando que o modelo falhou na validação, você poderá ter um problema de sintaxe em seu modelo.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Esse erro é fácil de fazer porque as expressões de modelo podem ser complexas. Por exemplo, a atribuição de nome a seguir para uma conta de armazenamento tem um conjunto de colchetes, três funções, três conjuntos de parênteses, um conjunto de aspas simples e uma propriedade:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Se você não fornecer a sintaxe correspondente, o modelo produzirá um valor diferente de sua intenção.

Quando você receber esse tipo de erro, examine atentamente a sintaxe da expressão. Considere o uso de um editor JSON como o [Visual Studio](create-visual-studio-deployment-project.md) ou o [Visual Studio Code](use-vs-code-to-create-template.md), que pode avisá-lo sobre erros de sintaxe.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Solução 2-comprimentos de segmento incorretos

Outro erro de modelo inválido ocorre quando o nome do recurso não está no formato correto.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Um recurso de nível raiz deve ter um segmento less no nome do que no tipo de recurso. Cada segmento é diferenciado por uma barra. No exemplo a seguir, o tipo tem dois segmentos e o nome tem um segmento, portanto, é um **nome válido**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Mas o exemplo a seguir **não é um nome válido** porque tem o mesmo número de segmentos que o tipo.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Para recursos filho, o tipo e o nome têm o mesmo número de segmentos. Esse número de segmentos faz sentido porque o nome completo e o tipo do filho incluem o nome e o tipo do pai. Portanto, o nome completo ainda tem um segmento menor do que o tipo completo.

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

Obter o direito dos segmentos pode ser complicado com tipos do Resource Manager que são aplicados em provedores de recursos. Por exemplo, a aplicação de um bloqueio de recurso a um site requer um tipo com quatro segmentos. Portanto, o nome tem três segmentos:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Solução 3-o parâmetro não é válido

Se você fornecer um valor de parâmetro que não seja um dos valores permitidos, você receberá uma mensagem semelhante ao seguinte erro:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Verifique as duas vezes os valores permitidos no modelo e forneça um durante a implantação. Para obter mais informações sobre os valores de parâmetro permitidos, consulte a [seção de parâmetros de modelos de Azure Resource Manager](template-syntax.md#parameters).

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Solução 4-muitos grupos de recursos de destino

Se você especificar mais de cinco grupos de recursos de destino em uma única implantação, receberá esse erro. Considere a consolidação do número de grupos de recursos em sua implantação ou a implantação de alguns dos modelos como implantações separadas. Para obter mais informações, consulte [implantar recursos do Azure em mais de uma assinatura ou grupo de recursos](cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Solução 5-dependência circular detectada

Você recebe esse erro quando os recursos dependem uns dos outros de uma maneira que impede a inicialização da implantação. Uma combinação de interdependências faz com que duas ou mais esperas de recursos para outros recursos que também estejam aguardando. Por exemplo, recurso1 depende de resource3, resource2 depende de recurso1 e resource3 depende de resource2. Normalmente, você pode resolver esse problema removendo dependências desnecessárias.

Para resolver uma dependência circular:

1. Em seu modelo, localize o recurso identificado na dependência circular. 
2. Para esse recurso, examine a propriedade **dependy** e quaisquer usos da função de **referência** para ver os recursos dos quais ela depende. 
3. Examine esses recursos para ver quais recursos eles dependem. Siga as dependências até observar um recurso que depende do recurso original.
5. Para os recursos envolvidos na dependência circular, Examine cuidadosamente todos os usos da propriedade **depending** para identificar as dependências que não são necessárias. Remova essas dependências. Se você não tiver certeza de que uma dependência é necessária, tente removê-la. 
6. Reimplante o modelo.

A remoção de valores da propriedade **dependy** pode causar erros quando você implanta o modelo. Se você receber um erro, adicione a dependência de volta ao modelo. 

Se essa abordagem não resolver a dependência circular, considere mover parte da lógica de implantação para recursos filho (como extensões ou definições de configuração). Configure os recursos filho a serem implantados após os recursos envolvidos na dependência circular. Por exemplo, suponha que você esteja implantando duas máquinas virtuais, mas deve definir propriedades em cada uma delas referente à outra. Você pode implantá-los na seguinte ordem:

1. vm1
2. vm2
3. A extensão em VM1 depende de VM1 e VM2. A extensão define valores em VM1 que obtém de VM2.
4. A extensão em VM2 depende de VM1 e VM2. A extensão define valores em VM2 que obtém de VM1.

A mesma abordagem funciona para aplicativos do serviço de aplicativo. Considere mover os valores de configuração para um recurso filho do recurso de aplicativo. Você pode implantar dois aplicativos Web na seguinte ordem:

1. webapp1
2. webapp2
3. a configuração para webapp1 depende de webapp1 e webapp2. Ele contém configurações de aplicativo com valores de webapp2.
4. a configuração para webapp2 depende de webapp1 e webapp2. Ele contém configurações de aplicativo com valores de webapp1.
