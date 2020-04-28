---
title: Configure o fluxo de trabalho de consentimento do administrador - Diretório Ativo Azure [ Azure Ative Diretório ] Microsoft Docs
description: Saiba como configurar uma forma de os utilizadores finais solicitarem o acesso a aplicações que requerem consentimento administrativo.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83b3f0d97daf0b4ac17f74981119b380d1776d97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75430206"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Configure o fluxo de trabalho de consentimento do administrador (pré-visualização)

Este artigo descreve como ativar a funcionalidade de fluxo de trabalho de consentimento administrativo (pré-visualização), que dá aos utilizadores finais uma forma de solicitar o acesso a aplicações que requerem consentimento administrativo.

Sem um fluxo de trabalho de consentimento administrativo, um utilizador de um inquilino onde o consentimento do utilizador é desativado será bloqueado quando tentar aceder a qualquer app que necessite de permissões para aceder a dados organizacionais. O utilizador vê uma mensagem de erro genérica que diz não estar autorizada a aceder à aplicação e deve pedir ajuda ao seu administrador. Mas, muitas vezes, o utilizador não sabe a quem contactar, pelo que ou desiste ou cria uma nova conta local na aplicação. Mesmo quando um administrador é notificado, nem sempre existe um processo simplificado para ajudar o administrador a conceder acesso e notificar os seus utilizadores.
 
O fluxo de trabalho de consentimento administrativo dá aos administradores uma forma segura de conceder acesso a pedidos que requerem aprovação administrativa. Quando um utilizador tenta aceder a uma aplicação, mas não consegue dar consentimento, pode enviar um pedido de aprovação administrativa. O pedido é enviado por e-mail para administradores que tenham sido designados como revisores. Um revisor toma medidas sobre o pedido, e o utilizador é notificado da ação.

Para aprovar pedidos, um revisor deve ser um administrador global, administrador de aplicações na nuvem ou administrador de aplicações. O revisor já deve ter uma destas funções de administrador atribuídas; simplesmente designá-los como revisor não eleva os seus privilégios.

## <a name="enable-the-admin-consent-workflow"></a>Ativar o fluxo de trabalho de consentimento administrativo

Para ativar o fluxo de trabalho de consentimento do administrador e escolher os revisores:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) como administrador global.
2. Clique em **todos os serviços** no topo do menu de navegação à esquerda. Abre a **extensão do Diretório Ativo Azure.**
3. Na caixa de pesquisa de filtros, escreva "**Azure Ative Directory**" e selecione o item **azure Ative Directory.**
4. A partir do menu de navegação, clique em **aplicações Da Enterprise**. 
5. Em **'Gerir',** selecione **as definições do utilizador**.
6. No âmbito dos pedidos de **consentimento da Admin (Pré-visualização),** os **Utilizadores podem solicitar o consentimento do administrador para aplicações** que não possam consentir ao **Sim**.

   ![Configure configurações de fluxo de trabalho de consentimento de administrador](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Configure as seguintes definições:

   * **Selecione utilizadores para rever pedidos**de consentimento administrativo . Selecione revisores para este fluxo de trabalho a partir de um conjunto de utilizadores que tenham o administrador global, administrador de aplicações na nuvem e funções de administrador de aplicações.
   * **Os utilizadores selecionados receberão notificações por e-mail para pedidos.** Ativar ou desativar notificações de e-mail para os revisores quando um pedido é feito.  
   * **Os utilizadores selecionados receberão lembretes**de validade do pedido. Ativar ou desativar notificações de e-mail de lembrete aos revisores quando um pedido está prestes a expirar.  
   * **O pedido de consentimento expira após (dias)**. Especifique quanto tempo os pedidos permanecem válidos.

7. Selecione **Guardar**. Pode levar até uma hora para que a funcionalidade fique ativada.

> [!NOTE]
> Pode adicionar ou remover revisores para este fluxo de trabalho modificando a lista de revisores de pedidos de consentimento de **administrador Select.** Note que uma limitação atual desta funcionalidade é que os revisores podem manter a capacidade de rever os pedidos que foram feitos enquanto foram designados como revisores.

## <a name="how-users-request-admin-consent"></a>Como os utilizadores solicitam consentimento administrativo

Após a ativação do fluxo de trabalho de consentimento da administração, os utilizadores podem solicitar a aprovação do administrador para uma aplicação à qual não estão autorizados a consentir. Os seguintes passos descrevem a experiência do utilizador ao solicitar a aprovação. 

1. O utilizador tenta iniciar sessão na aplicação.

2. A mensagem necessária à **aprovação** aparece. O utilizador escreve uma justificação para necessitar de acesso à app e, em seguida, seleciona a **aprovação**do Pedido .

   ![Pedido e justificação do utilizador de consentimento da Administração](media/configure-admin-consent-workflow/end-user-justification.png)

3. Uma mensagem **enviada** confirma que o pedido foi submetido ao administrador. Se o utilizador enviar vários pedidos, apenas o primeiro pedido é submetido ao administrador.

   ![Pedido e justificação do utilizador de consentimento da Administração](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. O utilizador recebe uma notificação por e-mail quando o seu pedido é aprovado, negado ou bloqueado. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Rever e tomar medidas sobre pedidos de consentimento da administração

Para rever os pedidos de consentimento do administrador e tomar medidas:

1. Inscreva-se no [portal Azure](https://portal.azure.com) como um dos revisores registados do fluxo de trabalho de consentimento do administrador.
2. Selecione **Todos os serviços** no topo do menu de navegação à esquerda. Abre a **extensão do Diretório Ativo Azure.**
3. Na caixa de pesquisa de filtros, escreva "**Azure Ative Directory**" e selecione o item **azure Ative Directory.**
4. A partir do menu de navegação, clique em **aplicações Da Enterprise**.
5. No âmbito **da Atividade,** selecione pedidos de **consentimento do Administrador (Pré-visualização)**.

   > [!NOTE]
   > Os revisores só verão pedidos de administração que foram criados depois de terem sido designados como revisores.

1. Selecione o pedido que está a ser solicitado.
2. Analisar detalhes sobre o pedido:  

   * Para ver quem está a pedir acesso e porquê, selecione o **Solicitado por** conta.
   * Para ver que permissões estão a ser solicitadas pelo pedido, selecione **permissões de revisão e consentimento**.

8. Avaliar o pedido e tomar as medidas adequadas:

   * **Aprove o pedido.** Para aprovar um pedido, conceda consentimento ao administrador para o pedido. Uma vez aprovado um pedido, todos os solicitados são notificados de que lhes foi concedido acesso.  
   * **Negue o pedido.** Para negar um pedido, deve fornecer uma justificação que será fornecida a todos os solicitadores. Uma vez negado um pedido, todos os pedidos são notificados de que lhes foi negado o acesso ao pedido. Negar um pedido não impedirá os utilizadores de solicitarem o consentimento da administração para a app novamente no futuro.  
   * **Bloqueie o pedido.** Para bloquear um pedido, deve fornecer uma justificação que será fornecida a todos os solicitadores. Uma vez bloqueado um pedido, todos os pedidos são notificados de que lhes foi negado acesso ao pedido. Bloquear um pedido cria um objeto principal de serviço para a aplicação no seu inquilino em estado de deficiência. Os utilizadores não poderão solicitar o consentimento do administrador para a aplicação no futuro.
 
## <a name="email-notifications"></a>Notificações por e-mail
 
Se configurado, todos os revisores receberão notificações de e-mail quando:

* Foi criado um novo pedido
* Um pedido expirou
* Um pedido está perto da data de validade  
 
Os requestdores receberão notificações por e-mail quando:

* Apresentam um novo pedido de acesso
* O seu pedido expirou.
* O seu pedido foi negado ou bloqueado.
* O seu pedido foi aprovado.
 
## <a name="audit-logs"></a>Registos de auditoria 
 
O quadro abaixo descreve os cenários e os valores de auditoria disponíveis para o fluxo de trabalho de consentimento do administrador. 

> [!NOTE]
> O contexto de utilizador do ator de auditoria está atualmente em falta em todos os cenários. Esta é uma limitação conhecida na versão de pré-visualização.


|Cenário  |Serviço de Auditoria  |Categoria de Auditoria  |Atividade de Auditoria  |Ator de Auditoria  |Limitações de registo de auditoria  |
|---------|---------|---------|---------|---------|---------|
|Administrador que permite o fluxo de trabalho do pedido de consentimento        |Revisões de Acesso           |Gestão de Utilizadores           |Criar modelo de política de governação          |Contexto de aplicativos            |Atualmente não encontra o contexto do utilizador            |
|Administrador desativando o fluxo de trabalho do pedido de consentimento       |Revisões de Acesso           |Gestão de Utilizadores           |Eliminar modelo de política de governação          |Contexto de aplicativos            |Atualmente não encontra o contexto do utilizador           |
|Administração atualizando as configurações de fluxo de trabalho de consentimento        |Revisões de Acesso           |Gestão de Utilizadores           |Atualizar modelo de política de governação          |Contexto de aplicativos            |Atualmente não encontra o contexto do utilizador           |
|Utilizador final criando um pedido de consentimento administrativo para uma app       |Revisões de Acesso           |Política         |Criar pedido           |Contexto de aplicativos            |Atualmente não encontra o contexto do utilizador           |
|Revisores que aprovam um pedido de consentimento da administração       |Revisões de Acesso           |Gestão de Utilizadores           |Aprovar todos os pedidos no fluxo empresarial          |Contexto de aplicativos            |Atualmente não é possível encontrar o contexto do utilizador ou o ID da aplicação que foi concedido consentimento administrativo.           |
|Revisores que negam um pedido de consentimento da administração       |Revisões de Acesso           |Gestão de Utilizadores           |Aprovar todos os pedidos no fluxo empresarial          |Contexto de aplicativos            | Atualmente não é possível encontrar o contexto de utilizador do ator que negou um pedido de consentimento administrativo          |

## <a name="faq"></a>FAQ 

**Eu desliguei este fluxo de trabalho, mas ao testar a funcionalidade, por que não posso ver a nova "Aprovação necessária" pronta permitindo-me pedir acesso?**

Depois de ligar a funcionalidade, pode demorar até 60 minutos para os utilizadores finais verem a atualização. Pode verificar se a configuração teve efeito corretamente ao visualizar `https://graph.microsoft.com/beta/settings` o valor **enableAdminConsentRequests** na API.

**Como revisor, por que não vejo todos os pedidos pendentes?**

Os revisores só podem ver pedidos de administração que foram criados depois de terem sido designados como revisores. Por isso, se foi recentemente adicionado como revisor, não verá nenhum pedido que tenha sido criado antes da sua atribuição.

**Como revisor, por que vejo vários pedidos para a mesma aplicação?**
  
Se um desenvolvedor de aplicações tiver configurado a sua aplicação para usar consentimento estático e dinâmico para solicitar o acesso aos dados do utilizador final, verá dois pedidos de consentimento administrativo. Um pedido representa as permissões estáticas, e o outro representa as permissões dinâmicas.

**Como solicitador, posso verificar o estado do meu pedido?**  

Não, por enquanto os pedidos só podem obter atualizações através de notificações de e-mail.

**Como revisor, é possível aprovar o pedido, mas não para todos?**
 
Se está preocupado em conceder o consentimento do administrador e permitir que todos os utilizadores do inquilino utilizem a aplicação, recomendamos que negue o pedido. Em seguida, concede manualmente o consentimento do administrador, restringindo o acesso à aplicação exigindo a atribuição do utilizador e atribuindo utilizadores ou grupos à aplicação. Para mais informações, consulte [Métodos de atribuição de utilizadores e grupos.](methods-for-assigning-users-and-groups.md)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o consentimento das candidaturas, consulte o quadro de [consentimento do Diretório Ativo do Azure](../develop/consent-framework.md).

[Configure como os utilizadores finais consentem com as aplicações](configure-user-consent.md)

[Conceda o consentimento do administrador de todo o inquilino a um pedido](grant-admin-consent.md)

[Permissões e consentimento na plataforma de identidade da Microsoft](../develop/active-directory-v2-scopes.md)

[Anúncio Azure em StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
