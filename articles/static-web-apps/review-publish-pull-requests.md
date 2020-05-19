---
title: Reveja pedidos de puxão em ambientes de pré-produção em Aplicações Web Estáticas Do Azure
description: Saiba como usar ambientes de pré-produção para rever as alterações dos pedidos de reprodução em Aplicações Web Estáticas do Azure.
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: 61c5917c1e4cb9dbf96e90af9a30777ea7c2e66c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597031"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps-preview"></a>Reveja pedidos de puxão em ambientes de pré-produção na Pré-visualização de aplicações da Web Estática do Azure

Este artigo demonstra como usar ambientes de pré-produção para rever alterações às aplicações implementadas com [aplicações Web Estáticas Azure](overview.md).

Um ambiente de pré-produção (encenação) é uma versão faseada totalmente funcional da sua aplicação que inclui alterações não disponíveis na produção.

As Aplicações Web Estáticas Azure geram um fluxo de trabalho gitHub Actions no repositório. Quando um pedido de puxão é criado contra um ramo que o fluxo de trabalho observa, o ambiente de pré-produção é construído. O ambiente de pré-produção é palco da app, permite-lhe realizar avaliações antes de avançar para a produção.

Vários ambientes de pré-produção podem coexistir ao mesmo tempo ao utilizar aplicações Web Estáticas Azure. Cada vez que cria um pedido de puxão contra o ramo assistido, uma versão encenada com as suas alterações é implantada para um ambiente de pré-produção distinto.

Existem muitos benefícios em utilizar ambientes de pré-produção. Pode, por exemplo:

- Reveja as alterações visuais entre a produção e a encenação. Por exemplo, visualizar atualizações de conteúdo e layout.
- Demonstre as mudanças na sua equipa.
- Compare diferentes versões da sua aplicação.
- Validar alterações utilizando testes de aceitação.
- Efetue verificações de sanidade antes de ser utilizado para a produção.

> [!NOTE]
> Durante a pré-visualização, é permitido um [máximo de apenas um ambiente de preparação](quotas.md) de cada vez.

## <a name="prerequisites"></a>Pré-requisitos

- Um repositório GitHub existente configurado com Aplicações Web Estáticas Azure. Veja [a construção da sua primeira aplicação estática](getting-started.md) se não tiver uma.

## <a name="make-a-change"></a>Faça uma mudança

Comece por fazer uma mudança no seu repositório. Pode fazê-lo diretamente no GitHub, como mostra os seguintes passos.

1. Navegue para o repositório do seu projeto no GitHub e, em seguida, clique no botão **Branch** para criar um novo ramo.

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="Criar novo ramo usando a interface GitHub":::]

    Digite um nome de ramo e clique no **ramo Criar**.

1. Vá à pasta da sua _aplicação_ e altere alguns conteúdos de texto. Por exemplo, pode alterar um título ou parágrafo. Assim que encontrar o ficheiro que pretende editar, clique em **Editar** para fazer a alteração.

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="Editar botão de ficheiro na interface GitHub":::

1. Depois de efazer as alterações, clique em **alterar-se** para comprometer as suas alterações no ramo.

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="Comprometa altera o botão na interface GitHub":::

## <a name="create-a-pull-request"></a>Criar um pedido Pull

Em seguida, crie um pedido de retirada desta mudança.

1. Abra o separador **de pedido pull** do seu projeto no GitHub:

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="Puxe o separador de pedido num repositório GitHub":::

1. Clique no botão **de pedido de puxar & comparar** da sua filial.

1. Pode preencher opcionalmente alguns detalhes sobre as suas alterações e, em seguida, clicar no **pedido de pull Create**.

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="Pull request criação no GitHub":::

Pode atribuir revisores e adicionar comentários para discutir as suas alterações, se necessário.

> [!NOTE]
> Pode fazer várias alterações empurrando novos compromissos para o seu ramo. O pedido de puxão é então atualizado automaticamente para refletir todas as alterações.

## <a name="review-changes"></a>Alterações de revisão

Após a criação do pedido de pull, o fluxo de trabalho de implementação [gitHub Actions](https://github.com/features/actions) executa e implementa as suas alterações para um ambiente de pré-produção.

Uma vez que o fluxo de trabalho tenha concluído a construção e a implementação da sua aplicação, o bot GitHub adiciona um comentário ao seu pedido de puxão que contém o URL do ambiente de pré-produção. Pode clicar neste link para ver as alterações encenadas.

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="Puxe o comentário de pedido com o URL de pré-produção":::

Clique no URL gerado para ver as alterações.

Se olharmais de perto para o URL, pode ver que é composta assim: `https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net` .

Para um determinado pedido de pull, o URL permanece o mesmo mesmo mesmo que empurre novas atualizações. Além da permanência do URL constante, o mesmo ambiente de pré-produção é reutilizado para a vida útil do pedido de puxão.

## <a name="publish-changes"></a>Publicar alterações

Uma vez aprovadas alterações, pode publicar as suas alterações à produção fundindo o pedido de pull.

Clique no pedido de **pull Merge:**

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="Funilhe o botão de pedido de puxar na interface GitHub":::

A fusão copia as suas alterações no ramo rastreado (o ramo de "produção"). Em seguida, o fluxo de trabalho de implantação começa no ramo rastreado e as alterações são ao vivo após a sua aplicação ter sido reconstruída.

Para verificar as alterações de produção, abra o URL de produção para carregar a versão ao vivo do website.

## <a name="limitations"></a>Limitações

As versões encenadas da sua aplicação são atualmente acessíveis publicamente pelo seu URL, mesmo que o seu repositório GitHub seja privado.

> [!WARNING]
> Tenha cuidado ao publicar conteúdo sensível em versões encenadas, uma vez que o acesso a ambientes de pré-produção não é restrito.

O número de ambientes de pré-produção disponíveis para cada aplicação implementada com Aplicações Web Estáticas depende do nível SKU que está a utilizar. Por exemplo, com o nível Livre pode ter 1 ambiente de pré-produção para além do ambiente de produção.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Configurar um domínio personalizado](custom-domain.md)
