---
title: Compreender o ciclo de vida de um plano gráfico
description: Saiba mais sobre o ciclo de vida que atravessa um plano gráfico e detalhes sobre cada fase.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 71a5233c6d5af21d7bc5430cda14e156e16d5cd4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58001569"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Compreender o ciclo de vida de um esquema do Azure

Como muitos recursos no Azure, um plano gráfico em esquemas do Azure tem um ciclo de vida típico e natural. Estão criados, implantados e, finalmente eliminados quando já não é necessário ou relevantes.
Planos gráficos suporta operações de ciclo de vida padrão. Em seguida, se baseia na-los para fornecer níveis adicionais de estado que suportam comuns integração contínua e pipelines de implementação contínua para as organizações que gerir a sua infraestrutura como código – um elemento-chave em DevOps.

Para compreender totalmente um plano gráfico e as fases, iremos abranger um ciclo de vida padrão:

> [!div class="checklist"]
> - Criação e edição de um plano gráfico
> - O plano gráfico de publicação
> - Criação e edição de uma nova versão do esquema
> - Publicar uma nova versão do esquema
> - A eliminação de uma versão específica do esquema
> - A eliminar o plano gráfico

## <a name="creating-and-editing-a-blueprint"></a>Criação e edição de um plano gráfico

Quando criar um plano gráfico, adiciona artefactos, guarde a um grupo de gestão ou a subscrição e forneceu um nome exclusivo e uma versão exclusiva. O plano gráfico está agora num **rascunho** modo e ainda não é possível atribuir. Enquanto estiver no **rascunho** modo, este pode continuar a ser atualizada e foi alterado.

A nunca publicadas esquema no **rascunho** modo apresenta um ícone diferente no **definições do esquema** página que as que tiverem sido **publicada**. O **versão mais recente** é apresentado como **rascunho** para estes nunca publicado esquemas.

Criar e editar um plano gráfico com o [portal do Azure](../create-blueprint-portal.md#create-a-blueprint) ou [REST API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publicação de um plano gráfico

Depois de todas as alterações planeadas foram feitas para um plano gráfico no **rascunho** modo, pode ser **publicada** e disponíveis para atribuição. O **publicado** não é possível alterar a versão do esquema.
Uma vez **publicada**, o plano gráfico é apresentada com um ícone diferente que **rascunho** esquemas e mostra o número de versão fornecida no **versão mais recente** coluna.

Publicar uma esquema com o [portal do Azure](../create-blueprint-portal.md#publish-a-blueprint) ou [REST API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Criação e edição de uma nova versão do esquema

R **publicado** versão de um plano gráfico não pode ser alterada. No entanto, uma nova versão do plano gráfico pode ser adicionada ao esquema existente e modificada conforme necessário. Fazer alterações numa esquema existente por editá-lo. Quando as novas alterações são guardadas, tem agora o plano gráfico **alterações não publicadas**. Estas alterações são uma nova **rascunho** versão do esquema.

Editar uma esquema com o [portal do Azure](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publicar uma nova versão do esquema

Cada versão editada de um plano gráfico tem de ser **publicado** antes que possa ser atribuído. Quando **alterações não publicadas** foram feitos para um plano gráfico, mas não **publicado**, o **publicar esquema** botão está disponível na página de esquema de edição. Se o botão não estiver visível, o plano gráfico já foi **publicada** e não tem nenhum **alterações não publicadas**.

> [!NOTE]
> Uma única esquema pode ter vários **publicado** versões que podem cada uma ser atribuídas a subscrições.

Para publicar um plano gráfico com **alterações não publicadas**, utilize os mesmos passos para a publicação de um plano gráfico novo.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>A eliminação de uma versão específica do esquema

Cada versão de um plano gráfico é um objeto exclusivo e podem ser individualmente **publicado**. Como tal, a cada versão de um plano gráfico também pode ser eliminada. A eliminação de uma versão de um plano gráfico não tem qualquer impacto em outras versões desse esquema.

> [!NOTE]
> Não é possível eliminar um plano gráfico com atribuições ativas. Elimine as atribuições de primeiro e, em seguida, elimine a versão que pretende remover.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione **definições de esquema** partir da página à esquerda e utilize o filtro de opções para localizar o plano gráfico que pretende eliminar uma versão do. Clique no mesmo para abrir a página de edição.

1. Clique nas **versões publicadas** separador e localize a versão que pretende eliminar.

1. Com o botão direito na versão a eliminar e selecione **eliminar esta versão**.

## <a name="deleting-the-blueprint"></a>A eliminar o plano gráfico

O esquema de núcleo também pode ser eliminado. A eliminar o esquema de núcleo também elimina a nenhuma versão de esquema desse plano gráfico completo, incluindo **rascunho** e **publicada** esquemas. Como com a eliminação de uma versão de um plano gráfico, a eliminar o esquema de principal não remove as atribuições de existentes de qualquer uma das versões de esquema.

> [!NOTE]
> Não é possível eliminar um plano gráfico com atribuições ativas. Elimine as atribuições de primeiro e, em seguida, elimine a versão que pretende remover.

Eliminar um plano gráfico com o [portal do Azure](../create-blueprint-portal.md#delete-a-blueprint) ou [REST API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Atribuições

Há vários pontos durante o ciclo de vida de que um plano gráfico pode ser atribuído a uma subscrição. Quando o modo de uma versão do esquema estiver **publicado**, em seguida, essa versão pode ser atribuído a uma subscrição. Este ciclo de vida que permite que as versões de um plano gráfico para ser utilizado e ativamente atribuído enquanto está a ser desenvolvida uma versão mais recente.

Como são atribuídas as versões de esquemas, é importante compreender onde que lhes foram atribuídas e com os quais os parâmetros tem sido atribuídos com. Os parâmetros podem ser estático ou dinâmico. Para obter mais informações, consulte [parâmetros estáticos e dinâmicos](parameters.md).

### <a name="updating-assignments"></a>A atualizar as atribuições

Quando lhe for atribuído um plano gráfico, a atribuição pode ser atualizada. Há vários motivos para atualizar uma atribuição existente, incluindo:

- Adicionar ou remover [bloqueio do recurso](resource-locking.md)
- Alterar o valor de [parâmetros dinâmicos](parameters.md#dynamic-parameters)
- Atualizar a atribuição para uma mais recente **publicado** versão do esquema

Para saber como, veja [atualizar atribuições existentes](../how-to/update-existing-assignments.md).

## <a name="next-steps"></a>Passos Seguintes

- Compreenda como utilizar [parâmetros estáticos e dinâmicos](parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).