---
title: Criar uma subscrição do Azure adicional
description: Saiba como adicionar uma nova subscrição do Azure no portal do Azure. Veja informações sobre os formulários de contas de faturação e recursos disponíveis adicionais.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 006c995217ced91690798ae68f04e3b6c3148228
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131960"
---
# <a name="create-an-additional-azure-subscription"></a>Criar uma subscrição do Azure adicional

Pode criar uma subscrição adicional para a conta de faturação do [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Contrato de Cliente da Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) ou [Contrato de Parceiro da Microsoft](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) no portal do Azure. Talvez queira uma subscrição adicional para evitar atingir os limites da subscrição, para criar ambientes separados ppor motivos de segurança ou para isolar dados por motivos de conformidade.

Se tiver uma conta de faturação do Programa do Serviço Online da Microsoft (MOSP), poderá criar subscrições adicionais no [portal de inscrição do Azure](https://account.azure.com/signup?offer=ms-azr-0003p).

Para saber mais sobre as contas de faturação e identificar o tipo da conta de faturação, veja [Ver as contas de faturação no portal do Azure](view-all-accounts.md).

## <a name="permission-required-to-create-azure-subscriptions"></a>Permissões necessárias para criar subscrições do Azure

Precisará das seguintes permissões para criar subscrições:

|Conta de faturação  |Permissão  |
|---------|---------|
|Contrato Enterprise (EA) |  Função de Proprietário de Conta na inscrição do Contrato Enterprise. Para obter mais informações, veja [Compreender as funções administrativas do Contrato Enterprise no Azure](understand-ea-roles.md).    |
|Contrato de Cliente da Microsoft (MCA) |  Função de proprietário ou contribuidor na secção da fatura, no perfil de faturação ou na conta de faturação. Ou função de criador da subscrição do Azure na secção da fatura.  Para obter mais informações, veja [Tarefa e funções da faturação da subscrição](understand-mca-roles.md#subscription-billing-roles-and-tasks).    |
|Contrato de Parceiro da Microsoft (MPA) |   Função de Administrador Global e de Agente de Administração na organização de parceiro CSP. Para saber mais, veja [Partner Center - Assign users roles and permissions](/partner-center/permissions-overview) (Centro de Parceiros – Atribuir funções e permissões de utilizadores).  O utilizador tem de iniciar sessão no inquilino parceiro para criar subscrições do Azure.   |

## <a name="create-a-subscription-in-the-azure-portal"></a>Criar uma subscrição no portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Escreva **Subscrições** na caixa de pesquisa.

   ![Captura de ecrã que mostra a pesquisa de uma subscrição no portal](./media/create-subscription/billing-search-subscription-portal.png)

1. Selecione **Adicionar**.

   ![Captura de ecrã que mostra o botão Adicionar na vista Subscrições](./media/create-subscription/subscription-add.png)

1. Se tiver acesso a várias contas de faturação, selecione a conta de faturação para a qual quer criar a subscrição.

1. Preencha o formulário e clique em **Criar**. As tabelas a seguir listam os campos no formulário para cada tipo de conta de faturação.

**Contrato Enterprise**

|Campo  |Definição  |
|---------|---------|
|Nome     | O nome a apresentar ajuda-o a identificar facilmente a subscrição no portal do Azure.  |
|Oferta     | Selecione EA Dev/Test se planear utilizar esta subscrição para desenvolvimento ou teste das cargas de trabalho. Caso contrário, utilize o Microsoft Azure Enterprise. A oferta DevTest deve estar ativada na conta de inscrição para criar subscrições EA Dev/Test.|

**Contrato de Cliente da Microsoft**

|Campo  |Definição  |
|---------|---------|
|Perfil de faturação     | Os custos da subscrição serão faturados no perfil de faturação que selecionar. Se tiver acesso a apenas um perfil de faturação, a seleção estará desativada.     |
|Secção de fatura     | Os custos da subscrição vão aparecer nesta secção da fatura do perfil de faturação. Se tiver acesso a apenas uma secção de fatura, a seleção estará desativada.  |
|Planear     | Selecione o Plano do Microsoft Azure para DevTest se planear utilizar esta subscrição para desenvolvimento ou teste das cargas de trabalho. Caso contrário, utilize o Plano do Microsoft Azure. Se apenas um plano estiver ativado para o perfil de faturação, a seleção estará desativada.  |
|Nome     | O nome a apresentar ajuda-o a identificar facilmente a subscrição no portal do Azure.  |

**Contrato de Parceiro da Microsoft**

|Campo  |Definição  |
|---------|---------|
|Cliente    | A subscrição é criada para o cliente que selecionar. Se tiver apenas um cliente, a seleção estará desativada.  |
|Revendedor    | O revendedor que prestará os serviços ao cliente. Este é um campo opcional, apenas aplicável a Fornecedores indiretos no modelo de duas camadas do CSP. |
|Nome     | O nome a apresentar ajuda-o a identificar facilmente a subscrição no portal do Azure.  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>Criar uma subscrição do Azure adicional através de programação

Também pode criar subscrições adicionais através de programação. Para obter mais informações, veja [Criar subscrições do Azure através de programação](programmatically-create-subscription.md).

## <a name="next-steps"></a>Passos seguintes

- [Adicionar ou alterar administradores de subscrição do Azure](add-change-subscription-administrator.md)
- [Move Resources to New Resource Group or Subscription (Mover recursos para um grupo de recursos ou subscrição nova)](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Criar grupos de gestão para a organização e gestão de recursos](../../governance/management-groups/create-management-group-portal.md)
- [Cancelar a subscrição do Azure](cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).