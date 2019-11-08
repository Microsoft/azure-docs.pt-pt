---
title: Lidando com os comentários de revisão do aplicativo do Azure | Azure Marketplace
description: Explica como usar o DevOps do Azure para lidar com os comentários de revisão das ofertas de aplicativos do Azure para o Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pabutler
ms.openlocfilehash: ef4aff57948034fb369bd74564306b7b8674b377
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827585"
---
# <a name="handling-review-feedback"></a>Processar comentários de crítica

Este artigo explica como acessar o ambiente de DevOps do Azure usado pela equipe de revisão de Microsoft Azure Marketplace.  Se forem encontrados problemas críticos em sua oferta de aplicativo do Azure durante a etapa de **revisão da Microsoft** , você poderá entrar neste sistema para exibir informações detalhadas sobre esses problemas (comentários de revisão).  Depois de corrigir todos esses problemas, você deve reenviar sua oferta para continuar a publicá-la no Azure Marketplace.  O diagrama a seguir ilustra como esse processo de comentários está relacionado ao processo de publicação.

![Etapas de publicação com os comentários do Azure DevOps](./media/pub-flow-vsts-access.png)

Normalmente, os problemas de revisão são referenciados como solicitação pull (PR).  Cada PR é vinculado a um item online [do Azure DevOps](https://azure.microsoft.com/services/devops/) (anteriormente denominado Visual Studio Team Services (VSTS)), que contém detalhes sobre o problema.  A imagem a seguir exibe um exemplo de uma referência de PR de revisão.  Para situações complexas, as equipes de revisão e suporte também podem enviar um email para você. 

![Guia status exibindo comentários de revisão](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Acesso DevOps do Azure

Para exibir os itens de PR referenciados em comentários de revisão, os editores devem primeiro receber a devida autorização.  Caso contrário, novos Publicadores receberão uma página de resposta `401 - Not Authorized` ao tentar exibir o PRs.  Para solicitar acesso a este repositório DevOps do Azure, execute as seguintes etapas:

1. Colete as seguintes informações:
    - O nome e a ID do seu editor
    - Tipo de oferta (aplicativo do Azure), nome da oferta e ID do SKU
    - O link da solicitação pull, por exemplo: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` essa URL pode ser recuperada da mensagem de notificação ou o endereço da página de resposta 401.
    - Os endereços de email dos indivíduos de sua organização de publicação aos quais você deseja acesso concedido.  Essa lista deve incluir os endereços do proprietário que você forneceu ao se registrar como um Publicador no Portal do Cloud Partner.
2. Crie um incidente de suporte.  Na barra de título da Portal do Cloud Partner, selecione o botão **ajuda** e escolha **suporte** no menu.  O navegador da Web padrão deve ser iniciado e navegar até a página novo incidente de suporte da Microsoft.  (Talvez você precise entrar primeiro.)
3. Especifique o **tipo de problema** como **integração do Marketplace** e **categoria** como **problema de acesso**e, em seguida, selecione **Iniciar solicitação**.

    ![Categoria do tíquete de suporte](./media/support-incident1.png)

4. Na página **etapa 1 de 2** , forneça suas informações de contato e selecione **continuar**.
5. Na página **etapa 2 de 2** , especifique um título de incidente (por exemplo `Request Azure DevOps access`) e forneça as informações coletadas na primeira etapa (acima).  Leia e aceite o contrato e, em seguida, selecione **Enviar**.

Se a criação do incidente tiver sido bem-sucedida, uma página de confirmação será exibida.  Salve as informações de confirmação nesta página para sua referência.  A equipe de Suporte da Microsoft deve responder à sua solicitação de acesso dentro de alguns dias úteis.


## <a name="reviewing-the-pull-request"></a>Revisando a solicitação pull 

Use o procedimento a seguir para examinar os problemas documentados na solicitação pull.

1. Na seção **revisão da Microsoft** do formulário **etapas de publicação** , clique em um link de PR para iniciar o navegador e navegue até a página de **visão geral** (página inicial) desta PR.  A imagem a seguir ilustra um exemplo de problema crítico home page para a oferta de aplicativo de exemplo da contoso.  Esta página contém informações de resumo úteis sobre os problemas de revisão encontrados no aplicativo do Azure.  

    [![Home Page de solicitação Pull](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Clique na imagem para expandir.*
    
2. Adicional No lado direito da janela, na seção **políticas**, clique na mensagem de problema (neste exemplo: falha na validação da **política**) para investigar os detalhes de baixo nível do problema, incluindo os arquivos de log associados.  Normalmente, os erros são exibidos na parte inferior dos arquivos de log.

3. No menu no lado esquerdo da home page, selecione **arquivos** para exibir os arquivos de lista que compõem os ativos técnicos desta oferta.  Os revisores da Microsoft devem ter comentários adicionais que descrevam os problemas críticos descobertos.  No exemplo a seguir, foram descobertos dois problemas. 

    [![Home Page de solicitação Pull](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Clique na imagem para expandir.*

4. Clique em cada nó de comentário na árvore à esquerda para navegar até o comentário no contexto do código ao redor.  Corrija o código-fonte no projeto da sua equipe para corrigir o problema descrito pelo comentário.

> [!Note]
> Você não pode editar os ativos técnicos da sua oferta dentro do ambiente de análise da equipe do Azure DevOps.  Para Publicadores, este é um ambiente somente leitura para o código-fonte contido.  No entanto, você pode deixar respostas para os comentários para o benefício da equipe de revisão da Microsoft.

   No exemplo a seguir, o Publicador revisou, corrigiu e respondeu ao primeiro problema.

   ![Primeira correção e resposta de comentário](./media/first-comment-reply.png)


## <a name="next-steps"></a>Passos seguintes

Depois de corrigir os problemas críticos documentados na PR (s) revisão (ões), você deve [republicar sua oferta de aplicativo do Azure](./cpp-publish-offer.md).
