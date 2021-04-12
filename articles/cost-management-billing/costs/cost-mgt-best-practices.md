---
title: Otimizar o investimento na cloud com o Azure Cost Management
description: Este artigo ajuda-o a obter o valor máximo dos seus investimentos na cloud, reduzir os custos e a avaliar onde está a gastar o seu dinheiro.
author: bandersmsft
ms.author: banders
ms.date: 04/02/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: e333b4aadce33e5303820951051ce83c7e65eaf9
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222389"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Como otimizar o investimento na cloud com o Azure Cost Management

O Azure Cost Management dá-lhe as ferramentas para planear, analisar e reduzir as despesas para maximizar o seu investimento na cloud. Este documento fornece uma abordagem metódica à gestão de custos e destaca as ferramentas que tem disponíveis para tratar dos desafios de custos da sua organização. O Azure facilita a criação e implementação de soluções na cloud. Contudo, é importante que essas soluções sejam otimizadas para minimizar o custo para a sua organização. Ao seguir os princípios descritos neste documento e ao utilizar as nossas ferramentas poderá garantir que a sua organização está preparada para o sucesso.

## <a name="methodology"></a>Metodologia

A gestão de custos é um problema organizacional e deverá ser uma prática contínua que inicia antes de investir dinheiro em recursos da cloud. Para implementar com sucesso a gestão de custos e otimizar os custos, a sua organização tem de:

- Estar preparada com as ferramentas adequadas para ter sucesso
- Ser responsável pelos custos
- Tomar as ações necessárias para otimizar as despesas

Existem três grupos-chave, descritos abaixo, que têm de estar alinhados na sua organização para garantir que faz uma gestão dos custos bem-sucedida.

- **Finanças** – as pessoas responsáveis pela aprovação dos pedidos de orçamento em toda a organização com base em previsões de gastos com a cloud. Pagam a fatura correspondente e atribuem os custos a várias equipas para gerar responsabilidade.
- **Gestores** – os tomadores de decisões de negócio numa organização que têm de compreender os gastos com a cloud para encontrar os melhores resultados relativos às despesas.
- **Equipas de aplicações** – engenheiros que gerem os recursos da cloud diariamente, desenvolvendo serviços para atender às necessidades da organização. Estas equipas precisam de ter flexibilidade para gerar o valor máximo para os orçamentos definidos.

### <a name="key-principles"></a>Princípios-chave

Utilize os princípios descritos abaixo para posicionar a sua organização no caminho do sucesso relativamente à gestão de custos na cloud.

Para saber mais, veja o vídeo [Cost Management setting up for success](https://www.youtube.com/watch?v=dVuwITdSAZ4) (Preparação do Cost Management para o sucesso). Para ver outros vídeos, visite o canal do YouTube [Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/dVuwITdSAZ4]

#### <a name="planning"></a>Planeamento

Um planeamento adiantado e abrangente permite-lhe adaptar a utilização da cloud aos requisitos específicos do seu negócio. Encontre a resposta para as seguintes perguntas:

- Que problema comercial tenho de resolver?
- Que padrões de utilização espero dos meus recursos?

As respostas irão ajudá-lo a selecionar as ofertas certas para si. Estas determinam que infraestrutura utilizar e como pode ser utilizada para maximizar a eficiência do Azure.

#### <a name="visibility"></a>Visibilidade

Quando estruturado de forma correta, o Cost Management ajuda-o a informar as pessoas sobre que custos do Azure são da sua responsabilidade e sobre quanto dinheiro estão a gastar. O Azure oferece serviços designados para lhe dar informações sobre *onde* está a investir o seu dinheiro. Tire partido destas ferramentas. Elas podem ajudá-lo a localizar os recursos subutilizados, eliminar desperdício e maximizar as oportunidades de poupança de custos.

#### <a name="accountability"></a>Responsabilidade

Faça a atribuição de custos na sua organização para garantir que as pessoas são responsáveis pelas despesas das equipas delas. Para compreender totalmente as despesas do Azure da sua organização, deverá organizar os recursos para maximizar as informações sobre a atribuição de custos. Uma boa organização ajuda a gerir e reduzir os custos e a responsabilizar as pessoas relativamente a despesas eficientes para a sua organização.

#### <a name="optimization"></a>Otimização

Aja de maneira a reduzir as despesas. Tire o máximo da otimização com base nas informações que reúne através do planeamento e aumentando a visibilidade de custos. Poderá ser útil comprar e licenciar as otimizações em conjunto com alterações de implementação da infraestrutura que são discutidas em detalhe neste documento.

#### <a name="iteration"></a>Iteração

Todos na sua organização têm de interagir no ciclo de vida da gestão de custos. Devem estar envolvidos de forma contínua para otimizar os custos. Deverá ser rigoroso no que diz respeito a este processo iterativo e deve torná-lo um princípio-chave na governação da cloud na sua organização.

![Diagrama de princípios-chave que mostra a visibilidade, responsabilidade e otimização](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Planear com o custo em mente

Antes de implementar recursos da cloud, avalie os seguintes itens:

- A oferta do Azure que melhor satisfaz as suas necessidades
- Os recursos que planeia utilizar
- Quanto poderão custar

O Azure fornece ferramentas para o ajudar no processo de avaliação. As ferramentas podem dar-lhe uma ideia do investimento necessário para ativar as cargas de trabalho. Depois, pode selecionar a melhor configuração para a sua situação.

### <a name="azure-onboarding-options"></a>Opções de integração do Azure

O primeiro passo para maximizar a sua experiência dentro do Cost Management é investigar e decidir sobre que oferta do Azure melhor se adequa a si. Pense sobre como pretende utilizar o Azure no futuro. Considere também como quer que o modelo de faturação seja configurado. Tenha em mente as seguintes perguntas quando tomar a sua decisão:

- Durante quanto tempo planeio utilizar o Azure? Estou a fazer um teste ou estou a planear criar uma infraestrutura de longo prazo?
- Como quero pagar o Azure? Devo fazer um pré-pagamento por um preço reduzido ou receber uma fatura no final do mês?

Para saber mais informações sobre as várias opções, visite [Como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/). Abaixo, identificámos alguns dos modelos de faturação mais comuns.

#### <a name="free"></a>[Gratuito](https://azure.microsoft.com/free/)

- 12 meses de serviços gratuitos populares
- Crédito USD200 na sua moeda de faturação para explorar serviços durante 30 dias
- Mais de 25 serviços sempre gratuitos

#### <a name="pay-as-you-go"></a>[Pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p)

- Sem mínimos nem alocações
- Preços competitivos
- Paga apenas o que utilizar
- Cancelamento a qualquer momento

#### <a name="enterprise-agreement"></a>[Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Opções de pagamento antecipado do Pré-pagamento do Azure (que antes se chamava alocação monetária)
- Acesso a preços do Azure com redução

#### <a name="azure-in-csp"></a>[Azure no CSP](https://azure.microsoft.com/offers/ms-azr-0145p/)

- Os parceiros CSP são o primeiro ponto de contacto para as necessidades dos clientes e o centro da relação com os mesmos
- Os parceiros CSP aprovisionam os novos clientes, encomendam subscrições, gerem subscrições e realizam tarefas de administração em nome dos seus clientes
- Os parceiros CSP agregam serviços com soluções únicas ou revendem o Azure enquanto controlam os preços, os termos e a faturação

## <a name="estimate-the-cost-of-your-solution"></a>Estimar o custo da solução

Antes de implementar qualquer infraestrutura, avalie quanto irá custar a solução. A avaliação ajudá-lo-á a criar um orçamento para a sua organização, prévio, pela carga de trabalho. Depois, poderá utilizar um orçamento ao longo do tempo para utilizar como referência relativamente à validade da estimativa inicial. E poderá compará-lo com o custo real da solução implementada.

### <a name="azure-pricing-calculator"></a>Calculadora de preços do Azure

A calculadora de preços do Azure permite-lhe misturar e corresponder diferentes combinações de serviços do Azure para ver uma estimativa dos custos. Pode implementar a solução com diferentes métodos no Azure; cada um poderá influenciar os custos globais. Pensar de forma adiantada sobre todas as necessidades de infraestrutura da implementação da cloud ajuda-o a utilizar a ferramenta da forma mais eficaz possível. Tal poderá ajudá-lo a obter uma estimativa sólida das despesas estimadas no Azure.

Para obter mais informações, consulte a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Migrate

O Azure Migrate é um serviço que avalia as cargas de trabalho atuais da sua organização em datacenters no local. Dá-lhe uma ideia daquilo que poderá precisar de uma solução de substituição do Azure. Primeiro, o Azure Migrate analisa as máquinas virtuais no local para determinar se a migração é viável. Depois, recomenda o dimensionamento de VM no Azure para maximizar o desempenho. Por fim, cria também uma estimativa de custos para uma solução baseada no Azure.

Para obter mais informações, veja [Azure Migrate](../../migrate/migrate-services-overview.md).

## <a name="analyze-and-manage-your-costs"></a>Analisar e gerir os custos

Mantenha-se informado sobre como os custos da sua organização evoluem com o tempo. Utilize as seguintes técnicas para compreender e gerir adequadamente as despesas.

### <a name="organize-resources-to-maximize-cost-insights-and-accountability"></a>Organizar os recursos para maximizar as informações de custos e a responsabilidade

Uma estrutura organizacional bem planeada para as suas hierarquias de faturação e recursos do Azure ajuda a dar uma boa compreensão e controlo sobre os custos à medida que cria a sua infraestrutura na cloud. Veja o vídeo [Configurar hierarquias de entidades](https://www.youtube.com/watch?v=n3TLRaYJ1NY) para obter uma melhor compreensão das ferramentas organizacionais disponíveis e como tirar partido delas. Para ver outros vídeos, visite o canal do YouTube [Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/n3TLRaYJ1NY]

À medida que avalia e cria uma hierarquia que satisfaz as suas necessidades, faça as seguintes perguntas a si mesmo.

*Que hierarquia de faturação tenho disponível e quais são os diferentes âmbitos que posso utilizar?*

Identifique o plano de faturação da sua organização ao determinar o seu tipo de oferta do Azure. Os âmbitos disponíveis para cada plano de faturação do Azure estão documentados em [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

*Se tenho várias equipas, como devo organizar as minhas subscrições e grupos de recursos?*

Criar uma subscrição ou grupo de recursos para cada equipa é uma prática comum. Podem ajudá-lo a diferenciar custos e responsabilizar as equipas. No entanto, os custos estão associados à subscrição ou grupo de recursos.

Se já tiver equipas com várias subscrições, considere agrupar as subscrições em grupos de gestão para analisar os custos em conjunto. Grupos de gestão, subscrições e grupos de recursos fazem parte da hierarquia RBAC do Azure. Utilize-os coletivamente para controlar o acesso nas suas equipas.

Os recursos podem abranger vários âmbitos, especialmente quando são partilhados por várias equipas ou cargas de trabalho. Considere identificar os recursos com etiquetas. As etiquetas são abordadas na secção seguinte.

*Tenho ambientes de Desenvolvimento e Produção?*

Considere criar subscrições de Dev/Test para os seus ambientes de desenvolvimento para tirar partido da redução de preços. Se as cargas de trabalho abrangerem várias equipas ou âmbitos do Azure, considere utilizar etiquetas para identificá-las.

### <a name="tag-shared-resources"></a>Identificar recursos partilhados

As etiquetas são uma forma eficaz de compreender os custos que se estendem por várias equipas e âmbitos do Azure. Por exemplo, pode ter um recurso, como um servidor de e-mail, que muitas equipas utilizam. Pode colocar um recurso partilhado, como o servidor de e-mail, numa subscrição dedicada a recursos partilhados ou numa subscrição existente. Se o colocar numa subscrição existente, o proprietário da subscrição pode não querer que o seu custo seja acumulado mensalmente para a sua equipa. Para este exemplo, pode utilizar uma etiqueta para identificar o recurso como sendo partilhado.

Da mesma forma, também pode ter aplicações Web ou ambientes, como Teste ou Produção, que utilizam recursos em várias subscrições de diferentes equipas. Para compreender melhor o custo total das cargas de trabalho, identifique os recursos que utilizam. Quando as etiquetas são aplicadas corretamente, pode aplicá-las como um filtro na análise de custos para compreender melhor as tendências.

Depois de planear a identificação de recursos, pode configurar uma política do Azure para impor a identificação de recursos. Veja o vídeo [Como rever as políticas de etiquetas com o Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) para compreender as ferramentas que estão disponíveis para ajudá-lo a impor a identificação de recursos dimensionáveis. Para ver outros vídeos, visite o canal do YouTube [Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/nHQYcYGKuyw]

### <a name="use-cost-analysis"></a>Utilizar a análise de custos

A análise de custos permite-lhe analisar os custos organizacionais em profundidade ao dividir e repartir os custos com as propriedades de recursos padrão. Considere as seguintes perguntas comuns como um guia para a sua análise. Responder a estas perguntas com regularidade irá ajudá-lo a manter-se mais informado e a tomar decisões mais cientes dos custos.

- **Custos estimados para o mês atual** – Em que valor de despesas já incorri durante estes mês? Ficarei abaixo do meu orçamento?
- **Investigar anomalias** – faça verificações de rotina para garantir que os custos se mantêm dentro de um intervalo razoável de utilização normal. Quais são as tendências? Existem valores atípicos?
- **Reconciliação de faturas** – A minha última fatura custou mais do que a do mês anterior? Em que medida os hábitos de despesa mudaram ao longos dos meses?
- **Estorno interno** – Agora que sei quanto me é cobrado, como é que esses custos devem ser divididos para a minha organização?

Para obter mais informações, veja [análise de custos](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Exportar dados de faturação com agendamento

Precisa de importar os dados de faturação para um sistema externo, como um dashboard ou um sistema financeiro? Defina exportações automatizadas para o Armazenamento do Microsoft Azure para evitar transferir ficheiros manualmente todos os meses. Pode, posteriormente, configurar integrações automáticas com outros sistemas para manter os dados de faturação sincronizados.

Para obter mais informações sobre a exportação de dados de faturação, veja [Criar e gerir dados exportados](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Criar orçamentos

Depois de ter identificado e analisado os padrões de despesas, é importante começar a definir limites para si e para as suas equipas. Os orçamentos do Azure dão-lhe a habilidade para definir um orçamento baseado nos custos ou na utilização com vários limiares e alertas. Confirme que revê os orçamentos que cria regularmente para ver o progresso de evolução do orçamento e fazer alterações conforme necessário. Os orçamentos do Azure permitem-lhe configurar um acionador de automatização quando determinado limiar do orçamento for atingido. Por exemplo, pode configurar o serviço para encerrar as VMs. Ou pode mover a infraestrutura para um escalão de preço diferente em resposta a um acionador do orçamento.

Para obter mais informações, veja [Orçamentos do Azure](tutorial-acm-create-budgets.md).

Para obter mais informações sobre a automatização baseada em orçamentos, veja [Automatização Baseada em Orçamentos](../manage/cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>Tome ações para otimizar
Utilize os seguintes métodos para otimizar as despesas.

### <a name="cut-out-waste"></a>Elimine o desperdício

Depois de implementar a sua infraestrutura no Azure, é importante verificar se está a ser utilizada. A maneira mais fácil de começar a poupar imediatamente é rever os seus recursos e remover os que não estejam a ser utilizados. A partir daí, deverá determinar se os recursos estão a ser utilizados da forma mais eficiente possível.

#### <a name="azure-advisor"></a>Azure Advisor

O Assistente do Azure é um serviço que, entre outras coisas, identifica máquinas virtuais com uma utilização baixa a partir de um ponto de vista de utilização da CPU ou da rede. A partir daí, poderá decidir se quer encerrar ou redimensionar a máquina virtual com base no custo estimado para continuar a executar as máquinas virtuais. O Assistente do Azure também fornece recomendações para compras de instâncias reservadas. As recomendações são feitas com base nos últimos 30 dias de utilização das máquinas virtuais. Quando segue as recomendações, estas podem ajudá-lo a reduzir as despesas.

Para obter mais informações, veja [Azure Advisor](../../advisor/advisor-overview.md) (Assistente do Azure).

### <a name="size-your-vms-properly"></a>Dimensionar as VMs adequadamente

O dimensionamento de uma VM tem um impacto significativo no custo geral do Azure. O número de VMs necessárias no Azure poderá não ser equivalente ao que tem atualmente implementado num datacenter no local. Verifique se escolhe o tamanho correto para as cargas de trabalho que planeia executar.

Para obter mais informações, veja [Azure IaaS: proper sizing and cost](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/) (Azure IaaS: dimensionamento adequado e custo).

### <a name="use-purchase-discounts"></a>Utilizar descontos de compra

O Azure tem vários descontos dos quais a sua organização deverá tirar partido para poupar dinheiro.

#### <a name="azure-reservations"></a>Reservas do Azure

As Reservas do Azure permitem o pré-pagamento por um ano ou três anos de capacidade de computação da máquina virtual ou Base de Dados SQL. O pré-pagamento permite-lhe obter um desconto nos recursos que utiliza. As reservas do Azure podem reduzir significativamente os custos de computação da sua máquina virtual ou base de dados SQL: até 72% nos preços pay as you go com um compromisso antecipado de um ou três anos. As reservas oferecem um desconto de faturação e não afetam o estado de runtime das máquinas virtuais ou bases de dados SQL.

Para obter mais informações, veja [O que são as Reservas do Azure?](../reservations/save-compute-costs-reservations.md)

#### <a name="use-azure-hybrid-benefit"></a>Utilizar o Benefício Híbrido do Azure

Se já tiver licenças do Windows Server ou do SQL Server nas implementações no local, poderá utilizar o programa Benefício Híbrido do Azure para poupar no Azure. Com o benefício do Windows Server, cada licença cobre o custo do sistema operativo (em até duas máquinas virtuais!) e paga apenas os custos de computação base. Pode utilizar licenças do SQL Server existentes para poupar até 55 por cento em opções da Base de Dados SQL baseadas em vCore. As opções incluem o SQL Server nas Máquinas Virtuais do Azure e SQL Server Integration Services.

Para obter mais informações, veja [Calculadora de poupanças do Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Outros recursos

O Azure também tem um serviço que lhe permite criar serviços que tiram partido da capacidade excedente no Azure para preços reduzidos. Para obter mais informações, veja [Utilizar VMs de baixa prioridade com o Batch](../../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Passos seguintes
- Se for a primeira vez que utiliza o Cost Management, veja [O que é o Azure Cost Management?](../cost-management-billing-overview.md) para saber como este ajuda a monitorizar e a controlar os gastos do Azure, bem como a otimizar a utilização de recursos.