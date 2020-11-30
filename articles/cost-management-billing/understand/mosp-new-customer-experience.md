---
title: Introdução à conta de faturação do Azure atualizada
description: Comece a utilizar a sua conta de faturação do Azure atualizada para compreender as alterações à nova experiência de faturação e gestão de custos
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: banders
ms.openlocfilehash: 7bec455b804d1f4b13ab7e13677092077214a121
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965855"
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

Quando a conta for atualizada, é criada uma secção de faturação para cada perfil de faturação e a subscrição relacionada é atribuída à secção de faturação. Ao adicionar mais subscrições, pode criar secções adicionais e atribuir as subscrições às secções de faturação. Verá que as secções na fatura do perfil de faturação refletem a utilização de cada subscrição que atribuiu às mesmas.

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

**Atribua vários administradores para realizar operações de faturação** - Atribua permissões de faturação a vários utilizadores para gerir a faturação da sua conta. Obtenha flexibilidade ao fornecer permissões de leitura, escrita ou ambas a outras pessoas.

**Crie subscrições adicionais diretamente no portal do Azure** - Crie todas as suas subscrições com um único clique no portal do Azure.

#### <a name="api-support"></a>Suporte de API

**Realize operações de gestão de custos e faturação através de APIs, SDKs e do PowerShell** - Utilize APIs de gestão de custos, faturação e consumo para extrair dados de faturação e custos para as suas ferramentas de análise de dados preferidas.

**Execute todas as operações de subscrição através de APIs, SDKs e do PowerShell** - Utilize APIs de subscrição do Azure para automatizar a gestão das suas subscrições do Azure, incluindo criar, mudar o nome e cancelar uma subscrição.

## <a name="get-prepared-for-your-new-experience"></a>Prepare-se para a nova experiência

Recomendamos o seguinte para se preparar para a sua nova experiência:

**Período de faturação mensal e data da fatura diferente**

Na nova experiência, a sua fatura será gerada por volta do nono dia de cada mês e conterá todos os custos do mês anterior. Esta data pode ser diferente da data em que a fatura é gerada na conta antiga. Se partilhar as suas faturas com outras pessoas, notifique as mesmas sobre a alteração na data.

**Novas APIs de gestão de custos e faturação**

Se estiver a utilizar APIs de Gestão e Custos e Faturação para ver e atualizar os dados de faturação ou de custos, tem de utilizar novas APIs. A tabela seguinte lista as APIs que não funcionarão na nova conta de faturação e as alterações que tem de fazer na sua nova conta de faturação.

|API | Alterações  |
|---------|---------|
|[Contas de Faturação - Listar](/rest/api/billing/2019-10-01-preview/billingaccounts/list) | Na API Contas de Faturação - Listar, a conta de faturação antiga tem agreementType **MicrosoftOnlineServiceProgram** e a nova conta de faturação terá agreementType **MicrosoftCustomerAgreement**. Se assumir uma dependência de agreementType, atualize-a. |
|[Faturas - Listar Por Subscrição de Faturação](/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | Esta API devolverá apenas as faturas que foram geradas antes da atualização da conta. Terá de utilizar a API [Faturas - Listar Por Conta de Faturação](/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount) para obter as faturas geradas na nova conta de faturação. |

## <a name="additional-information"></a>Informações adicionais

As secções seguintes fornecem informações adicionais sobre a nova experiência.

**Nenhum tempo de inatividade do serviço** Os serviços do Azure na sua subscrição continuam em execução sem quaisquer interrupções. A única atualização é à experiência de faturação. Não tem impacto nos recursos, grupos de recursos ou grupos de gestão existentes.

**Nenhuma alteração aos recursos do Azure** O acesso aos recursos do Azure definidos através do controlo de acesso baseado em funções do Azure (RBAC do Azure) não é afetado pela atualização.

**As faturas anteriores estão disponíveis na nova experiência** As faturas geradas antes da atualização da conta continuam disponíveis no portal do Azure.

**Faturas da conta atualizadas a meio do mês** Se a conta for atualizada a meio do mês, obterá uma fatura para os custos acumulados até ao dia em que a conta for atualizada. Receberá outra fatura referente ao restante do mês. Por exemplo, a sua conta tem uma subscrição e é atualizada a 15 de setembro. Receberá uma fatura dos custos acumulados até 15 de setembro. Receberá outra fatura pelo período entre 15 e 30 de setembro. Após setembro, obterá uma fatura por mês.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.

## <a name="next-steps"></a>Passos seguintes

Veja os seguintes artigos para saber mais sobre a sua conta de faturação.

- [Compreender as funções administrativas da nova conta de faturação](../manage/understand-mca-roles.md)
- [Criar uma subscrição do Azure adicional para a nova conta de faturação](../manage/create-subscription.md)
- [Criar secções na sua fatura para organizar os custos](../manage/mca-section-invoice.md)