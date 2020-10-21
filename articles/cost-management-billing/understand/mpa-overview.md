---
title: Introdução à conta de faturação do Contrato de Parceiro da Microsoft – Azure CSP
description: Compreender a conta de faturação do Contrato de Parceiro da Microsoft (CSP)
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 4dff56db9f1450ff7eb2a2b9ca6f4ca648e2ac38
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150086"
---
# <a name="get-started-with-your-microsoft-partner-agreement-billing-account"></a>Introdução à conta de faturação do Contrato de Parceiro da Microsoft

Ao inscrever-se para utilizar o Azure, é criada uma conta de faturação. Utiliza a conta de faturação para gerir faturas, pagamentos e controlar custos. Pode ter acesso a múltiplas contas de faturação. Por exemplo, talvez se tenha inscrito no Azure com o objetivo de desenvolver projetos pessoais. Também poderá ter acesso ao Azure através do Contrato Enterprise, do Contrato de Cliente da Microsoft ou do Contrato de Parceiro da Microsoft da sua organização. Para cada um desses cenários, teria uma conta de faturação separada.

Este artigo aplica-se às contas de faturação dos Contratos de Parceiro da Microsoft. Estas contas foram criadas para os Fornecedores de Soluções Cloud (CSPs) gerirem a faturação dos clientes na nova experiência comercial. A nova experiência está disponível apenas para os parceiros que tenham pelo menos um cliente que tenha celebrado um Contrato de Cliente da Microsoft e tenha um Plano do Azure. [Verifique se tem acesso a um Contrato de Parceiro da Microsoft](#check-access-to-a-microsoft-partner-agreement). O [plano do Azure](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) dá aos clientes acesso a serviços do Azure com base em taxas pay as you go ao abrigo de um Contrato de Cliente da Microsoft.

## <a name="your-billing-account"></a>A sua conta de faturação

A conta de faturação do Contrato de Parceiro da Microsoft contém um perfil de faturação para cada moeda na qual efetua transações comerciais. O perfil de faturação permite-lhe gerir as faturas nas respetivas moedas. Quando estabelece relações comerciais com clientes, em função das moedas destes, as subscrições do Azure e outras compras são faturadas nos respetivos perfis de faturação.

O diagrama seguinte mostra a relação entre uma conta de faturação, os perfis de faturação, os clientes e os revendedores.

![Diagrama a mostrar a hierarquia de faturação do Contrato de Parceiro da Microsoft](./media/mpa-overview/mpa-hierarchy.svg)

Os utilizadores com funções de **Administrador Global**  e de **Agentes de Administração** na sua organização podem gerir as contas de faturação, os perfis de faturação e os clientes. Para saber mais, veja [Partner Center - Assign users roles and permissions](/partner-center/permissions-overview) (Centro de Parceiros – Atribuir funções e permissões de utilizadores).

## <a name="billing-profiles"></a>Perfis de faturação

Utilize um perfil de faturação para gerir as suas faturas numa moeda. Uma fatura mensal é gerada no início do mês para cada perfil de faturação na sua conta. A fatura contém os custos na moeda do perfil de faturação para todas as subscrições do Azure e outras compras do mês anterior.

Pode visualizar a fatura e transferir os documentos relacionados, como o ficheiro de utilização e a folha de preços, no portal do Azure. Para obter mais informações, veja [Transferir as faturas de um Contrato de Parceiro da Microsoft](download-azure-invoice.md).

> [!IMPORTANT]
>
> As faturas dos perfis de faturação contêm os custos dos clientes com Planos do Azure, bem como as compras de reservas, de SaaS e do Azure Marketplace efetuadas pelos clientes que não aceitaram o Contrato de Cliente da Microsoft e não têm planos do Azure.

## <a name="customers"></a>Clientes

Pode visualizar e gerir os clientes que aceitaram um Contrato de Cliente da Microsoft e têm um Plano do Azure no portal do Azure. Pode visualizar os custos e as transações, bem como criar e gerir subscrições do Azure para estes clientes.

### <a name="enable-policy-to-give-visibility-into-cost"></a>Ativar a política para ter uma melhor visibilidade do custo

Aplique a política para controlar se os utilizadores na organização dos clientes podem ver e analisar o custo com as tarifas Pay As You Go relativamente ao consumo do Azure. Por predefinição, a política está desativada e os utilizadores não podem ver o custo. Quando está ativada, os utilizadores que têm o acesso [RBAC do Azure](../../role-based-access-control/overview.md) adequado numa subscrição podem ver e analisar o custo da subscrição.

Para ativar a política:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Faça uma pesquisa em **Gestão de Custos + Faturação**.

   ![Captura de ecrã que mostra a pesquisa da opção Cost Management + Faturação no portal do Azure.](./media/mpa-overview/search-cmb.png)

1. Selecione **Clientes** no lado esquerdo e, em seguida, selecione um cliente na lista.

   ![Captura de ecrã a mostrar a seleção de um cliente](./media/mpa-overview/mpa-customers.png)

1. Selecione **Políticas** no lado esquerdo.

   ![Captura de ecrã a mostrar as políticas](./media/mpa-overview/mpa-change-policy.png)

1. Selecione **Sim**.

## <a name="resellers"></a>Revendedores

Os fornecedores indiretos no [modelo de duas camadas](/partner-center) do CSP podem selecionar um revendedor quando criam as subscrições para os clientes no portal do Azure. Após a criação, podem ver a lista das subscrições, filtradas por revendedor, e analisar o custo de um cliente por revendedores na análise de custos do Azure.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Verificar o acesso a um Contrato de Parceiro da Microsoft
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

Veja os seguintes artigos para saber mais sobre a conta de faturação:

- [Criar uma subscrição do Azure adicional para o Contrato de Parceiro da Microsoft](../manage/create-subscription.md)
- Integre os dados da faturação com o seu próprio sistema de relatórios com as [APIs de Faturação do Azure](/rest/api/billing/)
- [Guia de início rápido do Azure Cost Management para os parceiros](../costs/get-started-partners.md)