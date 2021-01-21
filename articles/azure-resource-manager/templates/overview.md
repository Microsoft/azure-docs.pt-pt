---
title: Descrição geral de modelos
description: Descreve os benefícios usando modelos de Gestor de Recursos Azure (modelos ARM) para a implementação de recursos.
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: dd9207ca1d0397b7dce63eb826567f07b1d0b892
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621844"
---
# <a name="what-are-arm-templates"></a>O que são modelos do ARM?

Com a mudança para a nuvem, muitas equipas adotaram métodos de desenvolvimento ágeis. Estas equipas iteram rapidamente. Precisam de implementar repetidamente as suas soluções para a nuvem, e saber que as suas infraestruturas estão num estado fiável. À medida que as infraestruturas se tornaram parte do processo iterativo, a divisão entre operações e desenvolvimento desapareceu. As equipas precisam de gerir o código de infraestruturas e aplicações através de um processo unificado.

Para responder a estes desafios, pode automatizar implementações e usar a prática da infraestrutura como código. Em código, define-se a infraestrutura que precisa de ser implantada. O código de infraestrutura torna-se parte do seu projeto. Tal como o código de aplicação, armazena-se o código de infraestrutura num repositório de origem e versão-o. Qualquer um na sua equipa pode executar o código e implementar ambientes semelhantes.

Para implementar a infraestrutura como código para as suas soluções Azure, utilize modelos de Gestor de Recursos Azure (modelos ARM). O modelo é um ficheiro JavaScript Object Notation (JSON) que define a infraestrutura e configuração para o seu projeto. O modelo utiliza a sintaxe declarativa, que permite afirmar o que quer implementar sem ter de escrever a sequência de comandos de programação para a criar. No modelo, você especifica os recursos a implementar e as propriedades para esses recursos.

## <a name="why-choose-arm-templates"></a>Porquê escolher modelos ARM?

Se estiver a tentar decidir entre a utilização de modelos ARM e uma das outras infraestruturas como serviços de código, considere as seguintes vantagens de usar modelos:

* **Sintaxe declarativa**: Os modelos ARM permitem criar e implantar uma infraestrutura Azure inteira declarativamente. Por exemplo, pode implementar não só máquinas virtuais, mas também a infraestrutura de rede, sistemas de armazenamento e quaisquer outros recursos que possa necessitar.

* **Resultados repetíveis**: Implemente repetidamente a sua infraestrutura ao longo do ciclo de vida do desenvolvimento e tenha confiança de que os seus recursos são utilizados de forma consistente. Os modelos são idempotentes, o que significa que você pode implementar o mesmo modelo muitas vezes e obter os mesmos tipos de recursos no mesmo estado. Pode desenvolver um modelo que represente o estado pretendido, em vez de desenvolver muitos modelos separados para representar atualizações.

* **Orquestração**: Não é preciso preocupar-se com as complexidades das operações de encomenda. O Gestor de Recursos orquestra a implantação de recursos interdependentes para que sejam criados na ordem correta. Quando possível, o Gestor de Recursos implementa recursos em paralelo para que as suas implementações terminem mais rapidamente do que as implementações em série. Implementa o modelo através de um comando, em vez de através de múltiplos comandos imperativos.

   ![Comparação de implementação do modelo](./media/overview/template-processing.png)

* **Ficheiros modulares**: Pode partir os seus modelos em componentes menores e reutilizáveis e ligá-los no momento da implementação. Você também pode nidificar um modelo dentro de outro modelo.

* **Crie qualquer recurso Azure**: Pode utilizar imediatamente novos serviços e funcionalidades da Azure nos modelos. Assim que um fornecedor de recursos introduz novos recursos, pode implementar esses recursos através de modelos. Não é preciso esperar que as ferramentas ou módulos sejam atualizados antes de utilizar os novos serviços.

* **Extensibilidade**: Com [scripts de implementação,](deployment-script-template.md)pode adicionar scripts PowerShell ou Bash aos seus modelos. Os scripts de implantação ampliam a sua capacidade de configurar recursos durante a implantação. Um script pode ser incluído no modelo, ou armazenado numa fonte externa e referenciado no modelo. Os scripts de implementação dão-lhe a capacidade de completar a configuração do ambiente de ponta a ponta num único modelo ARM.

* **Testes**: Pode certificar-se de que o seu modelo segue as orientações recomendadas testando-as com o kit de ferramentas do modelo ARM (arm-ttk). Este kit de teste é um script PowerShell que pode descarregar a partir do [GitHub](https://github.com/Azure/arm-ttk). O kit de ferramentas facilita o desenvolvimento de conhecimentos especializados utilizando a linguagem do modelo.

* **Alterações de pré-visualização**: Pode utilizar a [operação "e se"](template-deploy-what-if.md) para obter uma pré-visualização das alterações antes de implantar o modelo. Com o "e se", vê quais os recursos que serão criados, atualizados ou eliminados, e quaisquer propriedades de recursos que serão alteradas. A operação "e se" verifica o estado atual do seu ambiente e elimina a necessidade de gerir o estado.

* **Validação incorporada**: O seu modelo é implantado apenas após a validação de passagem. O Gestor de Recursos verifica o modelo antes de iniciar a implementação para se certificar de que a implementação será bem sucedida. A sua implantação é menos provável de parar num estado semi-acabado.

* **Implementações rastreadas**: No portal Azure, pode rever o histórico de implementação e obter informações sobre a implementação do modelo. Pode ver o modelo que foi implementado, os valores dos parâmetros passados e quaisquer valores de saída. Outras infraestruturas como serviços de código não são rastreadas através do portal.

   ![Histórico de implantação](./media/overview/deployment-history.png)

* **Política como código**: [A política azul](../../governance/policy/overview.md) é uma política como quadro de código para automatizar a governação. Se estiver a utilizar as políticas do Azure, a remediação de políticas é feita em recursos não conformes quando implementados através de modelos.

* **Plantas de implementação**: Pode aproveitar as Plantas fornecidas pela Microsoft para cumprir as [normas regulamentares](../../governance/blueprints/overview.md) e de conformidade. Estas plantas incluem modelos pré-construídos para várias arquiteturas.

* **Integração CI/CD**: Pode integrar modelos nas suas ferramentas de integração contínua e implementação contínua (CI/CD), que podem automatizar os seus oleodutos de libertação para atualizações rápidas e fiáveis de aplicações e infraestruturas. Ao utilizar a tarefa do modelo Azure DevOps e Resource Manager, pode utilizar pipelines Azure para construir e implementar continuamente projetos de modelo ARM. Para saber mais, consulte [o projeto VS com oleodutos](add-template-to-azure-pipelines.md) e [Tutorial: Integração contínua dos modelos Azure Resource Manager com Pipelines Azure](./deployment-tutorial-pipeline.md).

* **Código exportável**: Pode obter um modelo para um grupo de recursos existente exportando o estado atual do grupo de recursos, ou visualizando o modelo utilizado para uma determinada implantação. Ver o [modelo exportado](export-template-portal.md) é uma forma útil de aprender sobre a sintaxe do modelo.

* **Ferramentas de autoria**: Pode autor de modelos com [Código de Estúdio Visual](quickstart-create-templates-use-visual-studio-code.md) e a extensão da ferramenta do modelo. Você tem intellisense, destaque de sintaxe, ajuda em linha, e muitas outras funções linguísticas. Além do Código do Estúdio Visual, também pode utilizar [o Visual Studio.](create-visual-studio-deployment-project.md)

## <a name="template-file"></a>Arquivo de modelo

Dentro do seu modelo, pode escrever [expressões de modelo que](template-expressions.md) prolongam as capacidades de JSON. Estas expressões utilizam as [funções fornecidas](template-functions.md) pelo Gestor de Recursos.

O modelo tem as seguintes secções:

* [Parâmetros](template-parameters.md) - Forneça valores durante a implantação que permitam que o mesmo modelo seja utilizado com diferentes ambientes.

* [Variáveis](template-variables.md) - Defina valores que são reutilizados nos seus modelos. Podem ser construídos a partir de valores de parâmetros.

* [Funções definidas pelo utilizador](template-user-defined-functions.md) - Crie funções personalizadas que simplifiquem o seu modelo.

* [Recursos](template-syntax.md#resources) - Especifique os recursos a utilizar.

* [Saídas](template-outputs.md) - Retorno dos valores dos recursos implantados.

## <a name="template-deployment-process"></a>Processo de implementação do modelo

Quando implementa um modelo, o Gestor de Recursos converte o modelo em operações de API REST. Por exemplo, quando o Resource Manager recebe um modelo com a seguinte definição de recursos:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-04-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "StorageV2",
    "properties": {}
  }
]
```

Converte a definição para a operação de API REST seguinte, a qual é enviada para o fornecedor de recursos Microsoft.Storage:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2019-04-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "StorageV2",
  "properties": {}
}
```

Note que a **apiversão** definida no modelo para o recurso é usada como a versão API para a operação REST. Pode implementar repetidamente o modelo e ter confiança de que continuará a funcionar. Ao utilizar a mesma versão API, não tem de se preocupar em quebrar alterações que possam ser introduzidas em versões posteriores.

## <a name="template-design"></a>Design de modelo

A forma como define modelos e grupos de recursos é inteiramente da sua responsabilidade, bem como pretende gerir a sua solução. Por exemplo, pode implementar a sua aplicação de três camadas através de um único modelo para um grupo de recursos.

![modelo de três camadas](./media/overview/3-tier-template.png)

No entanto, não precisa de definir toda a infraestrutura num único modelo. Muitas vezes, faz sentido dividir os requisitos de implementação num conjunto de modelos direcionados e com uma finalidade específica. Pode reutilizar facilmente estes modelos para soluções diferentes. Para implementar uma solução específica, cria-se um modelo principal que liga todos os modelos necessários. A imagem seguinte mostra como implementar uma solução de três camadas através de um modelo principal que inclui três modelos aninhados.

![modelo de camadas aninhado](./media/overview/nested-tiers-template.png)

Se imaginar as suas camadas com ciclos de vida separados, pode implementar as três camadas em grupos de recursos separados. Repare que os recursos ainda podem ser ligados a recursos noutros grupos de recursos.

![modelo de camadas](./media/overview/tier-templates.png)

Para obter mais informações sobre modelos aninhados, veja [Utilizar modelos ligados com o Azure Resource Manager](linked-templates.md).

## <a name="share-templates"></a>Modelos de partilha

Depois de criar o seu modelo, poderá pretender partilhá-lo com outros utilizadores da sua organização. [As especificações do modelo](template-specs.md) permitem-lhe armazenar um modelo como um tipo de recurso. Utiliza o controlo de acesso baseado em funções para gerir o acesso à especificação do modelo. Os utilizadores com acesso lido à especificação do modelo podem implantá-lo, mas não alterar o modelo.

Esta abordagem significa que você pode compartilhar com segurança modelos que cumprem os padrões da sua organização.

## <a name="next-steps"></a>Passos seguintes

* Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte [Tutorial: Crie e implemente o seu primeiro modelo ARM](template-tutorial-create-first-template.md).
* Para conhecer os modelos ARM através de um conjunto guiado de módulos no Microsoft Learn, consulte [implementar e gerir recursos em Azure utilizando modelos ARM](/learn/paths/deploy-manage-resource-manager-templates/).
* Para obter informações sobre as propriedades em ficheiros de modelos, consulte [compreender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
* Para saber mais sobre os modelos de exportação, consulte [Quickstart: Crie e implemente modelos ARM utilizando o portal Azure](quickstart-create-templates-use-the-portal.md).
* Para obter respostas a perguntas comuns, consulte [perguntas frequentes sobre modelos ARM](frequently-asked-questions.md).
