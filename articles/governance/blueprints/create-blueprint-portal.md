---
title: 'Quickstart: Criar uma planta no portal'
description: Neste arranque rápido, utiliza-se plantas Azure para criar, definir e implantar artefactos através do portal Azure.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: fa9eb8285086e35f8b23798c41a09529d0f49582
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240983"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Quickstart: Defina e atribua uma planta no portal

Quando aprender a criar e atribuir plantas, pode definir padrões comuns para desenvolver configurações reutilizáveis e rapidamente implantáveis com base em modelos de Gestor de Recursos Azure, política, segurança e muito mais. Neste tutorial, aprende-se a usar o Azure Blueprints para fazer algumas das tarefas comuns relacionadas com a criação, publicação e atribuição de um projeto dentro da sua organização. Estas tarefas incluem:

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-a-blueprint"></a>Criar um esquema

O primeiro passo na definição de um padrão de conformidade é compor um esquema a partir dos recursos disponíveis. Neste exemplo, crie um novo projeto chamado **MyBlueprint** para configurar tarefas de funções e políticas para a subscrição. Em seguida, adicione um novo grupo de recursos, e crie um modelo de Gestor de Recursos e atribuição de funções no novo grupo de recursos.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione **definições** de Blueprint a partir da página à esquerda e selecione o botão **de planta + Criar** na parte superior da página.

   Ou, selecione **Create** from the **Getting start** page para ir direto para criar uma planta.

   ![Criar uma planta a partir da página definições de Blueprint](./media/create-blueprint-portal/create-blueprint-button.png)

1. Forneça um **nome de Planta** como **MyBlueprint**. (Utilize até 48 letras e números, mas sem espaços ou caracteres especiais). Deixe a **descrição da planta** em branco por enquanto.

1. Na caixa de **localização Definição,** selecione a elipse à direita, selecione o grupo de [gestão](../management-groups/overview.md) ou subscrição onde pretende guardar a planta e escolha **Select**.

1. Verifique se a informação está correta. O **nome da planta** e os campos de localização **definição** não podem ser alterados mais tarde. Em seguida, selecione **Seguinte : Artefactos** na parte inferior da página ou o separador **Artefactos** na parte superior da página.

1. Adicione uma atribuição de funções ao nível da subscrição:

   1. Selecione a linha **+ Adicionar artefacto** **sintetizador sob subscrição**. A janela **de artefactos Add** abre-se no lado direito do navegador.

   1. Selecione **a atribuição de funções** para **o tipo Artefacto**.

   1. Em **funções,** selecione **Contributor**. Deixe o **utilizador, app ou** caixa de grupo Add com a caixa de verificação que indica um parâmetro dinâmico.

   1. Selecione **Adicionar** este artefacto à planta.

   ![Atribuição de funções para um artefacto de planta](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > A maioria dos artefactos suportam parâmetros. Um parâmetro que lhe foi atribuído um valor durante a criação de plantas é um *parâmetro estático.* Se o parâmetro for atribuído durante a atribuição da planta, é um *parâmetro dinâmico.* Para obter mais informações, veja [Parâmetros de esquema](./concepts/parameters.md).

1. Adicione uma atribuição de política ao nível da subscrição:

   1. Selecione a linha **+ Adicionar artefacto** sob o artefacto de atribuição de funções.

   1. Selecione **a atribuição de política** para o tipo **Artefacto**.

   1. Alterar **tipo** para **incorporado**. Em **Pesquisa,** insira **a etiqueta**.

   1. Clique fora da caixa **Pesquisar** para filtrar os resultados. Selecione **etiqueta de apêndice e o seu valor predefinido para grupos de recursos**.

   1. Selecione **Adicionar** este artefacto à planta.

1. Selecione a linha da etiqueta de atribuição de políticas **E o seu valor predefinido para grupos de recursos**.

1. A janela para fornecer parâmetros ao artefacto como parte da definição de planta abre e permite definir os parâmetros para todas as atribuições (parâmetros estáticos) com base nesta planta em vez de durante a atribuição (parâmetros dinâmicos). Este exemplo utiliza parâmetros dinâmicos durante a atribuição da planta, por isso deixe as predefinições e **selecione Cancelar**.

1. Adicione um grupo de recursos ao nível da subscrição:

   1. Selecione a linha **+ Adicionar artefacto** **sintetizador sob subscrição**.

   1. Selecione **grupo de recursos** para o tipo **Artefacto**.

   1. Deixe o nome de **exibição do Artefacto,** **Nome do Grupo de Recursos**e caixas de **localização** em branco, mas certifique-se de que a caixa de verificação é verificada para cada propriedade do parâmetro para os tornar parâmetros dinâmicos.

   1. Selecione **Adicionar** este artefacto à planta.

1. Adicione um modelo sob o grupo de recursos:

   1. Selecione a linha **+ Adicionar artefactos** sob a entrada **do Grupo de Recursos.**

   1. Selecione **o modelo do Gestor de Recursos Azure** para o tipo de **artefacto,** coloque o nome do **ecrã do artefacto** para **StorageAccount**, e deixe a **descrição** em branco.

   1. No separador **Modelo** na caixa do editor, cole o seguinte modelo do Resource Manager.
      Depois de colar o modelo, selecione o separador **Parâmetros** e note que os parâmetros de modelo **armazenamentoAccountType** e **localização** foram detetados. Cada parâmetro foi automaticamente detetado e povoado, mas configurado como um parâmetro dinâmico.

      > [!IMPORTANT]
      > Se estiver a importar o modelo, certifique-se de que o ficheiro é apenas JSON e não inclui HTML. Quando estiver a apontar para um URL no GitHub, certifique-se de que selecionou **RAW** para obter o ficheiro JSON puro e não o embrulhado com HTML para exibição no GitHub. Se o modelo importado não for JSON puro, ocorrerá um erro.

      ```json
      {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
              "storageAccountType": {
                  "type": "string",
                  "defaultValue": "Standard_LRS",
                  "allowedValues": [
                      "Standard_LRS",
                      "Standard_GRS",
                      "Standard_ZRS",
                      "Premium_LRS"
                  ],
                  "metadata": {
                      "description": "Storage Account type"
                  }
              },
              "location": {
                  "type": "string",
                  "defaultValue": "[resourceGroup().location]",
                  "metadata": {
                      "description": "Location for all resources."
                  }
              }
          },
          "variables": {
              "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
          },
          "resources": [{
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                  "name": "[parameters('storageAccountType')]"
              },
              "kind": "StorageV2",
              "properties": {}
          }],
          "outputs": {
              "storageAccountName": {
                  "type": "string",
                  "value": "[variables('storageAccountName')]"
              }
          }
      }
      ```

   1. Limpe a caixa de verificação **do StorageAccountType** e note que a lista de drop-down contém apenas valores incluídos no modelo de Gestor de Recursos em **valores permitidos**. Selecione a caixa para a colocar de volta num parâmetro dinâmico.

   1. Selecione **Adicionar** este artefacto à planta.

   ![Modelo de Gestor de Recursos para o artefacto da planta](./media/create-blueprint-portal/add-resource-manager-template.png)

1. O esquema concluído deve ter um aspeto semelhante ao seguinte. Note que cada artefacto tem  **_x_ de _y_ parâmetros povoados** na coluna **Parâmetros.** Os parâmetros dinâmicos são definidos durante cada atribuição da planta.

   ![Definição de planta completa](./media/create-blueprint-portal/completed-blueprint.png)

1. Agora que todos os artefactos planeados foram adicionados, selecione **Save Draft** na parte inferior da página.

## <a name="edit-a-blueprint"></a>Editar um esquema

Em [Create a blueprint,](#create-a-blueprint)não forneceu uma descrição ou adicionou a atribuição de funções ao novo grupo de recursos. Pode corrigir ambos seguindo estes passos:

1. Selecione **definições** de Blueprint a partir da página à esquerda.

1. Na lista de plantas, clique na direita daquela que criou anteriormente e selecione **editar a planta**.

1. Na **descrição do Blueprint,** forneça algumas informações sobre a planta e os artefactos que a compõem. Neste caso, insira algo como: Este projeto define a política de etiquetas e a atribuição de **papéis na subscrição, cria um ResourceGroup, e implementa um modelo** de recursos e atribuição de funções para esse Grupo de Recursos.

1. Selecione **Seguinte: Artefactos** na parte inferior da página ou o separador **Artefactos** na parte superior da página.

1. Adicione uma atribuição de funções no âmbito do grupo de recursos:

   1. Selecione a linha **+ Adicionar artefacto** diretamente sob a entrada do Grupo de **Recursos.**

   1. Selecione **a atribuição de funções** para **o tipo Artefacto**.

   1. Em **Funções**, selecione **Proprietário**, e limpe a caixa de verificação sob o utilizador, app ou caixa **de grupo Add.**

   1. Procure e selecione um utilizador, app ou grupo para adicionar. Este artefacto usa um parâmetro estático definido o mesmo em cada atribuição desta planta.

   e. Selecione **Adicionar** este artefacto à planta.

   ![Segunda função para o artefacto da planta](./media/create-blueprint-portal/add-role-assignment-2.png)

1. O esquema concluído deve ter um aspeto semelhante ao seguinte. Note que a atribuição de funções recentemente adicionada mostra **1 em 1 parâmetros povoados**. Significa que é um parâmetro estático.

   ![Segunda definição para o projeto concluído](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Selecione **Save Draft** agora que foi atualizado.

## <a name="publish-a-blueprint"></a>Publicar um esquema

Agora que os artefactos planeados foram adicionados ao esquema, é altura de o publicar.
A publicação disponibiliza a planta para ser atribuída a uma subscrição.

1. Selecione **definições** de Blueprint a partir da página à esquerda.

1. Na lista de plantas, clique à direita na que criou anteriormente e selecione **Publicar a planta**.

1. No painel que abre, forneça uma **Versão** (letras, números e hífenes com um comprimento máximo de 20 caracteres), como **v1**. Opcionalmente, introduza o texto nas **notas de alteração**, como a primeira **publicação**.

1. Selecione **Publicar** na parte inferior da página.

## <a name="assign-a-blueprint"></a>Atribuir um esquema

Depois de uma planta ter sido publicada, pode ser atribuída a uma subscrição. Atribua o projeto que criou a uma das subscrições sob a hierarquia do seu grupo de gestão. Se a planta for guardada para uma subscrição, só pode ser atribuída a essa subscrição.

1. Selecione **definições** de Blueprint a partir da página à esquerda.

1. Na lista de plantas, clique na direita na que criou anteriormente (ou selecione a elipsis) e selecione a **planta de atribuir**.

1. Na página de **design de Atribuição,** na lista de drop-down da **Subscrição,** selecione as subscrições para as quais pretende implementar esta planta.

   Se houver ofertas suportadas da Enterprise disponíveis a partir do [Azure Billing,](../../billing/index.md)um novo link **Create** é ativado sob a caixa **de subscrição.** Siga estes passos.

   1. Selecione o novo link **Criar** uma nova subscrição em vez de selecionar os existentes.

   1. Forneça um **nome de exibição** para a nova subscrição.

   1. Selecione a **Oferta** disponível na lista de lançamentos.

   1. Utilize a elipse para selecionar o grupo de [gestão](../management-groups/overview.md) de que a subscrição será uma criança.

   1. Selecione **Criar** na parte inferior da página.

   ![Criar uma subscrição para uma subscrição de atribuição de plantas](./media/create-blueprint-portal/assignment-create-subscription.png)

   > [!IMPORTANT]
   > A nova subscrição é criada imediatamente após selecionar **Criar**.

   > [!NOTE]
   > É criada uma atribuição para cada subscrição que selecionar. Pode efazer alterações numa única atribuição de subscrição posteriormente sem forçar alterações nas restantes subscrições selecionadas.

1. Para **designação,** forneça um nome único para esta atribuição.

1. No **Local**, selecione uma região para a identidade gerida e objeto de implementação de subscrição a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [Identidades geridas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Deixe a versão de **definição Blueprint** a seleção de versões **publicadas** na entrada **v1.** (O predefinido é a versão mais recentemente publicada.)

1. Em **Atribuição de Bloqueio**, deixe a predefinição **Não Bloquear**. Para mais informações, consulte o bloqueio de [recursos blueprints](./concepts/resource-locking.md).

   ![Bloqueio e identidades geridas para a atribuição](./media/create-blueprint-portal/assignment-locking-mi.png)

1. Em **Identidade Gerida,** deixe o padrão do **Sistema atribuído**.

1. Para a atribuição de função ao nível da subscrição **[Grupo de utilizadores ou nome da aplicação]: Contribuidor**, procure e selecione um utilizador, uma aplicação ou um grupo.

1. Para a atribuição da política de nível de subscrição, deset nome de **etiqueta** para **CostCenter** e o **Valor de Etiqueta** para **ContosoIT**.

1. Para **ResourceGroup,** forneça um **Nome** de **StorageAccount** e uma **localização** do East **US 2** a partir da lista de drop-down.

   > [!NOTE]
   > Para cada artefacto que adicionou sob o grupo de recursos durante a definição de plantas, esse artefacto está recuado para alinhar com o grupo de recursos ou objeto com o qual o irá implantar.
   > Os artefactos que não têm parâmetros ou não têm parâmetros a definir na atribuição são listados apenas para informações contextuais.

1. No modelo de **armazenamento do**gestor de recursos Azure, selecione **Standard_GRS** para o parâmetro **de armazenamentoAccountType.**

1. Leia a caixa de informação na parte inferior da página e, em seguida, **selecione Designar**.

## <a name="track-deployment-of-a-blueprint"></a>Controlar a implementação de um esquema

Após atribuir um esquema a uma ou mais subscrições, acontecem duas coisas:

- A planta é adicionada à página de **plantas atribuídapara** cada subscrição.
- Começa o processo de implantação de todos os artefactos definidos pela planta.

Agora que o projeto foi atribuído a uma subscrição, verifique o progresso da implementação:

1. Selecione **as plantas atribuídas** a partir da página à esquerda.

1. Na lista de plantas, clique na direita daquela que atribuiu anteriormente e selecione **Ver detalhes**de atribuição .

   ![Ver detalhes de atribuição da página de plantas atribuídas](./media/create-blueprint-portal/view-assignment-details.png)

1. Na página de atribuição do **Blueprint,** valide que todos os artefactos foram implantados com sucesso e que não houve erros durante a implementação. Se ocorrerem erros, consulte as plantas de [resolução de problemas](./troubleshoot/general.md) para as medidas para determinar o que correu mal.

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="unassign-a-blueprint"></a>Anular a atribuição de um esquema

Se já não precisar de uma atribuição de plantas, remova-a de uma subscrição. A planta pode ter sido substituída por um novo projeto com padrões, políticas e desenhos atualizados. Quando um esquema é removido, os artefactos atribuídos como parte desse esquema são deixados para trás. Para remover uma atribuição de esquema, siga estes passos:

1. Selecione **as plantas atribuídas** a partir da página à esquerda.

1. Na lista de plantas, selecione a planta que pretende desatribuir. Em seguida, selecione o botão de **planta Unassign** na parte superior da página.

1. Leia a mensagem de confirmação e, em seguida, selecione **OK**.

### <a name="delete-a-blueprint"></a>Eliminar um esquema

1. Selecione **definições** de Blueprint a partir da página à esquerda.

1. Clique na planta que pretende eliminar e **selecione Eliminar**a planta . Em seguida, selecione **Sim** na caixa de diálogo de confirmação.

> [!NOTE]
> A eliminação de uma planta neste método também elimina todas as versões publicadas da planta selecionada.
> Para eliminar uma única versão, abra a planta, selecione o separador **de versões Publicados,** selecione a versão que pretende eliminar e, em seguida, selecione **Delete This Version**. Além disso, não pode apagar uma planta até ter eliminado toda a definição de projeto.

## <a name="next-steps"></a>Passos Seguintes

Neste arranque rápido, criou, atribuiu e removeu uma planta com portal Azure. Para saber mais sobre as Plantas Azure, continue o artigo blueprint lifecycle.

> [!div class="nextstepaction"]
> [Conheça o ciclo de vida da planta](./concepts/lifecycle.md)