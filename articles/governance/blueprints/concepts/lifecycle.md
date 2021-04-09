---
title: Compreender o ciclo de vida de uma planta
description: Conheça o ciclo de vida que uma definição de planta passa e detalhes sobre cada etapa, incluindo atualizar e remover atribuições de projeto.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 721a198b5732af01c6712e86bc0c8e8ef543b404
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98918522"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Compreenda o ciclo de vida de uma Planta Azure

Como muitos recursos dentro de Azure, uma planta em Azure Blueprints tem um ciclo de vida típico e natural. São criados, implantados e finalmente apagados quando já não são necessários ou relevantes. A Azure Blueprints suporta operações padrão de ciclo de vida. Baseia-se então neles para fornecer níveis adicionais de estatuto que suportem a integração contínua comum e gasodutos de implantação contínua para organizações que gerem a sua Infraestrutura como Código – um elemento-chave em DevOps.

Para entender completamente uma planta e os estágios, cobriremos um ciclo de vida padrão:

> [!div class="checklist"]
> - Criar e editar uma planta
> - Publicar o projeto
> - Criar e editar uma nova versão do projeto
> - Publicar uma nova versão do projeto
> - Apagar uma versão específica da planta
> - Apagar a planta

## <a name="creating-and-editing-a-blueprint"></a>Criar e editar uma planta

Ao criar uma planta, adicione-lhe artefactos, guarde para um grupo de gestão ou subscrição, e forneceu um nome único e uma versão única. A planta está agora num modo **Draft** e ainda não pode ser atribuída. Enquanto no modo **Draft,** pode continuar a ser atualizado e alterado.

Uma planta nunca publicada no modo **Draft** exibe um ícone diferente na página **Definições de Plantas** do que as que foram **publicadas.** A **versão mais recente** é apresentada como **Draft** para estas plantas nunca publicadas.

Criar e editar uma planta com o [portal Azure](../create-blueprint-portal.md#create-a-blueprint) ou [REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publicar uma planta

Uma vez que todas as alterações planeadas tenham sido feitas para um projeto no modo **Draft,** pode ser **publicado** e disponibilizado para atribuição. A versão **publicada** da planta não pode ser alterada. Uma vez **publicado,** a planta apresenta um ícone diferente das plantas **do Draft** e exibe o número de versão fornecido na coluna Versão **Mais Recente.**

Publique uma planta com o [portal Azure](../create-blueprint-portal.md#publish-a-blueprint) ou [REST API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Criar e editar uma nova versão do projeto

Uma versão **publicada** de uma planta não pode ser alterada. No entanto, uma nova versão do projeto pode ser adicionada ao projeto existente e modificada conforme necessário. Faça alterações a uma planta existente editando-a. Quando as novas alterações são guardadas, o projeto tem agora **Alterações Não Publicadas**. Estas alterações são uma nova versão **draft** do projeto.

Editar uma planta com o [portal Azure.](../create-blueprint-portal.md#edit-a-blueprint)

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publicar uma nova versão do projeto

Cada versão editada de uma planta deve ser **publicada** antes de poder ser atribuída. Quando **as Alterações Não Publicadas** foram feitas a uma planta mas não **publicadas,** o botão **'Publish Blueprint'** está disponível na página de projeto de edição. Se o botão não estiver visível, a planta já foi **publicada** e não tem **Alterações Não Publicadas.**

> [!NOTE]
> Uma única planta pode ter **várias versões publicadas** que podem ser atribuídas a subscrições.

Para publicar um projeto com **Alterações Não Publicadas,** use os mesmos passos para publicar um novo projeto.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Apagar uma versão específica da planta

Cada versão de uma planta é um objeto único e pode ser **publicada** individualmente. Como tal, cada versão de uma planta também pode ser eliminada. Excluir uma versão de uma planta não tem qualquer impacto noutras versões dessa planta.

> [!NOTE]
> Não é possível apagar uma planta que tenha missões ativas. Elimine as atribuições primeiro e, em seguida, elimine a versão que pretende remover.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione **definições** de Planta a partir da página à esquerda e use as opções de filtro para localizar a planta que deseja eliminar uma versão de. Selecione-o para abrir a página de edição.

1. Selecione o separador **versões Publicado** e localize a versão deseja eliminar.

1. Clique com o botão direito na versão para eliminar e selecionar **Eliminar esta versão**.

## <a name="deleting-the-blueprint"></a>Apagar a planta

A planta central também pode ser eliminada. A eliminação da planta principal também elimina quaisquer versões de planta dessa planta, incluindo as plantas **draft** e **published.** Tal como a eliminação de uma versão de uma planta, a eliminação da planta central não remove as atribuições existentes de nenhuma das versões de planta.

> [!NOTE]
> Não é possível apagar uma planta que tenha missões ativas. Elimine as atribuições primeiro e, em seguida, elimine a versão que pretende remover.

Elimine uma planta com o [portal Azure](../create-blueprint-portal.md#delete-a-blueprint) ou [REST API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Atribuições

Há vários pontos durante o ciclo de vida que uma planta pode ser atribuída a uma subscrição. Quando o modo de uma versão da planta é **publicado**, então essa versão pode ser atribuída a uma subscrição. Este ciclo de vida permite que as versões de uma planta sejam usadas e atribuídas ativamente enquanto uma versão mais recente está a ser desenvolvida.

À medida que as versões das plantas são atribuídas, é importante entender onde são atribuídas e com que parâmetros foram atribuídos. Os parâmetros podem ser estáticos ou dinâmicos. Para saber mais, consulte [parâmetros estáticos e dinâmicos.](./parameters.md)

### <a name="updating-assignments"></a>Atualização de atribuições

Quando uma planta é atribuída, a atribuição pode ser atualizada. Existem várias razões para atualizar uma atribuição existente, incluindo:

- Adicionar ou remover [o bloqueio de recursos](./resource-locking.md)
- Alterar o valor dos [parâmetros dinâmicos](./parameters.md#dynamic-parameters)
- Atualize a atribuição para uma versão **mais recente da** planta

Para saber como, consulte [a atualização das atribuições existentes.](../how-to/update-existing-assignments.md)

### <a name="unassigning-assignments"></a>Atribuições não-assinadas

Se o projeto já não for necessário, pode ser não atribuído ao grupo de gestão ou subscrição. Durante a não assinatura da planta, ocorre o seguinte:

- Remoção do bloqueio de [recursos de plantas](./resource-locking.md)
- Supressão do objeto de atribuição de plantas
- (Condicional) Se uma **identidade gerida atribuída pelo sistema** foi usada, também é eliminada

> [!NOTE]
> Todos os recursos utilizados pela atribuição do projeto permanecem no local, mas já não estão protegidos pela Azure Blueprints.

## <a name="next-steps"></a>Passos seguintes

- Compreenda como utilizar [parâmetros estáticos e dinâmicos](./parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](./sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](./resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).