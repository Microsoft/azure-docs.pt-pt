---
title: Painel de clientes no mercado comercial da Microsoft análise no Partner Center, Azure Marketplace e Microsoft AppSource
description: Saiba como aceder a informação sobre os seus clientes, incluindo tendências de crescimento, utilizando o dashboard dos Clientes em análise de marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: shganesh-dev
ms.author: shganesh
ms.openlocfilehash: 08a889a0a26f90798f3ac8524cceb6ca83d4955a
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94415253"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>Painel de clientes na análise do mercado comercial

Este artigo fornece informações sobre o dashboard clientes no Partner Center. Este dashboard exibe informações sobre os seus clientes, incluindo tendências de crescimento, apresentadas num formato gráfico e transferível.

Para aceder ao dashboard de Clientes no Partner Center, no **Mercado Comercial** selecione **[Analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **Clientes.**

>[!NOTE]
> Para definições detalhadas de terminologia analítica, consulte [terminologia de análise de mercado comercial e questões comuns.](./partner-center-portal/faq-terminology.md)

## <a name="customers-dashboard"></a>Painel de clientes

O dashboard cliente exibe dados para clientes que adquiriram as suas ofertas. Pode ver as representações gráficas dos seguintes itens:

- Tendência ativa e agitada dos clientes
- Tendência de crescimento do cliente, incluindo clientes existentes, novos e agitados
- Clientes por encomendas e utilização
- Percentil de clientes 
- Tipo de cliente por encomendas e utilização
- Clientes por geografia
- Tabela de detalhes dos clientes
- Filtros de página de clientes

> [!NOTE]
> A latência máxima entre a aquisição e reporte de clientes no Partner Center é de 48 horas.

## <a name="elements-of-the-customers-dashboard"></a>Elementos do painel de clientes

As secções seguintes descrevem como utilizar o dashboard dos Clientes e como ler os dados.

### <a name="month-range"></a>Intervalo mensal

Pode encontrar uma seleção de mês no canto superior direito de cada página. Personalize a saída dos gráficos de página dos **Clientes** selecionando uma gama de meses com base nos últimos 6, ou 12 meses, ou selecionando uma gama de mês personalizada com uma duração máxima de 12 meses. A gama de mês predefinido (período de computação) é de seis meses.

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="Ilustra os filtros mensais na página clientes.":::

> [!NOTE]
> Todas as métricas nos widgets de visualização e relatórios de exportação honram o período de cálculo selecionado pelo utilizador.

### <a name="active-and-churned-customers-trend"></a>Tendência ativa e agitada dos clientes

Nesta secção, encontrará a tendência de crescimento dos seus clientes para o período de cálculo selecionado. As métricas e as tendências de crescimento são representadas por um gráfico de linha e exibem o valor para cada mês, pairando sobre a linha na tabela. O valor percentual abaixo **dos clientes Ativos** no widget representa a quantidade de crescimento durante o período de cálculo selecionado. Por exemplo, a imagem a seguir mostra um crescimento de 0,92% para o período de cálculo selecionado.

[![Ilustra o widget clientes na página clientes.](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

Existem três _tipos de clientes:_ novos, existentes e agitados.

- Um novo cliente adquiriu uma ou mais das suas ofertas pela primeira vez no mês selecionado.
- Um cliente existente adquiriu uma ou mais das suas ofertas antes do mês selecionado.
- Um cliente agitado cancelou todas as ofertas previamente compradas. Os clientes churned estão representados no eixo negativo no widget Trend.

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>Tendência de crescimento do cliente, incluindo clientes existentes, novos e agitados

Nesta secção, encontrará tendência e contagem de todos os clientes, incluindo novos, existentes e agitados, com uma tendência de crescimento mensal.

- O gráfico de linha representa as percentagens globais de crescimento do cliente.
- A coluna mensal representa a contagem de clientes empilhados por clientes novos, existentes e agitados.
- A contagem de clientes agitada é apresentada na direção negativa do Eixo X.
- Pode selecionar itens de lendas específicos para exibir vistas mais detalhadas. Por exemplo, selecione novos clientes da lenda para exibir apenas novos clientes.
- Pairar sobre uma coluna na tabela exibe detalhes apenas para esse mês.

[![Ilustra o widget de tendência dos Clientes na página clientes.](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>Clientes por encomendas/utilização

O **gráfico de encomendas/utilização dos Clientes** tem três separadores: Encomendas, utilização normalizada e utilização bruta. Selecione o separador **Ordens** para mostrar detalhes da encomenda.

:::image type="content" source="./media/customer-dashboard/customers-by-orders-usage.png" alt-text="Ilustra o separador Encomendas dos Clientes por encomendas e widget de utilização na página Clientes.":::

Tenha em atenção o seguinte:

- A Direção-geral apresenta detalhes dos clientes classificados pela contagem de encomendas. Após a seleção de um cliente, os dados do cliente são apresentados nas secções adjacentes "Detalhes", "Encomendas por SKUs" e "SKUs por Assentos".
- Os detalhes do perfil do Cliente são apresentados neste espaço quando as editoras são assinadas com uma função de proprietário. Se os editores forem contratados com uma função contributiva, os detalhes nesta secção não estarão disponíveis.
- O gráfico **de donuts da SKUs** apresenta a repartição das encomendas compradas para planos. Os cinco planos mais altos com a contagem de encomendas mais alta são apresentados, enquanto os restantes pedidos são agrupados em **Rest todos**.
- O gráfico **de donuts SKUs by Seats** exibe a avaria de lugares encomendados para planos. Os cinco primeiros planos com os lugares mais altos são apresentados, enquanto os restantes pedidos são agrupados em **Rest All**.

Também pode selecionar o separador **de utilização normalizado** ou **o separador de utilização Raw** para visualizar detalhes de utilização.

- A direção do Líder apresenta detalhes dos clientes classificados pela contagem de horas de utilização. Após a seleção de um cliente, os detalhes do cliente são apresentados na secção "Detalhes", "Utilização Normalizada por ofertas" e "Utilização Normalizada por Tamanho(s) (S) tamanho (s) de máquina virtual (VM)".
- Os detalhes do perfil do Cliente são apresentados neste espaço quando as editoras estão a iniciar sessão com uma função de proprietário. Se os editores estiverem com uma função de contribuinte, os detalhes nesta secção não estarão disponíveis.
- O **uso normalizado por Offers** donut chart mostra a desagregação do uso consumido pelas ofertas. Os cinco planos com a maior contagem de utilização são apresentados, enquanto as restantes ofertas são agrupadas em **Rest All**.
- A **utilização normalizada por gráfico de donuts VM Size(s)** apresenta a desagregação do uso consumido por diferentes tamanhos VM(s). Os cinco melhores tamanhos VM com a utilização normalizada mais alta são apresentados, enquanto o resto da utilização é agrupado em **Rest All**.

### <a name="top-customers-percentile"></a>Percentil de clientes de topo

O gráfico **percentil dos clientes top** tem três separadores, "Encomendas", "Uso normalizado" e "Uso bruto". O _percentil superior do cliente_ é apresentado ao longo do eixo x, conforme determinado pelo número de encomendas. O eixo y exibe a contagem de pedidos do cliente. O eixo y secundário (gráfico de linha) apresenta a percentagem cumulativa do número total de encomendas. Pode apresentar detalhes pairando sobre pontos ao longo da tabela de linha.

[![Ilustra o separador Encomendas do widget Percentil do cliente na página cliente.](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>Tipo de cliente por encomendas e utilização

O gráfico **de encomendas/utilização por tipo de cliente** apresenta o número de encomendas, o uso normalizado e as horas de utilização bruta divididas entre novos clientes e clientes existentes; com base na seleção de encomendas, normalizadas e em bruto, respectivamente, na tabela.

Este gráfico mostra o seguinte:

- Um novo cliente adquiriu uma ou mais das suas ofertas ou registou uso pela primeira vez no mesmo mês de calendário (eixo y).
- Um cliente existente adquiriu previamente uma oferta ou reportou-lhe a utilização antes do mês civil relatado (no eixo y).

[![Ilustra o separador Encomendas do widget Do Tipo cliente na página Clientes.](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>Clientes por geografia

Para o período de cálculo selecionado, o mapa de calor apresenta o número total de clientes e a percentagem de clientes recém-adicionados contra a dimensão geográfica. A cor clara para a luz no mapa representa o valor baixo a alto da contagem do cliente. Selecione um registo na tabela para fazer zoom em um país ou região.

[![Ilustra o separador Encomendas das Encomendas por widget de geografia na página clientes.](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

Tenha em atenção o seguinte:

- Pode mover o mapa para ver a localização exata.
- Pode aproximar-se de um local específico.
- O mapa de calor tem uma grelha suplementar para ver os detalhes da contagem do cliente, contagem de encomendas, horas de utilização normalizadas no local específico.
- Pode pesquisar e selecionar um país/região na grelha para ampliar a localização no mapa. Reverta para a vista original selecionando o botão **Home** no mapa.

### <a name="customer-details-table"></a>Tabela de detalhes do cliente

A tabela **de detalhes do Cliente** apresenta uma lista numerada dos 1.000 clientes mais bem classificados pela data em que adquiriram uma das suas ofertas. Pode expandir uma secção selecionando o ícone de expansão na fita de detalhes.

Tenha em atenção o seguinte:

- As informações pessoais do cliente só estarão disponíveis se o cliente tiver dado o seu consentimento. Só pode ver estas informações se tiver assinado com um nível de permissão de papel do proprietário.
- Cada coluna da grelha é ordenada.
- Os dados podem ser extraídos para um . CSV ou . . Ficheiro TSV se a contagem dos registos for inferior a 1.000.
- Se o número de registos for superior a 1.000, os dados exportados serão colocados de forma assíncronea numa página de downloads durante os próximos 30 dias.
- Aplique filtros na tabela para exibir apenas os dados em que está interessado. Filtrar dados por nome da Empresa, ID do Cliente, ID de Assinatura de Mercado, Tipo de Licença Azure, Data Adquirida, Data Perdida, E-mail do Cliente, País/Estado/Estado/Cidade/Zip, Linguagem do Cliente, e assim por diante.
- Quando uma oferta é comprada por um cliente protegido, as informações em **Dados Detalhados do Cliente** serão mascaradas (**********************************************************************************
- Os detalhes da dimensão do cliente, tais como nome da empresa, nome do cliente e e-mail do cliente estão a nível de ID da organização, não no nível de transação do Azure Marketplace ou do Microsoft AppSource.

_**Quadro 1: Dicionário de termos de dados**_

| Nome da coluna | Nome do atributo | Definição |
| ------------ | ------------- | ------------- |
| Id de assinatura de mercado | ID de assinatura de mercado | O identificador único associado à subscrição Azure que o cliente usou para adquirir a sua oferta de mercado comercial. ID foi anteriormente a Azure Subscription GUID. |
| DataAcquida | Data Adquirida | Na primeira data em que o cliente comprou qualquer oferta que tenha publicado. |
| DateLost | Data Perdida | Na última data em que o cliente cancelou a última de todas as ofertas previamente adquiridas. |
| Nome do provedor | Nome do provedor | O nome do fornecedor envolvido na relação entre a Microsoft e o cliente. Se o cliente for uma Enterprise através do Revendedor, este será o revendedor. Se estiver envolvido um Fornecedor de Solução Cloud (CSP), este será o CSP. |
| E-mail do fornecedor | E-mail do fornecedor | O endereço de e-mail do fornecedor envolvido na relação entre a Microsoft e o cliente. Se o cliente for uma Enterprise através do Revendedor, este será o revendedor. Se estiver envolvido um Fornecedor de Solução Cloud (CSP), este será o CSP. |
| FirstName | Primeiro nome do cliente | O primeiro nome fornecido pelo cliente. O nome pode ser diferente do nome fornecido na subscrição Azure de um cliente. |
| LastName | Último nome do cliente | O último nome fornecido pelo cliente. O nome pode ser diferente do nome fornecido na subscrição Azure de um cliente. |
| E-mail | E-mail do cliente | O endereço de e-mail fornecido pelo cliente final. O e-mail pode ser diferente do endereço de e-mail na subscrição Azure de um cliente. |
| Nome da empresa do cliente | Nome da empresa do cliente | O nome da empresa fornecido pelo cliente. O nome pode ser diferente da cidade na assinatura Azure de um cliente. |
| Cidade do Cliente | Cidade do Cliente | O nome da cidade fornecido pelo cliente. A cidade pode ser diferente da cidade na assinatura Azure de um cliente. |
| Código Postal do Cliente | Código Postal do Cliente | O código postal fornecido pelo cliente. O código pode ser diferente do código postal fornecido na subscrição Azure de um cliente. |
| Comunidade de ClientesCultura | Linguagem de comunicação do cliente | O idioma preferido pelo cliente para comunicação. |
| Região do País de Clientes | País/Região do Cliente | O nome país/região fornecido pelo cliente. O país/região poderia ser diferente do país/região na subscrição Azure de um cliente. |
| AzureLicenseType | Tipo de Licença Azure | O tipo de contrato de licenciamento usado pelos clientes para comprar a Azure. Também conhecido como _o canal._ Os valores possíveis são:<ul><li>Provedor de solução cloud</li><li>Enterprise</li><li>Empresa através de Revendedor</li><li>Pague como você vai</li></ul> |
| Promocionais | É Contacto Promocional Opt In | O valor irá informá-lo se o cliente optou proativamente pelo contacto promocional das editoras. Neste momento, não estamos a apresentar a opção aos clientes, por isso indicámos "Não" em todo o tabuleiro. Depois desta funcionalidade ser implementada, começaremos a atualizar em conformidade. |
| Estado do Cliente | Estado do Cliente | O estado de residência fornecido pelo cliente. O Estado pode ser diferente do estado fornecido na assinatura Azure de um cliente. |
| CommerceRootCustomer | Cliente raiz de comércio | Um ID de conta de faturação pode ser associado a vários IDs de cliente.<br>Uma combinação de um ID de conta de faturação e um ID do cliente pode ser associada a várias subscrições de marketplace comercial.<br>O Cliente Raiz de Comércio significa o nome do cliente da subscrição. |
| ID do cliente | ID de Cliente | O identificador único atribuído a um cliente. Um cliente pode ter zero ou mais subscrições do Azure Marketplace. |
| ID de conta de faturação | ID de conta de faturação | O identificador da conta em que a faturação é gerada. **Mapear iD de conta** de faturação ao **clienteID** para ligar o seu Relatório de Transações de Pagamento com os Relatórios de Cliente, Ordem e Utilização. |
||||

### <a name="customers-page-filters"></a>Filtros de página de clientes

Os filtros de página clientes são aplicados ao nível da página dos Clientes. Pode selecionar vários filtros para tornar o gráfico para os critérios que escolhe visualizar e os dados que pretende ver na grelha /exportação de "Dados de encomendas detalhadas". Os filtros são aplicados nos dados extraídos para a gama de mês que selecionou no canto superior direito da página Clientes.

> [!TIP]
> Pode utilizar o ícone de descarregamento no canto superior direito de qualquer widget para descarregar os dados. Pode fornecer feedback sobre cada um dos widgets clicando no ícone "polegares para cima" ou "polegares para baixo".

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral dos relatórios de análise disponíveis no mercado comercial, consulte os [relatórios analíticos do Access para o mercado comercial no Partner Center.](./partner-center-portal/analytics.md)
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para a sua oferta, consulte [o painel Sumário na análise do mercado comercial.](./summary-dashboard.md)
- Para obter informações sobre as suas encomendas num formato gráfico e transferível, consulte [o painel de encomendas em análise de marketplace comercial.](./orders-dashboard.md)
- Para máquinas virtuais (VM) oferece métricas de utilização e faturação medido, consulte [o Use Dashboard em análise de mercado comercial.](./usage-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento nos últimos 30 dias, consulte [downloads dashboard em análise de mercado comercial.](./partner-center-portal/downloads-dashboard.md)
- Para ver uma visão consolidada do feedback do cliente para as ofertas no Azure Marketplace e no Microsoft AppSource, consulte [o painel de análise de Avaliações & de Avaliações no Partner Center](./partner-center-portal/ratings-reviews.md).
- Para perguntas frequentes sobre análise de mercado comercial e para um dicionário abrangente de termos de dados, consulte a terminologia de análise de [mercado comercial e questões comuns.](./partner-center-portal/faq-terminology.md)
