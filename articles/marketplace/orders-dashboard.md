---
title: Partner Center Orders dashboard em | de análise de marketplace comercial Microsoft AppSource e Azure Marketplace
description: Saiba como aceder a relatórios analíticos sobre o seu mercado comercial oferece encomendas num formato gráfico e transferível.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: ed12e470f3f3d8c1035c1e4e2e0fa7a3b33e2369
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561386"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Dashboard de encomendas na análise do mercado comercial

Este artigo fornece informações sobre o dashboard encomendas no Partner Center. Este dashboard exibe informações sobre as suas encomendas, incluindo tendências de crescimento, apresentadas num formato gráfico e transferível.

Para aceder ao dashboard de Encomendas no Centro de Parceiros, no **Mercado Comercial,** selecione **[Ordens de Análise](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  .

>[!NOTE]
> Para definições detalhadas de terminologia analítica, consulte [terminologia de análise de mercado comercial e questões comuns.](./analytics-faq.md)

## <a name="orders-dashboard"></a>Dashboard de pedidos

O dashboard Encomendas exibe as encomendas atuais para todo o seu software como um serviço (SaaS) oferece. Pode ver as representações gráficas dos seguintes itens:

- Tendência das encomendas
- Encomendas por assento e tendência do site
- Encomendas por ofertas e SKUs
- Encomendas por geografia
- Tabela de encomendas detalhadas
- Encomendas filtros de página

> [!NOTE]
> A latência máxima entre a aquisição e reporte de clientes no Partner Center é de 48 horas.

## <a name="elements-of-the-orders-dashboard"></a>Elementos do painel de encomendas

As secções seguintes descrevem como utilizar o dashboard 'Encomendas' e como ler os dados.

### <a name="month-range"></a>Intervalo mensal

Pode encontrar uma seleção de mês no canto superior direito de cada página. Personalize a saída dos gráficos da página **encomendas** selecionando uma gama de meses com base nos últimos 6 ou 12 meses, ou selecionando uma gama de mês personalizada com uma duração máxima de 12 meses. A gama de mês predefinido (período de computação) é de seis meses.

:::image type="content" source="./media/orders-dashboard/month-filters.png" alt-text="Ilustra os filtros mensais no painel Encomendas.":::

> [!NOTE]
> Todas as métricas nos widgets de visualização e relatórios de exportação honram o período de cálculo selecionado pelo utilizador.

### <a name="orders-trend"></a>Tendência das encomendas

Nesta secção, encontrará o gráfico **de Encomendas** que mostra a tendência das suas encomendas ativas e canceladas para o período de cálculo selecionado. As métricas e as tendências de crescimento são representadas por um gráfico de linha e mostrarão o valor para cada mês, pairando sobre a linha na tabela. O valor percentual abaixo das métricas encomendas no widget representa a quantidade de crescimento ou declínio durante o período de cálculo selecionado.

Existem dois balcões de encomendas: _Ativo_ e _Cancelado._

- **Ative** é igual ao número de encomendas que estão atualmente a ser utilizadas pelos clientes durante o intervalo de datas selecionados.
- **O cancelamento** é igual ao número de encomendas que foram previamente compradas e depois canceladas durante o intervalo de datas selecionadas.

[![Ilustra o widget Encomendas no painel encomendas que mostra a tendência das encomendas ativas e canceladas.](./media/orders-dashboard/orders-trend.png)](./media/orders-dashboard/orders-trend.png#lightbox)

### <a name="orders-by-per-seat-and-site-trend"></a>Encomendas por assento e tendência do site

As **Encomendas por assento e** gráfico de linha baseado no site representam a métrica e tendência das encomendas SaaS por site e por assento SaaS compradas pelos clientes (este gráfico inclui encomendas canceladas).

[![Ilustra o widget Encomendas no painel encomendas que mostra as encomendas por assento e tendência do site.](./media/orders-dashboard/seats-sites.png)](./media/orders-dashboard/seats-sites.png#lightbox)

As ofertas do SaaS podem utilizar um de dois modelos de preços com cada plano: taxa fixa (baseada no site) ou por utilizador (à base de assentos).

- **Taxa fixa**: Permita o acesso à sua oferta com um preço fixo único mensal ou anual. Isto é por vezes referido como preços baseados no site.
- **Por utilizador**: Ative o acesso à sua oferta com um preço baseado no número de utilizadores que podem aceder à oferta ou ocupar lugares. Com este modelo baseado em utilização, pode definir o número mínimo e máximo de utilizadores suportados pelo plano. Pode criar vários planos para configurar diferentes pontos de preço com base no número de utilizadores. Estes campos são opcionais. Se não for selecionado, o número de utilizadores será interpretado como não tendo um limite (min de 1 e máximo de tantos quantos o seu serviço pode suportar). Estes campos podem ser editados como parte de uma atualização do seu plano.
- **Faturação medido**: Em cima do preço fixo. Com este modelo de preços, pode definir opcionalmente planos medidos que usam o serviço de medição de mercado API para cobrar aos clientes pelo uso que não está coberto pela taxa fixa.

Para mais detalhes sobre assento, site e faturação baseada em contadores, consulte [Como planear uma oferta SaaS para o mercado comercial.](plan-saas-offer.md)

### <a name="orders-by-offers-and-skus"></a>Encomendas por ofertas e SKUs

O gráfico de Encomendas por Ofertas e SKU mostra as medidas e tendências de todas as Ofertas:

- As ofertas de topo são apresentadas no gráfico e as restantes ofertas são agrupadas como **Rest All**.
- Pode selecionar ofertas específicas na legenda para exibir apenas essa oferta e os SKUs associados no gráfico.
- Pairar sobre uma fatia no gráfico mostra o número de encomendas e percentagem dessa oferta em comparação com o seu número total de encomendas em todas as ofertas.
- As **encomendas por ofertas de tendência** exibem tendências de crescimento mensais. A coluna mensal representa o número de encomendas pelo nome de oferta. O gráfico de linha apresenta a tendência de crescimento percentual traçada num eixo z.

:::image type="content" source="./media/orders-dashboard/orders-by-offers.png" alt-text="Ilustra o gráfico de Encomendas por Ofertas no painel de encomendas.":::

Você pode selecionar qualquer oferta e um máximo de três SKUs dessa oferta para ver a tendência mensal para a oferta, SKUs, e assentos.

### <a name="orders-by-geography"></a>Encomendas por geografia

Para o período de cálculo selecionado, o mapa de calor apresenta o número total de encomendas e a percentagem de crescimento de encomendas recém-adicionadas contra uma geografia.  A cor clara para a luz no mapa representa o valor baixo a alto da contagem do cliente. Selecione um registo na tabela para fazer zoom em um país ou região específico.

[![Ilustra o gráfico de spreads geográficos no painel de encomendas.](./media/orders-dashboard/geographical-spread.png)](./media/orders-dashboard/geographical-spread.png#lightbox)

Tenha em atenção o seguinte:

- Pode mover o mapa para ver a localização exata.
- Pode aproximar-se de um local específico.
- O mapa de calor tem uma grelha suplementar para ver os detalhes da contagem de clientes, contagem de encomendas e horas de utilização normalizadas para a localização específica.
- Pode pesquisar e selecionar um país/região na grelha para ampliar a localização no mapa. Reverta para a vista original selecionando o botão **Home** no mapa.

### <a name="orders-details-table"></a>Tabela de detalhes de encomendas

A tabela de detalhes da Ordem apresenta uma lista numerada das 1.000 principais encomendas classificadas por data de aquisição.

- Cada coluna da grelha é ordenada.
- Os dados podem ser extraídos para um . CSV ou . . Ficheiro TSV se a contagem dos registos for inferior a 1.000.
- Se os registos forem mais de 1.000, os dados exportados serão colocados de forma assíncronea numa página de downloads durante os próximos 30 dias.
- Aplique filtros na tabela **de detalhes** da Ordem para exibir apenas os dados que lhe interessam. Filtrar por País/Região, Tipo de licença Azure, tipo de licença de mercado comercial, tipo de oferta, estado de encomenda, trilhos gratuitos, ID de assinatura de mercado comercial, ID do cliente e nome da Empresa.
- Quando uma encomenda é comprada por um cliente protegido, as informações em **Dados Detalhados de Encomendas** são mascaradas (******

***Quadro 1: Dicionário de termos de dados***

| Nome da coluna em<br>interface de utilizador | Nome do atributo | Definição | Nome da coluna em programático<br>relatórios de acesso |
| ------------ | ------------- | ------------- | ------------- |
| ID de assinatura de mercado | ID de assinatura de mercado | O identificador único associado à subscrição Azure que o cliente usou para adquirir a sua oferta de mercado comercial. Para ofertas de infraestruturas, esta é a assinatura Azure do cliente GUID. Para as ofertas da SaaS, isto é mostrado como zeros uma vez que as compras da SaaS não requerem uma subscrição do Azure. | ID de assinatura de mercado |
| MêsStartDate | Data de Início do Mês | A Data de Início do Mês representa o mês de Compra. O formato é yyy-mm-dd. | MêsStartDate |
| Tipo de Oferta | Tipo de Oferta | O tipo de oferta de mercado comercial. | OfferType |
| Tipo de Licença Azure | Tipo de Licença Azure | O tipo de contrato de licenciamento usado pelos clientes para comprar a Azure. Também conhecido como Canal. Os valores possíveis são:<ul><li>Provedor de solução cloud</li><li>Grandes Empresas</li><li>Empresa através de Revendedor</li><li>Pague como você vai</li></ul> | AzureLicenseType |
| Tipo de Licença de Mercado | Tipo de Licença de Mercado | O método de faturação da oferta de mercado comercial. Os diferentes valores são:<ul><li>Faturado através de Azure</li><li>Traga a sua própria licença</li><li>Gratuito</li><li>Microsoft como Revendedor</li></ul> | MarketplaceLicenseType |
| SKU | SKU | O plano associado à oferta | SKU |
| País do Cliente | País/Região do Cliente | O nome país/região fornecido pelo cliente. O país/região poderia ser diferente do país/região na subscrição Azure de um cliente. | CustomerCountry |
| É pré-visualização SKU | É pré-visualização SKU | O valor irá informá-lo se marcou o SKU como "pré-visualização". O valor será "Sim" se o SKU tiver sido marcado em conformidade, e apenas as subscrições Azure autorizadas por você pode implementar e usar esta imagem. O valor será "Não" se o SKU não tiver sido identificado como "pré-visualização". | IsPreviewSKU |
| ID da Encomenda | ID da Encomenda | O identificador único da encomenda do cliente para o seu serviço de marketplace comercial. As ofertas baseadas em máquinas virtuais não estão associadas a uma encomenda. | OrderId |
| Quantidade de Encomendas | Quantidade de Encomendas | Número de ativos associados à ID de encomenda para encomendas ativas | OrderQuantity |
| Nome da instância da nuvem | Nome da instância da nuvem | A Cloud Microsoft em que ocorreu uma implementação em VM. | Nome cloudInstance |
| É novo cliente | É novo cliente | O valor identifica se um novo cliente adquiriu uma ou mais das suas ofertas pela primeira vez. O valor será "Sim" se no mesmo mês de calendário para "Data Adquirida". O valor será "Não" se o cliente tiver adquirido alguma das suas ofertas antes do mês civil reportado. | IsNewCustomer |
| Estado da Encomenda | Estado da Encomenda | O estado de uma ordem de mercado comercial no momento em que os dados foram atualizados pela última vez. | Ordem Estatísticas |
| Data de Cancelamento de Encomenda | Data de Cancelamento de Encomenda | A data em que a encomenda do mercado comercial foi cancelada. | OrderCancelDate |
| Nome da empresa do cliente | Nome da empresa do cliente | O nome da empresa fornecido pelo cliente. O nome pode ser diferente da cidade na assinatura Azure de um cliente. | Nome da Empresa de Clientes |
| Data de Compra da Encomenda | Data de Compra da Encomenda | A data em que a ordem do mercado comercial foi criada. O formato é yyy-mm-dd. | EncomendaDeseDate |
| Nome da Oferta | Nome da Oferta | O nome da oferta do mercado comercial. | OfferName |
| Data de Fim do Julgamento | Data de Fim do Julgamento | A data do período experimental para esta ordem terminará ou terminou. | Data de Teste |
| ID de Cliente | ID de Cliente | O identificador único atribuído a um cliente. Um cliente pode ter zero ou mais subscrições do Azure Marketplace. | CustomerId |
| ID de conta de faturação | ID de conta de faturação | O identificador da conta em que a faturação é gerada. **Mapear iD de conta** de faturação ao **clienteID** para ligar o seu Relatório de Transações de Pagamento com os Relatórios de Cliente, Ordem e Utilização. | BillingAccountId |
| Conta de Ativos | Contagem de ativos | O número de ativos associados à identificação da encomenda. | Preterido |
|||||

### <a name="orders-page-filters"></a>Encomendas filtros de página

Os filtros da página **Ordens** são aplicados ao nível da página Encomendas. Pode selecionar um ou vários filtros para tornar o gráfico para os critérios que escolhe visualizar e os dados que pretende ver na grelha /exportação de dados de encomendas detalhadas. Os filtros são aplicados nos dados extraídos para a gama de mês que selecionou no canto superior direito da página das encomendas.

> [!TIP]
> Pode utilizar o ícone de descarregamento no canto superior direito de qualquer widget para descarregar os dados. Pode fornecer feedback sobre cada um dos widgets clicando no ícone "polegares para cima" ou "polegares para baixo".

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral dos relatórios de análise disponíveis no mercado comercial, consulte os [relatórios analíticos do Access para o mercado comercial no Partner Center.](./partner-center-portal/analytics.md)
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para a sua oferta, consulte [o painel Sumário na análise do mercado comercial.](./summary-dashboard.md)
- Para obter informações sobre as suas encomendas num formato gráfico e transferível, consulte [o Painel de Encomendas em análise de marketplace comercial.](orders-dashboard.md)
- Para Máquina Virtual (VM) oferece métricas de utilização e faturação medido, consulte [o painel de uso em análise de mercado comercial.](./usage-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento nos últimos 30 dias, consulte [downloads dashboard em análise de mercado comercial.](./partner-center-portal/downloads-dashboard.md)
- Para ver uma visão consolidada do feedback do cliente para as ofertas no Azure Marketplace e appSource, consulte [o painel de avaliação de avaliações & ratings no Partner Center](./partner-center-portal/ratings-reviews.md).
- Para perguntas frequentes sobre análise de mercado comercial e para um dicionário abrangente de termos de dados, consulte a terminologia de análise de [mercado comercial e questões comuns.](./analytics-faq.md)