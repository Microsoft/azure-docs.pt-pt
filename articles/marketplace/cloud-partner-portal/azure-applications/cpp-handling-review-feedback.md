---
title: Manipulação de comentários de revisão de aplicações do Azure - Azure Marketplace | Documentos da Microsoft
description: Explica como utilizar o Azure DevOps para lidar com os comentários da revisão das ofertas de aplicação do Azure para o Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pbutlerm
ms.openlocfilehash: e5fe076cfe733b4fa0151570603c090af98de0e8
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882693"
---
# <a name="handling-review-feedback"></a>Manipulação de comentários da revisão

Este artigo explica como acessar o ambiente de DevOps do Azure utilizado pela equipa de revisão do Microsoft Azure Marketplace.  Se forem encontrados problemas de críticos na sua oferta de aplicação do Azure durante a **revisão Microsoft** passo, pode iniciar sessão neste sistema para ver informações detalhadas sobre estes problemas (revisar comentários).  Depois de corrigir todos esses problemas, tem de submeter novamente sua oferta continuar a publicá-la no Azure Marketplace.  O diagrama seguinte ilustra como esse processo de comentários se relaciona com o processo de publicação.

![Publicação passos com comentários do VSTS](./media/pub-flow-vsts-access.png)

Normalmente, os problemas de revisão são referenciados como pedido pull (PR).  Cada pedido Pull está ligada a um online [do Azure DevOps](https://azure.microsoft.com/services/devops/) (anteriormente chamado Visual Studio Team Services (VSTS)) item, que contém detalhes sobre o problema.  A imagem seguinte mostra um exemplo de uma referência de PR de revisão.  Para situações complexas, as equipes de suporte e de revisão podem também enviar um e-mail. 

![Comentários de revisão de apresentação de separador de status](./media/status-tab-ms-review.png)


## <a name="vsts-access"></a>Acesso do VSTS

Para ver os itens de PR referenciados em comentários da revisão, os publicadores têm primeiro de ser concedidos autorização adequada.  Caso contrário, os novos editores recebem um `401 - Not Authorized` página de resposta ao tentar ver os pedidos pull.  Para pedir acesso para este repositório de DevOps do Azure, execute os seguintes passos:

1. Recolha as seguintes informações:
    - O nome do publicador e o ID
    - (Aplicação do Azure) do tipo de oferta, nome e ID de SKU de oferta
    - O pedido pull ligar, por exemplo: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`  Este URL pode ser obtido a partir da mensagem de notificação ou o endereço da página de 401 resposta.
    - Os endereços de e-mail das pessoas da sua organização de publicação que pretende conceder acesso a.  Esta lista deve incluir os endereços de proprietário fornecidos ao registrar como um publicador no Portal de parceiros de nuvem.
2. Crie um incidente de suporte.  Na barra de título do Cloud Partner Portal, selecione o **ajudar** botão, em seguida, escolha **suporte** no menu.  Default web browser deve iniciar e navegue para a Microsoft novo incidente página de suporte.  (Poderá ter de entrar primeiro).
3. Especifique a **tipo de problema** como **inclusão de marketplace** e **categoria** como **problemas de acesso ao**, em seguida, selecione **iniciar pedido**.

    ![Categoria de pedido de suporte](./media/support-incident1.png)

4. Na **passo 1 de 2** página, forneça as informações de contacto e selecione **continuar**.
5. Na **passo 2 de 2** , especifique um título do incidente (por exemplo `Request VSTS access`) e forneça as informações recolhidas no primeiro passo (acima).  Leia e aceite o contrato, em seguida, selecione **submeter**.

Se a criação de incidentes foi concluída com êxito, é apresentada uma página de confirmação.  Guarde as informações de confirmação nesta página para referência.  A equipa de suporte da Microsoft deve responder ao seu pedido de acesso em poucos dias de negócios.


## <a name="reviewing-the-pull-request"></a>Rever o pedido pull 

Utilize o procedimento seguinte para rever problemas documentados no pedido pull.

1. No **revisão do Microsoft** secção **passos de publicação** de formulário, clique num link de PR para iniciar o seu browser e navegue para o **descrição geral** página (home) para este pedido Pull.  A imagem seguinte ilustra uma home page do exemplo problema crítico para a oferta de aplicação de exemplo de Contoso.  Esta página contém informações úteis de resumidas sobre os problemas de revisão encontrado na aplicação do Azure.  

    [![Home page do pedido Pull](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Clique na imagem para expandir.*
    
2. (Opcional) No lado direito da janela, na secção **políticas**, clique na mensagem de problema (neste exemplo: **Falha ao validar a política**) para investigar os detalhes de baixo nível do problema, incluindo os ficheiros de registo associados.  Normalmente, são apresentados erros na parte inferior dos ficheiros de registo.

3. No menu do lado esquerdo da home page, selecione **ficheiros** para exibir os arquivos de lista que compõem os recursos técnicos para esta oferta.  Os revisores da Microsoft devem ter adicionado os comentários que descrevem os problemas críticos detetados.  No exemplo a seguir, os dois problemas tenham sido detetados. 

    [![Home page do pedido Pull](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Clique na imagem para expandir.*

4. Clique em cada nó de comentário na árvore à esquerda para navegar para o comentário no contexto do código ao redor.  Corrija o seu código-fonte no projeto de sua equipe para corrigir o problema descrito pelo comentário.

> [!Note]
> Não é possível editar ativos técnicos da sua oferta no ambiente de Azure DevOps da equipa de revisão.  Os publicadores, este é um ambiente de só de leitura para o código-fonte contido.  No entanto, pode deixar respostas para os comentários para o benefício da equipa de revisão da Microsoft.

   No exemplo a seguir, o publicador tem revisto, corrigido e respondeu ao primeiro problema.

   ![Primeira resposta de correção e comentário](./media/first-comment-reply.png)


## <a name="next-steps"></a>Passos Seguintes

Depois de corrigir os problemas críticos documentados em PR(s) a revisão, deve [voltar a publicar a oferta de aplicação do Azure](./cpp-publish-offer.md).
