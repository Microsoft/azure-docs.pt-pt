---
title: Otimizar o seu investimento na cloud com o Azure Cost Management | Documentos da Microsoft
description: Este artigo ajuda a tirar o máximo proveito pelos seus investimentos na cloud, reduzir os custos e avaliar onde é gasto o dinheiro.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 493b71e503b6023eeea4a71e34d5405f84821e28
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58009650"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Como otimizar o seu investimento na cloud com o Azure Cost Management

O Azure Cost Management fornece as ferramentas para planejar, analisar e reduzir os gastos para maximizar o seu investimento na cloud. Este documento fornece-lhe uma abordagem metódica para gestão de custos e realça as ferramentas disponíveis para, como resolver desafios de custo de sua organização. O Azure torna fácil criar e implementar soluções na cloud. No entanto, é importante que essas soluções são otimizadas para minimizar o custo para a sua organização. Seguir os princípios descritos neste documento e usar que nossas ferramentas irão ajudar a certificar-se de que sua organização está preparado para o sucesso.

## <a name="methodology"></a>Metodologia

Gestão de custos é um problema de organizacional e deve ser uma prática em curso que começa antes de investir recursos em nuvem. Para implementar a gestão de custos e otimizar os custos com êxito, sua organização tem de:

- Esteja preparado com as ferramentas apropriadas para o sucesso
- Responsabilizar-se de que os custos de
- Execute as ações apropriadas para otimizar os gastos

Três grupos principais, descritos a seguir, devem obrigatoriamente estar alinhados na sua organização para se certificar de que gerencie com êxito os custos.

- **Finanças** -responsáveis pela aprovação de pedidos de orçamento em toda a organização com base em previsões de gastos da cloud. Eles pagam a correspondente fatura e atribuir custos a várias equipes a responsabilidade de unidade.
- **Os gerentes** - Business decisores numa organização que tem de compreender os gastos para localizar os melhores resultados de gastos da cloud.
- **As equipas de aplicações** - gerir recursos de nuvem em todos os dias, os engenheiros desenvolver serviços para atender às necessidades da organização. Essas equipes tem a flexibilidade de fornecer o máximo proveito de seus orçamentos definidos.

### <a name="key-principles"></a>Princípios fundamentais

Utilize os princípios descritos abaixo para posicionar sua organização para o sucesso na gestão de custos na cloud.

#### <a name="planning"></a>Planeamento

Planejamento abrangente e inicial permite que personalize a utilização da cloud aos seus requisitos comerciais específicos. Pergunte-se:

- Que problema de negócios que estou a resolver
- Quais padrões de uso que espero do meus recursos?

Suas respostas irão ajudá-lo a selecionar as ofertas que são adequadas para si. Eles determinam a infraestrutura de utilização e como são utilizadas para maximizar sua eficiência do Azure.

#### <a name="visibility"></a>Visibilidade

Quando estruturados bem, o Cost Management ajuda-o para informar as pessoas sobre os custos do Azure são responsáveis para ou por que gastam dinheiro. O Azure tem serviços concebidos para serem facultam informações sobre *onde* seu dinheiro é gasto. Tire partido destas ferramentas. Eles podem ajudá-lo a localizar os recursos que são utilizados, remova o desperdício e maximizam as oportunidades de poupança de custos.

#### <a name="accountability"></a>Responsabilidade

Os custos de atributo na sua organização para garantir que as pessoas responsáveis são responsáveis pela gastos da sua equipe. Para compreender totalmente gastos do Azure da sua organização, deve organizar os recursos para maximizar a informações sobre a atribuição de custo. Boa organização ajuda a gerir e reduzir os custos e manter as pessoas responsáveis pela gastos eficiente na sua organização.

#### <a name="optimization"></a>Otimização

Act para reduzir os gastos. Aproveite ao máximo-lo com base em averiguações coletadas por meio de planejamento e aumentar a visibilidade de custo. Pode considerar a compra e licenciamento otimizações, juntamente com as alterações de implementação de infraestrutura que são discutidas detalhadamente mais adiante neste documento.

#### <a name="iteration"></a>Iteração

Todas as pessoas na sua organização devem fazer do ciclo de vida de gestão de custos. Eles precisam para se manter envolvidos numa base contínua para otimizar os custos. Ser rigoroso sobre esse processo interativo e torná-lo um princípio importante de governação de responsável de cloud na sua organização.

![Diagrama de princípios fundamentais que mostra visibilidade, da responsabilização e Otimização](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Planear com custo em mente

Antes de implementar recursos na cloud, avalie os seguintes itens:

- A oferta do Azure que melhor atenda às suas necessidades
- Os recursos que pretende utilizar
- Quanto poderão custar

O Azure fornece ferramentas para ajudá-lo no processo de avaliação. As ferramentas podem fornecer uma boa noção sobre o investimento necessário para permitir que as cargas de trabalho. Em seguida, pode selecionar a melhor configuração para a sua situação.

### <a name="azure-onboarding-options"></a>Opções de integração do Azure

A primeira etapa no aumento de sua experiência de gestão de custo é investigar e decidir qual oferta do Azure é melhor para. Pense sobre como planear a utilização do Azure no futuro. Além disso, considere como pretende que o modelo de faturação configurado. Ao tomar a sua decisão, considere as seguintes perguntas:

- Quanto posso planear a utilização do Azure? Estou testando, ou posso planear criar a infraestrutura de longo prazo?
- Como deseja pagar para o Azure? Deve, efetue o pré-pagamento de um preço reduzido ou obter faturado no final do mês?

Para saber mais sobre as várias opções, visite [como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/). Vários dos modelos de faturação mais comuns são identificados abaixo.

#### <a name="freehttpsazuremicrosoftcomfree"></a>[Gratuito](https://azure.microsoft.com/free/)

- 12 meses de serviços gratuitos populares
- $200 em créditos para explorar os serviços durante 30 dias
- 25 serviços sempre são gratuitos

#### <a name="pay-as-you-gohttpsazuremicrosoftcomoffersms-azr-0003p"></a>[Pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p)

- Sem valores mínimos ou compromissos
- Preços competitivos
- Paga apenas o que utilizar
- Cancele quando quiser

#### <a name="enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement"></a>[Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Opções para compromissos monetários iniciais
- Acesso a redução de preços do Azure

## <a name="estimate-the-cost-of-your-solution"></a>Calcular o custo da sua solução

Antes de implementar qualquer infraestrutura, avalie a sua solução quanto custará. A avaliação irá ajudá-lo a criar um orçamento para a sua organização para a carga de trabalho, inicial. Em seguida, pode usar um orçamento ao longo do tempo para efetuar testes de desempenho a validade de sua estimativa inicial. E pode compará-lo com o custo real da sua solução implementada.

### <a name="azure-pricing-calculator"></a>Calculadora de preços do Azure

A Calculadora de preços do Azure permite-lhe combinar e corresponder diferentes combinações de serviços do Azure para ver uma estimativa dos custos. Pode implementar a sua solução com diferentes formas no Azure – cada pode influenciar seus gastos gerais. Pensar no início sobre todas as necessidades de infraestrutura da sua implementação na cloud ajuda-o a utilizar a ferramenta de forma mais eficaz. Ele pode ajudá-lo a obter uma estimativa sólida dos seus gastos estimados no Azure.

Para obter mais informações, consulte a [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Migrate

O Azure Migrate é um serviço que avalia as cargas de trabalho atual da sua organização em datacenters no local. Ele fornece informações sobre o que poderão precisar de uma solução de substituição do Azure. Em primeiro lugar, para migrar analisa as suas máquinas no local para determinar se a migração é viável. Em seguida, ele recomenda o dimensionamento de VM no Azure para maximizar o desempenho. Por fim, também cria uma estimativa de custo para uma solução baseada no Azure.

Para obter mais informações, consulte [do Azure Migrate](../migrate/migrate-overview.md).

## <a name="analyze-and-manage-your-costs"></a>Analisar e gerir os seus custos

Mantenha-se informado sobre como os custos da sua organização evoluam ao longo do tempo. Utilize as seguintes técnicas para compreender e gerir os seus gastos adequadamente.

### <a name="organize-and-tag-your-resources"></a>Organizar e marcar seus recursos

Organize os recursos com custo em mente. Como criar subscrições e grupos de recursos, considere as equipes responsáveis pelos custos associados. Certifique-se de que os relatórios de mantém a sua organização em mente. As subscrições e grupos de recursos fornecem registos boa para organizar e atributo gastos na sua organização. Etiquetas fornecem uma boa maneira de custo de atributo. Pode utilizar etiquetas como um filtro. E pode usá-los para agrupar por, quando analisa dados e investigar os custos. Clientes com contrato Enterprise também podem criar os departamentos e colocar as subscrições abaixo deles. Com base no custo de organização no Azure ajuda a manter as pessoas relevantes na sua organização responsáveis pela reduzir os gastos da sua equipe.

### <a name="use-cost-analysis"></a>Utilizar a análise de custos

Análise de custo permite-lhe analisar os custos organizacionais aprofundados ao dividir e repartir os custos com propriedades de recurso padrão. Considere as seguintes perguntas comuns como guia para sua análise. Resposta a estas perguntas regularmente irá ajudá-lo a manter-se mais informadas e ativar mais econômico decisões.

- **Os custos estimados para o mês atual** – quanto ter incorrido até aqui este mês? Irá permanecer em meu orçamento?
- **Investigar anomalias** – fazer verificações de rotina para se certificar de que os custos de se manter dentro de uma gama razoável de utilização normal. Quais são as tendências? Existem quaisquer valores atípicos?
- **Reconciliação de nota fiscal** -é meu mais recente faturadas custam mais do que o mês anterior? Como hábitos gastos alterar mês ano-mês?
- **Estorno interno** - agora que sei quanto eu estou a ser cobrado, como devem esses custos ser divididos para a minha organização?

Para obter mais informações, consulte [análise de custo](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Exportar dados de faturação com base numa agenda

Precisa importar os seus dados de faturação para um sistema externo, como um dashboard ou o sistema financeiro? Configurar exportações automatizadas para o armazenamento do Azure e evitar o download manualmente ficheiros de todos os meses. Pode, em seguida, configurar facilmente automática integrações com outros sistemas para manter os seus dados de faturação em sincronia.

Para obter mais informações sobre a exportação de dados de faturação, consulte [criar e gerir os dados exportados](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Criar orçamentos

Depois de ter identificado e analisado os padrões de gastos, é importante começar a definir limites para si e para as suas equipas. Orçamentos do Azure dão-lhe a capacidade de definir um custo ou o orçamento de baseada na utilização de mensagens em fila com muitos limiares e alertas. Certifique-se rever os orçamentos de que cria regularmente para ver o progresso de grave pendente de orçamento e fazer alterações, conforme necessário. Orçamentos do Azure também permitem-lhe configurar um acionador de automatização quando for atingido um limiar de orçamento determinado. Por exemplo, pode configurar o seu serviço para encerrar as VMs. Ou, pode mover a infraestrutura para um escalão de preço diferente em resposta a um acionador de orçamento.

Para obter mais informações, consulte [orçamentos de Azure](tutorial-acm-create-budgets.md).

Para obter mais informações sobre a automatização com base no orçamento, consulte [automatização com base do orçamento](../billing/billing-cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>Agir de forma a otimizar
Utilize as seguintes opções para otimizar os gastos.

### <a name="cut-out-waste"></a>Recortar desperdício

Depois de implementar a infraestrutura no Azure, é importante certificar-se de que está a ser utilizado. A maneira mais fácil para começar a economizar imediatamente é rever seus recursos e remova qualquer um que não estão a ser utilizado. A partir daí, deve determinar se seus recursos estão a ser utilizados mais eficientemente possível.

#### <a name="azure-advisor"></a>Azure Advisor

O Azure Advisor é um serviço que, entre outras coisas, identifica as máquinas virtuais com baixa utilização de um ponto de vista de utilização da CPU ou de rede. A partir daí, pode decidir encerrar ou redimensionar a máquina baseada no custo estimado para continuar a executar as máquinas. O Advisor também fornece recomendações para compras de instância reservada. As recomendações baseiam-se os últimos 30 dias de utilização da máquina virtual. Quando acionado, as recomendações podem ajudar a reduzir os gastos.

Para obter mais informações, consulte [do Assistente do Azure](../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>As VMs de tamanho corretamente

Dimensionamento de VM tem um impacto significativo sobre o custo geral do Azure. O número de VMs necessárias no Azure poderá não são equivalentes para o que atualmente implementados num datacenter no local. Certifique-se de sua escolha com a dimensão certa para as cargas de trabalho que pretende executar.

Para obter mais informações, consulte [IaaS do Azure: dimensionamento correto e o custo](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/).

### <a name="use-purchase-discounts"></a>Descontos de compra de utilização

O Azure tem muitas descontos que sua organização deve tirar vantagem para poupar dinheiro.

#### <a name="azure-reservations"></a>Reservas do Azure

Reservas do Azure permitem que efetue o pré-pagamento de um ano ou três anos de máquina virtual ou base de dados SQL a capacidade de computação. Pré-pagamento permitirá que obter um desconto sobre os recursos que utiliza. Reservas do Azure podem reduzir significativamente sua máquina virtual ou os custos de computação de base de dados do SQL — até 72% em preços pay as you go com o compromisso inicial de um ano ou três anos. reservas de fornecem um desconto de faturação e não afetam o estado de tempo de execução das suas máquinas virtuais ou bases de dados SQL.

Para obter mais informações, consulte [quais são as reservas do Azure?](../billing/billing-save-compute-costs-reservations.md).

#### <a name="use-azure-hybrid-benefit"></a>Utilize o benefício híbrido do Azure

Se já tiver licenças do Windows Server ou SQL Server das implementações no local, pode utilizar o programa benefício híbrido do Azure para guardar no Azure. Com o benefício do Windows Server, cada licença abrange o custo do sistema operacional (até duas máquinas de virtuais) e só paga os custos de computação base. Pode utilizar licenças existentes do SQL Server para poupar até 55 por cento nas opções de base de dados SQL baseada em vCore. as opções incluem o SQL Server em máquinas virtuais do Azure e SQL Server Integration Services.

Para obter mais informações, consulte [Calculadora de poupanças do benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Outros recursos

O Azure também tem um serviço que lhe permite criar serviços que tiram partido da capacidade excedente no Azure para taxas reduzidas. Para obter mais informações, consulte [utilizar VMs de baixa prioridade com o Batch](../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Passos Seguintes
- Se estiver familiarizado com a gestão de custos, leia [o que é o Azure Cost Management?](overview-cost-mgt.md) para saber como o ajuda a monitorizar e controlar os gastos do Azure e para otimizar o uso de recursos.
