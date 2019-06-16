---
title: Criar um plano gráfico no portal
description: Utilize planos gráficos do Azure para criar, definir e implementar artefactos através do portal do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 97d0615fcc4f86c295441ec6a0eb8b86ba6b11b5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059709"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Início rápido: Definir e atribuir um plano gráfico no portal

Quando aprender a criar e atribuir esquemas, pode definir padrões comuns para desenvolver as configurações de reutilizáveis e implantáveis rapidamente com base no Azure Resource Manager modelos, políticas, segurança e muito mais. Neste tutorial, vai aprender a utilizar o Azure esquemas para fazer algumas das tarefas comuns relacionadas com a criação, publicação e atribuir um plano gráfico dentro da sua organização. Essas tarefas incluem:

> [!div class="checklist"]
> - Criar um novo esquema e adicionar vários artefactos suportados
> - Fazer alterações a um esquema existente ainda no modo de **Rascunho**
> - Marcar um esquema como pronto a atribuir com o estado **Publicado**
> - Atribuir um esquema a uma subscrição existente
> - Verificar o estado e progresso de um esquema atribuído
> - Remover um esquema que foi atribuído a uma subscrição

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-a-blueprint"></a>Criar um esquema

O primeiro passo na definição de um padrão de conformidade é compor um esquema a partir dos recursos disponíveis. Neste exemplo, criar um novo plano de gráfico com o nome **MyBlueprint** para configurar as atribuições de função e a política para a subscrição. Em seguida, adicionar um novo grupo de recursos e criar uma atribuição de função e o modelo do Resource Manager no novo grupo de recursos.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione **definições de esquema** da página à esquerda e selecione o **+ criar esquema** botão na parte superior da página.

   Em alternativa, selecione **Create** da **introdução** página para ir diretamente para a criação de um plano gráfico.

   ![Criar um plano gráfico a partir da página de definições de esquema](./media/create-blueprint-portal/create-blueprint-button.png)

1. Fornecer um **nome do esquema** como **MyBlueprint**. (Utilize até 48 letras e números, mas sem espaços ou carateres especiais). Deixe **esquema Descrição** em branco por agora.

1. Na **localização da definição** caixa, selecione as reticências à direita, selecione a [grupo de gestão](../management-groups/overview.md) ou uma subscrição onde pretende guardar o plano gráfico e escolha **selecione**.

1. Certifique-se de que as informações estão corretas. O **nome do esquema** e **localização da definição** campos não não possível alterar mais tarde. Em seguida, selecione **seguinte: Artefactos** na parte inferior da página ou o **artefactos** separador na parte superior da página.

1. Adicione uma atribuição de função ao nível da subscrição:

   1. Selecione o **+ adicionar artefacto** linha sob **subscrição**. O **adicionar artefacto** é aberta a janela à direita do navegador.

   1. Selecione **atribuição de função** para **tipo de Artefato**.

   1. Sob **função**, selecione **contribuinte**. Deixe o **adicionar utilizador, aplicação ou grupo** caixa de com a caixa de verificação que indica um parâmetro dinâmico.

   1. Selecione **adicionar** para adicionar este artefacto ao esquema.

   ![Atribuição de função para um artefacto de esquema](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > A maioria dos artefatos suportam parâmetros. Um parâmetro que tenha atribuído um valor durante a criação de esquema é uma *parâmetro estático*. Se o parâmetro é atribuído durante a atribuição do esquema, é um *parâmetro dinâmico*. Para obter mais informações, veja [Parâmetros de esquema](./concepts/parameters.md).

1. Adicione uma atribuição de política ao nível da subscrição:

   1. Selecione o **+ adicionar artefacto** linha sob o artefacto de atribuição de função.

   1. Selecione **atribuição de política** para **tipo de Artefato**.

   1. Alteração **tipo** ao **incorporadas**. Na **pesquisa**, introduza **marca**.

   1. Clique fora da caixa **Pesquisar** para filtrar os resultados. Selecione **acrescentar etiqueta e respetivo valor predefinido para grupos de recursos**.

   1. Selecione **adicionar** para adicionar este artefacto ao esquema.

1. Selecione a linha de atribuição de política **acrescentar etiqueta e respetivo valor predefinido para grupos de recursos**.

1. A janela para fornecer os parâmetros para o artefacto como parte da definição do esquema é aberto e permite a definição de parâmetros para todas as atribuições (parâmetros estáticos) com base nesse plano gráfico, em vez de durante a atribuição (parâmetros dinâmicos). Este exemplo utiliza os parâmetros dinâmicos durante a atribuição do esquema, então, deixe as predefinições e selecione **Cancelar**.

1. Adicione um grupo de recursos ao nível da subscrição:

   1. Selecione o **+ adicionar artefacto** linha sob **subscrição**.

   1. Selecione **grupo de recursos** para **tipo de Artefato**.

   1. Deixe o **nome a apresentar do artefacto**, **nome do grupo de recursos**, e **localização** caixas em branco, mas certifique-se de que a caixa de verificação está selecionada para cada propriedade de parâmetro para torná-las parâmetros dinâmicos.

   1. Selecione **adicionar** para adicionar este artefacto ao esquema.

1. Adicione um modelo no grupo de recursos:

   1. Selecione o **+ adicionar artefacto** linha sob o **ResourceGroup** entrada.

   1. Selecione **modelo Azure Resource Manager** para **tipo de Artefato**, defina **nome a apresentar do artefacto** para **StorageAccount**e deixe  **Descrição** em branco.

   1. No separador **Modelo** na caixa do editor, cole o seguinte modelo do Resource Manager.
      Depois de o colar o modelo, selecione o **parâmetros** separador e tenha em atenção que os parâmetros de modelo **storageAccountType** e **localização** foram detetados. Cada parâmetro foi automaticamente detectado e preenchido, mas configurado como um parâmetro dinâmico.

      > [!IMPORTANT]
      > Se estiver a importar o modelo, certifique-se de que o arquivo é apenas um JSON e não inclui o HTML. Quando está a apontar para um URL no GitHub, certifique-se de que selecionou **RAW** para obter o ficheiro JSON puro e não um inseridos em HTML para exibição no GitHub. Se o modelo importado não for JSON puro, ocorrerá um erro.

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

   1. Limpar o **storageAccountType** caixa de verificação e tenha em atenção que a lista pendente contém apenas os valores incluídos no modelo do Resource Manager sob **allowedValues**. Selecione a caixa para defini-lo novamente como um parâmetro dinâmico.

   1. Selecione **adicionar** para adicionar este artefacto ao esquema.

   ![Modelo do Resource Manager para o artefacto de esquema](./media/create-blueprint-portal/add-resource-manager-template.png)

1. O esquema concluído deve ter um aspeto semelhante ao seguinte. Tenha em atenção que tem de cada artefato  **_x_ de _y_ parâmetros preenchidos** no **parâmetros** coluna. Os parâmetros dinâmicos são definidos durante cada atribuição do esquema.

   ![Definição do esquema concluída](./media/create-blueprint-portal/completed-blueprint.png)

1. Agora que tiverem sido adicionados todos os artefactos planeados, selecione **Guardar rascunho** na parte inferior da página.

## <a name="edit-a-blueprint"></a>Editar um esquema

Na [criar um plano gráfico](#create-a-blueprint), não forneça uma descrição ou adicionar a atribuição de função para o novo grupo de recursos. Pode corrigir os dois ao seguir estes passos:

1. Selecione **definições de esquema** partir da página à esquerda.

1. Na lista de esquemas, com o botão direito aquele que criou anteriormente e selecione **esquema de edição**.

1. Na **esquema Descrição**, fornecer algumas informações sobre o esquema e os artefatos que compõem ele. Neste caso, insira algo parecido com: **Este esquema define a atribuição de política e a função de etiquetas na subscrição, cria um ResourceGroup e implementa uma atribuição de função e o modelo de recurso para esse ResourceGroup.**

1. Selecione **seguinte: Artefactos** na parte inferior da página ou o **artefactos** separador na parte superior da página.

1. Adicione uma atribuição de função sob o grupo de recursos:

   1. Selecione o **+ adicionar artefacto** linha diretamente sob o **ResourceGroup** entrada.

   1. Selecione **atribuição de função** para **tipo de Artefato**.

   1. Sob **função**, selecione **proprietário**e desmarque a caixa de verificação sob o **adicionar utilizador, aplicação ou grupo** caixa.

   1. Procure e selecione um utilizador, aplicação ou grupo a adicionar. Este artefacto utiliza um conjunto de parâmetros estáticos as mesmas em cada atribuição deste esquema.

   e. Selecione **adicionar** para adicionar este artefacto ao esquema.

   ![Segundo atribuição de função para o artefacto de esquema](./media/create-blueprint-portal/add-role-assignment-2.png)

1. O esquema concluído deve ter um aspeto semelhante ao seguinte. Tenha em atenção que a atribuição de função recém-adicionada mostra **parâmetros de 1 de 1 preenchidos**. Isso significa que é um parâmetro estático.

   ![Segunda definição para o esquema concluída](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Selecione **Guardar rascunho** agora que ele foi atualizado.

## <a name="publish-a-blueprint"></a>Publicar um esquema

Agora que os artefactos planeados foram adicionados ao esquema, é altura de o publicar.
Publicação faz com que o esquema disponível para serem atribuídas a uma subscrição.

1. Selecione **definições de esquema** partir da página à esquerda.

1. Na lista de esquemas, com o botão direito aquele que criou anteriormente e selecione **publicar esquema**.

1. No painel que se abre, fornecer uma **versão** (letras, números e hífenes com um comprimento máximo de 20 carateres), tal como **v1**. Opcionalmente, introduza o texto no **alterar notas**, tal como **primeiro publicar**.

1. Selecione **publicar** na parte inferior da página.

## <a name="assign-a-blueprint"></a>Atribuir um esquema

Depois de um plano gráfico foi publicado, pode ser atribuída a uma subscrição. Atribua o esquema que criou para uma das subscrições na hierarquia de grupo de gestão. Se o plano gráfico é guardado para uma subscrição, só podem ser atribuído a essa subscrição.

1. Selecione **definições de esquema** partir da página à esquerda.

1. Na lista de esquemas, com o botão direito aquele que criou anteriormente (ou selecione as reticências) e selecione **esquema de atribuir**.

1. No **atribuir esquema** página, além do **subscrição** na lista pendente, selecione as subscrições que pretende implementar esse plano gráfico para.

   Se existirem suportadas Enterprise ofertas disponíveis a partir [faturação do Azure](../../billing/index.md), uma **criar nova** ligação está ativada no **subscrição** caixa. Siga estes passos.

   1. Selecione o **criar novo** ligação para criar uma nova subscrição em vez de selecionar os existentes.

   1. Fornecer um **nome a apresentar** para a nova subscrição.

   1. Selecione o disponíveis **oferecem** na lista pendente.

   1. Utilize o botão de reticências para selecionar o [grupo de gestão](../management-groups/index.md) que a subscrição será um filho do.

   1. Selecione **criar** na parte inferior da página.

   ![Criar uma subscrição para uma subscrição de atribuição do esquema](./media/create-blueprint-portal/assignment-create-subscription.png)

   > [!IMPORTANT]
   > A nova subscrição é criada imediatamente depois de selecionar **criar**.

   > [!NOTE]
   > Uma atribuição é criada para cada subscrição que selecionou. Pode efetuar alterações à atribuição única subscrição numa altura posterior sem forçar alterações no restante das subscrições selecionadas.

1. Para **nome da atribuição**, forneça um nome exclusivo para esta atribuição.

1. Na **localização**, selecione uma região para o objeto de implementação de identidade e subscrição gerenciado a ser criada no. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [Identidades geridas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Deixe o **versão da definição do esquema** seleção de lista pendente de **publicado** versões no **v1** entrada. (A predefinição é a versão publicada mais recentemente.)

1. Em **Atribuição de Bloqueio**, deixe a predefinição **Não Bloquear**. Para obter mais informações, consulte [bloqueio de recursos de planos gráficos](./concepts/resource-locking.md).

   ![Identidades de bloqueios e geridas para a atribuição](./media/create-blueprint-portal/assignment-locking-mi.png)

1. Sob **identidade gerido**, deixe a predefinição **sistema atribuído**.

1. Para a atribuição de função de nível de subscrição **[grupo de utilizadores ou o nome da aplicação]: Contribuinte**, procure e selecione um utilizador, aplicação ou grupo.

1. Para a atribuição de política de nível de subscrição, defina **nome da etiqueta** ao **Centrodecustos** e o **valor de etiqueta** para **ContosoIT**.

1. Para **ResourceGroup**, forneça um **nome** de **StorageAccount** e uma **localização** de **E.U.A. Leste 2** do a lista pendente.

   > [!NOTE]
   > Para cada artefato que adicionou no grupo de recursos durante a definição do esquema, esse artefato é recuado para alinhar com o grupo de recursos ou o objeto que irá implantá-la com.
   > Artefatos que não aceitam parâmetros ou não ter parâmetros definidos na atribuição são listados apenas para informações contextuais.

1. No modelo do Azure Resource Manager **StorageAccount**, selecione **Standard_GRS** para o **storageAccountType** parâmetro.

1. Leia a caixa de informações na parte inferior da página e, em seguida, selecione **atribuir**.

## <a name="track-deployment-of-a-blueprint"></a>Controlar a implementação de um esquema

Após atribuir um esquema a uma ou mais subscrições, acontecem duas coisas:

- O plano gráfico é adicionado para o **atribuído a planos gráficos** página para cada subscrição.
- O processo de implantação de todos os artefactos definidos pelo plano gráfico começa.

Agora que o esquema tiver sido atribuído a uma subscrição, verifique se o progresso da implementação:

1. Selecione **atribuído a planos gráficos** partir da página à esquerda.

1. Na lista de esquemas, com o botão direito aquele que atribuído anteriormente e selecione **ver os detalhes de atribuição**.

   ![Ver detalhes de atribuição da página de planos gráficos de atribuído](./media/create-blueprint-portal/view-assignment-details.png)

1. Sobre o **esquema atribuição** página, confirme que todos os artefactos foram implementados com êxito e que não havia nenhum erro durante a implementação. Se ocorreram erros, veja [planos gráficos de resolução de problemas](./troubleshoot/general.md) para obter os passos determinar o que correu mal.

## <a name="unassign-a-blueprint"></a>Anular a atribuição de um esquema

Se já não precisar de uma atribuição do esquema, removê-lo a partir de uma subscrição. O plano gráfico pode foram substituído por uma esquema mais recente com padrões atualizadas, políticas e designs. Quando um esquema é removido, os artefactos atribuídos como parte desse esquema são deixados para trás. Para remover uma atribuição de esquema, siga estes passos:

1. Selecione **atribuído a planos gráficos** partir da página à esquerda.

1. Na lista de esquemas, selecione o plano gráfico de que pretende anular a atribuição. Em seguida, selecione o **Unassign esquema** botão na parte superior da página.

1. Leia a mensagem de confirmação e, em seguida, selecione **OK**.

## <a name="delete-a-blueprint"></a>Eliminar um esquema

1. Selecione **definições de esquema** partir da página à esquerda.

1. Com o plano gráfico de que pretende eliminar e selecione o botão direito **esquema de eliminação**. Em seguida, selecione **Sim** na caixa de diálogo de confirmação.

> [!NOTE]
> Eliminar um plano gráfico neste método também elimina a todas as versões publicadas do esquema selecionado.
> Para eliminar uma única versão, abra o plano gráfico, selecione o **versões publicadas** separador, selecione a versão que pretende eliminar e, em seguida, selecione **eliminar esta versão**. Além disso, não é possível eliminar um plano gráfico até que excluiu todos da atribuição do esquema de definição do esquema.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [ciclo de vida de esquema](./concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](./concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](./concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](./concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](./how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](./troubleshoot/general.md).