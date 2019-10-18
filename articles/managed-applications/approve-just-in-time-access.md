---
title: Aprovar o acesso just-in-time-aplicativo gerenciado do Azure
description: Descreve como os consumidores de aplicativos gerenciados do Azure aprovam solicitações de acesso just-in-time a um aplicativo gerenciado.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: dd5c3f21b1a4b71d129ccbebeaa7ee66274a672f
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529121"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Configurar e aprovar o acesso just-in-time para aplicativos gerenciados do Azure

Como um consumidor de um aplicativo gerenciado, talvez você não esteja familiarizado com o acesso permanente do Publicador ao grupo de recursos gerenciado. Para oferecer maior controle sobre a concessão de acesso a recursos gerenciados, os aplicativos gerenciados do Azure fornecem um recurso chamado acesso JIT (just-in-time), que está atualmente em versão prévia. Ele permite que você aprove quando e por quanto tempo o Publicador tem acesso ao grupo de recursos. O Publicador pode fazer as atualizações necessárias durante esse tempo, mas quando esse tempo terminar, o acesso do editor expirará.

O fluxo de trabalho para conceder acesso é:

1. O Publicador adiciona um aplicativo gerenciado ao Marketplace e especifica que o acesso JIT está disponível.

1. Durante a implantação, você habilita o acesso JIT para sua instância do aplicativo gerenciado.

1. Após a implantação, você pode alterar as configurações de acesso JIT.

1. O Publicador envia uma solicitação de acesso.

1. Você aprova a solicitação.

Este artigo se concentra nas ações que os consumidores adotam para habilitar o acesso JIT e aprovar solicitações. Para saber mais sobre como publicar um aplicativo gerenciado com acesso JIT, consulte [solicitar acesso just-in-time em aplicativos gerenciados do Azure](request-just-in-time-access.md).

> [!NOTE]
> Para usar o acesso just-in-time, você deve ter uma [licença Azure Active Directory P2](../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Habilitar durante a implantação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Encontre uma entrada do Marketplace para um aplicativo gerenciado com o JIT habilitado. Selecione **Criar**.

1. Ao fornecer valores para o novo aplicativo gerenciado, a etapa de **configuração JIT** permite habilitar ou desabilitar o acesso JIT para o aplicativo gerenciado. Selecione **Sim** para **habilitar o acesso JIT**. Essa opção é selecionada por padrão para aplicativos gerenciados que definidos com o JIT habilitado no Marketplace.

   ![Configurar o acesso](./media/approve-just-in-time-access/configure-jit-access.png)

   Você só pode habilitar o acesso JIT durante a implantação. Se você selecionar **não**, o Publicador obterá acesso permanente ao grupo de recursos gerenciado. Você não pode habilitar o acesso JIT posteriormente.

1. Para alterar as configurações de aprovação padrão, selecione **Personalizar configuração de JIT**.

   ![Personalizar o acesso](./media/approve-just-in-time-access/customize-jit-access.png)

   Por padrão, um aplicativo gerenciado com o JIT habilitado tem as seguintes configurações:

   * Modo de aprovação – automático
   * Duração máxima de acesso – 8 horas
   * Aprovadores – nenhum

   Quando o modo de aprovação é definido como **automático**, os aprovadores recebem uma notificação para cada solicitação, mas a solicitação é aprovada automaticamente. Quando definido como **manual**, os aprovadores recebem uma notificação para cada solicitação e um deles deve aprová-lo.

   A duração máxima da ativação especifica a quantidade máxima de tempo que um Publicador pode solicitar para acessar o grupo de recursos gerenciado.

   A lista de aprovadores é a Azure Active Directory usuários que podem aprovar solicitações de acesso JIT. Para adicionar um aprovador, selecione **Adicionar aprovador** e pesquise o usuário.

   Depois de atualizar a configuração, selecione **salvar**.

## <a name="update-after-deployment"></a>Atualizar após a implantação

Você pode alterar os valores de como as solicitações são aprovadas. No entanto, se você não tiver habilitado o acesso JIT durante a implantação, não poderá habilitá-lo mais tarde.

Para alterar as configurações de um aplicativo gerenciado implantado:

1. No portal, selecione o aplicativo gerenciar.

1. Selecione **configuração de JIT** e altere as configurações conforme necessário.

   ![Alterar configurações de acesso](./media/approve-just-in-time-access/change-settings.png)

1. Quando terminar, selecione **Guardar**.

## <a name="approve-requests"></a>Aprovar pedidos

Quando o Publicador solicitar acesso, você será notificado sobre a solicitação. Você pode aprovar solicitações de acesso JIT diretamente por meio do aplicativo gerenciado ou em todos os aplicativos gerenciados por meio do serviço de Azure AD Privileged Identity Management. Para usar o acesso just-in-time, você deve ter uma [licença Azure Active Directory P2](../active-directory/privileged-identity-management/subscription-requirements.md).

Para aprovar solicitações por meio do aplicativo gerenciado:

1. Selecione **acesso JIT** para o aplicativo gerenciado e selecione **aprovar solicitações**.

   ![Aprovar pedidos](./media/approve-just-in-time-access/approve-requests.png)
 
1. Selecione a solicitação a ser aprovada.

   ![Selecionar solicitação](./media/approve-just-in-time-access/select-request.png)

1. No formulário, forneça o motivo para a aprovação e selecione **aprovar**.

Para aprovar solicitações por meio de Azure AD Privileged Identity Management:

1. Selecione **todos os serviços** e comece a procurar por **Azure ad Privileged Identity Management**. Selecione-o nas opções disponíveis.

   ![Pesquisar serviço](./media/approve-just-in-time-access/search.png)

1. Selecione **aprovar solicitações**.

   ![Selecione aprovar solicitações](./media/approve-just-in-time-access/select-approve-requests.png)

1. Selecione **aplicativos gerenciados do Azure**e selecione a solicitação a ser aprovada.

   ![Solicitações de seleção](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como publicar um aplicativo gerenciado com acesso JIT, consulte [solicitar acesso just-in-time em aplicativos gerenciados do Azure](request-just-in-time-access.md).
