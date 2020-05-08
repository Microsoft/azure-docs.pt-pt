---
title: Compreender o ciclo de vida de uma planta
description: Saiba mais sobre o ciclo de vida que uma definição de planta passa e detalhes sobre cada etapa, incluindo atualizar e remover atribuições de plantas.
ms.date: 05/06/2020
ms.topic: conceptual
ms.openlocfilehash: dc024c0e6643420d26bdc92e47fabe647c55ae7b
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864033"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Compreenda o ciclo de vida de um Projeto Azure

Como muitos recursos dentro do Azure, uma planta em Azure Blueprints tem um ciclo de vida típico e natural. São criados, implantados e finalmente apagados quando já não são necessários ou relevantes. A Azure Blueprints suporta operações padrão de ciclo de vida. Em seguida, baseia-se neles para fornecer níveis adicionais de estado que suportam a integração contínua comum e os gasodutos de implantação contínua para organizações que gerem a sua Infraestrutura como Código – um elemento-chave na DevOps.

Para entender completamente uma planta e as etapas, cobriremos um ciclo de vida padrão:

> [!div class="checklist"]
> - Criar e editar uma planta
> - Publicação da planta
> - Criar e editar uma nova versão do projeto
> - Publicação de uma nova versão do projeto
> - Apagar uma versão específica da planta
> - Apagar a planta

## <a name="creating-and-editing-a-blueprint"></a>Criar e editar uma planta

Ao criar uma planta, adicione artefactos, guarde para um grupo de gestão ou subscrição, e forneceu um nome único e uma versão única. A planta está agora em modo **Draft** e ainda não pode ser atribuída. Enquanto está no modo **Draft,** pode continuar a ser atualizado e alterado.

Uma planta nunca publicada no modo **Draft** apresenta um ícone diferente na página **Definições** de Plantas do que as que foram **publicadas**. A **versão mais recente** é apresentada como **Draft** para estas plantas nunca publicadas.

Crie e edite uma planta com o [portal Azure](../create-blueprint-portal.md#create-a-blueprint) ou [REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publicar uma planta

Uma vez feitas todas as alterações planeadas para uma planta no modo **Draft,** pode ser **publicado** e disponibilizado para atribuição. A versão **publicada** da planta não pode ser alterada. Uma vez **publicado,** a planta exibe um ícone diferente do **Projeto** de Plantas e exibe o número de versão fornecido na coluna **Versão Mais Recente.**

Publique uma planta com o [portal Azure](../create-blueprint-portal.md#publish-a-blueprint) ou [REST API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Criar e editar uma nova versão do projeto

Uma versão **publicada** de uma planta não pode ser alterada. No entanto, uma nova versão da planta pode ser adicionada ao plano existente e modificada conforme necessário. Faça alterações numa planta existente editando-a. Quando as novas alterações são guardadas, o projeto tem agora **Alterações Inéditas**. Estas alterações são uma nova versão **do Projeto** da planta.

Editar uma planta com o [portal Azure.](../create-blueprint-portal.md#edit-a-blueprint)

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publicação de uma nova versão do projeto

Cada versão editada de uma planta deve ser **publicada** antes de poder ser atribuída. Quando foram feitas **alterações inéditas** numa planta mas não **publicadas,** o botão **Publicar Blueprint** está disponível na página de plantas de edição. Se o botão não estiver visível, a planta já foi **publicada** e não tem **Alterações Inéditas**.

> [!NOTE]
> Uma única planta pode ter várias versões **publicadas** que podem ser atribuídas a subscrições.

Para publicar um projeto com **Alterações Não Publicadas,** use os mesmos passos para publicar um novo projeto.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Apagar uma versão específica da planta

Cada versão de uma planta é um objeto único e pode ser **publicado**individualmente. Como tal, cada versão de uma planta também pode ser eliminada. Apagar uma versão de uma planta não tem qualquer impacto noutras versões dessa planta.

> [!NOTE]
> Não é possível apagar uma planta que tem missões ativas. Elimine as atribuições primeiro e, em seguida, elimine a versão que pretende remover.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione **definições** de Blueprint a partir da página à esquerda e utilize as opções de filtro para localizar a planta de que pretende eliminar uma versão. Selecione-o para abrir a página de edição.

1. Selecione o separador **de versões Publicados** e localize a versão que pretende eliminar.

1. Clique à direita na versão para eliminar e selecionar **Eliminar esta versão**.

## <a name="deleting-the-blueprint"></a>Apagar a planta

A planta principal também pode ser eliminada. A eliminação da planta principal também elimina quaisquer versões de plantas dessa planta, incluindo as plantas **Draft** e **Published.** Tal como com a eliminação de uma versão de uma planta, a eliminação da planta principal não remove as atribuições existentes de nenhuma das versões da planta.

> [!NOTE]
> Não é possível apagar uma planta que tem missões ativas. Elimine as atribuições primeiro e, em seguida, elimine a versão que pretende remover.

Elimine uma planta com o [portal Azure](../create-blueprint-portal.md#delete-a-blueprint) ou [REST API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Atribuições

Há vários pontos durante o ciclo de vida que uma planta pode ser atribuída a uma subscrição. Quando o modo de uma versão da planta for **publicado,** então essa versão pode ser atribuída a uma subscrição. Este ciclo de vida permite que as versões de uma planta sejam usadas e ativamente atribuídas enquanto uma versão mais recente está sendo desenvolvida.

À medida que as versões das plantas são atribuídas, é importante entender onde são atribuídos e com que parâmetros foram atribuídos. Os parâmetros podem ser estáticos ou dinâmicos. Para saber mais, consulte [parâmetros estáticos e dinâmicos.](parameters.md)

### <a name="updating-assignments"></a>Atribuição de atribuições

Quando uma planta é atribuída, a atribuição pode ser atualizada. Existem várias razões para atualizar uma atribuição existente, incluindo:

- Adicionar ou remover [o bloqueio de recursos](resource-locking.md)
- Alterar o valor dos [parâmetros dinâmicos](parameters.md#dynamic-parameters)
- Atualize a atribuição para uma versão mais recente **publicada** do projeto

Para saber como, consulte a atualização das [atribuições existentes.](../how-to/update-existing-assignments.md)

### <a name="unassigning-assignments"></a>Tarefas não atribuídas

Se o projeto já não for necessário, pode não ser atribuído ao grupo de gestão ou à subscrição. Durante a desatribuição da planta, ocorre o seguinte:

- Remoção do bloqueio de recursos de [plantas](resource-locking.md)
- Eliminação do objeto de atribuição de plantas
- (Condicional) Se uma **identidade gerida atribuída** ao sistema foi usada, também é eliminada

> [!NOTE]
> Todos os recursos utilizados pela atribuição do projeto permanecem no local, mas já não estão protegidos pela Azure Blueprints.

## <a name="next-steps"></a>Passos seguintes

- Compreenda como utilizar [parâmetros estáticos e dinâmicos](parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).