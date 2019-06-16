---
title: Configurar e aprovar o acesso just-in-time para aplicações geridas do Azure
description: Descreve como os consumidores de aplicações geridas do Azure aprovar pedidos de acesso just-in-time a uma aplicação gerida.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 55fbf7292ab894cad3de3de9e96ddc96fe0b79b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481718"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Configurar e aprovar o acesso just-in-time para aplicações geridas do Azure

Como um consumidor de um aplicativo gerenciado, talvez não esteja à vontade de dando o publicador acesso permanente ao grupo de recursos gerido. Para obter maior controlo sobre a concessão do acesso a recursos geridos, as aplicações geridas do Azure fornece um recurso chamado acesso de (JIT) just-in-time, o que está atualmente em pré-visualização. Ele permite-lhe aprovar quando e para o quanto o publicador tem acesso ao grupo de recursos. O Editor pode efetuar atualizações necessárias durante esse tempo, mas quando essa hora está acima, o acesso do publicador expira.

O fluxo de trabalho para a concessão de acesso é:

1. O publicador adiciona uma aplicação gerida no Marketplace e especifica que o acesso JIT está disponível.

1. Durante a implementação, ativar o acesso JIT para a sua instância da aplicação gerida.

1. Após a implementação, pode alterar as definições de acesso JIT.

1. O publicador envia um pedido de acesso.

1. Aprovar o pedido.

Este artigo enfoca as ações que os consumidores seguir para ativar o acesso JIT e aprovar pedidos. Para saber mais sobre a publicação de uma aplicação gerida com acesso JIT, veja [pedir acesso just-in-time nas aplicações geridas do Azure](request-just-in-time-access.md).

> [!NOTE]
> Para utilizar o acesso just-in-time, tem de ter uma [licença do Azure Active Directory P2](../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Ativar durante a implementação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Localize uma entrada de marketplace para uma aplicação gerida com o JIT ativada. Selecione **Criar**.

1. Ao fornecer valores para a nova aplicação gerida, o **JIT configuração** passo permite-lhe ativar ou desativar o acesso JIT para a aplicação gerida. Selecione **Sim** para **ativar o acesso JIT**. Esta opção está selecionada por predefinição para aplicativos gerenciados que definidos com o JIT ativada no marketplace.

   ![Configurar o acesso](./media/approve-just-in-time-access/configure-jit-access.png)

   Apenas pode ativar o acesso JIT durante a implementação. Se selecionou **não**, o publicador obtém acesso permanente ao grupo de recursos gerido. Não é possível ativar o acesso JIT mais tarde.

1. Para alterar as predefinições de aprovação, selecione **Personalizar configuração de JIT**.

   ![Personalizar o acesso](./media/approve-just-in-time-access/customize-jit-access.png)

   Por predefinição, uma aplicação gerida com o JIT ativada, tem as seguintes definições:

   * Modo de aprovação – automática
   * Duração máxima de acesso – 8 horas
   * Os aprovadores – nenhum

   Quando o modo de aprovação está definido como **automática**, aprovadores recebem uma notificação para cada solicitação, mas o pedido é aprovado automaticamente. Quando definido como **manual**aprovadores recebem uma notificação para cada solicitação e um deles tem de aprová-lo.

   A duração máxima da ativação Especifica a quantidade máxima de tempo de que um publicador pode pedir para acesso ao grupo de recursos gerido.

   A lista de aprovadores é os utilizadores do Azure Active Directory que podem aprovar de pedidos de acesso JIT. Para adicionar um aprovador, selecione **adicionar aprovador** e procure o utilizador.

   Depois de atualizar a definição, selecione **guardar**.

## <a name="update-after-deployment"></a>Após a implementação de atualização

Pode alterar os valores para a forma como os pedidos são aprovados. No entanto, se não tiver ativado o acesso JIT durante a implementação, não é possível ativá-la mais tarde.

Para alterar as definições para uma aplicação gerida implementada:

1. No portal, selecione a aplicação de gerir.

1. Selecione **JIT configuração** e alterar as definições conforme necessário.

   ![Alterar as definições de acesso](./media/approve-just-in-time-access/change-settings.png)

1. Quando terminar, selecione **Guardar**.

## <a name="approve-requests"></a>Aprovar pedidos

Quando o publicador solicita acesso, receberá uma notificação do pedido. Pode aprovar pedidos de acesso JIT diretamente por meio da aplicação gerida ou em todas as aplicações geridas através do serviço do Azure AD Privileged Identity Management. Para utilizar o acesso just-in-time, tem de ter uma [licença do Azure Active Directory P2](../active-directory/privileged-identity-management/subscription-requirements.md).

Para aprovar pedidos através do aplicativo gerenciado:

1. Selecione **acesso JIT** para a aplicação gerida e selecione **aprovar pedidos**.

   ![Aprovar pedidos](./media/approve-just-in-time-access/approve-requests.png)
 
1. Selecione o pedido para aprovar.

   ![Selecione a pedido](./media/approve-just-in-time-access/select-request.png)

1. No formulário, indique o motivo para a aprovação e selecione **aprovar**.

Para aprovar pedidos através do Azure AD Privileged Identity Management:

1. Selecione **todos os serviços** e começar a procurar **Azure AD Privileged Identity Management**. Selecione-o nas opções disponíveis.

   ![Procurar por serviço](./media/approve-just-in-time-access/search.png)

1. Selecione **aprovar pedidos**.

   ![Selecione aprovar pedidos](./media/approve-just-in-time-access/select-approve-requests.png)

1. Selecione **aplicações geridas do Azure**e selecione o pedido para aprovar.

   ![Seleccione os pedidos](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a publicação de uma aplicação gerida com acesso JIT, veja [pedir acesso just-in-time nas aplicações geridas do Azure](request-just-in-time-access.md).
