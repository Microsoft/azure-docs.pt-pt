---
title: Feedback de análise para apps Azure oferece - Mercado comercial da Microsoft
description: Como lidar com o feedback da sua oferta de aplicações Azure da equipa de revisão do Microsoft Azure Marketplace. Pode aceder ao feedback em Azure DevOps com as suas credenciais do Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: ede4e5dd781851c781407a8acea640e3e501f259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96436390"
---
# <a name="handling-review-feedback-for-azure-application-offers"></a>Lidar com comentários de revisão para ofertas de aplicações da Azure

Este artigo explica como aceder ao feedback da equipa de revisão do Microsoft Azure Marketplace em [Azure DevOps](https://azure.microsoft.com/services/devops/). Se forem encontrados problemas críticos na sua oferta de aplicação Azure durante o passo **de revisão** da Microsoft, pode assinar neste sistema para visualizar informações detalhadas sobre estes problemas (rever feedback). Depois de corrigir todos os problemas, deve reenviar a sua oferta para continuar a publicá-la no Azure Marketplace. O diagrama seguinte ilustra como este processo de feedback se relaciona com o processo de publicação.

![Rever processo de feedback](./media/review-feedback-process.png)

Normalmente, as questões de revisão são referenciadas como um pedido de puxar (PR). Cada PR está ligado a um item Azure DevOps on-line, que contém detalhes sobre o assunto. A imagem que se segue mostra um exemplo da experiência do Centro de Parceiros se os problemas forem encontrados durante as avaliações. 

![Estado de publicação](./media/publishing-status.png)

O PR que contenha detalhes específicos sobre a submissão será mencionado no link "Ver Relatório de Certificação". Para situações complexas, as equipas de revisão e apoio também podem enviar-lhe um e-mail.

## <a name="azure-devops-access"></a>Acesso a Azure DevOps

Todos os utilizadores com acesso à função de "desenvolvedor" no Partner Center terão acesso para visualizar os itens de RP referenciados no feedback de revisão.

## <a name="reviewing-the-pull-request"></a>Rever o pedido de puxar

Utilize o seguinte procedimento para rever as questões documentadas no pedido de retirada.

1. No formulário de **revisão** da Microsoft de etapas de publicação, selecione um link de relações públicas para lançar o seu navegador e navegar para a página **'Visão Geral'** (home) para este PR. A imagem que se segue mostra um exemplo da página inicial da edição crítica para a oferta da aplicação de amostras Contoso. Esta página contém informações úteis sobre os problemas de revisão encontrados na aplicação Azure.

    [![Puxe a página inicial do pedido](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Clique nesta imagem para expandir.*

1. (Opcional) No lado direito da janela, na secção **Políticas,** selecione a mensagem de assunto (neste exemplo: **Validação de Políticas falhou**) para investigar os detalhes de baixo nível do problema, incluindo os ficheiros de registo associados. Os erros são normalmente apresentados na parte inferior dos ficheiros de registo.

1. No menu do lado esquerdo da página inicial, selecione **Ficheiros** para exibir os ficheiros da lista que compõem os ativos técnicos desta oferta. Os revisores da Microsoft deveriam ter adicionado comentários descrevendo os problemas críticos descobertos. No exemplo seguinte, foram descobertas duas questões.

    [![Screenshot que destaca ficheiros e os dois problemas que foram descobertos.](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Clique nesta imagem para expandir.*

1. Selecione cada nó de comentário na árvore esquerda para navegar para o comentário no contexto do código circundante. Corrija o seu código fonte no projeto da sua equipa para corrigir o problema descrito pelo comentário.

>[!Note]
>Não é possível editar os ativos técnicos da sua oferta no ambiente Azure DevOps da equipa de revisão. Para os editores, este é um ambiente apenas de leitura para o código fonte contido. No entanto, pode deixar respostas aos comentários em benefício da equipa de revisão da Microsoft.

   No exemplo seguinte, a editora reviu, corrigiu e respondeu à primeira edição.

   ![Primeira resposta de correção e comentário](./media/first-comment-reply.png)

## <a name="next-steps"></a>Passos seguintes

Depois de corrigir as questões críticas documentadas nas relações públicas de revisão, deve [republicar a sua oferta de aplicações Azure](../create-new-azure-apps-offer.md).
