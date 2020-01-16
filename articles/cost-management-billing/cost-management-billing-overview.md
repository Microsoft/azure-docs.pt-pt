---
title: Visão geral do gerenciamento de custos e cobrança do Azure | Microsoft Docs
description: Você usa os recursos de cobrança e gerenciamento de custos do Azure para realizar tarefas administrativas de cobrança e gerenciar o acesso de cobrança aos custos. Você também tem o recurso de monitorar e controlar os gastos do Azure e otimizar o uso de recursos do Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/20/2019
ms.topic: overview
ms.service: cost-management-billing
manager: dougeby
ms.custom: ''
ms.openlocfilehash: cadff1d83a8b47a540efe9b74ffaf6de171138b3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987517"
---
# <a name="what-is-azure-cost-management-and-billing"></a>O que é o gerenciamento de custos e a cobrança do Azure?

Com os produtos e serviços do Azure, você paga apenas pelo que usar. Ao criar e usar os recursos do Azure, você será cobrado pelos recursos. Você usa os recursos de cobrança e gerenciamento de custos do Azure para realizar tarefas administrativas de cobrança e gerenciar o acesso de cobrança aos custos. Você também tem seus recursos para monitorar e controlar os gastos do Azure e para otimizar o uso de recursos do Azure.

## <a name="understand-azure-billing"></a>Entender a cobrança do Azure

Os recursos de cobrança do Azure são usados para examinar os custos faturados e gerenciar o acesso às informações de cobrança. Em organizações maiores, as equipes de compras e de finanças geralmente realizam tarefas de cobrança.

Ao inscrever-se para utilizar o Azure, é criada uma conta de faturação. Utiliza a conta de faturação para gerir faturas, pagamentos e controlar custos. Pode ter acesso a múltiplas contas de faturação. Por exemplo, talvez se tenha inscrito no Azure com o objetivo de desenvolver projetos pessoais. Portanto, você pode ter uma assinatura individual do Azure com uma conta de cobrança. Também poderá ter acesso através do Contrato Enterprise ou do Contrato de Cliente da Microsoft da sua organização. Para cada cenário, você teria uma conta de cobrança separada.

### <a name="billing-accounts"></a>Contas de cobrança

O portal do Azure atualmente dá suporte aos seguintes tipos de contas de cobrança:

- **Programa do Microsoft Online Services**: uma conta de cobrança individual para um programa do Microsoft Online Services é criada quando você se inscreve no Azure por meio do site do Azure. Por exemplo, quando se inscreve numa [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [conta com tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise Agreement**: uma conta de cobrança para um Enterprise Agreement é criada quando sua organização assina um [ea (Enterprise Agreement)](https://azure.microsoft.com/pricing/enterprise-agreement/) para usar o Azure.

- **Contrato do cliente da Microsoft**: uma conta de cobrança para um contrato de cliente da Microsoft é criada quando sua organização trabalha com um representante da Microsoft para assinar um contrato de cliente da Microsoft. Alguns clientes em determinadas regiões, que se inscrevem no site do Azure numa [conta com tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou atualizam a respetiva [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), também podem ter uma conta de faturação para um Contrato de Cliente da Microsoft. Para obter mais informações, veja [Introdução à conta de faturação para o Contrato de Cliente da Microsoft](./understand/mca-overview.md).

### <a name="scopes-for-billing-accounts"></a>Âmbitos de contas de faturação
Um escopo é um nó em uma conta de cobrança que você usa para exibir e gerenciar a cobrança. É onde você gerencia os dados de cobrança, pagamentos, faturas e realiza o gerenciamento geral da conta.

#### <a name="microsoft-online-services-program"></a>Programa Microsoft Online Services

|Âmbito  |Definição  |
|---------|---------|
|Conta de faturação     | Representa um único proprietário (Administrador da conta) de uma ou mais subscrições do Azure. Um Administrador de Conta está autorizado a executar várias tarefas de faturação, como criar subscrições, ver faturas ou alterar a faturação para subscrições.  |
|Subscrição     |  Representa um agrupamento de recursos do Azure. Uma fatura é gerada no escopo da assinatura. Tem os seus próprios métodos de pagamento que são utilizados para pagar as faturas.|


#### <a name="enterprise-agreement"></a>Enterprise Agreement

|Âmbito  |Definição  |
|---------|---------|
|Conta de faturação    | Representa uma inscrição do Contrato Enterprise. A nota fiscal é gerada no escopo da conta de cobrança. Ele é estruturado usando departamentos e contas de registro.  |
|Departamento     |  Agrupamento opcional de contas de inscrição.      |
|Conta de inscrição     |  Representa um único proprietário da conta. As assinaturas do Azure são criadas no escopo da conta de registro.  |


#### <a name="microsoft-customer-agreement"></a>Contrato de Cliente Microsoft

|Âmbito  |Tarefas  |
|---------|---------|
|Conta de faturação     |   Representa um contrato de cliente para múltiplos produtos e serviços Microsoft. A conta de cobrança é estruturada usando perfis de cobrança e seções de fatura.   |
|Perfil de faturação     |  Representa uma fatura e os respetivos métodos de pagamento. A fatura é gerada neste âmbito. O perfil de cobrança pode ter várias seções de fatura.      |
|Secção de fatura     |   Representa um grupo de custos numa fatura. As assinaturas e outras compras estão associadas ao escopo da seção fatura.    |


## <a name="understand-azure-cost-management"></a>Entender o gerenciamento de custos do Azure
A gestão de custos é o processo que diz respeito ao planeamento e ao controlo eficazes dos custos envolvidos na sua empresa. Normalmente, as tarefas de gestão de custos são realizadas por equipas de finanças, gestão e aplicações. O gerenciamento de custos do Azure + cobrança ajuda as organizações a planejar com o custo em mente. Ele também ajuda a analisar os custos com eficiência e tomar medidas para otimizar os gastos com a nuvem. Para saber mais sobre como abordar a gestão de custos como uma organização, veja o artigo [Melhores práticas do Azure Cost Management](./costs/cost-mgt-best-practices.md).

Assista ao [vídeo visão geral do gerenciamento de custos do Azure](https://www.youtube.com/watch?v=el4yN5cHsJ0) para obter uma visão geral rápida sobre como o gerenciamento de custos do Azure pode ajudá-lo a economizar dinheiro no Azure.

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Embora relacionada, a faturação é diferente da gestão de custos. A faturação é o processo de faturar clientes relativamente a bens ou serviços e gerir a relação comercial.

O Cost Management apresenta padrões de utilização e custos da organização com uma análise avançada. Os relatórios no gerenciamento de custos mostram os custos com base no uso consumidos pelos serviços do Azure e ofertas do Marketplace de terceiros. Os custos são baseados em preços negociados e no fator de reserva e Benefício Híbrido do Azure descontos. Em conjunto, os relatórios apresentam os seus custos internos e externos relativamente à utilização e os custos do Azure Marketplace. Outros custos, como compras de reservas, suporte e impostos, ainda não são apresentados nos relatórios. Os relatórios ajudam-no a compreender os seus gastos e a utilização de recursos, e podem ajudar a detetar anomalias nos gastos. Também está disponível a análise preditiva. O Cost Management utiliza grupos de gestão, orçamentos e recomendações do Azure para mostrar claramente como as suas despesas estão organizadas e como poderá reduzir os custos.

Pode utilizar o portal do Azure ou várias APIs para a automatização de exportações, para integrar dados de custos em sistemas e processos externos. Também estão disponíveis a exportação automatizada de dados de faturação e o agendamento de relatórios.

### <a name="plan-and-control-expenses"></a>Planear e controlar despesas

As maneiras pelas quais o gerenciamento de custos ajuda você a planejar e controlar seus custos incluem: análise de custos, orçamentos, recomendações e exportação de dados de gerenciamento de custos.

Pode utilizar a análise de custos para explorar e analisar os custos da organização. Pode ver os custos agregados por organização para compreender onde os custos são acumulados e identificar as tendências das despesas. Além disso, pode ver os custos acumulados ao longo do tempo para fazer uma estimativa mensal, trimestral ou até anual das tendências das despesas em relação a um orçamento.

Os orçamentos ajudam-no a planear e cumprir a responsabilidade financeira da sua organização. Ajudam a impedir que os limiares ou limites de custos sejam ultrapassados. Os orçamentos também podem ajudá-lo a informar outras pessoas sobre os seus gastos para gerir proativamente os custos. Além disso, com os orçamentos, pode ver o progresso dos gastos ao longo do tempo.

As recomendações mostram como pode otimizar e melhorar a eficiência ao identificar os recursos inativos e subutilizados. Em alternativa, podem mostrar opções de recursos menos dispendiosas. Ao tomar medidas com base nas recomendações, pode alterar a forma como utiliza os recursos para poupar dinheiro. Para tomar medidas, veja primeiro as recomendações de otimização de custos para identificar as potenciais ineficiências de utilização. Em seguida, tome medidas com base numa recomendação para mudar a sua utilização dos recursos do Azure para uma opção mais económica. Em seguida, verifique a ação para se certificar de que a alteração feita é bem-sucedida.

Se utilizar sistemas externos para analisar ou aceder a dados de gestão de custos, pode exportar facilmente os dados do Azure. Além disso, pode definir o agendamento diário de uma exportação no formato CSV e armazenar os ficheiros de dados no armazenamento do Azure. Em seguida, pode aceder aos dados a partir do seu sistema externo.

### <a name="consider-cloudyn"></a>Considere o Cloudyn

O [Cloudyn](./cloudyn/overview.md) é um serviço do Azure relacionado com o Cost Management. No Cloudyn, pode controlar a utilização e os gastos com a cloud para os seus recursos do Azure. O Cloudyn também suporta outros fornecedores de serviços cloud, incluindo a AWS e a Google. Os relatórios do dashboard fáceis de compreender também ajudam na alocação de custos e showbacks/estornos. Atualmente, o Cost Management não suporta a análise de custos/estorno ou outros fornecedores de serviços cloud. No entanto, o Cloudyn é uma opção que _de facto_ os suporta. Atualmente, o gerenciamento de custos não dá suporte a Microsoft Cloud contas do CSP (provedor de serviços), mas o Cloudyn. Se você tiver contas de CSP ou se quiser usar o regressivo/chargeback, poderá usar o Cloudyn para ajudar a gerenciar seus custos.

Assista ao [vídeo de gerenciamento de custos e Cloudyn do Azure](https://www.youtube.com/watch?v=PmwFWwSluh8) para ver as recomendações quando você deve usar o gerenciamento de custos do Azure ou o Cloudyn, com base em suas necessidades de negócios.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

### <a name="additional-azure-tools"></a>Ferramentas do Azure adicionais

O Azure tem outras ferramentas que não fazem parte do conjunto de recursos de cobrança e gerenciamento de custos do Azure. No entanto, eles desempenham um papel importante no processo de gerenciamento de custos. Para saber mais sobre estas ferramentas, veja as ligações seguintes.

- [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/): utilize esta ferramenta para estimar os seus custos iniciais com a cloud.
- [Azure Migrate](../migrate/migrate-overview.md): avalie a carga de trabalho do seu datacenter atual para obter informações sobre o que precisa numa solução de substituição do Azure.
- [Assistente do Azure](../advisor/advisor-overview.md): identifique VMs não utilizadas e obtenha recomendações sobre compras de instâncias reservadas do Azure.
- [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/): utilize as suas licenças do Windows Server ou SQL Server atuais no local para que as VMs no Azure as guardem.


## <a name="next-steps"></a>Passos seguintes

Agora que você está familiarizado com o gerenciamento de custos e a cobrança, a próxima etapa é começar a usar o serviço.

- Comece a utilizar o Azure Cost Management para [analisar os custos](./costs/quick-acm-cost-analysis.md).
- Também pode saber mais sobre as [práticas recomendadas do Azure Cost Management](./costs/cost-mgt-best-practices.md).
