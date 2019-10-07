---
title: Usar parâmetros para criar plantas dinâmicas
description: Saiba mais sobre os parâmetros estáticos e dinâmicos e como usá-los cria plantas dinâmicas.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 2bb38e0698d7504ba1bb139ca1bd5e3b14e5cdd4
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981057"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>Criando plantas dinâmicas por meio de parâmetros

Um plano gráfico totalmente definido com vários artefatos (como grupos de recursos, modelos do Resource Manager, políticas ou atribuições de função) oferece a criação rápida e a criação consistente de objetos no Azure. Para habilitar o uso flexível desses padrões e contêineres de design reutilizáveis, os planos gráficos do Azure dão suporte a parâmetros. O parâmetro cria flexibilidade, durante a definição e atribuição, para alterar propriedades nos artefatos implantados pelo plano gráfico.

Um exemplo simples é o artefato do grupo de recursos. Quando um grupo de recursos é criado, ele tem dois valores necessários que devem ser fornecidos: nome e local. Ao adicionar um grupo de recursos ao seu plano gráfico, se não houver parâmetros, você definiria esse nome e o local para cada uso do plano gráfico. Essa repetição faria com que cada uso do plano gráfico criasse artefatos no mesmo grupo de recursos. Os recursos dentro desse grupo de recursos se tornarão duplicados e causarão um conflito.

> [!NOTE]
> Não é um problema para duas plantas diferentes incluir um grupo de recursos com o mesmo nome.
> Se um grupo de recursos incluído em um plano gráfico já existir, o plano gráfico continuará criando os artefatos relacionados nesse grupo de recursos. Isso pode causar um conflito, pois dois recursos com o mesmo nome e tipo de recurso não podem existir dentro de uma assinatura.

A solução para esse problema são os parâmetros. Os planos gráficos permitem que você defina o valor de cada propriedade do artefato durante a atribuição a uma assinatura. O parâmetro torna possível reutilizar um plano gráfico que cria um grupo de recursos e outros recursos dentro de uma única assinatura sem ter conflito.

## <a name="blueprint-parameters"></a>Parâmetros de esquema

Por meio da API REST, os parâmetros podem ser criados no próprio plano gráfico. Esses parâmetros são diferentes dos parâmetros em cada um dos artefatos com suporte. Quando um parâmetro é criado no plano gráfico, ele pode ser usado pelos artefatos nesse plano gráfico. Um exemplo pode ser o prefixo para a nomenclatura do grupo de recursos. O artefato pode usar o parâmetro Blueprint para criar um parâmetro "principalmente dinâmico". Como o parâmetro também pode ser definido durante a atribuição, esse padrão permite uma consistência que pode aderir às regras de nomenclatura. Para obter as etapas, consulte [definindo parâmetros estáticos-parâmetro de nível do Blueprint](#blueprint-level-parameter).

### <a name="using-securestring-and-secureobject-parameters"></a>Usando parâmetros secureString e secureobject

Embora um _artefato_ de modelo do Resource Manager dê suporte a parâmetros dos tipos **secureString** e **Secureobject** , os planos gráficos do Azure exigem que cada um seja conectado a um Azure Key Vault.
Essa medida de segurança impede a prática não segura de armazenar segredos junto com o plano gráfico e incentiva o emprego de padrões seguros. Os planos gráficos do Azure dão suporte a essa medida de segurança, detectando a inclusão de um parâmetro seguro em um _artefato_de modelo do Resource Manager. O serviço, em seguida, solicita durante a atribuição as seguintes propriedades de Key Vault por parâmetro seguro detectado:

- ID do recurso de Key Vault
- Nome do segredo de Key Vault
- Key Vault versão de segredo

Se a atribuição Blueprint usar uma **identidade gerenciada atribuída pelo sistema**, o Key Vault referenciado _deverá_ existir na mesma assinatura à qual a definição de Blueprint está atribuída.

Se a atribuição Blueprint usar uma **identidade gerenciada atribuída pelo usuário**, o Key Vault referenciado _poderá_ existir em uma assinatura centralizada. A identidade gerenciada deve receber os direitos apropriados no Key Vault antes da atribuição de Blueprint.

> [!IMPORTANT]
> Em ambos os casos, o Key Vault deve ter **habilitar acesso ao Azure Resource Manager para implantação de modelo** configurada na página **políticas de acesso** . Para obter instruções sobre como habilitar esse recurso, consulte [Key Vault-habilitar implantação de modelo](../../../managed-applications/key-vault-access.md#enable-template-deployment).

Para obter mais informações sobre Azure Key Vault, consulte [Key Vault visão geral](../../../key-vault/key-vault-overview.md).

## <a name="parameter-types"></a>Tipos de parâmetro

### <a name="static-parameters"></a>Parâmetros estáticos

Um valor de parâmetro definido na definição de um plano gráfico é chamado de **parâmetro estático**, pois cada uso do Blueprint implantará o artefato usando esse valor estático. No exemplo de grupo de recursos, embora não faça sentido para o nome do grupo de recursos, isso pode fazer sentido para o local. Em seguida, cada atribuição do plano gráfico criaria o grupo de recursos, o que for chamado durante a atribuição, no mesmo local. Essa flexibilidade permite que você seja seletivo no que você define conforme necessário versus o que pode ser alterado durante a atribuição.

#### <a name="setting-static-parameters-in-the-portal"></a>Configurando parâmetros estáticos no portal

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione **definições de plantas** na página à esquerda.

1. Clique em um plano gráfico existente e, em seguida, clique em **Editar Blueprint** ou clique em **+ criar plano gráfico** e preencha as informações na guia **noções básicas** .

1. Clique em **Seguinte: Artefatos @ no__t-0 ou clique na guia **artefatos** .

1. Os artefatos adicionados ao plano gráfico com opções de parâmetro exibem **X de parâmetros Y preenchidos** na coluna **parâmetros** . Clique na linha de artefato para editar os parâmetros de artefato.

   ![Parâmetros de Blueprint em uma definição de Blueprint](../media/parameters/parameter-column.png)

1. A página **Editar artefato** exibe as opções de valor apropriadas para o artefato clicado. Cada parâmetro no artefato tem um título, uma caixa de valor e uma CheckBox. Defina a caixa como desmarcada para torná-la um **parâmetro estático**. No exemplo a seguir, somente _Location_ é um **parâmetro estático** , pois ele está desmarcado e o _nome do grupo de recursos_ está marcado.

   ![Parâmetros estáticos do Blueprint em um artefato do Blueprint](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>Configurando parâmetros estáticos da API REST

Em cada URI da API REST, existem variáveis que são utilizadas que precisa de substituir pelos seus próprios valores:

- `{YourMG}` - substituir pelo nome do seu grupo de gestão
- `{subscriptionId}` - substituir pelo ID da subscrição

##### <a name="blueprint-level-parameter"></a>Parâmetro de nível do Blueprint

Ao criar um plano gráfico por meio da API REST, é possível criar [parâmetros de plano gráfico](#blueprint-parameters). Para fazer isso, use o seguinte URI da API REST e o formato do corpo:

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- Corpo do Pedido

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

Depois que um parâmetro de nível do plano gráfico é criado, ele pode ser usado em artefatos adicionados a esse plano gráfico.
O exemplo de API REST a seguir cria um artefato de atribuição de função no Blueprint e usa o parâmetro de nível Blueprint.

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- Corpo do Pedido

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

Neste exemplo, a propriedade **principalIds** usa o parâmetro de nível de planta de **proprietários** usando um valor de `[parameters('owners')]`. Definir um parâmetro em um artefato usando um parâmetro de nível Blueprint ainda é um exemplo de um **parâmetro estático**. O parâmetro de nível do plano gráfico não pode ser definido durante a atribuição do Blueprint e será o mesmo valor em cada atribuição.

##### <a name="artifact-level-parameter"></a>Parâmetro de nível de artefato

A criação de **parâmetros estáticos** em um artefato é semelhante, mas usa um valor reto em vez de usar a função `parameters()`. O exemplo a seguir cria dois parâmetros estáticos, **TagName** e **tagValue**. O valor em cada um é fornecido diretamente e não usa uma chamada de função.

- URI da API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- Corpo do Pedido

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>Parâmetros dinâmicos

O oposto de um parâmetro estático é um **parâmetro dinâmico**. Esse parâmetro não é definido no plano gráfico, mas, em vez disso, é definido durante cada atribuição do plano gráfico. No exemplo de grupo de recursos, o uso de um **parâmetro dinâmico** faz sentido para o nome do grupo de recursos. Ele fornece um nome diferente para cada atribuição do plano gráfico. Para obter uma lista de funções de plano gráfico, consulte a referência de [funções de Blueprint](../reference/blueprint-functions.md) .

#### <a name="setting-dynamic-parameters-in-the-portal"></a>Configurando parâmetros dinâmicos no portal

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione **definições de plantas** na página à esquerda.

1. Clique com o botão direito do mouse no plano gráfico que você deseja atribuir. Selecione **atribuir plano gráfico** ou clique no plano gráfico que você deseja atribuir e clique no botão **atribuir Blueprint** .

1. Na página **atribuir Blueprint** , localize a seção **parâmetros de artefato** . Cada artefato com pelo menos um **parâmetro dinâmico** exibe o artefato e as opções de configuração. Forneça os valores necessários para os parâmetros antes de atribuir o plano gráfico. No exemplo a seguir, _Name_ é um **parâmetro dinâmico** que deve ser definido para concluir a atribuição de Blueprint.

   ![Parâmetro dinâmico do Blueprint durante a atribuição do Blueprint](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>Definindo parâmetros dinâmicos da API REST

A configuração de **parâmetros dinâmicos** durante a atribuição é feita inserindo-se o valor diretamente. Em vez de usar uma função, como [Parameters ()](../reference/blueprint-functions.md#parameters), o valor fornecido é uma cadeia de caracteres apropriada. Os artefatos de um grupo de recursos são definidos com as propriedades "nome do modelo", **nome**e **local** . Todos os outros parâmetros do artefato incluído são definidos em **parâmetros** com um par de chaves **\<name @ no__t-3** e **valor** . Se o Blueprint estiver configurado para um parâmetro dinâmico que não é fornecido durante a atribuição, a atribuição falhará.

- URI da API REST

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- Corpo do Pedido

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>Passos seguintes

- Consulte a lista de [funções de plantas](../reference/blueprint-functions.md).
- Saiba mais sobre o [ciclo de vida do esquema](lifecycle.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).