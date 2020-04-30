---
title: Descrição geral de modelos
description: Descreve os benefícios usando modelos do Gestor de Recursos Azure para a implantação de recursos.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: b3b5fb383ac89d0968a437f35aab656afa1913f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086339"
---
# <a name="what-are-arm-templates"></a>O que são modelos do ARM?

Com a mudança para a nuvem, muitas equipas adotaram métodos de desenvolvimento ágeis. Estas equipas iteram rapidamente. Precisam de implantar repetidamente as suas soluções na nuvem, e sabem que as suas infraestruturas estão num estado fiável. À medida que as infraestruturas se tornaram parte do processo iterativo, a divisão entre operações e desenvolvimento desapareceu. As equipas precisam de gerir a infraestrutura e o código de aplicação através de um processo unificado.

Para responder a estes desafios, pode automatizar implementações e usar a prática da infraestrutura como código. Em código, define-se a infraestrutura que precisa de ser implantada. O código de infraestrutura torna-se parte do seu projeto. Tal como o código de aplicação, armazena-se o código de infraestrutura num repositório de origem e versão-o. Qualquer um da sua equipa pode executar o código e implementar ambientes semelhantes.

Para implementar a infraestrutura como código para as suas soluções Azure, utilize os modelos Azure Resource Manager (ARM). O modelo é um ficheiro JavaScript Object Notation (JSON) que define a infraestrutura e a configuração para o seu projeto. O modelo usa sintaxe declarativa, que permite indicar o que pretende implementar sem ter de escrever a sequência de comandos de programação para criá-la. No modelo, especifica os recursos para implantar e as propriedades para esses recursos.

## <a name="why-choose-arm-templates"></a>Por que escolher os modelos ARM?

Se estiver a tentar decidir entre usar modelos ARM e uma das outras infraestruturas como serviços de código, considere as seguintes vantagens de usar modelos:

* **Sintaxe declarativa:** Os modelos ARM permitem criar e implantar toda uma infraestrutura Azure declarativamente. Por exemplo, pode implantar não só máquinas virtuais, mas também a infraestrutura de rede, sistemas de armazenamento e quaisquer outros recursos que possa necessitar.

* **Resultados repetíveis**: Implemente repetidamente a sua infraestrutura durante todo o ciclo de vida de desenvolvimento e tenha confiança de que os seus recursos são implantados de forma consistente. Os modelos são idempotentes, o que significa que você pode implementar o mesmo modelo muitas vezes e obter os mesmos tipos de recursos no mesmo estado. Você pode desenvolver um modelo que representa o estado desejado, em vez de desenvolver muitos modelos separados para representar atualizações.

* **Orquestração**: Não tem que se preocupar com as complexidades das operações de encomenda. O Gestor de Recursos orquestra a implantação de recursos interdependentes para que sejam criados na ordem correta. Quando possível, o Gestor de Recursos implanta recursos em paralelo para que as suas implementações terminem mais rapidamente do que as implementações em série. Você implanta o modelo através de um comando, em vez de através de múltiplos comandos imperativos.

   ![Comparação de implementação de modelos](./media/overview/template-processing.png)

* **Ficheiros modulares:** Pode quebrar os seus modelos em componentes mais pequenos e reutilizáveis e ligá-los no momento da implantação. Você também pode nidificar um modelo dentro de outro modelo.

* **Crie qualquer recurso Azure:** Pode utilizar imediatamente novos serviços e funcionalidades azure em modelos. Assim que um fornecedor de recursos introduzir novos recursos, pode implementar esses recursos através de modelos. Não é preciso esperar que as ferramentas ou módulos sejam atualizados antes de utilizar os novos serviços.

* **Extensibility**: Com scripts de [implementação,](deployment-script-template.md)pode adicionar scripts PowerShell ou Bash aos seus modelos. Os scripts de implantação alargam a sua capacidade de criar recursos durante a implantação. Um script pode ser incluído no modelo, ou armazenado numa fonte externa e referenciado no modelo. Os scripts de implementação dão-lhe a capacidade de completar a configuração do ambiente de ponta a ponta num único modelo ARM.

* **Teste**: Pode certificar-se de que o seu modelo segue as diretrizes recomendadas testando-a com o kit de ferramentas do modelo ARM (arm-ttk). Este kit de teste é um script PowerShell que pode supor a partir do [GitHub.](https://github.com/Azure/arm-ttk) O kit de ferramentas facilita o desenvolvimento de conhecimentos especializados utilizando a linguagem do modelo.

* **Alterações de pré-visualização**: Pode utilizar a [operação "what-if"](template-deploy-what-if.md) para obter uma pré-visualização das alterações antes de implementar o modelo. Com o e-se, você vê quais os recursos que serão criados, atualizados ou eliminados, e quaisquer propriedades de recursos que irão mudar. A operação e se a operação verifica o estado atual do seu ambiente e elimina a necessidade de gerir o estado.

* **Validação incorporada**: O seu modelo só é implantado após a passagem da validação. O Gestor de Recursos verifica o modelo antes de iniciar a implementação para se certificar de que a implementação terá sucesso. O seu destacamento é menos provável que pare num estado semi-acabado.

* **Implementações rastreadas**: No portal Azure, pode rever o histórico de implementação e obter informações sobre a implementação do modelo. Pode ver o modelo que foi implantado, os valores do parâmetro passados e quaisquer valores de saída. Outras infraestruturas como serviços de código não são rastreadas através do portal.

   ![Histórico de implantação](./media/overview/deployment-history.png)

* **Política como código**: [A Política Azure](../../governance/policy/overview.md) é uma política como quadro de código para automatizar a governação. Se estiver a usar as políticas do Azure, a reparação de políticas é feita em recursos não conformes quando implementada através de modelos.

* **Planos de implementação:** Pode aproveitar as plantas fornecidas pela Microsoft para cumprir as [normas](../../governance/blueprints/overview.md) regulamentares e de conformidade. Estas plantas incluem modelos pré-construídos para várias arquiteturas.

* **Integração CI/CD:** Pode integrar modelos nas suas ferramentas de integração contínua e implantação contínua (CI/CD), que podem automatizar os seus gasodutos de libertação para atualizações rápidas e fiáveis de aplicações e infraestruturas. Ao utilizar a tarefa de modelo Azure DevOps e Resource Manager, pode utilizar os Pipelines Azure para construir e implementar continuamente projetos de modelo sarm. Para saber mais, consulte o [projeto VS com oleodutos](add-template-to-azure-pipelines.md) e tutorial: Integração contínua de modelos de Gestor de [Recursos Azure com Pipelines Azure](./deployment-tutorial-pipeline.md).

* **Código exportável**: Pode obter um modelo para um grupo de recursos existente, exportando o estado atual do grupo de recursos, ou visualizando o modelo utilizado para uma determinada implantação. Ver o [modelo exportado](export-template-portal.md) é uma forma útil de aprender sobre a sintaxe do modelo.

* **Ferramentas de autoria**: Pode autor modelos com [Código de Estúdio Visual](use-vs-code-to-create-template.md) e a extensão da ferramenta de modelo. Obtém-se intellisense, sintaxe realçando, ajuda em linha e muitas outras funções linguísticas. Além do código Visual Studio, também pode utilizar o [Visual Studio.](create-visual-studio-deployment-project.md)

## <a name="template-file"></a>Ficheiro de modelo

Dentro do seu modelo, pode escrever [expressões](template-expressions.md) de modelo que alargam as capacidades da JSON. Estas expressões utilizam as [funções](template-functions.md) fornecidas pelo Gestor de Recursos.

O modelo tem as seguintes secções:

* [Parâmetros](template-parameters.md) - Forneça valores durante a implantação que permitam que o mesmo modelo seja utilizado com diferentes ambientes.

* [Variáveis](template-variables.md) - Defina valores que são reutilizados nos seus modelos. Podem ser construídos a partir de valores de parâmetros.

* [Funções definidas pelo utilizador](template-user-defined-functions.md) - Crie funções personalizadas que simplificam o seu modelo.

* [Recursos](template-syntax.md#resources) - Especificar os recursos a implantar.

* [Saídas](template-outputs.md) - Valores de devolução dos recursos implantados.

## <a name="template-deployment-process"></a>Processo de implementação do modelo

Quando implementa um modelo, o Gestor de Recursos converte o modelo em operações de API REST. Por exemplo, quando o Resource Manager recebe um modelo com a seguinte definição de recursos:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Converte a definição para a operação de API REST seguinte, a qual é enviada para o fornecedor de recursos Microsoft.Storage:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage",
  "properties": {}
}
```

## <a name="template-design"></a>Design de modelo

A forma como define modelos e grupos de recursos é inteiramente da sua responsabilidade, bem como pretende gerir a sua solução. Por exemplo, pode implementar a sua aplicação de três camadas através de um único modelo para um grupo de recursos.

![modelo de três camadas](./media/overview/3-tier-template.png)

No entanto, não precisa de definir toda a infraestrutura num único modelo. Muitas vezes, faz sentido dividir os requisitos de implementação num conjunto de modelos direcionados e com uma finalidade específica. Pode reutilizar facilmente estes modelos para soluções diferentes. Para implementar uma solução específica, cria um modelo global que liga todos os modelos necessários. A imagem seguinte mostra como implementar uma solução de três camadas através de um modelo principal que inclui três modelos aninhados.

![modelo de camadas aninhado](./media/overview/nested-tiers-template.png)

Se imaginar as suas camadas com ciclos de vida separados, pode implementar as três camadas em grupos de recursos separados. Repare que os recursos ainda podem ser ligados a recursos noutros grupos de recursos.

![modelo de camadas](./media/overview/tier-templates.png)

Para obter mais informações sobre modelos aninhados, veja [Utilizar modelos ligados com o Azure Resource Manager](linked-templates.md).

## <a name="next-steps"></a>Passos seguintes

* Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte [Tutorial: Crie e implante o seu primeiro modelo ARM](template-tutorial-create-first-template.md).
* Para obter informações sobre as propriedades em ficheiros de modelos, consulte [Compreender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
* Para aprender sobre modelos de exportação, consulte [Quickstart: Crie e implante modelos ARM utilizando o portal Azure](quickstart-create-templates-use-the-portal.md).
