---
title: 'Quickstart: Criar uma planta no portal'
description: Neste arranque rápido, você usa Azure Blueprints para criar, definir e implantar artefactos através do portal Azure.
ms.date: 08/27/2020
ms.topic: quickstart
ms.openlocfilehash: bb9aeb4d4b96227f7bf7296854c56df4058fbe69
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022535"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Quickstart: Definir e atribuir uma planta no portal

Quando aprender a criar e atribuir plantas, pode definir padrões comuns para desenvolver configurações reutilizáveis e rapidamente implantáveis com base em modelos de Gestor de Recursos Azure (modelos ARM), política, segurança e muito mais. Neste tutorial, aprende-se a usar a Azure Blueprints para fazer algumas das tarefas comuns relacionadas com a criação, publicação e atribuição de um projeto dentro da sua organização. Estas tarefas incluem:

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-a-blueprint"></a>Criar um esquema

O primeiro passo na definição de um padrão de conformidade é compor um esquema a partir dos recursos disponíveis. Neste exemplo, crie um novo projeto chamado **MyBlueprint** para configurar funções e atribuições políticas para a subscrição. Em seguida, adicione um novo grupo de recursos e crie um modelo de Gestor de Recursos e atribuição de funções no novo grupo de recursos.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione **definições** de Planta a partir da página à esquerda e selecione o botão + Criar o **botão de planta** no topo da página.

   Ou, **selecione Criar** a partir da página **Iniciar** para ir direto para a criação de uma planta.

   :::image type="content" source="./media/create-blueprint-portal/create-blueprint-button.png" alt-text="Screenshot do botão 'Criar planta' na página definições de Blueprint." border="false":::

1. Selecione Comece com a **planta em branco** do cartão no topo da lista de plantas incorporadas.

1. Forneça um **nome Blueprint** como **MyBlueprint**. (Utilize até 48 letras e números, mas sem espaços ou caracteres especiais). Deixe **a descrição da Planta** em branco por enquanto.

1. Na caixa **de localização Definição,** selecione a elipse à direita, selecione o grupo de [gestão](../management-groups/overview.md) ou subscrição onde pretende guardar a planta e escolha **Select**.

1. Verifique se a informação está correta. O **nome blueprint** e os campos de **localização de definição** não podem ser alterados mais tarde. Em seguida, selecione **Seguinte : Artefactos** na parte inferior da página ou o separador **Artefactos** no topo da página.

1. Adicione uma atribuição de função ao nível da subscrição:

   1. Selecione a linha **de artefacto +** em **subscrição.** A janela **de artefactos Add** abre-se no lado direito do navegador.

   1. Selecione **atribuição de função** para **o tipo artefacto**.

   1. Under **Role**, select **Contributor**. Deixe o **utilizador, aplicação ou** caixa de grupo adicionar com a caixa de verificação que indica um parâmetro dinâmico.

   1. **Selecione Adicione** para adicionar este artefacto à planta.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment.png" alt-text="Screenshot das opções de artefacto de atribuição de funções para adicionar a uma definição de planta." border="false":::

   > [!NOTE]
   > A maioria dos artefactos suporta parâmetros. Um parâmetro que é atribuído a um valor durante a criação do projeto é um _parâmetro estático._ Se o parâmetro for atribuído durante a atribuição da planta, é um _parâmetro dinâmico._ Para obter mais informações, consulte [os parâmetros do Plano.](./concepts/parameters.md)

1. Adicione uma atribuição de política ao nível da subscrição:

   1. Selecione a linha **de artefacto + adicionar** sob o artefacto de atribuição de funções.

   1. Selecione **a atribuição de política** para o tipo de **artefacto**.

   1. Alterar **tipo** para **incorporado**. Em **Busca,** **insira a etiqueta**.

   1. Mude o foco fora da **Procura** para que a filtragem ocorra. Selecione **a etiqueta de apêndice e o seu valor predefinido para grupos de recursos.**

   1. **Selecione Adicione** para adicionar este artefacto à planta.

1. Selecione a linha da atribuição de políticas **A etiqueta do apêndice e o seu valor predefinido para grupos de recursos**.

1. É apresentada a janela com os parâmetros para o artefacto como parte da definição do esquema e que permite definir os parâmetros para todas as atribuições (parâmetros estáticos) com base neste esquema, em vez de o fazer durante a atribuição (parâmetros dinâmicos). Este exemplo utiliza parâmetros dinâmicos durante a atribuição do projeto, por isso deixe as predefinições e **selecione Cancelar**.

1. Adicione um grupo de recursos ao nível de subscrição:

   1. Selecione a linha **de artefacto +** em **subscrição.**

   1. Selecione **grupo de recursos** para o tipo **artefacto**.

   1. Deixe o nome de **exibição do Artefacto,** **Nome do Grupo de Recursos** e caixas de **localização** em branco, mas certifique-se de que a caixa de verificação é verificada para cada propriedade de parâmetros para torná-los parâmetros dinâmicos.

   1. **Selecione Adicione** para adicionar este artefacto à planta.

1. Adicione um modelo sob o grupo de recursos:

   1. Selecione a linha **de artefacto + adicionar** sob a entrada do Grupo de **Recursos.**

   1. Selecione **o modelo de Gestor de Recursos Azure** para o tipo de **artefacto,** desenhe **o nome do visor do artefacto** para o **StorageAccount** e deixe **a Descrição** em branco.

   1. No **separador Modelo** na caixa do editor, cole o seguinte modelo ARM. Depois de colar o modelo, selecione o **separador Parâmetros** e note que os parâmetros do modelo **armazenam o Número** de Registos e **a localização** foram detetados. Cada parâmetro foi automaticamente detetado e povoado, mas configurado como um parâmetro dinâmico.

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

   1. Limpe a caixa de verificação **de armazenamentoAcomprendaType** e note que a lista de drop-down contém apenas valores incluídos no modelo ARM sob **o permitidoValues**. Selecione a caixa para a reussar para um parâmetro dinâmico.

   1. **Selecione Adicione** para adicionar este artefacto à planta.

   :::image type="content" source="./media/create-blueprint-portal/add-resource-manager-template.png" alt-text="Screenshot das opções de artefacto do modelo do gestor de recursos para adicionar a uma definição de planta." border="false":::

1. O esquema concluído deve ter um aspeto semelhante ao seguinte. Note que cada artefacto tem **_x_ fora de _parâmetros y_ povoados** na coluna **Parâmetros.** Os parâmetros dinâmicos são definidos durante cada atribuição do esquema.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint.png" alt-text="Screenshot de uma definição de planta completa com cada tipo de artefacto." border="false":::

1. Agora que todos os artefactos planeados foram adicionados, **selecione Save Draft** na parte inferior da página.

## <a name="edit-a-blueprint"></a>Editar um esquema

Na [Criação de uma planta,](#create-a-blueprint)não forneceu uma descrição ou adicionou a atribuição de funções ao novo grupo de recursos. Pode corrigir ambos seguindo estes passos:

1. Selecione **definições** de Planta da página à esquerda.

1. Na lista de plantas, clique com o botão direito no que criou anteriormente e selecione **Editar o projeto**.

1. Na **descrição do Blueprint,** forneça algumas informações sobre a planta e os artefactos que a compõem. Neste caso, introduza algo como: **Este projeto define a política de marcação e a atribuição de funções na subscrição, cria um Grupo de Recursos e implementa um modelo de recursos e uma atribuição de funções a esse Grupo de Recursos.**

1. Selecione **Seguinte : Artefactos** na parte inferior da página ou no separador **Artefactos** no topo da página.

1. Adicione uma atribuição de funções no grupo de recursos:

   1. Selecione a linha **de artefacto + adicionar** diretamente sob a entrada do Grupo de **Recursos.**

   1. Selecione **atribuição de função** para **o tipo artefacto**.

   1. Under **Role**, selecione **Owner**, e limpe a caixa de verificação sob o utilizador, app ou caixa de grupo **Do Add.**

   1. Procure e selecione um utilizador, app ou grupo para adicionar. Este artefacto utiliza um parâmetro estático que é o mesmo em cada atribuição deste esquema.

   1. **Selecione Adicione** para adicionar este artefacto à planta.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment-2.png" alt-text="Screenshot das opções de artefactos de segunda função para adicionar a uma definição de planta." border="false":::

1. O esquema concluído deve ter um aspeto semelhante ao seguinte. Note que a atribuição de funções recentemente adicionada mostra **1 em 1 parâmetros povoados**. Significa que é um parâmetro estático.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint-2.png" alt-text="Screenshot da segunda definição de planta concluída com o artefacto adicional de atribuição de funções." border="false":::

1. **Selecione Save Draft** agora que foi atualizado.

## <a name="publish-a-blueprint"></a>Publicar um esquema

Agora que os artefactos planeados foram adicionados ao esquema, é altura de o publicar.
A publicação disponibiliza o projeto para ser atribuído a uma subscrição.

1. Selecione **definições** de Planta da página à esquerda.

1. Na lista de plantas, clique com o botão direito no que criou anteriormente e selecione **Publicar o projeto**.

1. No painel que abre, forneça uma **versão** (letras, números e hífens com um comprimento máximo de 20 caracteres), como **v1**. Opcionalmente, introduza texto em **notas Change**, como a **Primeira publicação**.

1. Selecione **Publicar** na parte inferior da página.

## <a name="assign-a-blueprint"></a>Atribuir um esquema

Depois de publicado um projeto, pode ser atribuído a uma subscrição. Atribua o projeto que criou para uma das subscrições sob a hierarquia do seu grupo de gestão. Se a planta for guardada para uma subscrição, só pode ser atribuída a essa subscrição.

1. Selecione **definições** de Planta da página à esquerda.

1. Na lista de plantas, clique com o botão direito no que criou anteriormente (ou selecione a elipse) e selecione **Designar a planta**.

1. Na página **de blueprint 'Atribuir',** na lista de entrega de **assinaturas,** selecione as subscrições para as quais pretende implementar esta planta.

   Se houver ofertas empresariais suportadas disponíveis a partir de [Azure Billing,](../../cost-management-billing/index.yml)um novo link **Create** é ativado na caixa **de Subscrição.** Siga estes passos:

   1. Selecione o novo link **Criar** uma nova subscrição em vez de selecionar as existentes.

   1. Forneça um **nome de Exibição** para a nova subscrição.

   1. Selecione a **Oferta** disponível na lista de drop-down.

   1. Utilize a elipse para selecionar o grupo de [gestão](../management-groups/overview.md) de que a subscrição será uma criança.

   1. Selecione **Criar** na parte inferior da página.

      :::image type="content" source="./media/create-blueprint-portal/assignment-create-subscription.png" alt-text="Screenshot da janela de subscrição Criar e opções para a nova subscrição." border="false":::

      > [!IMPORTANT]
      > A nova subscrição é criada imediatamente após a seleção **criar Criar**.

   > [!NOTE]
   > É criada uma atribuição para cada subscrição que selecionar. Pode escoar alterações numa única subscrição posteriormente sem forçar alterações no restante das subscrições selecionadas.

1. Para **o nome de atribuição,** forneça um nome único para esta atribuição.

1. In **Location**, selecione uma região para o objeto de implantação de identidade e subscrição gerido a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [Identidades geridas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Deixe a versão **de definição Blueprint** de versão pendente de versões **publicadas** na entrada **v1.** (O padrão é a versão mais recentemente publicada.)

1. Em **Atribuição de Bloqueio**, deixe a predefinição **Não Bloquear**. Para obter mais informações, consulte [o bloqueio de recursos da Blueprints.](./concepts/resource-locking.md)

   :::image type="content" source="./media/create-blueprint-portal/assignment-locking-mi.png" alt-text="Screenshot da atribuição de bloqueio e opções de identidade geridas para a atribuição do projeto." border="false":::

1. Sob **Identidade Gerida,** deixe o padrão do **Sistema atribuído**.

1. Para a atribuição de função ao nível da subscrição **[Grupo de utilizadores ou nome da aplicação]: Contribuidor**, procure e selecione um utilizador, uma aplicação ou um grupo.

1. Para a atribuição de política de nível de subscrição, deite **o Nome tag** para **CostCenter** e o **Valor** de Marca para **ContosoIT**.

1. Para **o ResourceGroup**, forneça um **Nome** de Contagem de **Armazenamento** e uma **localização** de **East US 2** da lista de drop-down.

   > [!NOTE]
   > Para cada artefacto que adicionou sob o grupo de recursos durante a definição de planta, esse artefacto é recuado para alinhar-se com o grupo de recursos ou objeto com o qual irá implantá-lo.
   > Os artefactos que não tomam parâmetros ou não têm parâmetros a definir na atribuição são listados apenas para informações contextuais.

1. Na contagem **de armazenamento** do modelo ARM, selecione **Standard_GRS** para o parâmetro **De armazenamentoAcolho.**

1. Leia a caixa de informações na parte inferior da página e, em seguida, selecione **Atribuir**.

## <a name="track-deployment-of-a-blueprint"></a>Controlar a implementação de um esquema

Após atribuir um esquema a uma ou mais subscrições, acontecem duas coisas:

- A planta é adicionada à página **de plantas atribuídas** para cada subscrição.
- Começa o processo de implantação de todos os artefactos definidos pela planta.

Agora que a planta foi atribuída a uma subscrição, verifique o progresso da implementação:

1. Selecione **as plantas atribuídas** da página à esquerda.

1. Na lista de plantas, clique com o botão direito no que já atribuiu e selecione **Ver detalhes da atribuição**.

   :::image type="content" source="./media/create-blueprint-portal/view-assignment-details.png" alt-text="Screenshot do menu de contexto de atribuição de plantas com a opção 'Ver detalhes da atribuição' selecionada." border="false":::

1. Na página **de atribuição do Blueprint,** valide que todos os artefactos foram implantados com sucesso e que não houve erros durante a implementação. Se ocorrerem [erros, consulte as plantas de resolução de problemas](./troubleshoot/general.md) para obter passos para determinar o que correu mal.

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="unassign-a-blueprint"></a>Anular a atribuição de um esquema

Se já não precisar de uma atribuição de planta, remova-a de uma subscrição. A planta pode ter sido substituída por uma nova planta com padrões, políticas e desenhos atualizados. Quando um esquema é removido, os artefactos atribuídos como parte desse esquema são deixados para trás. Para remover uma atribuição de esquema, siga estes passos:

1. Selecione **as plantas atribuídas** da página à esquerda.

1. Na lista de plantas, selecione a planta que pretende não assinar. Em seguida, selecione o botão **de planta de adign** no topo da página.

1. Leia a mensagem de confirmação e, em seguida, selecione **OK**.

### <a name="delete-a-blueprint"></a>Eliminar um esquema

1. Selecione **definições** de Planta da página à esquerda.

1. Clique com o botão direito na planta que pretende eliminar e selecione **Eliminar a planta**. Em seguida, selecione **Sim** na caixa de diálogo de confirmação.

> [!NOTE]
> A eliminação de uma planta neste método também elimina todas as versões publicadas da planta selecionada.
> Para eliminar uma única versão, abra a planta, selecione o separador **versões Publicado,** selecione a versão que pretende eliminar e, em seguida, **selecione Eliminar Esta Versão**. Além disso, não podes apagar uma planta até teres apagado toda a definição de planta.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criaste, atribuiste e removeste uma planta com o portal Azure. Para saber mais sobre a Azure Blueprints, continue para o artigo do ciclo de vida do projeto.

> [!div class="nextstepaction"]
> [Conheça o ciclo de vida da planta](./concepts/lifecycle.md)
