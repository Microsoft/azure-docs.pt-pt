---
title: Criar um plano gráfico no portal
description: Use plantas do Azure para criar, definir e implantar artefatos por meio do portal do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: blueprints
ms.openlocfilehash: f4dc13717b65a53b90a68ac48a2281687c157edf
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978565"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Início rápido: Definir e atribuir um plano gráfico no portal

Ao aprender a criar e atribuir plantas, você pode definir padrões comuns para desenvolver configurações reutilizáveis e implantáveis rapidamente com base em modelos de Azure Resource Manager, política, segurança e muito mais. Neste tutorial, você aprenderá a usar os planos gráficos do Azure para realizar algumas das tarefas comuns relacionadas à criação, publicação e atribuição de um plano gráfico em sua organização. Essas tarefas incluem:

> [!div class="checklist"]
> - Criar um novo esquema e adicionar vários artefactos suportados
> - Fazer alterações a um esquema existente ainda no modo de **Rascunho**
> - Marcar um esquema como pronto a atribuir com o estado **Publicado**
> - Atribuir um esquema a uma subscrição existente
> - Verificar o estado e progresso de um esquema atribuído
> - Remover um esquema que foi atribuído a uma subscrição

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-a-blueprint"></a>Criar um esquema

O primeiro passo na definição de um padrão de conformidade é compor um esquema a partir dos recursos disponíveis. Neste exemplo, crie um novo plano gráfico chamado **myblueprint** para configurar atribuições de função e política para a assinatura. Em seguida, adicione um novo grupo de recursos e crie um modelo do Resource Manager e uma atribuição de função no novo grupo de recursos.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione **definições de plantas** na página à esquerda e selecione o botão **+ criar Blueprint** na parte superior da página.

   Ou então, selecione **criar** na página de **introdução** para ir direto para a criação de um plano gráfico.

   ![Criar um plano gráfico na página Definições de plantas](./media/create-blueprint-portal/create-blueprint-button.png)

1. Forneça um **nome de plano gráfico** , como **myblueprint**. (Use até 48 letras e números, mas sem espaços ou caracteres especiais). Deixe a **Descrição do plano gráfico** em branco por enquanto.

1. Na caixa **local da definição** , selecione as reticências à direita, selecione o [grupo de gerenciamento](../management-groups/overview.md) ou a assinatura em que você deseja salvar o plano gráfico e escolha **selecionar**.

1. Verifique se as informações estão corretas. Os campos **nome do plano gráfico** e **local de definição** não podem ser alterados posteriormente. Em seguida, selecione **Next: Artefatos @ no__t-0 na parte inferior da página ou na guia **artefatos** na parte superior da página.

1. Adicione uma atribuição de função no nível de assinatura:

   1. Selecione a linha **+ Adicionar artefato** em **assinatura**. A janela **Adicionar artefato** é aberta no lado direito do navegador.

   1. Selecione **atribuição de função** para **tipo de artefato**.

   1. Em **função**, selecione **colaborador**. Deixe a caixa **Adicionar usuário, aplicativo ou grupo** com a caixa de seleção que indica um parâmetro dinâmico.

   1. Selecione **Adicionar** para adicionar este artefato ao plano gráfico.

   ![Atribuição de função para um artefato do Blueprint](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > A maioria dos artefatos dá suporte a parâmetros. Um parâmetro que é atribuído a um valor durante a criação do Blueprint é um *parâmetro estático*. Se o parâmetro for atribuído durante a atribuição de Blueprint, será um *parâmetro dinâmico*. Para obter mais informações, veja [Parâmetros de esquema](./concepts/parameters.md).

1. Adicione uma atribuição de política no nível de assinatura:

   1. Selecione a linha **+ Adicionar artefato** no artefato de atribuição de função.

   1. Selecione **atribuição de política** para **tipo de artefato**.

   1. Altere o **tipo** para **interno**. Em **Pesquisar**, insira a **marca**.

   1. Clique fora da caixa **Pesquisar** para filtrar os resultados. Selecione **acrescentar marca e seu valor padrão para grupos de recursos**.

   1. Selecione **Adicionar** para adicionar este artefato ao plano gráfico.

1. Selecione a linha da marca de acréscimo de atribuição de política **e seu valor padrão para grupos de recursos**.

1. A janela para fornecer parâmetros para o artefato como parte da definição do Blueprint é aberta e permite definir os parâmetros de todas as atribuições (parâmetros estáticos) com base neste projeto, em vez de durante a atribuição (parâmetros dinâmicos). Este exemplo usa parâmetros dinâmicos durante a atribuição de Blueprint, portanto, deixe os padrões e selecione **Cancelar**.

1. Adicione um grupo de recursos no nível da assinatura:

   1. Selecione a linha **+ Adicionar artefato** em **assinatura**.

   1. Selecione **grupo de recursos** para **tipo de artefato**.

   1. Deixe o **nome de exibição do artefato**, o **nome do grupo de recursos**e as caixas de **local** em branco, mas verifique se a caixa de seleção está marcada para cada propriedade de parâmetro para torná-los parâmetros dinâmicos.

   1. Selecione **Adicionar** para adicionar este artefato ao plano gráfico.

1. Adicione um modelo sob o grupo de recursos:

   1. Selecione a linha **+ Adicionar artefato** na entrada do **resourcegroup** .

   1. Selecione **Azure Resource Manager modelo** para **tipo de artefato**, defina **nome de exibição do artefato** como **StorageAccount**e deixe a **Descrição** em branco.

   1. No separador **Modelo** na caixa do editor, cole o seguinte modelo do Resource Manager.
      Depois de colar o modelo, selecione a guia **parâmetros** e observe que os parâmetros de modelo **storageAccountType** e **local** foram detectados. Cada parâmetro foi detectado e preenchido automaticamente, mas configurado como um parâmetro dinâmico.

      > [!IMPORTANT]
      > Se você estiver importando o modelo, certifique-se de que o arquivo é apenas JSON e não inclui HTML. Quando você estiver apontando para uma URL no GitHub, verifique se selecionou **RAW** para obter o arquivo JSON puro e não aquele encapsulado com HTML para exibição no github. Se o modelo importado não for JSON puro, ocorrerá um erro.

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

   1. Desmarque a caixa de seleção **storageAccountType** e observe que a lista suspensa contém apenas os valores incluídos no modelo do Resource Manager em **allowedValues**. Selecione a caixa para defini-la de volta para um parâmetro dinâmico.

   1. Selecione **Adicionar** para adicionar este artefato ao plano gráfico.

   ![Modelo do Resource Manager para o artefato do Blueprint](./media/create-blueprint-portal/add-resource-manager-template.png)

1. O esquema concluído deve ter um aspeto semelhante ao seguinte. Observe que cada artefato tem  **_x_ de parâmetros _y_ preenchidos** na coluna **parâmetros** . Os parâmetros dinâmicos são definidos durante cada atribuição do plano gráfico.

   ![Definição de Blueprint concluída](./media/create-blueprint-portal/completed-blueprint.png)

1. Agora que todos os artefatos planejados foram adicionados, selecione **salvar rascunho** na parte inferior da página.

## <a name="edit-a-blueprint"></a>Editar um esquema

Em [criar um plano gráfico](#create-a-blueprint), você não forneceu uma descrição ou adicionou a atribuição de função ao novo grupo de recursos. Você pode corrigir ambos seguindo estas etapas:

1. Selecione **definições de plantas** na página à esquerda.

1. Na lista de plantas, clique com o botão direito do mouse no que você criou anteriormente e selecione **Editar plano gráfico**.

1. Em **Descrição do plano gráfico**, forneça algumas informações sobre o plano gráfico e os artefatos que a compõem. Nesse caso, insira algo como: **Este projeto define a política de marca e a atribuição de função na assinatura, cria um resourcegroup e implanta um modelo de recurso e uma atribuição de função para esse resourcegroup.**

1. Selecione **Next: Artefatos @ no__t-0 na parte inferior da página ou na guia **artefatos** na parte superior da página.

1. Adicione uma atribuição de função sob o grupo de recursos:

   1. Selecione a linha **+ Adicionar artefato** diretamente sob a entrada **resourcegroup** .

   1. Selecione **atribuição de função** para **tipo de artefato**.

   1. Em **função**, selecione **proprietário**e desmarque a caixa de seleção na caixa **Adicionar usuário, aplicativo ou grupo** .

   1. Pesquise e selecione um usuário, aplicativo ou grupo para adicionar. Esse artefato usa um parâmetro estático definido da mesma forma em todas as atribuições deste projeto.

   e. Selecione **Adicionar** para adicionar este artefato ao plano gráfico.

   ![Segunda atribuição de função para o artefato do Blueprint](./media/create-blueprint-portal/add-role-assignment-2.png)

1. O esquema concluído deve ter um aspeto semelhante ao seguinte. Observe que a atribuição de função recém-adicionada mostra **1 de 1 parâmetro preenchido**. Isso significa que é um parâmetro estático.

   ![Segunda definição para o plano gráfico concluído](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Selecione **salvar rascunho** agora que ele foi atualizado.

## <a name="publish-a-blueprint"></a>Publicar um esquema

Agora que os artefactos planeados foram adicionados ao esquema, é altura de o publicar.
A publicação torna o Blueprint disponível para ser atribuído a uma assinatura.

1. Selecione **definições de plantas** na página à esquerda.

1. Na lista de plantas, clique com o botão direito do mouse no que você criou anteriormente e selecione **publicar plano gráfico**.

1. No painel que é aberto, forneça uma **versão** (letras, números e hifens com um comprimento máximo de 20 caracteres), como **v1**. Opcionalmente, digite o texto em **anotações de alteração**, como **primeira publicação**.

1. Selecione **publicar** na parte inferior da página.

## <a name="assign-a-blueprint"></a>Atribuir um esquema

Depois que um plano gráfico tiver sido publicado, ele poderá ser atribuído a uma assinatura. Atribua o plano gráfico que você criou a uma das assinaturas na hierarquia do grupo de gerenciamento. Se o plano gráfico for salvo em uma assinatura, ele só poderá ser atribuído a essa assinatura.

1. Selecione **definições de plantas** na página à esquerda.

1. Na lista de plantas, clique com o botão direito do mouse no que você criou anteriormente (ou selecione as reticências) e selecione **atribuir plano gráfico**.

1. Na página **atribuir Blueprint** , na lista suspensa **assinatura** , selecione as assinaturas para as quais você deseja implantar esse projeto.

   Se houver ofertas da empresa com suporte disponíveis na [cobrança do Azure](../../billing/index.md), um link **criar novo** será ativado na caixa **assinatura** . Siga estes passos.

   1. Selecione o link **criar novo** para criar uma nova assinatura em vez de selecionar as existentes.

   1. Forneça um **nome de exibição** para a nova assinatura.

   1. Selecione a **oferta** disponível na lista suspensa.

   1. Use as reticências para selecionar o [grupo de gerenciamento](../management-groups/overview.md) do qual a assinatura será filha.

   1. Selecione **criar** na parte inferior da página.

   ![Criar uma assinatura para uma assinatura de atribuição do Blueprint](./media/create-blueprint-portal/assignment-create-subscription.png)

   > [!IMPORTANT]
   > A nova assinatura é criada imediatamente depois que você seleciona **criar**.

   > [!NOTE]
   > Uma atribuição é criada para cada assinatura que você selecionar. Você pode fazer alterações em uma única atribuição de assinatura em um momento posterior sem forçar alterações no restante das assinaturas selecionadas.

1. Para **nome da atribuição**, forneça um nome exclusivo para essa atribuição.

1. Em **local**, selecione uma região para a identidade gerenciada e o objeto de implantação de assinatura a ser criado no. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [Identidades geridas para os recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Deixe a seleção da lista suspensa **versão de definição do Blueprint** de versões **publicadas** na entrada **v1** . (O padrão é a versão publicada mais recentemente.)

1. Em **Atribuição de Bloqueio**, deixe a predefinição **Não Bloquear**. Para saber mais, confira [bloqueio de recursos de planos gráficos](./concepts/resource-locking.md).

   ![Bloqueio e identidades gerenciadas para a atribuição](./media/create-blueprint-portal/assignment-locking-mi.png)

1. Em **identidade gerenciada**, deixe o padrão **atribuído pelo sistema**.

1. Para a atribuição de função de nível de assinatura ** [nome do aplicativo ou grupo de usuários]: Colaborador @ no__t-0, procure e selecione um usuário, aplicativo ou grupo.

1. Para a atribuição de política de nível de assinatura, defina o **nome da marca** como **CostCenter** e o **valor da marca** como **ContosoIT**.

1. Para **resourcegroup**, forneça um **nome** de **StorageAccount** e um **local** de leste dos **EUA 2** na lista suspensa.

   > [!NOTE]
   > Para cada artefato que você adicionou no grupo de recursos durante a definição do Blueprint, esse artefato é recuado para alinhar-se com o grupo de recursos ou objeto com o qual você o implantará.
   > Os artefatos que não usam parâmetros ou não têm parâmetros a serem definidos na atribuição são listados apenas para informações contextuais.

1. No modelo de Azure Resource Manager **StorageAccount**, selecione **Standard_GRS** para o parâmetro **storageAccountType** .

1. Leia a caixa de informações na parte inferior da página e selecione **atribuir**.

## <a name="track-deployment-of-a-blueprint"></a>Controlar a implementação de um esquema

Após atribuir um esquema a uma ou mais subscrições, acontecem duas coisas:

- O plano gráfico é adicionado à página **plantas atribuídas** para cada assinatura.
- O processo de implantação de todos os artefatos definidos pelo plano gráfico começa.

Agora que o plano gráfico foi atribuído a uma assinatura, verifique o progresso da implantação:

1. Selecione **plantas atribuídas** na página à esquerda.

1. Na lista de plantas, clique com o botão direito do mouse no que você atribuiu anteriormente e selecione **Exibir detalhes da atribuição**.

   ![Exibir detalhes de atribuição da página plantas atribuídas](./media/create-blueprint-portal/view-assignment-details.png)

1. Na página **atribuição de Blueprint** , valide se todos os artefatos foram implantados com êxito e se não houve erros durante a implantação. Se ocorrerem erros, consulte [plantas de solução de problemas](./troubleshoot/general.md) para obter as etapas para determinar o que deu errado.

## <a name="unassign-a-blueprint"></a>Anular a atribuição de um esquema

Se você não precisar mais de uma atribuição de plano gráfico, remova-a de uma assinatura. O plano gráfico pode ter sido substituído por um plano gráfico mais recente com padrões, políticas e designs atualizados. Quando um esquema é removido, os artefactos atribuídos como parte desse esquema são deixados para trás. Para remover uma atribuição de esquema, siga estes passos:

1. Selecione **plantas atribuídas** na página à esquerda.

1. Na lista de plantas, selecione o plano gráfico que você deseja cancelar a atribuição. Em seguida, selecione o botão **desatribuir Blueprint** na parte superior da página.

1. Leia a mensagem de confirmação e selecione **OK**.

## <a name="delete-a-blueprint"></a>Eliminar um esquema

1. Selecione **definições de plantas** na página à esquerda.

1. Clique com o botão direito do mouse no plano gráfico que você deseja excluir e selecione **excluir plano gráfico**. Em seguida, selecione **Sim** na caixa de diálogo de confirmação.

> [!NOTE]
> A exclusão de um plano gráfico nesse método também exclui todas as versões publicadas do plano gráfico selecionado.
> Para excluir uma única versão, abra o plano gráfico, selecione a guia **versões publicadas** , selecione a versão que você deseja excluir e, em seguida, selecione **excluir esta versão**. Além disso, não é possível excluir um plano gráfico até que você tenha excluído toda a atribuição Blueprint da definição do Blueprint.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](./concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](./concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](./concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](./concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](./how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](./troubleshoot/general.md).