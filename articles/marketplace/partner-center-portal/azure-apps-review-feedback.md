---
title: Lidando com comentários de revisão para a oferta de aplicativos do Azure no Marketplace comercial
description: Como lidar com comentários de revisão para a oferta de aplicativos do Azure para listagem ou venda no Azure Marketplace, AppSource ou por meio do programa CSP (provedor de soluções na nuvem) usando o portal do Marketplace comercial no Microsoft Partner Center.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 5dd74a7be95ecf92289f2a144c9c509c71ce935b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933651"
---
# <a name="handling-review-feedback"></a>Processar comentários de crítica

Este artigo explica como acessar o ambiente de DevOps do Azure usado pela equipe de revisão de Microsoft Azure Marketplace. Se forem encontrados problemas críticos em sua oferta de aplicativo do Azure durante a etapa de **revisão da Microsoft** , você poderá entrar neste sistema para exibir informações detalhadas sobre esses problemas (comentários de revisão). Depois de corrigir todos esses problemas, você deve reenviar sua oferta para continuar a publicá-la no Azure Marketplace. O diagrama a seguir ilustra como esse processo de comentários está relacionado ao processo de publicação.

![Examinar o processo de comentários](./media/review-feedback-process.png)

Normalmente, os problemas de revisão são referenciados como solicitação pull (PR). Cada PR é vinculado a um item online [do Azure DevOps](https://azure.microsoft.com/services/devops/) (anteriormente denominado Visual Studio Team Services (VSTS)), que contém detalhes sobre o problema. A imagem a seguir exibe um exemplo da experiência do Partner Center se forem encontrados problemas durante as revisões. 

![Estado de publicação](./media/publishing-status.png)

A PR que contém detalhes específicos sobre o envio será mencionada no link "Exibir relatório de certificação". Para situações complexas, as equipes de revisão e suporte também podem enviar um email para você.

## <a name="azure-devops-access"></a>Acesso DevOps do Azure

Todos os usuários com acesso à função de "desenvolvedor" no Partner Center terão acesso para exibir os itens de PR referenciados em comentários de revisão.

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

## <a name="reviewing-the-pull-request"></a>Revisando a solicitação pull

Use o procedimento a seguir para examinar os problemas documentados na solicitação pull.

1. Nas seções de **revisão da Microsoft** do formulário etapas de publicação, clique em um link de PR para iniciar o navegador e navegue até a página **visão geral** (página inicial) desta PR. A imagem a seguir mostra um exemplo do problema crítico home page para a oferta de aplicativo de exemplo da contoso. Esta página contém informações de resumo úteis sobre os problemas de revisão encontrados no aplicativo do Azure.

    [![Home Page de solicitação Pull](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Clique na imagem para expandir.*

1. Adicional No lado direito da janela, na seção **políticas**, clique na mensagem de problema (neste exemplo: falha na validação da **política**) para investigar os detalhes de baixo nível do problema, incluindo os arquivos de log associados. Normalmente, os erros são exibidos na parte inferior dos arquivos de log.
1. No menu no lado esquerdo da home page, selecione **arquivos** para exibir os arquivos de lista que compõem os ativos técnicos desta oferta. Os revisores da Microsoft devem ter comentários adicionais que descrevam os problemas críticos descobertos. No exemplo a seguir, foram descobertos dois problemas.

    [![Home Page de solicitação Pull](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Clique na imagem para expandir.*

1. Clique em cada nó de comentário na árvore à esquerda para navegar até o comentário no contexto do código ao redor. Corrija o código-fonte no projeto da sua equipe para corrigir o problema descrito pelo comentário.

>[!Note]
>Você não pode editar os ativos técnicos da sua oferta dentro do ambiente de análise da equipe do Azure DevOps. Para Publicadores, este é um ambiente somente leitura para o código-fonte contido. No entanto, você pode deixar respostas para os comentários para o benefício da equipe de revisão da Microsoft.

   No exemplo a seguir, o Publicador revisou, corrigiu e respondeu ao primeiro problema.

   ![Primeira correção e resposta de comentário](./media/first-comment-reply.png)

## <a name="next-steps"></a>Passos seguintes

Depois de corrigir os problemas críticos documentados na PR (s) revisão (ões), você deve [republicar sua oferta de aplicativo do Azure](./create-new-azure-apps-offer.md#publish).
