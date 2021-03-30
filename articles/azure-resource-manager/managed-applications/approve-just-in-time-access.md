---
title: Aprovar acesso just-in-time
description: Descreve como os consumidores de Aplicações Geridas Azure aprovam pedidos de acesso just-in-time a uma aplicação gerida.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "75651023"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Configure e aprove acesso just-in-time para aplicações geridas azure

Como consumidor de uma aplicação gerida, pode não se sentir confortável em dar ao editor acesso permanente ao grupo de recursos geridos. Para lhe dar um maior controlo sobre a concessão de acesso a recursos geridos, a Azure Managed Applications fornece uma funcionalidade chamada acesso just-in-time (JIT), que está atualmente em pré-visualização. Permite-lhe aprovar quando e por quanto tempo a editora tem acesso ao grupo de recursos. A editora pode fazer as atualizações necessárias durante esse tempo, mas quando esse tempo acabar, o acesso da editora expira.

O fluxo de trabalho para a concessão de acesso é:

1. A editora adiciona uma aplicação gerida ao mercado e especifica que o acesso ao JIT está disponível.

1. Durante a implementação, permite o acesso ao JIT para o seu exemplo da aplicação gerida.

1. Após a implementação, pode alterar as definições para acesso JIT.

1. A editora envia um pedido de acesso.

1. Aprova o pedido.

Este artigo centra-se nas ações que os consumidores tomam para permitir o acesso ao JIT e aprovar pedidos. Para saber mais sobre a publicação de uma aplicação gerida com acesso JIT, consulte [Pedido de acesso just-in-time em Aplicações Geridas Azure](request-just-in-time-access.md).

> [!NOTE]
> Para utilizar o acesso just-in-time, tem de ter uma [licença Azure Ative Directory P2](../../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Ativar durante a implantação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Encontre uma entrada no mercado para uma aplicação gerida com JIT ativada. Selecione **Criar**.

1. Ao mesmo tempo que fornece valores para a nova aplicação gerida, o passo **de Configuração JIT** permite-lhe ativar ou desativar o acesso jit para a aplicação gerida. Selecione **Sim** para **Ativar o Acesso JIT**. Esta opção é selecionada por padrão para aplicações geridas que definiram com JIT ativado no mercado.

   ![Acesso configure](./media/approve-just-in-time-access/configure-jit-access.png)

   Só é possível ativar o acesso ao JIT durante a implantação. Se selecionar **Nº,** o editor terá acesso permanente ao grupo de recursos geridos. Não pode permitir o acesso ao JIT mais tarde.

1. Para alterar as definições de aprovação predefinidos, **selecione Personalizar configuração JIT**.

   ![Personalizar o acesso](./media/approve-just-in-time-access/customize-jit-access.png)

   Por predefinição, uma aplicação gerida com JIT ativada tem as seguintes definições:

   * Modo de aprovação – automático
   * Duração máxima de acesso – 8 horas
   * Aprovadores – nenhum

   Quando o modo de aprovação é definido como **automático,** os aprovadores recebem uma notificação para cada pedido, mas o pedido é automaticamente aprovado. Quando definidos como **manual,** os aprovadores recebem uma notificação para cada pedido, e um deles deve aprová-lo.

   A duração máxima da ativação especifica o período máximo de tempo que um editor pode solicitar para aceder ao grupo de recursos geridos.

   A lista de aprovadores é a Azure Ative Directory utilizadores que podem aprovar pedidos de acesso JIT. Para adicionar um aprovador, **selecione Add Approver** e procure o utilizador.

   Depois de atualizar a definição, **selecione Guardar**.

## <a name="update-after-deployment"></a>Atualização após implementação

Pode alterar os valores da forma como os pedidos são aprovados. No entanto, se não permitiu o acesso ao JIT durante a implementação, não pode ative-lo mais tarde.

Para alterar as definições de uma aplicação gerida implementada:

1. No portal, selecione a aplicação de gestão.

1. Selecione **configuração JIT** e altere as definições conforme necessário.

   ![Alterar definições de acesso](./media/approve-just-in-time-access/change-settings.png)

1. Quando terminar, selecione **Guardar**.

## <a name="approve-requests"></a>Aprovar pedidos

Quando a editora pede acesso, é notificado do pedido. Pode aprovar pedidos de acesso JIT diretamente através da aplicação gerida, ou em todas as aplicações geridas através do serviço de Gestão de Identidade Privilegiada Azure AD. Para utilizar o acesso just-in-time, tem de ter uma [licença Azure Ative Directory P2](../../active-directory/privileged-identity-management/subscription-requirements.md).

Para aprovar pedidos através da aplicação gerida:

1. Selecione **JIT Access** para a aplicação gerida e selecione **'Aprovar Pedidos'.**

   ![Aprovar pedidos](./media/approve-just-in-time-access/approve-requests.png)
 
1. Selecione o pedido de aprovação.

   ![Selecione pedido](./media/approve-just-in-time-access/select-request.png)

1. No formulário, forneça o motivo da aprovação e **selecione Aprovar**.

Para aprovar pedidos através da Azure AD Gestão de Identidade Privilegiada:

1. Selecione **Todos os serviços** e comece a procurar **a Azure AD Gestão de Identidade Privilegiada.** Selecione-o nas opções disponíveis.

   ![Pesquisa de serviço](./media/approve-just-in-time-access/search.png)

1. **Selecione Aprovar pedidos**.

   ![Selecione aprovar pedidos](./media/approve-just-in-time-access/select-approve-requests.png)

1. Selecione **as aplicações geridas pela Azure** e selecione o pedido para aprovar.

   ![Selecione pedidos](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a publicação de uma aplicação gerida com acesso JIT, consulte [Pedido de acesso just-in-time em Aplicações Geridas Azure](request-just-in-time-access.md).
