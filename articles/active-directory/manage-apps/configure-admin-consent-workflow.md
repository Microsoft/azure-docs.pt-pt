---
title: Configure o fluxo de trabalho de consentimento administrativo - Azure Ative Directory | Microsoft Docs
description: Saiba como configurar uma forma de os utilizadores finais solicitarem o acesso a aplicações que requerem consentimento administrativo.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2019
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95d89ea0cbc7d1e0379a9cbfce40f11d4f8ac93f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101643767"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Configure o fluxo de trabalho de consentimento administrativo (pré-visualização)

Este artigo descreve como ativar a funcionalidade de fluxo de trabalho de consentimento de administração (pré-visualização), que dá aos utilizadores finais uma forma de solicitar o acesso a aplicações que requerem consentimento administrativo.

Sem um fluxo de trabalho de consentimento administrativo, um utilizador num inquilino onde o consentimento do utilizador é desativado será bloqueado quando tenta aceder a qualquer app que exija permissões para aceder a dados organizacionais. O utilizador vê uma mensagem de erro genérica que diz não estar autorizado a aceder à aplicação e que deve pedir ajuda ao administrador. Mas, muitas vezes, o utilizador não sabe a quem contactar, pelo que ou desiste ou cria uma nova conta local na aplicação. Mesmo quando um administrador é notificado, nem sempre há um processo simplificado para ajudar o administrador a conceder acesso e notificar os seus utilizadores.
 
O fluxo de trabalho de consentimento administrativo dá aos administradores uma forma segura de conceder acesso a aplicações que requerem aprovação de administração. Quando um utilizador tenta aceder a uma aplicação mas não consegue dar o seu consentimento, pode enviar um pedido de aprovação de administração. O pedido é enviado por e-mail para administradores que foram designados como revisores. Um revisor toma medidas sobre o pedido, e o utilizador é notificado da ação.

Para aprovar pedidos, um revisor deve ser um administrador global, administrador de aplicação na nuvem ou administrador de aplicação. O revisor já deve ter uma destas funções de administrador atribuídas; simplesmente designá-los como um revisor não eleva os seus privilégios.

## <a name="enable-the-admin-consent-workflow"></a>Ativar o fluxo de trabalho de consentimento administrativo

Para ativar o fluxo de trabalho de consentimento administrativo e escolher revisores:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) como administrador global.
2. Clique em **todos os serviços** no topo do menu de navegação à esquerda. Abre **a Extensão do Diretório Ativo Azure.**
3. Na caixa de pesquisa de filtros, escreva "**Azure Ative Directory**" e selecione o item **Azure Ative Directory.**
4. A partir do menu de navegação, clique nas **aplicações Da Enterprise.** 
5. Em **Gestão**, selecione **as definições do Utilizador**.
6. Nos termos dos pedidos de **consentimento da Administração (Preview)**, **os utilizadores podem solicitar o consentimento administrativo para aplicações que não podem consentir com** **Sim**.

   ![Configurar definições de fluxo de trabalho de consentimento administrativo](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Configure as seguintes definições:

   * **Selecione os utilizadores para rever os pedidos de consentimento administrativo.** Selecione os revisores para este fluxo de trabalho de um conjunto de utilizadores que têm o administrador global, administrador de aplicação na nuvem e funções de administrador de aplicações.
   * **Os utilizadores selecionados receberão notificações por e-mail para pedidos.** Ativar ou desativar notificações de e-mail para os revisores quando um pedido é feito.  
   * **Os utilizadores selecionados receberão lembretes de expiração do pedido.** Ative ou desative notificações de e-mail de lembrete para os revisores quando um pedido está prestes a expirar.  
   * **O pedido de consentimento expira após (dias)**. Especifique quanto tempo os pedidos permanecem válidos.

7. Selecione **Guardar**. Pode levar até uma hora para que a funcionalidade fique ativada.

> [!NOTE]
> Pode adicionar ou remover revisores para este fluxo de trabalho modificando a lista **de revisores de pedidos de consentimento de administração Select.** Note que uma limitação atual desta funcionalidade é que os revisores podem manter a capacidade de rever os pedidos que foram feitos enquanto foram designados como revisores.

## <a name="how-users-request-admin-consent"></a>Como os utilizadores solicitam consentimento administrativo

Após o fluxo de trabalho de consentimento administrativo ser ativado, os utilizadores podem solicitar a aprovação de um pedido que não estão autorizados a consentir. Os seguintes passos descrevem a experiência do utilizador ao solicitar a aprovação. 

1. O utilizador tenta iniciar sção na aplicação.

2. A **mensagem necessária para a aprovação** aparece. O utilizador escreve uma justificação para necessitar de acesso à app e, em seguida, seleciona **a aprovação do Pedido.**

   ![A screenshot mostra uma caixa de diálogo necessária para a aprovação da aprovação da Aprovação.](media/configure-admin-consent-workflow/end-user-justification.png)

3. Um **Pedido enviado** mensagem confirma que o pedido foi submetido à administração. Se o utilizador enviar vários pedidos, apenas o primeiro pedido é submetido à administração.

   ![A screenshot mostra a confirmação enviada pelo Pedido.](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. O utilizador recebe uma notificação por e-mail quando o seu pedido é aprovado, negado ou bloqueado. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Rever e tomar medidas sobre pedidos de consentimento administrativo

Para rever os pedidos de consentimento administrativo e tomar medidas:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como um dos revisores registados do fluxo de trabalho de consentimento administrativo.
2. Selecione **Todos os serviços** no topo do menu de navegação à esquerda. Abre **a Extensão do Diretório Ativo Azure.**
3. Na caixa de pesquisa de filtros, escreva "**Azure Ative Directory**" e selecione o item **Azure Ative Directory.**
4. A partir do menu de navegação, clique nas **aplicações Da Enterprise.**
5. No âmbito **da Atividade**, selecione **pedidos de consentimento de administração (Pré-visualização)**.

   > [!NOTE]
   > Os revisores só verão pedidos de administração que foram criados depois de terem sido designados como revisores.

1. Selecione a aplicação que está a ser solicitada.
2. Rever detalhes sobre o pedido:  

   * Para ver quem está a solicitar acesso e porquê, selecione o **Pedido por** aba.
   * Para ver que permissões estão a ser solicitadas pela aplicação, selecione **permissões de Revisão e consentimento.**

8. Avaliar o pedido e tomar as medidas adequadas:

   * **Aprovar o pedido.** Para aprovar um pedido, conceda o consentimento administrativo ao pedido. Uma vez aprovado o pedido, todos os solicitadores são notificados de que lhes foi concedido acesso.  
   * **Negue o pedido.** Para negar um pedido, deve fornecer uma justificação que será fornecida a todos os solicitadores. Uma vez negado um pedido, todos os solicitadores são notificados de que lhes foi negado o acesso ao pedido. Negar um pedido não impedirá os utilizadores de solicitarem o consentimento da administração para a app no futuro.  
   * **Bloqueie o pedido.** Para bloquear um pedido, deve fornecer uma justificação que será fornecida a todos os solicitadores. Uma vez que um pedido é bloqueado, todos os solicitadores são notificados que lhes foi negado acesso ao pedido. O bloqueio de um pedido cria um objeto principal de serviço para o pedido no seu inquilino em estado de deficiente. Os utilizadores não poderão solicitar o consentimento da administração da aplicação no futuro.
 
## <a name="email-notifications"></a>Notificações por e-mail
 
Se configurar, todos os revisores receberão notificações por e-mail quando:

* Foi criado um novo pedido
* Um pedido expirou
* Um pedido está perto da data de validade  
 
Os solicitadores receberão notificações por e-mail quando:

* Eles submetem um novo pedido de acesso
* O seu pedido expirou.
* O seu pedido foi negado ou bloqueado.
* O seu pedido foi aprovado.
 
## <a name="audit-logs"></a>Registos de auditoria 
 
O quadro abaixo descreve os cenários e os valores de auditoria disponíveis para o fluxo de trabalho de consentimento administrativo. 

> [!NOTE]
> O contexto de utilizador do ator de auditoria está atualmente desaparecido em todos os cenários. Esta é uma limitação conhecida na versão de pré-visualização.


|Scenario  |Serviço de Auditoria  |Categoria de Auditoria  |Atividade de Auditoria  |Ator de Auditoria  |Limitações do registo de auditoria  |
|---------|---------|---------|---------|---------|---------|
|Administração que permite o fluxo de trabalho do pedido de consentimento        |Revisões de Acesso           |Gestão de Utilizador           |Criar modelo de política de governação          |Contexto de aplicativo            |Atualmente não é possível encontrar o contexto do utilizador            |
|Administração desativando o fluxo de trabalho do pedido de consentimento       |Revisões de Acesso           |Gestão de Utilizador           |Eliminar o modelo de política de governação          |Contexto de aplicativo            |Atualmente não é possível encontrar o contexto do utilizador           |
|Administração atualizando as configurações do fluxo de trabalho de consentimento        |Revisões de Acesso           |Gestão de Utilizador           |Atualizar o modelo de política de governação          |Contexto de aplicativo            |Atualmente não é possível encontrar o contexto do utilizador           |
|Utilizador final criando um pedido de consentimento administrativo para uma aplicação       |Revisões de Acesso           |Política         |Criar pedido           |Contexto de aplicativo            |Atualmente não é possível encontrar o contexto do utilizador           |
|Revisores que aprovam um pedido de consentimento administrativo       |Revisões de Acesso           |Gestão de Utilizador           |Aprovar todos os pedidos no fluxo de negócios          |Contexto de aplicativo            |Atualmente não é possível encontrar o contexto do utilizador ou o ID da aplicação que foi concedido consentimento administrativo.           |
|Revisores que negam um pedido de consentimento administrativo       |Revisões de Acesso           |Gestão de Utilizador           |Aprovar todos os pedidos no fluxo de negócios          |Contexto de aplicativo            | Atualmente não é possível encontrar o contexto de utilizador do ator que negou um pedido de consentimento administrativo          |

## <a name="faq"></a>FAQ 

**Liguei este fluxo de trabalho, mas ao testar a funcionalidade, por que não vejo a nova "Aprovação necessária" que me permite solicitar acesso?**

Depois de ligar a funcionalidade, pode demorar até 60 minutos para os utilizadores finais verem a atualização. Pode verificar se a configuração teve um efeito adequado ao visualizar o valor **enableAdminConsentRequests** na `https://graph.microsoft.com/beta/settings` API.

**Como revisor, por que não posso ver todos os pedidos pendentes?**

Os revisores só podem ver pedidos de administração que foram criados depois de terem sido designados como revisores. Por isso, se foi recentemente adicionado como revisor, não verá nenhum pedido que tenha sido criado antes da sua atribuição.

**Como revisor, por que vejo vários pedidos para a mesma aplicação?**
  
Se um desenvolvedor de aplicações tiver configurado a sua app para usar o consentimento estático e dinâmico para solicitar o acesso aos dados do utilizador final, verá dois pedidos de consentimento administrativo. Um pedido representa as permissões estáticas, e o outro representa as permissões dinâmicas.

**Como solicitador, posso verificar o estado do meu pedido?**  

Não, por enquanto os solicitadores só podem obter atualizações através de notificações por e-mail.

**Como revisor, é possível aprovar o pedido, mas não para todos?**
 
Se está preocupado em conceder o consentimento administrativo e permitir que todos os utilizadores do arrendatário utilizem a aplicação, recomendamos que negue o pedido. Em seguida, conceda manualmente o consentimento da administração, restringindo o acesso à aplicação, exigindo a atribuição do utilizador e atribuindo utilizadores ou grupos à aplicação. Para obter mais informações, consulte [Métodos para atribuir utilizadores e grupos.](./assign-user-or-group-access-portal.md)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o consentimento dos pedidos, consulte [o quadro de consentimento do Diretório Ativo Azure](../develop/consent-framework.md).

[Configurar a forma como os utilizadores finais concedem consentimento às aplicações](configure-user-consent.md)

[Conceder consentimento de administrador ao nível do inquilino a uma aplicação](grant-admin-consent.md)

[Permissões e consentimento na plataforma de identidade da Microsoft](../develop/v2-permissions-and-consent.md)

[Azure AD no Microsoft Q&A](/answers/topics/azure-active-directory.html)