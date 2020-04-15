---
title: Descrição geral das funcionalidades Azure Cost Management e Faturação| Microsoft Docs
description: Utilize as funcionalidades Azure Cost Management e Faturação para realizar tarefas administrativas de faturação e para gerir o acesso de faturação aos custos. Também pode utilizar a funcionalidade para monitorizar e controlar as despesas do Azure e para otimizar a utilização de recursos do Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: overview
ms.service: cost-management-billing
ms.custom: ''
ms.openlocfilehash: 2f96208ff3f9664d82bfc1d9ddf9bc5b9aec37c3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879093"
---
# <a name="what-is-azure-cost-management-and-billing"></a>O que é o Azure Cost Management e a Faturação?

Com os produtos e os serviços do Azure, paga apenas o que utiliza. Os recursos do Azure são-lhe cobrados à medida que os cria e os utiliza. Utilize as funcionalidades Azure Cost Management e Faturação para realizar tarefas administrativas de faturação e para gerir o acesso de faturação aos custos. Também pode utilizar as funcionalidades para monitorizar e controlar as despesas do Azure e otimizar a utilização dos recursos do Azure.

## <a name="understand-azure-billing"></a>Compreender a Faturação do Azure

As funcionalidades Faturação do Azure servem para analisar os custos faturados e gerir o acesso às informações de faturação. Em organizações maiores, normalmente, as equipas de aprovisionamento e finanças realizam tarefas de faturação.

Ao inscrever-se para utilizar o Azure, é criada uma conta de faturação. Utiliza a conta de faturação para gerir faturas, pagamentos e controlar custos. Pode ter acesso a múltiplas contas de faturação. Por exemplo, talvez se tenha inscrito no Azure com o objetivo de desenvolver projetos pessoais. Assim, é possível que tenha uma subscrição do Azure individual com uma conta de faturação. Também poderá ter acesso através do Contrato Enterprise ou do Contrato de Cliente da Microsoft da sua organização. Para cada cenário, teria uma conta de faturação separada.

### <a name="billing-accounts"></a>Contas de faturação

O portal do Azure suporta atualmente os seguintes tipos de contas de faturação:

- **Programa Microsoft Online Services**: uma conta de faturação individual para um Programa Microsoft Online Services é criada ao inscrever-se no Azure através do site do Azure. Por exemplo, quando se inscreve numa [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [conta com tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou como [subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Contrato Enterprise**: uma conta de faturação para um Contrato Enterprise é criada quando a sua organização assina um [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) para utilizar o Azure.

- **Contrato de Cliente da Microsoft**: uma conta de faturação para um Contrato de Cliente Microsoft é criada quando a sua organização trabalha com um representante da Microsoft para assinar um Contrato de Cliente da Microsoft. Alguns clientes em determinadas regiões, que se inscrevem no site do Azure numa [conta com tarifas pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) ou atualizam a respetiva [Conta Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), também podem ter uma conta de faturação para um Contrato de Cliente da Microsoft. Para obter mais informações, veja [Introdução à conta de faturação para o Contrato de Cliente da Microsoft](./understand/mca-overview.md).

### <a name="scopes-for-billing-accounts"></a>Âmbitos de contas de faturação
Um âmbito é um nó numa conta de faturação que utiliza para ver e gerir a faturação. É onde gere os dados de faturação, os pagamentos, as faturas e onde realiza a gestão global da conta.

#### <a name="microsoft-online-services-program"></a>Programa Microsoft Online Services

|Âmbito  |Definição  |
|---------|---------|
|Conta de faturação     | Representa um único proprietário (Administrador da conta) de uma ou mais subscrições do Azure. Um Administrador de Conta está autorizado a executar várias tarefas de faturação, como criar subscrições, ver faturas ou alterar a faturação para subscrições.  |
|Subscrição     |  Representa um agrupamento de recursos do Azure. Uma fatura é gerada no âmbito da subscrição. Tem os seus próprios métodos de pagamento que são utilizados para pagar as faturas.|


#### <a name="enterprise-agreement"></a>Contrato Enterprise

|Âmbito  |Definição  |
|---------|---------|
|Conta de faturação    | Representa uma inscrição do Contrato Enterprise. A fatura é gerada no âmbito da conta de faturação. É estruturada através de departamentos e contas de inscrição.  |
|Departamento     |  Agrupamento opcional de contas de inscrição.      |
|Conta de inscrição     |  Representa um único proprietário da conta. As subscrições do Azure são criadas no âmbito da conta de inscrição.  |


#### <a name="microsoft-customer-agreement"></a>Contrato de Cliente da Microsoft

|Âmbito  |Tarefas  |
|---------|---------|
|Conta de faturação     |   Representa um contrato de cliente para múltiplos produtos e serviços Microsoft. A conta de faturação é estruturada através de perfis de faturação e secções de fatura.   |
|Perfil de faturação     |  Representa uma fatura e os respetivos métodos de pagamento. A fatura é gerada neste âmbito. O perfil de faturação pode ter várias secções de fatura.      |
|Secção de fatura     |   Representa um grupo de custos numa fatura. As subscrições e outras compras estão associadas ao âmbito da secção de faturação.    |


## <a name="understand-azure-cost-management"></a>Compreender o Azure Cost Management
A gestão de custos é o processo que diz respeito ao planeamento e ao controlo eficazes dos custos envolvidos na sua empresa. Normalmente, as tarefas de gestão de custos são realizadas por equipas de finanças, gestão e aplicações. A funcionalidade Azure Cost Management + Faturação ajuda as organizações a planear com os custos em mente. Também ajuda a analisar eficazmente os custos e a tomar medidas para otimizar os gastos com a cloud. Para saber mais sobre como abordar a gestão de custos como uma organização, veja o artigo [Melhores práticas do Azure Cost Management](./costs/cost-mgt-best-practices.md).

Veja o vídeo [Azure Cost Management overview](https://www.youtube.com/watch?v=el4yN5cHsJ0) (Descrição geral do Azure Cost Management) para obter uma descrição geral rápida sobre como é que o Azure Cost Management pode ajudar a poupar no Azure. Para ver outros vídeos, visite o canal do YouTube [Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Embora relacionada, a faturação é diferente da gestão de custos. A faturação é o processo de faturar clientes relativamente a bens ou serviços e gerir a relação comercial.

O Cost Management apresenta padrões de utilização e custos da organização com uma análise avançada. Os relatórios no Cost Management mostram os custos com base na utilização que foram consumidos pelos serviços do Azure e pelas ofertas do Marketplace de terceiros. Os custos baseiam-se nos preços negociados e têm em conta a reserva e os descontos do Benefício Híbrido do Azure. Em conjunto, os relatórios apresentam os seus custos internos e externos relativamente à utilização e os custos do Azure Marketplace. Outros custos, como compras de reservas, suporte e impostos, ainda não são apresentados nos relatórios. Os relatórios ajudam-no a compreender os seus gastos e a utilização de recursos, e podem ajudar a detetar anomalias nos gastos. Também está disponível a análise preditiva. O Cost Management utiliza grupos de gestão, orçamentos e recomendações do Azure para mostrar claramente como as suas despesas estão organizadas e como poderá reduzir os custos.

Pode utilizar o portal do Azure ou várias APIs para a automatização de exportações, para integrar dados de custos em sistemas e processos externos. Também estão disponíveis a exportação automatizada de dados de faturação e o agendamento de relatórios.

### <a name="plan-and-control-expenses"></a>Planear e controlar despesas

As formas através das quais o Cost Management ajuda a planear e controlar os custos incluem: análise de custos, orçamentos, recomendações e exportação de dados de gestão de custos.

Pode utilizar a análise de custos para explorar e analisar os custos da organização. Pode ver os custos agregados por organização para compreender onde os custos são acumulados e identificar as tendências das despesas. Além disso, pode ver os custos acumulados ao longo do tempo para fazer uma estimativa mensal, trimestral ou até anual das tendências das despesas em relação a um orçamento.

Os orçamentos ajudam-no a planear e cumprir a responsabilidade financeira da sua organização. Ajudam a impedir que os limiares ou limites de custos sejam ultrapassados. Os orçamentos também podem ajudá-lo a informar outras pessoas sobre os seus gastos para gerir proativamente os custos. Além disso, com os orçamentos, pode ver o progresso dos gastos ao longo do tempo.

As recomendações mostram como pode otimizar e melhorar a eficiência ao identificar os recursos inativos e subutilizados. Em alternativa, podem mostrar opções de recursos menos dispendiosas. Ao tomar medidas com base nas recomendações, pode alterar a forma como utiliza os recursos para poupar dinheiro. Para tomar medidas, veja primeiro as recomendações de otimização de custos para identificar as potenciais ineficiências de utilização. Em seguida, tome medidas com base numa recomendação para mudar a sua utilização dos recursos do Azure para uma opção mais económica. Em seguida, verifique a ação para se certificar de que a alteração feita é bem-sucedida.

Se utilizar sistemas externos para analisar ou aceder a dados de gestão de custos, pode exportar facilmente os dados do Azure. Além disso, pode definir o agendamento diário de uma exportação no formato CSV e armazenar os ficheiros de dados no armazenamento do Azure. Em seguida, pode aceder aos dados a partir do seu sistema externo.

### <a name="cloudyn-deprecation"></a>Preterição da Cloudyn

[Cloudyn](./cloudyn/overview.md) é um serviço do Azure relacionado com o Cost Management, que está a ser preterido até ao fim de 2020. As funcionalidades existentes da Cloudyn estão a ser integradas diretamente no portal do Azure sempre que possível. Neste momento, não estão a ser integrados clientes novos, mas o suporte do produto permanecerá até que seja totalmente preterido.
 
Veja o vídeo [Azure Cost Management and Cloudyn](https://www.youtube.com/watch?v=15DzKPMBRxM) para obter mais informações sobre quando deve utilizar o Azure Cost Management ou a Cloudyn, com base nas necessidades do seu negócio. Para ver outros vídeos, visite o canal do YouTube [Cost Management](https://www.youtube.com/c/AzureCostManagement).
 
>[!VIDEO https://www.youtube.com/embed/15DzKPMBRxM]

### <a name="additional-azure-tools"></a>Ferramentas do Azure adicionais

O Azure tem outras ferramentas que não fazem parte do conjunto de funcionalidades Azure Cost Management e Faturação. No entanto, desempenham um papel importante no processo de gestão de custos. Para saber mais sobre estas ferramentas, veja as ligações seguintes.

- [Calculadora de Preços do Azure](https://azure.microsoft.com/pricing/calculator/): utilize esta ferramenta para estimar os seus custos iniciais com a cloud.
- [Azure Migrate](../migrate/migrate-overview.md): avalie a carga de trabalho do seu datacenter atual para obter informações sobre o que precisa numa solução de substituição do Azure.
- [Assistente do Azure](../advisor/advisor-overview.md): identifique VMs não utilizadas e obtenha recomendações sobre compras de instâncias reservadas do Azure.
- [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/): utilize as suas licenças do Windows Server ou SQL Server atuais no local para que as VMs no Azure as guardem.


## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com o Cost Management e a Faturação, o próximo passo é começar a utilizar o serviço.

- Comece a utilizar o Azure Cost Management para [analisar os custos](./costs/quick-acm-cost-analysis.md).
- Também pode saber mais sobre as [práticas recomendadas do Azure Cost Management](./costs/cost-mgt-best-practices.md).
