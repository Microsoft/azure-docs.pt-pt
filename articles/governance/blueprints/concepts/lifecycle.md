---
title: Compreender o ciclo de vida de um esquema
description: Saiba mais sobre o ciclo de vida que uma definição do plano gráfico passa e detalhes sobre cada estágio, incluindo a atualização e a remoção de atribuições de plano gráfico.
ms.date: 07/30/2019
ms.topic: conceptual
ms.openlocfilehash: 4dd5cb7d085744377cf12998f14c994fb1dcd2d7
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404580"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Entender o ciclo de vida de um Azure Blueprint

Como muitos recursos no Azure, um plano gráfico nas plantas do Azure tem um ciclo de vida típico e natural. Eles são criados, implantados e, por fim, excluídos quando não forem mais necessários ou relevantes.
Os planos gráficos oferecem suporte a operações de ciclo de vida padrão. Em seguida, ele se baseia neles para fornecer níveis adicionais de status que dão suporte a integração contínua comum e a pipelines de implantação contínua para organizações que gerenciam sua infraestrutura como código – um elemento-chave no DevOps.

Para entender totalmente um plano gráfico e os estágios, abordaremos um ciclo de vida padrão:

> [!div class="checklist"]
> - Criando e editando um plano gráfico
> - Publicando o Blueprint
> - Criando e editando uma nova versão do Blueprint
> - Publicando uma nova versão do Blueprint
> - Excluindo uma versão específica do Blueprint
> - Excluindo o plano gráfico

## <a name="creating-and-editing-a-blueprint"></a>Criando e editando um plano gráfico

Ao criar um plano gráfico, adicione artefatos a ele, salve em um grupo de gerenciamento ou assinatura e forneceu um nome exclusivo e uma versão exclusiva. O plano gráfico agora está em um modo de **rascunho** e ainda não pode ser atribuído. No modo de **rascunho** , ele pode continuar a ser atualizado e alterado.

Um plano gráfico nunca publicado no modo de **rascunho** exibe um ícone diferente na página **definições do plano gráfico** do que os que foram **publicados**. A **versão mais recente** é exibida como **rascunho** para essas nunca publicadas.

Crie e edite um plano gráfico com o [portal do Azure](../create-blueprint-portal.md#create-a-blueprint) ou a [API REST](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publicando um plano gráfico

Depois que todas as alterações planejadas forem feitas em um plano gráfico no modo de **rascunho** , elas poderão ser **publicadas** e disponibilizadas para atribuição. A versão **publicada** do Blueprint não pode ser alterada. Depois de **publicado**, o plano gráfico é exibido com um ícone diferente de planos gráficos de **rascunho** e exibe o número de versão fornecido na coluna **versão mais recente** .

Publicar um plano gráfico com o [portal do Azure](../create-blueprint-portal.md#publish-a-blueprint) ou a [API REST](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Criando e editando uma nova versão do Blueprint

Uma versão **publicada** de um plano gráfico não pode ser alterada. No entanto, uma nova versão do Blueprint pode ser adicionada ao plano gráfico existente e modificada conforme necessário. Faça alterações em um plano gráfico existente editando-o. Quando as novas alterações são salvas, o plano gráfico agora tem **alterações não publicadas**. Essas alterações são uma nova versão de **rascunho** do plano gráfico.

Edite um plano gráfico com o [portal do Azure](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publicando uma nova versão do Blueprint

Cada versão editada de um plano gráfico deve ser **publicada** antes que possa ser atribuída. Quando foram feitas **alterações não publicadas** em um plano gráfico, mas não **publicadas**, o botão **publicar Blueprint** está disponível na página Editar Blueprint. Se o botão não estiver visível, o plano gráfico já foi **publicado** e não tem **alterações não publicadas**.

> [!NOTE]
> Um único Blueprint pode ter várias versões **publicadas** que podem ser atribuídas a assinaturas.

Para publicar um plano gráfico com **alterações não publicadas**, use as mesmas etapas para publicar um novo plano gráfico.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Excluindo uma versão específica do Blueprint

Cada versão de um plano gráfico é um objeto exclusivo e pode ser **publicada**individualmente. Assim, cada versão de um plano gráfico também pode ser excluída. A exclusão de uma versão de um plano gráfico não tem nenhum impacto sobre outras versões do plano gráfico.

> [!NOTE]
> Não é possível excluir um plano gráfico com atribuições ativas. Exclua as atribuições primeiro e, em seguida, exclua a versão que você deseja remover.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione **definições de plano gráfico** na página à esquerda e use as opções de filtro para localizar o plano gráfico para o qual você deseja excluir uma versão. Selecione-o para abrir a página Editar.

1. Selecione a guia **versões publicadas** e localize a versão que você deseja excluir.

1. Clique com o botão direito do mouse na versão a ser excluída e selecione **excluir esta versão**.

## <a name="deleting-the-blueprint"></a>Excluindo o plano gráfico

O plano gráfico principal também pode ser excluído. A exclusão do principal Blueprint também exclui todas as versões do Blueprint do plano gráfico, incluindo plantas de **rascunho** e **publicadas** . Assim como a exclusão de uma versão de um plano gráfico, a exclusão do plano gráfico principal não remove as atribuições existentes de nenhuma das versões do Blueprint.

> [!NOTE]
> Não é possível excluir um plano gráfico com atribuições ativas. Exclua as atribuições primeiro e, em seguida, exclua a versão que você deseja remover.

Exclua um plano gráfico com o [portal do Azure](../create-blueprint-portal.md#delete-a-blueprint) ou a [API REST](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Atribuições

Há vários pontos durante o ciclo de vida em que um plano gráfico pode ser atribuído a uma assinatura. Quando o modo de uma versão do plano gráfico é **publicado**, essa versão pode ser atribuída a uma assinatura. Esse ciclo de vida permite que versões de um plano gráfico sejam usadas e atribuídas ativamente enquanto uma versão mais recente está sendo desenvolvida.

À medida que as versões de plantas são atribuídas, é importante entender onde elas são atribuídas e com quais parâmetros elas foram atribuídas. Os parâmetros podem ser estáticos ou dinâmicos. Para saber mais, confira [parâmetros estáticos e dinâmicos](parameters.md).

### <a name="updating-assignments"></a>Atualizando atribuições

Quando um plano gráfico é atribuído, a atribuição pode ser atualizada. Há várias razões para atualizar uma atribuição existente, incluindo:

- Adicionar ou remover [bloqueio de recursos](resource-locking.md)
- Alterar o valor de [parâmetros dinâmicos](parameters.md#dynamic-parameters)
- Atualizar a atribuição para uma versão **publicada** mais recente do Blueprint

Para saber como, consulte [Atualizar atribuições existentes](../how-to/update-existing-assignments.md).

### <a name="unassigning-assignments"></a>Cancelando atribuição de atribuições

Se o plano gráfico não for mais necessário, ele poderá não ser atribuído do grupo de gerenciamento ou da assinatura. Durante a desatribuição do plano gráfico, ocorre o seguinte:

- Remoção do [bloqueio de recursos do Blueprint](resource-locking.md)
- Exclusão do objeto de atribuição Blueprint
- Condiciona Se uma **identidade gerenciada atribuída pelo sistema** foi usada, ela também será excluída

> [!NOTE]
> Todos os recursos implantados pela atribuição Blueprint permanecem em vigor, mas não são mais protegidos pelas plantas do Azure.

## <a name="next-steps"></a>Passos seguintes

- Compreenda como utilizar [parâmetros estáticos e dinâmicos](parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).