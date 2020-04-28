---
title: Aprovar acesso just-in-time
description: Descreve como os consumidores de Aplicações Geridas azure aprovam pedidos de acesso just-in-time a uma aplicação gerida.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75651023"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Configure e aprove o acesso just-in-time para aplicações geridas pelo Azure

Como consumidor de uma aplicação gerida, pode não se sentir confortável em dar ao editor acesso permanente ao grupo de recursos geridos. Para lhe conferir um maior controlo sobre a concessão de acesso a recursos geridos, a Azure Managed Applications fornece uma funcionalidade chamada acesso just-in-time (JIT), que está atualmente em pré-visualização. Permite-lhe aprovar quando e por quanto tempo a editora tem acesso ao grupo de recursos. A editora pode fazer atualizações necessárias durante esse período, mas quando esse tempo terminar, o acesso da editora expira.

O fluxo de trabalho para a concessão de acesso é:

1. A editora adiciona uma aplicação gerida ao mercado e especifica que o acesso ao JIT está disponível.

1. Durante a implementação, permite o acesso ao JIT para a sua aplicação gerida.

1. Após a implementação, pode alterar as definições para acesso jIT.

1. A editora envia um pedido de acesso.

1. Aprova o pedido.

Este artigo centra-se nas ações que os consumidores tomam para permitir o acesso ao JIT e aprovar pedidos. Para saber publicar uma aplicação gerida com acesso jIT, consulte [Solicit just-in-time access in-time in Azure Managed Applications](request-just-in-time-access.md).

> [!NOTE]
> Para utilizar o acesso just-in-time, deve ter uma [licença Azure Ative Directory P2](../../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Ativar durante a implantação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Encontre uma entrada no mercado para uma aplicação gerida com JIT habilitado. Selecione **Criar**.

1. Ao mesmo tempo que fornece valores para a nova aplicação gerida, o passo **de Configuração JIT** permite-lhe ativar ou desativar o acesso do JIT à aplicação gerida. Selecione **Sim** para **ativar o acesso ao JIT**. Esta opção é selecionada por padrão para aplicações geridas que definidas com JIT ativadas no mercado.

   ![Configure o acesso](./media/approve-just-in-time-access/configure-jit-access.png)

   Só é possível ativar o acesso ao JIT durante a implantação. Se selecionar **Não,** a editora obtém acesso permanente ao grupo de recursos geridos. Não pode permitir o acesso ao JIT mais tarde.

1. Para alterar as definições de aprovação predefinidas, selecione Personalizar a **configuração jIT**.

   ![Personalizar o acesso](./media/approve-just-in-time-access/customize-jit-access.png)

   Por predefinição, uma aplicação gerida com JIT ativada tem as seguintes definições:

   * Modo de aprovação – automático
   * Duração máxima de acesso - 8 horas
   * Aprovadores - nenhum

   Quando o modo de aprovação é definido **para automático,** os aprovadores recebem uma notificação para cada pedido, mas o pedido é automaticamente aprovado. Quando definidos **manualmente,** os aprovadores recebem uma notificação para cada pedido, e um deles deve aprová-lo.

   A duração máxima de ativação especifica o tempo máximo que um editor pode solicitar para aceder ao grupo de recursos geridos.

   A lista de aprovadores é o Diretório Ativo Azure que pode aprovar pedidos de acesso jit. Para adicionar um aprovador, selecione **Adicionar O Aforr** e procurar o utilizador.

   Depois de atualizar a definição, selecione **Guardar**.

## <a name="update-after-deployment"></a>Atualizar após a implementação

Pode alterar os valores de como os pedidos são aprovados. No entanto, se não permitiu o acesso ao JIT durante a implementação, não poderá permitir mais tarde.

Para alterar as definições para uma aplicação gerida implantada:

1. No portal, selecione a aplicação de gestão.

1. Selecione **configuração JIT** e altere as definições conforme necessário.

   ![Alterar as definições de acesso](./media/approve-just-in-time-access/change-settings.png)

1. Quando terminar, selecione **Guardar**.

## <a name="approve-requests"></a>Aprovar pedidos

Quando a editora pede acesso, é notificado do pedido. Pode aprovar pedidos de acesso jit diretamente através da aplicação gerida, ou em todas as aplicações geridas através do serviço de Gestão de Identidade Privilegiada azure AD. Para utilizar o acesso just-in-time, deve ter uma [licença Azure Ative Directory P2](../../active-directory/privileged-identity-management/subscription-requirements.md).

Para aprovar pedidos através do pedido gerido:

1. Selecione **Acesso JIT** para a aplicação gerida e selecione **Pedidos de Aprovação**.

   ![Aprovar pedidos](./media/approve-just-in-time-access/approve-requests.png)
 
1. Selecione o pedido para aprovar.

   ![Selecione pedido](./media/approve-just-in-time-access/select-request.png)

1. No formulário, forneça o motivo da aprovação e selecione **Aprovar**.

Para aprovar pedidos através da Azure AD Privileged Identity Management:

1. Selecione **Todos os serviços** e comece a procurar a **Azure AD Privileged Identity Management**. Selecione-o a partir das opções disponíveis.

   ![Procurar serviço](./media/approve-just-in-time-access/search.png)

1. Selecione **Aprovar pedidos**.

   ![Selecione aprovar pedidos](./media/approve-just-in-time-access/select-approve-requests.png)

1. Selecione **aplicações geridas pelo Azure**e selecione o pedido de aprovação.

   ![Selecione pedidos](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Passos seguintes

Para saber publicar uma aplicação gerida com acesso jIT, consulte [Solicit just-in-time access in-time in Azure Managed Applications](request-just-in-time-access.md).
