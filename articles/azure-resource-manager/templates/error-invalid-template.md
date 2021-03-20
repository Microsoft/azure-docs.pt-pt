---
title: Erros do modelo inválidos
description: Descreve como resolver erros de modelo inválidos ao implementar modelos do Gestor de Recursos Azure.
ms.topic: troubleshooting
ms.date: 05/22/2020
ms.openlocfilehash: ba19d3c4e72a765e2aaff7393915b77a80daf2ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185798"
---
# <a name="resolve-errors-for-invalid-template"></a>Resolver erros de modelo inválido

Este artigo descreve como resolver erros de modelo inválidos.

## <a name="symptom"></a>Sintoma

Ao implementar um modelo, recebe um erro que indica:

```
Code=InvalidTemplate
Message=<varies>
```

A mensagem de erro depende do tipo de erro.

## <a name="cause"></a>Causa

Este erro pode resultar de vários tipos de erros diferentes. Geralmente envolvem uma sintaxe ou erro estrutural no modelo.

<a id="syntax-error"></a>

## <a name="solution-1---syntax-error"></a>Solução 1 - erro de sintaxe

Se receber uma mensagem de erro que indique a validação falhada do modelo, poderá ter um problema de sintaxe no seu modelo.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Este erro é fácil de fazer porque as expressões do modelo podem ser complexas. Por exemplo, a seguinte atribuição de nome para uma conta de armazenamento tem um conjunto de parênteses, três funções, três conjuntos de parênteses, um conjunto de cotações únicas e uma propriedade:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Se não fornecer a sintaxe correspondente, o modelo produz um valor diferente da sua intenção.

Quando receber este tipo de erro, reveja cuidadosamente a sintaxe da expressão. Considere usar um editor JSON como [Visual Studio](create-visual-studio-deployment-project.md) ou Visual [Studio Code,](quickstart-create-templates-use-visual-studio-code.md)que pode avisá-lo sobre erros de sintaxe.

<a id="incorrect-segment-lengths"></a>

## <a name="solution-2---incorrect-segment-lengths"></a>Solução 2 - comprimentos de segmento incorretos

Outro erro de modelo inválido ocorre quando o nome do recurso não está no formato correto.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Um recurso de nível de raiz deve ter um segmento a menos no nome do que no tipo de recurso. Cada segmento é diferenciado por um corte. No exemplo seguinte, o tipo tem dois segmentos e o nome tem um segmento, por isso é um **nome válido.**

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Mas o próximo exemplo não é **um nome válido** porque tem o mesmo número de segmentos que o tipo.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Para os recursos infantis, o tipo e o nome têm o mesmo número de segmentos. Este número de segmentos faz sentido porque o nome e o tipo completos da criança incluem o nome e o tipo de pai. Portanto, o nome completo ainda tem um segmento a menos do que o tipo completo.

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

Acertar os segmentos pode ser complicado com tipos de Gestor de Recursos que são aplicados entre fornecedores de recursos. Por exemplo, a aplicação de um bloqueio de recursos num web site requer um tipo com quatro segmentos. Portanto, o nome é de três segmentos:

```json
{
  "type": "Microsoft.Web/sites/providers/locks",
  "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
  ...
}
```

<a id="parameter-not-valid"></a>

## <a name="solution-3---parameter-is-not-valid"></a>Solução 3 - parâmetro não é válido

Se fornecer um valor de parâmetro que não seja um dos valores permitidos, recebe uma mensagem semelhante ao seguinte erro:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Verifique novamente os valores permitidos no modelo e forneça um durante a implementação. Para obter mais informações sobre os [valores dos parâmetros permitidos, consulte a secção parâmetros dos modelos do Gestor de Recursos Azure](template-syntax.md#parameters).

<a id="too-many-resource-groups"></a>

## <a name="solution-4---too-many-target-resource-groups"></a>Solução 4 - Demasiados grupos de recursos-alvo

Pode ver este erro em implementações anteriores porque estava limitado a cinco grupos de recursos-alvo numa única implantação. Em maio de 2020, esse limite foi aumentado para 800 grupos de recursos. Para obter mais informações, consulte [os recursos do Deploy Azure para mais de uma subscrição ou grupo de recursos.](./deploy-to-resource-group.md)

<a id="circular-dependency"></a>

## <a name="solution-5---circular-dependency-detected"></a>Solução 5 - dependência circular detetada

Recebe este erro quando os recursos dependem uns dos outros de uma forma que impede o início da implantação. Uma combinação de interdependências faz com que dois ou mais recursos esperem por outros recursos que também estão à espera. Por exemplo, o recurso1 depende dos recursos3, o recurso 2 depende do recurso1, e os recursos3 dependem dos recursos2. Normalmente, pode resolver este problema removendo dependências desnecessárias.

Para resolver uma dependência circular:

1. No seu modelo, encontre o recurso identificado na dependência circular.
2. Para esse recurso, examine a propriedade **dependOn** e quaisquer utilizações da função de **referência** para ver de que recursos depende.
3. Examine esses recursos para ver de que recursos dependem. Siga as dependências até notar um recurso que depende do recurso original.
5. Para os recursos envolvidos na dependência circular, examine cuidadosamente todos os usos da propriedade **dependon** para identificar quaisquer dependências que não são necessárias. Remova as dependências. Se não tem a certeza de que é necessária uma dependência, tente removê-la.
6. Reimplantar o modelo.

Remover valores da propriedade **dependOn** pode causar erros quando implementa o modelo. Se tiver um erro, adicione a dependência de volta ao modelo.

Se essa abordagem não resolver a dependência circular, considere mover parte da sua lógica de implantação para recursos infantis (tais como extensões ou configurações). Configure esses recursos infantis para implantar após os recursos envolvidos na dependência circular. Por exemplo, suponha que está a implantar duas máquinas virtuais, mas tem de definir propriedades em cada uma que se refira à outra. Pode implantá-los na seguinte ordem:

1. vm1
2. vm2
3. A extensão em vm1 depende de Vm1 e vm2. A extensão define valores em Vm1 que obtém de vm2.
4. A extensão em vm2 depende de Vm1 e vm2. A extensão define valores em Vm2 que obtém de vm1.

A mesma abordagem funciona para aplicações do Serviço de Aplicações. Considere mover os valores de configuração para um recurso infantil do recurso da aplicação. Pode implementar duas aplicações web na seguinte ordem:

1. webapp1
2. webapp2
3. Config para webapp1 depende do webapp1 e webapp2. Contém configurações de aplicativos com valores do webapp2.
4. Config para webapp2 depende do webapp1 e webapp2. Contém configurações de aplicativos com valores do webapp1.