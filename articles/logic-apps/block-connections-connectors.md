---
title: Ligações de blocos para conectores API específicos
description: Restringir a criação e utilização de conexões API em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: bd6afa8b3776ed48d4b25a36b2902265fa0ab5c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269765"
---
# <a name="block-connections-created-by-connectors-in-azure-logic-apps"></a>Ligações de blocos criadas por conectores em Azure Logic Apps

Se a sua organização não permitir a ligação a recursos restritos ou não aprovados utilizando os seus conectores em Azure Logic Apps, pode bloquear a capacidade de criar e utilizar essas ligações em fluxos de trabalho de aplicações lógicas. Com [a Política Azure,](../governance/policy/overview.md)pode definir e impor [políticas](../governance/policy/overview.md#policy-definition) que impeçam a criação ou utilização de ligações para conectores que pretende bloquear. Por exemplo, por razões de segurança, é melhor bloquear ligações a plataformas de redes sociais específicas ou a outros serviços e sistemas.

Este tópico mostra como configurar uma política que bloqueia ligações específicas utilizando o portal Azure, mas pode criar definições de política de outras formas, por exemplo, através dos modelos Azure REST API, Azure PowerShell, Azure CLI e Azure Resource Manager. Para obter mais informações, consulte [Tutorial: Criar e gerir políticas para impor o cumprimento.](../governance/policy/tutorials/create-and-manage.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [crie uma conta Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

* A identificação de referência para o conector que pretende bloquear. Para obter mais informações, consulte [Encontre o ID de referência do conector](#connector-reference-ID).

<a name="connector-reference-ID"></a>

## <a name="find-connector-reference-id"></a>Encontre iD de referência do conector

Se já tem uma aplicação lógica com a ligação que pretende bloquear, siga os [passos para o portal Azure](#connector-ID-portal). Caso contrário, siga estes passos:

1. Visite a [lista de conectores De Aplicações Lógicas.](/connectors/connector-reference/connector-reference-logicapps-connectors)

1. Encontre a página de referência para o conector que pretende bloquear.

   Por exemplo, se quiser bloquear o conector do Instagram, que está depreciado, vá a esta página:

   `https://docs.microsoft.com/connectors/instagram/`

1. A partir do URL da página, copie e guarde o ID de referência do conector no final sem o corte dianteiro `/` (, por exemplo, `instagram` .

   Mais tarde, quando criar a sua definição de política, utiliza este ID na declaração de condição da definição, por exemplo:

   `"like": "*managedApis/instagram"`

<a name="connector-ID-portal"></a>

### <a name="azure-portal"></a>Portal do Azure

1. No [portal Azure,](https://portal.azure.com)encontre e abra a sua aplicação lógica.

1. No menu de aplicativos logic, selecione **Logic app code view** para que possa ver a definição JSON da sua aplicação lógica.

   ![Abra "Logic app code view" para encontrar iD do conector](./media/block-connections-connectors/code-view-connector-id.png)

1. Encontre o `parameters` objeto que contém o `$connections` objeto, que inclui um objeto para `{connection-name}` cada ligação na sua aplicação lógica e especifica informações sobre essa ligação:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "{connection-name}": {
                  "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Web/connections/{connection-name}",
                  "connectionName": "{connection-name}",
                  "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"
               }
            }
         }
      }
   }
   ```

   Por exemplo, para o conector do Instagram, encontre o `instagram` objeto, que identifica uma ligação do Instagram:

   ```json
   {
      "parameters": {
         "$connections": {
            "value" : {
               "instagram": {
                  "connectionId": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Web/connections/instagram",
                  "connectionName": "instagram",
                  "id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"
               }
            }
         }
      }
   }
   ```

1. Para a ligação que pretende bloquear, encontre o `id` imóvel e o valor, que segue este formato: 

   `"id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{connection-name}"`

   Por exemplo, aqui está a `id` propriedade e o valor para uma ligação instagram:

   `"id": "/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Web/locations/westus/managedApis/instagram"`

1. A partir do `id` valor da propriedade, copie e guarde o ID de referência do conector no final, por exemplo, `instagram` .

   Mais tarde, quando criar a sua definição de política, utiliza este ID na declaração de condição da definição, por exemplo:

   `"like": "*managedApis/instagram"`

<a name="create-policy-connections"></a>

## <a name="create-policy-to-block-creating-connections"></a>Criar política para bloquear a criação de ligações

Para bloquear a criação de uma ligação completamente numa aplicação lógica, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Na caixa de pesquisa do portal, insira `policy` e selecione **Política**.

   ![No portal Azure, encontre e selecione "política"](./media/block-connections-connectors/find-select-azure-policy.png)

1. No menu **Política,** em **Autoria,** selecione **Definições**  >  **+ Definição de Política**.

   ![Selecione "Definições" > "+ Definição de Política"](./media/block-connections-connectors/add-new-policy-definition.png)

1. Na **definição de Política,** forneça as informações para a sua definição de política, com base nas propriedades descritas sob o exemplo:

   ![Screenshot que mostra as propriedades de "Definição de Política".](./media/block-connections-connectors/policy-definition-create-connections-1.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Localização de definição** | Sim | <*Nome de subscrição Azure*> | A assinatura Azure para usar para a definição de política <p><p>1. Para encontrar a sua subscrição, selecione o botão elipses **(...**) . <br>2. A partir da lista **de assinaturas,** encontre e selecione a sua subscrição. <br>3. Quando terminar, **selecione Select**. |
   | **Nome** | Sim | <*nome de definição de política*> | O nome a usar para a definição de política |
   | **Descrição** | Não | <*nome de definição de política*> | Uma descrição para a definição de política |
   | **Categoria** | Sim | **Aplicativos lógicos** | O nome de uma categoria ou nova categoria existente para a definição de política |
   | **Imposição de políticas** | Sim | **Ativado** | Esta definição especifica se ativa ou desativa a definição de política quando salva o seu trabalho. |
   ||||

1. De acordo com **a REGRA POLÍTICA,** a caixa de edição JSON é pré-povoada com um modelo de definição de política. Substitua este modelo pela [definição de política](../governance/policy/concepts/definition-structure.md) com base nas propriedades descritas na tabela abaixo e seguindo esta sintaxe:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | `mode` | `All` | O modo que determina os tipos de recursos que a apólice avalia. <p><p>Este cenário `mode` `All` define-se a , que aplica a política aos grupos de recursos Azure, subscrições e todos os tipos de recursos. <p><p>Para obter mais informações, consulte [a estrutura de definição de política - modo](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | A condição que determina quando aplicar a regra da política <p><p>Neste cenário, `{condition-to-evaluate}` determina-se se o `api.id` valor em `Microsoft.Web/connections/api.id` `*managedApis/{connector-name}` fósforos, que especifica um valor wildcard (*). <p><p>Para obter mais informações, consulte [a estrutura de definição de política - Regra de política.](../governance/policy/concepts/definition-structure.md#policy-rule) |
   | `field` | `Microsoft.Web/connections/api.id` | O `field` valor a comparar com a condição <p><p>Neste cenário, `field` usa o [*pseudónimo,*](../governance/policy/concepts/definition-structure.md#aliases) `Microsoft.Web/connections/api.id` para aceder ao valor na propriedade do conector, `api.id` . |
   | `like` | `*managedApis/{connector-name}` | O operador lógico e o valor a utilizar para comparar o `field` valor <p><p>Neste cenário, o `like` operador e o personagem wildcard (*) certificam-se de que a regra funciona independentemente da região, e a `*managedApis/{connector-name}` cadeia, é o valor a corresponder onde `{connector-name}` está o ID para o conector que pretende bloquear. <p><p>Por exemplo, suponha que pretende bloquear a criação de ligações a plataformas de redes sociais ou bases de dados: <p><p>- Twitter: `twitter` <br>- Instagram: `instagram` <br>- Facebook: `facebook` <br>- Pinterest: `pinterest` <br>- SQL Server ou Azure SQL: `sql` <p><p>Para encontrar estes IDs do conector, consulte [o ID de referência do conector](#connector-reference-ID) mais cedo neste tópico. |
   | `then` | `{effect-to-apply}` | O efeito a aplicar quando a `if` condição é satisfeita <p><p>Neste cenário, `{effect-to-apply}` é bloquear e falhar um pedido ou operação que não cumpra a política. <p><p>Para obter mais informações, consulte [a estrutura de definição de política - Regra de política.](../governance/policy/concepts/definition-structure.md#policy-rule) |
   | `effect` | `deny` | É `effect` bloquear o pedido, que é criar a ligação especificada <p><p>Para obter mais informações, consulte [os efeitos da Política de Azure - Negar](../governance/policy/concepts/effects.md#deny). |
   ||||

   Por exemplo, suponha que pretende bloquear a criação de ligações com o conector do Instagram. Aqui está a definição de política que pode usar:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "field": "Microsoft.Web/connections/api.id",
            "like": "*managedApis/instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
   }
   ```

   Aqui está a forma como aparece a caixa **POLICY RULE:**

   ![Screenshot que mostra a caixa "POLICY RULE" com um exemplo de regra de política.](./media/block-connections-connectors/policy-definition-create-connections-2.png)

   Para vários conectores, pode adicionar mais condições, por exemplo:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "anyOf": [
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/instagram"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/twitter"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/facebook"
               },
               {
                  "field": "Microsoft.Web/connections/api.id",
                  "like": "*managedApis/pinterest"
               }
            ]
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

1. Quando tiver terminado, selecione **Guardar**. Depois de guardar a definição de política, a Política Azure gera e adiciona mais valores de propriedade à definição de política.

1. Em seguida, para atribuir a definição de política onde pretende fazer cumprir a política, [crie uma atribuição política](#create-policy-assignment).

Para obter mais informações sobre as definições políticas da Azure, consulte estes tópicos:

* [Definição de estrutura política](../governance/policy/concepts/definition-structure.md)
* [Tutorial: Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md)
* [Definições políticas incorporadas da Azure Policy para apps Azure Logic](../logic-apps/policy-samples.md)

<a name="create-policy-connector-usage"></a>

## <a name="create-policy-to-block-using-connections"></a>Criar política para bloquear a utilização de ligações

Quando cria uma ligação dentro de uma aplicação lógica, essa ligação existe como recurso Azure separado. Se eliminar apenas a aplicação lógica, a ligação não é automaticamente eliminada e continua a existir até ser eliminada. Pode ter um cenário em que a ligação já existe ou onde tem de criar a ligação para utilização fora de uma aplicação lógica. Ainda é possível bloquear a capacidade de utilizar uma ligação existente numa aplicação lógica, criando uma política que impeça a poupança de apps lógicas que tenham a ligação restrita ou não aprovada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Na caixa de pesquisa do portal, insira `policy` e selecione **Política**.

   ![No portal Azure, encontre e selecione "política"](./media/block-connections-connectors/find-select-azure-policy.png)

1. No menu **Política,** em **Autoria,** selecione **Definições**  >  **+ Definição de Política**.

   ![Selecione "Definições" > "+ Definição de Política"](./media/block-connections-connectors/add-new-policy-definition.png)

1. Na **definição de Política,** forneça as informações para a sua definição de política, com base nas propriedades descritas sob o exemplo e continue usando o Instagram como exemplo:

   ![Propriedades de definição de política](./media/block-connections-connectors/policy-definition-using-connections-1.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Localização de definição** | Sim | <*Nome de subscrição Azure*> | A assinatura Azure para usar para a definição de política <p><p>1. Para encontrar a sua subscrição, selecione o botão elipses **(...**) . <br>2. A partir da lista **de assinaturas,** encontre e selecione a sua subscrição. <br>3. Quando terminar, **selecione Select**. |
   | **Nome** | Sim | <*nome de definição de política*> | O nome a usar para a definição de política |
   | **Descrição** | Não | <*nome de definição de política*> | Uma descrição para a definição de política |
   | **Categoria** | Sim | **Aplicativos lógicos** | O nome de uma categoria ou nova categoria existente para a definição de política |
   | **Imposição de políticas** | Sim | **Ativado** | Esta definição especifica se ativa ou desativa a definição de política quando salva o seu trabalho. |
   ||||

1. De acordo com **a REGRA POLÍTICA,** a caixa de edição JSON é pré-povoada com um modelo de definição de política. Substitua este modelo pela [definição de política](../governance/policy/concepts/definition-structure.md) com base nas propriedades descritas na tabela abaixo e seguindo esta sintaxe:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "{connector-name}"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | `mode` | `All` | O modo que determina os tipos de recursos que a apólice avalia. <p><p>Este cenário `mode` `All` define-se a , que aplica a política aos grupos de recursos Azure, subscrições e todos os tipos de recursos. <p><p>Para obter mais informações, consulte [a estrutura de definição de política - modo](../governance/policy/concepts/definition-structure.md#mode). |
   | `if` | `{condition-to-evaluate}` | A condição que determina quando aplicar a regra da política <p><p>Neste cenário, `{condition-to-evaluate}` determina se a saída da corda `[string(field('Microsoft.Logic/workflows/parameters'))]` contém a corda, `{connector-name}` . <p><p>Para obter mais informações, consulte [a estrutura de definição de política - Regra de política.](../governance/policy/concepts/definition-structure.md#policy-rule) |
   | `value` | `[string(field('Microsoft.Logic/workflows/parameters'))]` | O valor a comparar com a condição <p><p>Neste cenário, `value` é a saída da corda `[string(field('Microsoft.Logic/workflows/parameters'))]` de , que converte o objeto dentro do objeto para uma `$connectors` `Microsoft.Logic/workflows/parameters` corda. |
   | `contains` | `{connector-name}` | O operador lógico e valor a utilizar para comparar com a `value` propriedade <p><p>Neste cenário, o `contains` operador garante que a regra funciona independentemente do local onde `{connector-name}` aparece, onde a `{connector-name}` cadeia, é o ID para o conector que pretende restringir ou bloquear. <p><p>Por exemplo, suponha que pretende bloquear a utilização de ligações a plataformas de redes sociais ou bases de dados: <p><p>- Twitter: `twitter` <br>- Instagram: `instagram` <br>- Facebook: `facebook` <br>- Pinterest: `pinterest` <br>- SQL Server ou Azure SQL: `sql` <p><p>Para encontrar estes IDs do conector, consulte [o ID de referência do conector](#connector-reference-ID) mais cedo neste tópico. |
   | `then` | `{effect-to-apply}` | O efeito a aplicar quando a `if` condição é satisfeita <p><p>Neste cenário, `{effect-to-apply}` é bloquear e falhar um pedido ou operação que não cumpre a política. <p><p>Para obter mais informações, consulte [a estrutura de definição de política - Regra de política.](../governance/policy/concepts/definition-structure.md#policy-rule) |
   | `effect` | `deny` | O `effect` é ou bloquear o pedido para salvar uma `deny` aplicação lógica que usa a ligação especificada <p><p>Para obter mais informações, consulte [os efeitos da Política de Azure - Negar](../governance/policy/concepts/effects.md#deny). |
   ||||

   Por exemplo, suponha que pretende bloquear aplicações lógicas de poupança que usam ligações do Instagram. Aqui está a definição de política que pode usar:

   ```json
   {
      "mode": "All",
      "policyRule": {
         "if": {
            "value": "[string(field('Microsoft.Logic/workflows/parameters'))]",
            "contains": "instagram"
         },
         "then": {
            "effect": "deny"
         }
      },
      "parameters": {}
    }
    ```

   Aqui está a forma como aparece a caixa **POLICY RULE:**

   ![Regra para definição de política](./media/block-connections-connectors/policy-definition-using-connections-2.png)

1. Quando tiver terminado, selecione **Guardar**. Depois de guardar a definição de política, a Política Azure gera e adiciona mais valores de propriedade à definição de política.

1. Em seguida, para atribuir a definição de política onde pretende fazer cumprir a política, [crie uma atribuição política](#create-policy-assignment).

Para obter mais informações sobre as definições políticas da Azure, consulte estes tópicos:

* [Definição de estrutura política](../governance/policy/concepts/definition-structure.md)
* [Tutorial: Criar e gerir políticas para impor o cumprimento](../governance/policy/tutorials/create-and-manage.md)
* [Definições políticas incorporadas da Azure Policy para apps Azure Logic](../logic-apps/policy-samples.md)

<a name="create-policy-assignment"></a>

## <a name="create-policy-assignment"></a>Criar atribuição de políticas

Em seguida, você precisa atribuir a definição de política onde você quer aplicar a política, por exemplo, a um único grupo de recursos, vários grupos de recursos, Azure Ative Directory (Azure AD) inquilino, ou assinatura Azure. Para esta tarefa, siga estes passos para criar uma atribuição política:

1. Se assinou, volte a entrar no [portal Azure.](https://portal.azure.com) Na caixa de pesquisa do portal, insira `policy` e selecione **Política**.

   ![No portal Azure, encontre e selecione "Política"](./media/block-connections-connectors/find-select-azure-policy.png)

1. No menu **Política,** em **Autoria,** selecione **Política**  >  **de Atribuição**.

   ![Selecione "Atribuições" > "Atribuir"](./media/block-connections-connectors/add-new-policy-assignment.png)

1. De acordo com **o Básico,** forneça esta informação para a atribuição de políticas:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Âmbito** | Sim | Os recursos onde quer fazer cumprir a atribuição de políticas. <p><p>1. Ao lado da caixa **Scope,** selecione o botão elipses **(...**) . <br>2. A partir da lista **de assinaturas,** selecione a subscrição do Azure. <br>3. Opcionalmente, a partir da lista **do Grupo de Recursos,** selecione o grupo de recursos. <br>4. Quando terminar, **selecione Select**. |
   | **Exclusões** | Não | Quaisquer recursos da Azure para excluir da atribuição de políticas. <p><p>1. Ao lado da caixa **Exclusions,** selecione o botão elipses **(...**) . <br>2. Na lista **de recursos,** selecione o recurso > **Adicionar ao Âmbito Selecionado.** <br>3. Quando terminar, **selecione Save**. |
   | **Definição de política** | Sim | O nome para a definição de política que quer atribuir e impor. Este exemplo continua com o exemplo da política do Instagram, "Bloquear as ligações do Instagram". <p><p>1. Ao lado da caixa **de definição de política,** selecione o botão elipses **(...**) . <br>2. Encontre e selecione a definição de política utilizando o filtro **Tipo** ou a caixa **de busca.** <br>3. Quando terminar, **selecione Select**. |
   | **Nome da atribuição** | Sim | O nome a usar para a atribuição de políticas, se diferente da definição de política |
   | **ID de atribuição** | Sim | O ID gerado automaticamente para a atribuição de políticas |
   | **Descrição** | Não | Uma descrição para a atribuição de políticas |
   | **Imposição de políticas** | Sim | A definição que permite ou desativa a atribuição de políticas |
   | **Atribuído por** | Não | O nome para a pessoa que criou e aplicou a atribuição de política |
   ||||

   Por exemplo, atribuir a política a um grupo de recursos Azure utilizando o exemplo do Instagram:

   ![Propriedades de atribuição de políticas](./media/block-connections-connectors/policy-assignment-basics.png)

1. Quando terminar, selecione **Review + create**.

   Depois de criar uma apólice, poderá ter de esperar até 15 minutos antes de a apólice produzir efeitos. As alterações podem também ter efeitos atrasados semelhantes.

1. Depois de a apólice produzir efeito, pode [testar a sua política](#test-policy).

Para obter mais informações, consulte [Quickstart: Crie uma atribuição de política para identificar recursos não conformes](../governance/policy/assign-policy-portal.md).

<a name="test-policy"></a>

## <a name="test-the-policy"></a>Testar a política

Para experimentar a sua política, comece a criar uma ligação utilizando o conector agora restrito no Logic App Designer. Continuando com o exemplo do Instagram, quando entra no Instagram, obtém-se este erro que a sua aplicação lógica não conseguiu criar a ligação:

![Falha de ligação devido à política aplicada](./media/block-connections-connectors/connection-failure-message.png)

A mensagem inclui esta informação:

| Descrição | Conteúdo |
|-------------|---------|
| Razão para o fracasso | `"Resource 'instagram' was disallowed by policy."` |
| Nome da atribuição | `"Block Instagram connections"` |
| ID de atribuição | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/resourceGroups/MyLogicApp-RG/providers/Microsoft.Authorization/policyAssignments/4231890fc3bd4352acb0b673"` |
| ID de definição de política | `"/subscriptions/xxxxxXXXXXxxxxxXXXXXxxxxxXXXXX/providers/Microsoft.Authorization/policyDefinitions/b5ddcfec-1b24-4cac-a353-360846a59f24"` |
|||

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a Política Azure](../governance/policy/overview.md)
