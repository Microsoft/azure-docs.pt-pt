---
title: Rever pedidos de retirada em ambientes de pré-produção em Azure Static Web Apps
description: Aprenda a usar ambientes de pré-produção para rever alterações de pedidos de pull em Azure Static Web Apps.
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: 1cb5f2f9ac941001efb18301f4a54bd0092920ba
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172745"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps-preview"></a>Rever pedidos Pull em ambientes de pré-produção na Pré-visualização das Aplicações Web Estáticas do Azure

Este artigo demonstra como usar ambientes de pré-produção para rever alterações às aplicações implementadas com [Azure Static Web Apps](overview.md).

Um ambiente de pré-produção (encenação) é uma versão faseada totalmente funcional da sua aplicação que inclui alterações não disponíveis na produção.

Azure Static Web Apps gera um fluxo de trabalho GitHub Actions no repositório. Quando um pedido de puxar é criado contra um ramo que o fluxo de trabalho observa, o ambiente de pré-produção é construído. O ambiente de pré-produção encena a aplicação, permite-lhe realizar avaliações antes de empurrar para a produção.

Vários ambientes de pré-produção podem coexistir ao mesmo tempo quando se utilizam aplicações web estáticas Azure. Cada vez que cria um pedido de puxar contra o ramo observado, uma versão encenada com as suas alterações é implantada para um ambiente de pré-produção distinto.

Existem muitos benefícios de utilizar ambientes de pré-produção. Pode, por exemplo:

- Reveja as alterações visuais entre a produção e a encenação. Por exemplo, visualizar atualizações para conteúdo e layout.
- Demonstre as alterações na sua equipa.
- Compare diferentes versões da sua aplicação.
- Validar alterações utilizando testes de aceitação.
- Efetue verificações de sanidade antes de ser implantado na produção.

> [!NOTE]
> Durante a pré-visualização, um [máximo de três ambientes de preparação](quotas.md) são permitidos de cada vez.

## <a name="prerequisites"></a>Pré-requisitos

- Um repositório gitHub existente configurado com Azure Static Web Apps. Consulte [a Construção da sua primeira aplicação estática](getting-started.md) se não tiver uma.

## <a name="make-a-change"></a>Fazer uma mudança

Comece por fazer uma mudança no seu repositório. Pode fazê-lo diretamente no GitHub, como mostrado nos passos seguintes.

1. Navegue para o repositório do seu projeto no GitHub e, em seguida, clique no botão **Branch** para criar um novo ramo.

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="Criar novo ramo utilizando a interface GitHub":::]

    Digite um nome de ramo e clique no **ramo Criar.**

1. Vá à sua pasta _de aplicativos_ e altere alguns conteúdos de texto. Por exemplo, pode alterar um título ou parágrafo. Assim que encontrar o ficheiro que pretende editar, clique em **Editar** para escoar a alteração.

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="Editar botão de ficheiro na interface GitHub":::

1. Depois de escoar as alterações, clique em **Cometer alterações** para comprometer as suas alterações no ramo.

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="Cometer botão de alterações na interface GitHub":::

## <a name="create-a-pull-request"></a>Criar um pedido Pull

Em seguida, crie um pedido de retirada desta mudança.

1. Abra o separador **de pedido** de pull do seu projeto no GitHub:

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="Puxe o separador de pedido num repositório GitHub":::

1. Clique no botão **De pedido de & Compare** do seu ramo.

1. Pode, opcionalmente, preencher alguns detalhes sobre as suas alterações e, em seguida, clicar no **pedido de puxar por 'Criar'.**

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="Pull request criação no GitHub":::

Pode atribuir revisores e adicionar comentários para discutir as suas alterações, se necessário.

> [!NOTE]
> Pode fazer várias alterações empurrando novos compromissos para o seu ramo. O pedido de puxar é então automaticamente atualizado para refletir todas as alterações.

## <a name="review-changes"></a>Alterações de revisão

Após a criação do pedido de puxar, o fluxo de trabalho de implementação de [ações GitHub executa](https://github.com/features/actions) e implementa as suas alterações num ambiente de pré-produção.

Uma vez concluído o fluxo de trabalho, a construção e implementação da sua aplicação, o bot GitHub adiciona um comentário ao seu pedido de pull que contém o URL do ambiente de pré-produção. Pode clicar nesta ligação para ver as alterações testadas.

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="Faça comentários de pedido com o URL de pré-produção":::

Clique no URL gerado para ver as alterações.

Se olhar mais de perto para a URL, pode ver que é composta assim: `https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net` .

Para um dado pedido de puxar, o URL permanece o mesmo mesmo se premir novas atualizações. Além da permanência constante do URL, o mesmo ambiente de pré-produção é reutilizado para a vida do pedido de puxão.

## <a name="publish-changes"></a>Publicar alterações

Uma vez aprovadas as alterações, pode publicar as suas alterações à produção através da fusão do pedido de pull.

Clique no **pedido de puxar da Fusão:**

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="Botão de pedido de puxar por fusão na interface GitHub":::

A fusão copia as suas alterações no ramo rastreado (o ramo de "produção"). Em seguida, o fluxo de trabalho de implantação começa no ramo rastreado e as alterações são em direto após a sua aplicação ter sido reconstruída.

Para verificar as alterações na produção, abra o URL de produção para carregar a versão ao vivo do website.

## <a name="limitations"></a>Limitações

As versões encenadas da sua aplicação são atualmente acessíveis publicamente pelo seu URL, mesmo que o seu repositório GitHub seja privado.

> [!WARNING]
> Tenha cuidado ao publicar conteúdo sensível em versões encenadas, uma vez que o acesso a ambientes de pré-produção não é restrito.

O número de ambientes de pré-produção disponíveis para cada aplicação implementada com Aplicações Web Estáticas depende do nível SKU que está a utilizar. Por exemplo, com o nível Livre pode ter 3 ambientes de pré-produção para além do ambiente de produção.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar um domínio personalizado](custom-domain.md)
