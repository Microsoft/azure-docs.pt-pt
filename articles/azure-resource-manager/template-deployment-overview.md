---
title: Modelos do Azure Resource Manager
description: Descreve como usar modelos de Azure Resource Manager para a implantação de recursos.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: tomfitz
ms.openlocfilehash: 61e9bbabee969280c07521edb05d67ba68c0c58e
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802001"
---
# <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Com a mudança para a nuvem, muitas equipes adotaram métodos de desenvolvimento Agile. Essas equipes iteram rapidamente. Eles precisam implantar repetidamente suas soluções na nuvem e saber que sua infraestrutura está em um estado confiável. À medida que a infraestrutura se tornou parte do processo iterativo, a divisão entre operações e desenvolvimento desapareceu. As equipes precisam gerenciar a infraestrutura e o código do aplicativo por meio de um processo unificado.

Para atender a esses desafios, você pode automatizar implantações e usar a prática da infraestrutura como código. No código, você define a infraestrutura que precisa ser implantada. O código de infraestrutura torna-se parte do seu projeto. Assim como o código do aplicativo, você armazena o código de infraestrutura em um repositório de origem e a versão. Qualquer um em sua equipe pode executar o código e implantar ambientes semelhantes.

Para implementar a infraestrutura como código para suas soluções do Azure, use Azure Resource Manager modelos. O modelo é um arquivo JavaScript Object Notation (JSON) que define a infraestrutura e a configuração do seu projeto. O modelo usa a sintaxe declarativa, que permite que você declare o que pretende implantar sem precisar escrever a sequência de comandos de programação para criá-lo. No modelo, você especifica os recursos a serem implantados e as propriedades desses recursos.

## <a name="benefits-of-resource-manager-templates"></a>Benefícios dos modelos do Resource Manager

Os modelos do Resource Manager oferecem os seguintes benefícios:

* Implante, gerencie e monitore todos os recursos de sua solução como um grupo, em vez de manipular esses recursos individualmente.

* Implante repetidamente sua solução em todo o ciclo de vida de desenvolvimento e tenha confiança de que seus recursos sejam implantados em um estado consistente.

* Gerencie sua infraestrutura por meio de modelos declarativos em vez de scripts.

Se você estiver tentando decidir entre usar modelos do Resource Manager ou uma das outras infraestruturas como serviços de código, considere os seguintes modelos de vantagens em relação a esses serviços:

* Os novos serviços e recursos do Azure estão imediatamente disponíveis em modelos. Assim que um provedor de recursos apresenta novos recursos, você pode implantar esses recursos por meio de modelos. Com outra infraestrutura como serviços de código, você precisa esperar que terceiros implementem interfaces para os novos recursos.

* As implantações de modelo são tratadas por meio de um único envio do modelo, em vez de vários comandos imperativos. O Resource Manager orquestra a implantação de recursos interdependentes para que eles sejam criados na ordem correta. Ele analisa o modelo e determina a ordem correta para a implantação com base nas referências entre os recursos.

   ![Comparação de Implantação de modelo](./media/template-deployment-overview/template-processing.png)

* As implantações de modelo são controladas no portal do Azure. Você pode examinar o histórico de implantação e obter informações sobre a implantação do modelo. Você pode ver o modelo que foi implantado, os valores de parâmetro passados e todos os valores de saída. Outra infraestrutura como serviços de código não é rastreada por meio do Portal.

   ![Histórico de implementações](./media/template-deployment-overview/deployment-history.png)

* As implantações de modelo passam por uma validação preliminar. O Gerenciador de recursos verifica o modelo antes de iniciar a implantação para garantir que a implantação terá sucesso. Sua implantação é menos provável de parar em um estado de meia-conclusão.

* Se você estiver usando [políticas do Azure](../governance/policy/overview.md), a correção de política será feita em recursos sem conformidade quando implantada por meio de modelos.

* A Microsoft fornece [plantas](../governance/blueprints/overview.md) de implantação para atender aos padrões normativos e de conformidade. Esses planos gráficos incluem modelos predefinidos para várias arquiteturas.

## <a name="idempotent"></a>Idempotente

Idempotente simplesmente significa que você pode executar as mesmas operações muitas vezes e obter o mesmo resultado. A implantação de um modelo do Resource Manager é idempotente. Você pode implantar o mesmo modelo muitas vezes e obter os mesmos tipos de recursos no mesmo estado. Esse conceito é importante porque significa que você obtém resultados consistentes se reimplanta um modelo em um grupo de recursos existente ou implanta um modelo de um novo grupo de recursos.

Vamos supor que você tenha implantado três recursos em um grupo de recursos e, em seguida, decida que precisa adicionar um quarto recurso. Em vez de criar um novo modelo que contenha apenas o novo recurso, você pode adicionar o quarto recurso ao modelo existente. Quando você implanta o novo modelo no grupo de recursos que já tinha três recursos, o Resource Manager descobre quais ações executar.

Se o recurso existir no grupo de recursos e a solicitação não contiver atualizações para as propriedades, nenhuma ação será executada. Se o recurso existir, mas as propriedades tiverem sido alteradas, o recurso existente será atualizado. Se o recurso não existir, o novo recurso será criado.

Você tem certeza de que, quando a implantação é concluída, os recursos estão sempre no estado esperado.

## <a name="template-file"></a>Arquivo de modelo

Em seu modelo, você pode escrever [expressões de modelo](template-expressions.md) que estendam os recursos do JSON. Essas expressões fazem uso das [funções](resource-group-template-functions.md) fornecidas pelo Resource Manager.

O modelo tem as seguintes seções:

* [Parâmetros](template-parameters.md) – forneça valores durante a implantação que permitem que o mesmo modelo seja usado com ambientes diferentes.

* [Variáveis](template-variables.md) – defina valores que são reutilizados em seus modelos. Eles podem ser construídos com base em valores de parâmetro.

* [Funções definidas pelo usuário](template-user-defined-functions.md) – crie funções personalizadas que simplificam seu modelo.

* [Recursos](resource-group-authoring-templates.md#resources) -especifique os recursos a serem implantados.

* [Saídas](template-outputs.md) – retornam valores dos recursos implantados.

## <a name="template-features"></a>Recursos de modelo

O Gerenciador de recursos analisa as dependências para garantir que os recursos sejam criados na ordem correta. A maioria das dependências é determinada implicitamente. No entanto, você pode definir explicitamente uma dependência para garantir que um recurso seja implantado antes de outro recurso. Para obter mais informações, consulte [Definir dependências nos modelos do Azure Resource Manager](resource-group-define-dependencies.md).

Você pode adicionar um recurso ao seu modelo e, opcionalmente, implantá-lo. Normalmente, você passa um valor de parâmetro que indica se o recurso precisa ser implantado. Para obter mais informações, consulte [implantação condicional em modelos do Resource Manager](conditional-resource-deployment.md).

Em vez de blocos repetitivos de JSON muitas vezes em seu modelo, você pode usar um elemento de cópia para especificar mais de uma instância de uma variável, propriedade ou recurso. Para obter mais informações, consulte [recurso, propriedade ou iteração de variável em modelos de Azure Resource Manager](resource-group-create-multiple.md).

## <a name="export-templates"></a>Exportar modelos

Você pode obter um modelo para um grupo de recursos existente exportando o estado atual do grupo de recursos ou exibindo o modelo usado para uma implantação específica. Visualizar o [modelo exportado](export-template-portal.md) é uma forma útil de saber mais sobre a sintaxe do modelo.

Quando cria uma solução a partir do portal, esta inclui automaticamente um modelo de implementação. Não precisa de criar o modelo a partir do zero porque pode começar com o modelo para a sua solução e personalizá-lo de modo a satisfazer as suas necessidades específicas. Para obter um exemplo, [consulte início rápido: Crie e implante modelos de Azure Resource Manager usando o portal do Azure](./resource-manager-quickstart-create-templates-use-the-portal.md).

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
* Para começar a desenvolver o modelo, consulte [usar Visual Studio Code para criar modelos de Azure Resource Manager](resource-manager-tools-vs-code.md).
* Para obter uma introdução ao serviço do Gerenciador de recursos, incluindo seus recursos de gerenciamento, consulte [Azure Resource Manager visão geral](resource-group-overview.md).

