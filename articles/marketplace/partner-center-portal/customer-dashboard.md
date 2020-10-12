---
title: Painel de clientes no mercado comercial da Microsoft análise no Partner Center, Azure Marketplace e Microsoft AppSource
description: Saiba como aceder a informação sobre os seus clientes, incluindo tendências de crescimento, utilizando o painel de clientes em análise de mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2020
author: shganesh-dev
ms.author: shganesh
ms.openlocfilehash: 44e992bb1445e1e58f42ec150a8e2c0682eea98b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87317591"
---
# <a name="customer-dashboard-in-commercial-marketplace-analytics"></a>Dashboard do Cliente na análise do mercado comercial

Este artigo fornece informações sobre o **painel de clientes** no Partner Center. Este dashboard exibe informações sobre os seus clientes, incluindo tendências de crescimento, apresentadas num formato gráfico e transferível.

Para aceder ao **painel de instrumentos**do Cliente, abra o painel **[de instrumentos de análise](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** no Mercado Comercial.

>[!NOTE]
> Para definições detalhadas de terminologia analítica, consulte [perguntas frequentes e terminologia para análise de mercado comercial.](./faq-terminology.md)

## <a name="customer-dashboard"></a>Dashboard de cliente

O painel de instrumentos do **menu 'Cliente'** do menu **Analyze** apresenta dados para clientes que adquiriram as suas ofertas. Pode ver as representações gráficas dos seguintes itens:

- [Resumo do cliente](#customer-summary)
- [Cliente por geografia](#customer-by-geography)
- [Tendências do cliente](#customer-trends)
- [Clientes por encomendas e utilização](#customers-by-orders-and-usage)
- [Clientes por planos](#customers-by-plans)
- [Encomendas e utilização por tipo de cliente](#orders-and-usage-by-customer-type)
- [Tabela de detalhes do cliente](#customer-details-table)
- [Filtros de página do cliente](#customer-page-filters)

A latência máxima entre a aquisição e reporte de clientes no Partner Center é de 48 horas.

### <a name="customer-summary"></a>Resumo do cliente

A secção de resumo do Cliente apresenta uma contagem de todos os clientes, incluindo novos, existentes e agitados, durante o intervalo de datas selecionados.

- O total de clientes é definido como a contagem de todos os clientes que compraram a sua oferta e têm pelo menos uma encomenda que não foi cancelada.
- A percentagem de crescimento do cliente face ao mês anterior é indicada pelo indicador número e para cima no indicador verde ou descendente no vermelho.
- As tendências de crescimento são representadas por gráficos de barras e mostrarão o valor para cada mês pairando sobre as colunas do gráfico.

Existem três **tipos de clientes:** novos, existentes e agitados.

- Um novo cliente adquiriu uma ou mais das suas ofertas pela primeira vez no mês selecionado.
- Um cliente existente adquiriu uma ou mais das suas ofertas antes do mês selecionado.
- Um cliente agitado cancelou todas as ofertas previamente compradas.

### <a name="customer-by-geography"></a>Cliente por geografia

O **gráfico de geografia do Cliente** mostra as contagens de todos os clientes e clientes adquiridos durante a gama de datas selecionadas, que são mapeadas com base no País/Região do Cliente. A cor clara para a luz no mapa representa o valor baixo a alto da contagem do cliente. Selecione um registo na tabela para fazer zoom em um país/região.

O mapa de calor exibe a contagem e percentagem do cliente por país/região do cliente. Pode mover o mapa para ver a localização exata e fazer zoom para um local específico. Este mapa tem uma rede suplementar que permite visualizar a % dos clientes por localização, bem como os clientes recém-adicionados a esse local.

### <a name="customer-trends"></a>Tendências do cliente

O gráfico de **tendências** do Cliente apresenta uma contagem de todos os clientes, incluindo novos, existentes e agitados, com uma tendência de crescimento mensal.

- O gráfico de linha representa as percentagens globais de crescimento do cliente.
- A coluna mensal representa a contagem de clientes empilhados por clientes novos, existentes e agitados.
- A contagem de clientes agitada é apresentada na direção negativa do Eixo Y.
- Pode selecionar itens de lendas específicos para visualizar vistas mais detalhadas. Por exemplo, selecione novos clientes da lenda para apenas exibir novos clientes.
- Pode utilizar o slider no topo da tabela para deslocar para a direita e para a esquerda no eixo x e concentrar-se em pontos de dados específicos para ver mais detalhadamente.
- Pairar sobre uma coluna da tabela apresentará detalhes apenas nesse mês.

### <a name="customers-by-orders-and-usage"></a>Clientes por encomendas e utilização

O **gráfico de encomendas/utilização** dos Clientes tem três separadores, "encomendas", "utilização normalizada" e "utilização bruta". O **percentil superior** do cliente é apresentado ao longo do eixo x, conforme determinado pelo seu número de encomendas. O eixo y exibe a contagem de pedidos do cliente. O eixo y secundário (gráfico de linha) apresenta a percentagem cumulativa do número total de encomendas. Pode apresentar detalhes pairando sobre pontos ao longo da tabela de linha.

Como exemplo, veja o gráfico abaixo para uma utilização normalizada: O percentil 30 dos clientes está a contribuir para 87% do uso normalizado cumulativamente. O percentil 30 dos clientes está apenas a contribuir com 1,57 milhões de horas de utilização.

### <a name="customers-by-plans"></a>Clientes por planos

Os **clientes por planos/gráficos de utilização** são descritos abaixo.

1. A direção do Líder apresenta detalhes dos 50 melhores clientes classificados pela contagem de encomendas. Após a seleção de um cliente, os detalhes do cliente são apresentados nas secções 2, 3 e 4 deste quadro de líderes.
2. Os detalhes do perfil do Cliente são apresentados neste espaço quando as editoras estão a iniciar sessão com uma função de proprietário. Se os editores estiverem com uma função de contribuinte, os detalhes nesta secção não estarão disponíveis.
3. As **Encomendas por planos** gráfico de donut apresenta a desagregação das encomendas compradas para planos. Os cinco primeiros planos com a contagem de encomendas mais alta são apresentados, enquanto os restantes pedidos são agrupados em "rest all".
4. O **seats by plans** donut chart mostra a avaria de lugares encomendados para planos. Os cinco primeiros planos com os lugares mais altos são apresentados, enquanto os restantes pedidos estão agrupados em repouso.

### <a name="orders-and-usage-by-customer-type"></a>Encomendas e utilização por tipo de cliente

O gráfico **de encomendas/utilização por tipo de cliente** apresenta o número de encomendas, o uso normalizado e as horas de utilização bruta divididas entre novos clientes e clientes existentes; com base na seleção de encomendas, normalizadas e em bruto, respectivamente, na tabela.

- Um novo cliente adquiriu uma ou mais das suas ofertas ou registou uso pela primeira vez no mesmo mês de calendário (eixo y).
- Um cliente existente adquiriu previamente uma oferta ou reportou-lhe a utilização antes do mês civil relatado (no eixo y).

### <a name="customer-details-table"></a>Tabela de detalhes do cliente

A tabela **de detalhes do Cliente** apresenta uma lista numerada dos 1000 melhores clientes classificados pela data em que adquiriram uma das suas ofertas.

- As informações pessoais do cliente só estarão disponíveis se o cliente tiver dado o seu consentimento. Só pode ver estas informações se tiver iniciado sessão com um nível de permissão de papel do proprietário. Saiba mais sobre as funções e permissões do utilizador.
- Cada coluna da grelha é ordenada.
- Os dados podem ser extraídos para um ficheiro TSV se a contagem dos registos for inferior a 1000.
- Se o número de registos for superior a 1000, os dados exportados serão colocados de forma assíncronea numa página de downloads durante os próximos 30 dias.
- Aplique filtros na tabela para exibir apenas os dados em que está interessado. Filtrar dados por nome da Empresa, ID do Cliente, ID de Assinatura de Mercado, Tipo de Licença Azure, Data Adquirida, Data Perdida, E-mail do Cliente, País/Estado/Estado/Cidade/Zip, Linguagem do Cliente, e assim por diante.
- Quando uma oferta é comprada por um cliente protegido, as informações em **Dados Detalhados do Cliente** serão mascaradas (**********************************************************************************
- Os detalhes da dimensão do cliente, tais como Nome da Empresa, Nome do Cliente e E-mail do Cliente estão a nível de ID da organização, não ao nível de transações do Azure Marketplace ou appSource.

### <a name="customer-page-filters"></a>Filtros de página do cliente

Os filtros **de página clientes** são aplicados ao nível da página dos Clientes. Pode selecionar vários filtros para tornar o gráfico para os critérios que escolhe visualizar e os dados que pretende ver na grelha /exportação de dados de encomendas detalhadas. Os filtros são aplicados nos dados extraídos para a gama de dados que selecionou no canto superior direito da página das encomendas.

>[!NOTE]
> Definições detalhadas para cada um dos campos na grelha do cliente, filtros de página e suas possíveis seleções estão localizadas no artigo de FAQ abaixo indicado.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral dos relatórios de análise disponíveis no mercado comercial do Partner Center, consulte [Analytics para o mercado comercial no Partner Center](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para a sua oferta, consulte [o Painel De Resumo em análise de mercado comercial.](./summary-dashboard.md)
- Para obter informações sobre as suas encomendas num formato gráfico e transferível, consulte [o Painel de Encomendas em análise de marketplace comercial.](./orders-dashboard.md)
- Para Máquina Virtual (VM) oferece métricas de utilização e faturação medido, consulte [o Painel de Utilização em análise de mercado comercial.](./usage-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento nos últimos 30 dias, consulte [Downloads Dashboard em análise de mercado comercial.](./downloads-dashboard.md)
- Para ver uma visão consolidada do feedback dos clientes para as ofertas no Azure Marketplace e appSource, consulte [ratings e comentários no painel de análise de mercado comercial.](./ratings-reviews.md)
- Para perguntas frequentes sobre análise de mercado comercial e para um dicionário abrangente de termos de dados, consulte [perguntas frequentes e terminologia para análise de mercado comercial.](./faq-terminology.md)
