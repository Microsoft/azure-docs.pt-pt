---
title: Feedback de revisão para ofertas de aplicações Do Azure - Mercado comercial da Microsoft
description: Como lidar com o feedback da sua oferta de aplicação Azure da equipa de revisão do Microsoft Azure Marketplace. Pode aceder ao feedback em Azure DevOps com as suas credenciais partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: c9e441512177d731c5ad47b66b454e5722483507
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659840"
---
# <a name="handling-review-feedback-for-azure-application-offers"></a>Manipulação de feedback de revisão para ofertas de aplicações do Azure

Este artigo explica como aceder ao feedback da equipa de revisão do Microsoft Azure Marketplace em [Azure DevOps](https://azure.microsoft.com/services/devops/). Se forem encontrados problemas críticos na sua oferta de aplicação Azure durante a fase de revisão da **Microsoft,** pode iniciar sessão neste sistema para visualizar informações detalhadas sobre estes problemas (feedback de revisão). Depois de corrigir todos os problemas, deve reenviar a sua oferta para continuar a publicá-la no Azure Marketplace. O diagrama seguinte ilustra como este processo de feedback se relaciona com o processo de publicação.

![Rever o processo de feedback](./media/review-feedback-process.png)

Tipicamente, as questões de revisão são referenciadas como um pedido de puxão (PR). Cada PR está ligado a um item azure DevOps online, que contém detalhes sobre o assunto. A imagem que se segue apresenta um exemplo da experiência do Partner Center se os problemas forem encontrados durante as avaliações. 

![Estado de publicação](./media/publishing-status.png)

O PR que contiver detalhes específicos sobre a submissão será mencionado no link "Ver Relatório de Certificação". Para situações complexas, as equipas de revisão e suporte também podem enviar-lhe um e-mail.

## <a name="azure-devops-access"></a>Acesso azure DevOps

Todos os utilizadores com acesso à função "developer" no Partner Center terão acesso a visualização dos itens de RP referenciados no feedback da revisão.

## <a name="reviewing-the-pull-request"></a>Rever o pedido de puxão

Utilize o seguinte procedimento para rever as questões documentadas no pedido de retirada.

1. Nas secções de revisão da **Microsoft** do formulário de etapas de publicação, selecione um link de RELAÇÕES para lançar o seu navegador e navegar para a página **de Visão Geral** (casa) para este PR. A imagem que se segue retrata um exemplo da página inicial da edição crítica para a oferta da aplicação da amostra Contoso. Esta página contém informações sumárias úteis sobre os problemas de revisão encontrados na aplicação Azure.

    [![Puxe a página inicial do pedido de pedido](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Clique nesta imagem para expandir.*

1. (Opcional) Do lado direito da janela, na secção **Políticas,** selecione a mensagem de emissão (neste exemplo: **Validação de Políticas falhou)** para investigar os detalhes de baixo nível do problema, incluindo os ficheiros de registo associados. Os erros são normalmente apresentados na parte inferior dos ficheiros de registo.

1. No menu do lado esquerdo da página inicial, selecione **Ficheiros** para exibir os ficheiros da lista que compõem os ativos técnicos para esta oferta. Os revisores da Microsoft deveriam ter adicionado comentários descrevendo os problemas críticos descobertos. No exemplo seguinte, foram descobertas duas questões.

    [![Puxe a página inicial do pedido de pedido](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Clique nesta imagem para expandir.*

1. Selecione cada nó de comentário na árvore esquerda para navegar para o comentário no contexto do código circundante. Corrija o seu código fonte no projeto da sua equipa para corrigir o problema descrito pelo comentário.

>[!Note]
>Não é possível editar os ativos técnicos da sua oferta no ambiente Azure DevOps da equipa de revisão. Para os editores, este é um ambiente só para leitura para o código fonte contido. No entanto, pode deixar respostas aos comentários em benefício da equipa de revisão da Microsoft.

   No exemplo seguinte, a editora reviu, corrigiu e respondeu à primeira edição.

   ![Primeira correção e resposta de comentário](./media/first-comment-reply.png)

## <a name="next-steps"></a>Passos seguintes

Depois de corrigir as questões críticas documentadas na revisão pr(s), deve voltar a publicar a sua oferta de [aplicações Azure](./create-new-azure-apps-offer.md#publish).
