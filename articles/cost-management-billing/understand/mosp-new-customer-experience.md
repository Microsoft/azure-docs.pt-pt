---
title: Introdução à conta de faturação do Azure atualizada
description: Comece a utilizar a sua conta de faturação do Azure atualizada para compreender as alterações à nova experiência de faturação e gestão de custos
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 01/11/2021
ms.author: banders
ms.openlocfilehash: f0645115246995c9605563626d99bbf6a76784e1
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133575"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>Introdução à conta de faturação do Azure atualizada

A gestão de custos e faturas é um dos principais componentes da sua experiência na cloud. Ajuda a controlar e compreender as suas despesas na cloud. Para facilitar a gestão de custos e faturas, a Microsoft está a atualizar a sua conta de faturação do Azure para incluir capacidades de gestão de custos e faturação melhoradas. Este artigo descreve as atualizações à sua conta de faturação e apresenta as novas capacidades.

> [!IMPORTANT]
> A sua conta será atualizada quando receber um e-mail da Microsoft a notificá-lo sobre as atualizações à conta. A notificação é enviada 60 dias antes da atualização da conta.

## <a name="more-flexibility-with-your-new-billing-account"></a>Mais flexibilidade com a nova conta de faturação

O diagrama seguinte compara a sua conta de faturação antiga e a nova:

![Diagrama que mostra a comparação entre a hierarquia de faturação na conta antiga e na nova](./media/mosp-new-customer-experience/comparison-old-new-account.png)

A sua nova conta de faturação contém um ou mais perfis de faturação, que lhe permitem gerir as suas faturas e métodos de pagamento. Cada perfil de faturação contém uma ou mais secções de faturação que lhe permitem organizar os custos na fatura do perfil de faturação.

![Diagrama que mostra a nova hierarquia de faturação](./media/mosp-new-customer-experience/new-billing-account-hierarchy.png)

As funções na conta de faturação têm o nível mais elevado de permissões. Estas funções devem ser atribuídas a utilizadores que precisam de ver as faturas e controlar os custos de toda a conta, como gestores financeiros ou de TI numa organização ou a pessoa que se inscreveu numa conta. Para obter mais informações, veja [Funções e tarefas da conta de faturação](../manage/understand-mca-roles.md#billing-account-roles-and-tasks). Quando a conta é atualizada, o utilizador que tem uma função de administrador de conta na conta de faturação antiga recebe uma função de proprietário na nova conta.

## <a name="billing-profiles"></a>Perfis de faturação

É utilizado um perfil de faturação para gerir a sua fatura e métodos de pagamento. Uma fatura mensal é gerada no início do mês para cada perfil de faturação na sua conta. A fatura contém os custos respetivos do mês anterior para todas as subscrições associadas ao perfil de faturação.

Quando a conta for atualizada, é criado automaticamente um perfil de faturação para cada subscrição. Os custos da subscrição são faturados no perfil de faturação e apresentados na respetiva fatura.

As funções nos perfis de faturação têm permissões para ver e gerir faturas e métodos de pagamento. Estas funções devem ser atribuídas a utilizadores que pagam faturas, como membros da equipa de contabilidade numa organização. Para obter mais informações, veja [Funções e tarefas do perfil de faturação](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Quando a conta for atualizada, para cada subscrição na qual concedeu permissão para [ver faturas](download-azure-invoice.md#allow-others-to-download-the-your-subscription-invoice), os utilizadores que tiverem uma função do Azure de proprietário, contribuidor, leitor ou leitor de faturação receberão a função de leitor no respetivo perfil de faturação.

## <a name="invoice-sections"></a>Secções de faturação

É utilizada uma secção de faturação para organizar os custos na fatura. Por exemplo, pode precisar de uma única fatura, mas quer organizar os custos por departamento, equipa ou projeto. Para este cenário, existe um único perfil de faturação onde se cria uma secção de faturação para cada departamento, equipa ou projeto.

Quando a conta for atualizada, é criada uma secção de faturação para cada perfil de faturação e a subscrição relacionada é atribuída à secção de faturação. Ao adicionar mais subscrições, pode criar mais secções e atribuir as subscrições às secções de faturação. Verá que as secções na fatura do perfil de faturação refletem a utilização de cada subscrição que atribuiu às mesmas.

As funções na secção de faturação têm permissões para controlar quem cria as subscrições do Azure. As funções devem ser atribuídas a utilizadores que configuram o ambiente do Azure para as equipas numa organização, como líderes de engenharia e arquitetos técnicos. Para obter mais informações, veja [Funções e tarefas da secção de faturação](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="enhanced-features-in-your-new-experience"></a>Funcionalidades melhoradas na nova experiência

A nova experiência inclui as seguintes capacidades de gestão de custos e faturação que facilitam a gestão dos seus custos e faturas:

#### <a name="invoice-management"></a>Gestão de faturas

**Período de faturação mensal mais previsível** - Na sua nova conta, o período de faturação começa no primeiro dia do mês e termina no último dia do mês, independentemente de quando se inscreve para utilizar o Azure. Será gerada uma fatura no início de cada mês com todos os custos do mês anterior.

**Obtenha uma única fatura mensal para várias subscrições** - Tem a flexibilidade de obter uma fatura mensal para cada uma das suas subscrições ou uma única fatura para várias subscrições.

**Receba uma única fatura mensal para subscrições do Azure, planos de suporte e produtos do Azure Marketplace** - Obterá uma fatura mensal para todos os custos, incluindo custos de utilização para subscrições do Azure, planos de suporte e compras do Azure Marketplace.

**Agrupe os custos na fatura com base nas suas necessidades** - Pode agrupar os custos na fatura com base em suas necessidades, por departamentos, projetos ou equipas.

**Defina um número de ordem de compra opcional na fatura** – Para associar a fatura aos seus sistemas financeiros internos, defina um número de ordem de compra. Faça a gestão e atualize-o em qualquer altura no portal do Azure.

#### <a name="payment-management"></a>Gestão de pagamentos

**Pague as faturas imediatamente com um cartão de crédito** - Não é necessário aguardar pelo pagamento automático a cobrar no cartão de crédito. Pode utilizar qualquer cartão de crédito para pagar uma fatura em dívida ou vencida no portal do Azure.

**Mantenha o controlo de todos os pagamentos aplicados à conta** - Veja todos os pagamentos aplicados à sua conta, incluindo o método de pagamento utilizado, o valor pago e a data de pagamento no portal do Azure.

#### <a name="cost-management"></a>Gestão de custos

**Agende as exportações mensais dos dados de utilização para uma conta de armazenamento** - Publique automaticamente os seus dados de custo e de utilização para uma conta de armazenamento numa base diária, semanal ou mensal.

#### <a name="account-and-subscription-management"></a>Gestão de contas e subscrições

**Atribua vários administradores para realizar operações de faturação** - Atribua permissões de faturação a vários utilizadores para gerir a faturação da sua conta. Obtenha flexibilidade ao dar permissões de leitura, escrita ou ambas a outras pessoas.

**Crie mais subscrições diretamente no portal do Azure** - Crie todas as suas subscrições com uma única seleção no portal do Azure.

#### <a name="api-support"></a>Suporte de API

**Realize operações de faturação e de gestão de custos através de APIs, SDK e do PowerShell** - Utilize APIs de gestão de custos, faturação e consumo para extrair dados de faturação e de custos para as suas ferramentas de análise de dados preferidas.

**Realize todas as operações de subscrição através de APIs, SDK e do PowerShell** - Utilize APIs de subscrição do Azure para automatizar a gestão das suas subscrições do Azure, incluindo criar, mudar o nome e cancelar uma subscrição.

## <a name="get-prepared-for-your-new-experience"></a>Prepare-se para a nova experiência

Recomendamos o seguinte para se preparar para a sua nova experiência:

**Período de faturação mensal e data da fatura diferente**

Na nova experiência, a sua fatura será gerada por volta do nono dia de cada mês e conterá todos os custos do mês anterior. Esta data pode ser diferente da data em que a fatura é gerada na conta antiga. Se partilhar as suas faturas com outras pessoas, notifique as mesmas sobre a alteração na data.

**Novas APIs de gestão de custos e faturação**

Se estiver a utilizar APIs do Cost Management ou de Faturação para consultar e atualizar os dados de faturação ou de custos, tem de utilizar novas APIs. A tabela seguinte lista as APIs que não funcionarão na nova conta de faturação e as alterações que tem de fazer na sua nova conta de faturação.

|API | Alterações  |
|---------|---------|
|[Contas de Faturação - Listar](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | Na API Contas de Faturação - Listar, a conta de faturação antiga tem agreementType **MicrosoftOnlineServiceProgram** e a nova conta de faturação terá agreementType **MicrosoftCustomerAgreement**. Se assumir uma dependência em agreementType, atualize-a. |
|[Faturas - Listar Por Subscrição de Faturação](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Esta API devolverá apenas as faturas que foram geradas antes da atualização da conta. Terá de utilizar a API [Faturas - Listar Por Conta de Faturação](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount) para obter as faturas geradas na nova conta de faturação. |

## <a name="cost-management-updates-after-account-update"></a>Atualizações do Cost Management após uma atualização da conta

A sua conta de faturação do Azure atualizada do seu Contrato de Cliente Microsoft dá-lhe acesso a novas experiências avançadas do Cost Management no portal do Azure que não tinha com a sua conta do pay as you go.

### <a name="new-capabilities"></a>Novas funcionalidades

As novas capacidades seguintes estão disponíveis com a sua conta de faturação do Azure.

#### <a name="new-billing-scopes"></a>Novos âmbitos de faturação

Como parte da sua conta atualizada, tem novos âmbito no Cost Management + Faturação. Para além de ajudarem com a organização hierárquica e a faturação, estes âmbitos também permitem ver as cobranças combinadas de várias subscrições subjacentes. Para obter mais informações sobre os âmbitos de faturação, veja [Âmbitos do Contrato de Cliente Microsoft](../costs/understand-work-scopes.md#microsoft-customer-agreement-scopes).

Também pode aceder às APIs do Cost Management para obter vistas de custos combinados em âmbitos mais altos. Todas as APIs do Cost Management que utilizam o âmbito de subscrição ainda estão disponíveis com algumas pequenas alterações no esquema. Para obter mais informações sobre as APIs, veja [APIs do Azure Cost Management](/rest/api/cost-management/) e [APIs de Consumo do Azure](/rest/api/consumption/).

#### <a name="cost-allocation"></a>Alocação de custos

Com a sua conta atualizada, pode utilizar as capacidades de alocação de custos para distribuir custos de serviços partilhados na sua organização. Para obter mais informações sobre a alocação de custos, veja [Criar e gerir regras de alocação de custos do Azure](../costs/allocate-costs.md).

#### <a name="power-bi"></a>Power BI

O conector do Azure Cost Management para o Power BI Desktop ajuda-o a criar visualizações e relatórios personalizados da utilização e gastos que faz no Azure. Pode aceder aos seus dados de custos e utilização depois de se ligar à sua conta atualizada. Para obter mais informações sobre o conector do Azure Cost Management para o Power BI Desktop, veja [Criar elementos visuais e relatórios com o conector do Azure Cost Management no Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management).

### <a name="updated-capabilities"></a>Capacidades atualizadas

As capacidades atualizadas que se seguem estão disponíveis com a sua conta de faturação do Azure.

#### <a name="cost-analysis"></a>Análise de custos

Pode continuar a ver e rastrear os seus custos de consumo mês a mês, além de que, agora, pode ver os custos de compra de reservas e de compras no Marketplace na Análise de custos.

Com a sua conta atualizada, recebe uma única fatura para todas as cobranças do Azure. Agora, também tem uma vista única de calendário mensal simplificada que vem substituir a vista dos períodos de faturação que tinha anteriormente.

Por exemplo, se o período de faturação da sua conta antiga ia de 24 de novembro a 23 de dezembro, nesse caso, após a atualização, o período passa a ser de 1 de novembro a 30 de novembro, de 1 de dezembro a 31 de dezembro e assim sucessivamente.

:::image type="content" source="./media/mosp-new-customer-experience/billing-periods.png" alt-text="Imagem que mostra uma comparação entre os novos períodos de faturação e os antigos" lightbox="./media/mosp-new-customer-experience/billing-periods.png" :::

#### <a name="budgets"></a>Orçamentos

Agora, pode criar orçamentos para a conta de faturação, o que lhe permite controlar os custos nas várias subscrições. Também utilizar orçamentos para se manter a par das suas cobranças de compras. Para obter mais informações sobre orçamentos, veja [Criar e gerir orçamentos do Azure](../costs/tutorial-acm-create-budgets.md).

#### <a name="exports"></a>Exportações

A sua nova conta de faturação fornece funcionalidade de exportação melhorada. Por exemplo, pode criar exportações de custos reais que incluam compras ou custos amortizados (os custos de compra de reservas são distribuídos pelo termo da compra). Também pode criar uma exportação da conta de faturação para obter dados de utilização e de cobranças em todas as subscrições da conta de faturação. Para obter mais informações sobre exportações, veja [Criar e gerir dados exportados](../costs/tutorial-export-acm-data.md).

> [!NOTE]
> As exportações criadas antes da atualização da sua conta com o tipo **Exportação mensal dos custos do último mês** irá exportar os dados do último mês do calendário, não do último período de faturação.

Por exemplo, para um período de faturação que fosse de 23 de dezembro a 22 de Janeiro, o ficheiro CSV exportado teria os dados de utilização e de custos referentes a esse período. Após a atualização, a exportação passa a conter os dados do mês de calendário. Por exemplo, de 1 de janeiro a 31 de janeiro e assim por diante.

:::image type="content" source="./media/mosp-new-customer-experience/export-amortized-costs.png" alt-text="Imagem que mostra uma comparação entre os novos detalhes de exportação e os antigos" lightbox="./media/mosp-new-customer-experience/export-amortized-costs.png" :::

## <a name="additional-information"></a>Informações adicionais

As secções seguintes fornecem informações adicionais sobre a nova experiência.

**Nenhum tempo de inatividade do serviço** Os serviços do Azure na sua subscrição continuam em execução sem quaisquer interrupções. A única atualização é à experiência de faturação. Não tem impacto nos recursos, grupos de recursos ou grupos de gestão existentes.

**Nenhuma alteração aos recursos do Azure** O acesso aos recursos do Azure definidos através do controlo de acesso baseado em funções do Azure (RBAC do Azure) não é afetado pela atualização.

**As faturas anteriores estão disponíveis na nova experiência** As faturas geradas antes da atualização da conta continuam disponíveis no portal do Azure.

**Faturas da conta atualizadas a meio do mês** Se a conta for atualizada a meio do mês, obterá uma fatura para os custos acumulados até ao dia em que a conta for atualizada. Receberá outra fatura referente ao resto do mês. Por exemplo, a sua conta tem uma subscrição que é atualizada a 15 de setembro. Receberá uma fatura das cobranças acumuladas até 15 de setembro. Receberá outra fatura pelo período entre 15 e 30 de setembro. Após setembro, obterá uma fatura por mês.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

Veja os seguintes artigos para saber mais sobre a sua conta de faturação.

- [Compreender as funções administrativas da nova conta de faturação](../manage/understand-mca-roles.md)
- [Criar uma subscrição do Azure adicional para a nova conta de faturação](../manage/create-subscription.md)
- [Criar secções na sua fatura para organizar os custos](../manage/mca-section-invoice.md)