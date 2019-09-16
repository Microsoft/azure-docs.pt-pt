---
title: Modelos do Azure Resource Manager
description: Descreve como usar modelos de Azure Resource Manager para a implantação de recursos.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: tomfitz
ms.openlocfilehash: 6d0d162f0f6f3024f6b4b63b8df1df9fd413afc8
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965302"
---
# <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Com a mudança para a nuvem, muitas equipes adotaram métodos de desenvolvimento Agile. Essas equipes iteram rapidamente. Eles precisam implantar repetidamente suas soluções na nuvem e saber que sua infraestrutura está em um estado confiável. À medida que a infraestrutura se tornou parte do processo iterativo, a divisão entre operações e desenvolvimento desapareceu. As equipes precisam gerenciar a infraestrutura e o código do aplicativo por meio de um processo unificado.

Para atender a esses desafios, você pode automatizar implantações e usar a prática da infraestrutura como código. No código, você define a infraestrutura que precisa ser implantada. O código de infraestrutura torna-se parte do seu projeto. Assim como o código do aplicativo, você armazena o código de infraestrutura em um repositório de origem e a versão. Qualquer um em sua equipe pode executar o código e implantar ambientes semelhantes.

Para implementar a infraestrutura como código para suas soluções do Azure, use Azure Resource Manager modelos. O modelo é um arquivo JavaScript Object Notation (JSON) que define a infraestrutura e a configuração do seu projeto. O modelo usa a sintaxe declarativa, que permite que você declare o que pretende implantar sem precisar escrever a sequência de comandos de programação para criá-lo. No modelo, você especifica os recursos a serem implantados e as propriedades desses recursos.

## <a name="why-choose-resource-manager-templates"></a>Por que escolher modelos do Resource Manager?

Se você estiver tentando decidir entre usar modelos do Resource Manager e uma das outras infraestruturas como serviços de código, considere as seguintes vantagens de usar modelos:

* **Sintaxe declarativa**: Os modelos do Resource Manager permitem criar e implantar uma infraestrutura inteira do Azure de forma declarativa. Por exemplo, você pode implantar não apenas máquinas virtuais, mas também a infraestrutura de rede, os sistemas de armazenamento e quaisquer outros recursos que você possa precisar.

* **Resultados repetíveis**: Implante repetidamente sua infraestrutura em todo o ciclo de vida de desenvolvimento e tenha confiança de que seus recursos sejam implantados de maneira consistente. Os modelos são idempotentes, o que significa que você pode implantar o mesmo modelo muitas vezes e obter os mesmos tipos de recursos no mesmo estado. Você pode desenvolver um modelo que representa o estado desejado, em vez de desenvolver muitos modelos separados para representar atualizações.

* **Orquestração**: Você não precisa se preocupar com as complexidades das operações de ordenação. O Resource Manager orquestra a implantação de recursos interdependentes para que eles sejam criados na ordem correta. Quando possível, o Resource Manager implanta recursos em paralelo para que suas implantações sejam concluídas mais rapidamente do que as implantações seriais. Você implanta o modelo por meio de um comando, em vez de vários comandos imperativos.

   ![Comparação de Implantação de modelo](./media/template-deployment-overview/template-processing.png)

* **Validação interna**: Seu modelo é implantado somente após a aprovação da validação. O Gerenciador de recursos verifica o modelo antes de iniciar a implantação para garantir que a implantação terá sucesso. Sua implantação é menos provável de parar em um estado de meia-conclusão.

* **Arquivos modulares**: Você pode dividir seus modelos em componentes menores e reutilizáveis e vinculá-los no momento da implantação. Você também pode aninhar um modelo dentro de outros modelos.

* **Criar qualquer recurso do Azure**: Você pode usar imediatamente novos serviços e recursos do Azure em modelos. Assim que um provedor de recursos apresenta novos recursos, você pode implantar esses recursos por meio de modelos. Você não precisa aguardar que as ferramentas ou os módulos sejam atualizados antes de usar os novos serviços.

* **Implantações controladas**: No portal do Azure, você pode examinar o histórico de implantação e obter informações sobre a implantação do modelo. Você pode ver o modelo que foi implantado, os valores de parâmetro passados e todos os valores de saída. Outra infraestrutura como serviços de código não é rastreada por meio do Portal.

   ![Histórico de implementações](./media/template-deployment-overview/deployment-history.png)

* **Política como código**: [Azure Policy](../governance/policy/overview.md) é uma política como estrutura de código para automatizar a governança. Se você estiver usando políticas do Azure, a correção de política será feita em recursos sem conformidade quando implantada por meio de modelos.

* **Plantas de implantação**: Você pode aproveitar os [planos gráficos](../governance/blueprints/overview.md) fornecidos pela Microsoft para atender aos padrões normativos e de conformidade. Esses planos gráficos incluem modelos predefinidos para várias arquiteturas.

* **Código exportável**: Você pode obter um modelo para um grupo de recursos existente exportando o estado atual do grupo de recursos ou exibindo o modelo usado para uma implantação específica. Visualizar o [modelo exportado](export-template-portal.md) é uma forma útil de saber mais sobre a sintaxe do modelo.

* **Ferramentas de criação**: Você pode criar modelos com [Visual Studio Code](resource-manager-tools-vs-code.md) e a extensão de ferramenta de modelo. Você Obtém o IntelliSense, o realce de sintaxe, a ajuda online e muitas outras funções de linguagem.

## <a name="template-file"></a>Arquivo de modelo

Em seu modelo, você pode escrever [expressões de modelo](template-expressions.md) que estendam os recursos do JSON. Essas expressões fazem uso das [funções](resource-group-template-functions.md) fornecidas pelo Resource Manager.

O modelo tem as seguintes seções:

* [Parâmetros](template-parameters.md) – forneça valores durante a implantação que permitem que o mesmo modelo seja usado com ambientes diferentes.

* [Variáveis](template-variables.md) – defina valores que são reutilizados em seus modelos. Eles podem ser construídos com base em valores de parâmetro.

* [Funções definidas pelo usuário](template-user-defined-functions.md) – crie funções personalizadas que simplificam seu modelo.

* [Recursos](resource-group-authoring-templates.md#resources) -especifique os recursos a serem implantados.

* [Saídas](template-outputs.md) – retornam valores dos recursos implantados.

## <a name="template-deployment-process"></a>Processo de Implantação de modelo

Quando você implanta um modelo, o Resource Manager converte o modelo em operações da API REST. Por exemplo, quando o Resource Manager recebe um modelo com a seguinte definição de recursos:

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
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
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

## <a name="template-design"></a>Design de modelo

A forma como define modelos e grupos de recursos é inteiramente da sua responsabilidade, bem como pretende gerir a sua solução. Por exemplo, pode implementar a sua aplicação de três camadas através de um único modelo para um grupo de recursos.

![modelo de três camadas](./media/template-deployment-overview/3-tier-template.png)

No entanto, não precisa de definir toda a infraestrutura num único modelo. Muitas vezes, faz sentido dividir os requisitos de implementação num conjunto de modelos direcionados e com uma finalidade específica. Pode reutilizar facilmente estes modelos para soluções diferentes. Para implementar uma solução específica, cria um modelo global que liga todos os modelos necessários. A imagem seguinte mostra como implementar uma solução de três camadas através de um modelo principal que inclui três modelos aninhados.

![modelo de camadas aninhado](./media/template-deployment-overview/nested-tiers-template.png)

Se imaginar as suas camadas com ciclos de vida separados, pode implementar as três camadas em grupos de recursos separados. Repare que os recursos ainda podem ser ligados a recursos noutros grupos de recursos.

![modelo de camadas](./media/template-deployment-overview/tier-templates.png)

Para obter mais informações sobre modelos aninhados, veja [Utilizar modelos ligados com o Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre as propriedades em arquivos de modelo, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](resource-group-authoring-templates.md).
* Para definir dependências explicitamente para que um recurso seja implantado antes de outro recurso, consulte [definindo dependências em modelos de Azure Resource Manager](resource-group-define-dependencies.md).
* Você pode adicionar um recurso ao seu modelo e, opcionalmente, implantá-lo. Para obter mais informações, consulte [implantação condicional em modelos do Resource Manager](conditional-resource-deployment.md).
* Em vez de blocos repetitivos de JSON muitas vezes em seu modelo, você pode especificar mais de uma instância de uma variável, propriedade ou recurso. Para obter mais informações, consulte [recurso, propriedade ou iteração de variável em modelos de Azure Resource Manager](resource-group-create-multiple.md).
* Para saber mais sobre como exportar modelos [, consulte início rápido: Crie e implante modelos de Azure Resource Manager usando o portal do Azure](./resource-manager-quickstart-create-templates-use-the-portal.md).
