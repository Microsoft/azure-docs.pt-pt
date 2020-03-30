---
title: Manipulação de feedback de revisão para oferta de Apps Azure no Mercado Comercial
description: Como lidar com o feedback de revisão das Aplicações Azure para listagem ou venda no Azure Marketplace, AppSource ou através do programa Cloud Solution Provider (CSP) utilizando o portal Mercado Comercial no Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 3932f3fc71dc6427b6cdf93d3a7bc58534a9981c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279798"
---
# <a name="handling-review-feedback"></a>Processar comentários de crítica

Este artigo explica como aceder ao ambiente Azure DevOps utilizado pela equipa de revisão do Microsoft Azure Marketplace. Se forem encontrados problemas críticos na sua oferta de aplicação Azure durante a fase de revisão da **Microsoft,** pode iniciar sessão neste sistema para visualizar informações detalhadas sobre estes problemas (feedback de revisão). Depois de corrigir todos estes problemas, deve reenviar a sua oferta para continuar a publicá-la no Azure Marketplace. O diagrama seguinte ilustra como este processo de feedback se relaciona com o processo de publicação.

![Rever o processo de feedback](./media/review-feedback-process.png)

Tipicamente, as questões de revisão são referenciadas como pedido de puxão (PR). Cada PR está ligado a um item online [Azure DevOps](https://azure.microsoft.com/services/devops/) (anteriormente chamado Visual Studio Team Services (VSTS),que contém detalhes sobre o problema. A imagem que se segue apresenta um exemplo da experiência do Partner Center se os problemas forem encontrados durante as avaliações. 

![Estado de publicação](./media/publishing-status.png)

O PR que contiver detalhes específicos sobre a submissão será mencionado no link "Ver Relatório de Certificação". Para situações complexas, as equipas de revisão e suporte também podem enviar-lhe um e-mail.

## <a name="azure-devops-access"></a>Acesso azure DevOps

Todos os utilizadores com acesso à função "developer" no Partner Center terão acesso a visualização dos itens de RP referenciados no feedback da revisão.

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>Rever o pedido de puxão

Utilize o seguinte procedimento para rever as questões documentadas no pedido de retirada.

1. Nas secções de revisão da **Microsoft** do formulário de etapas de publicação, clique num link de RELAÇÕES para lançar o seu navegador e navegar para a página **de Visão Geral** (casa) para este PR. A imagem que se segue retrata um exemplo da página inicial da edição crítica para a oferta da aplicação da amostra Contoso. Esta página contém informações sumárias úteis sobre os problemas de revisão encontrados na aplicação Azure.

    [![Puxe a página inicial do pedido de pedido](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Clique na imagem para expandir.*

1. (Opcional) No lado direito da janela, na secção **Políticas,** clique na mensagem de emissão (neste exemplo: **Validação de Políticas falhou)** para investigar os detalhes de baixo nível do problema, incluindo os ficheiros de registo associados. Os erros são normalmente apresentados na parte inferior dos ficheiros de registo.
1. No menu do lado esquerdo da página inicial, selecione **Ficheiros** para exibir os ficheiros da lista que compõem os ativos técnicos para esta oferta. Os revisores da Microsoft deveriam ter adicionado comentários descrevendo os problemas críticos descobertos. No exemplo seguinte, foram descobertas duas questões.

    [![Puxe a página inicial do pedido de pedido](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Clique na imagem para expandir.*

1. Clique em cada nó de comentário na árvore esquerda para navegar para o comentário no contexto do código circundante. Corrija o seu código fonte no projeto da sua equipa para corrigir o problema descrito pelo comentário.

>[!Note]
>Não é possível editar os ativos técnicos da sua oferta no ambiente Azure DevOps da equipa de revisão. Para os editores, este é um ambiente só para leitura para o código fonte contido. No entanto, pode deixar respostas aos comentários em benefício da equipa de revisão da Microsoft.

   No exemplo seguinte, a editora reviu, corrigiu e respondeu à primeira edição.

   ![Primeira correção e resposta de comentário](./media/first-comment-reply.png)

## <a name="next-steps"></a>Passos seguintes

Depois de corrigir as questões críticas documentadas na revisão pr(s), deve voltar a publicar a sua oferta de [aplicações Azure](./create-new-azure-apps-offer.md#publish).
