---
title: Painel do cliente na análise do Marketplace comercial no Partner Center
description: Saiba como acessar informações sobre seus clientes, incluindo tendências de crescimento, usando o painel do cliente na análise do Marketplace comercial.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: f2eaaf2ec2f3dad1da345d0cfeadf1cae223a592
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264021"
---
# <a name="customer-dashboard-in-commercial-marketplace-analytics"></a>Painel do cliente na análise do Marketplace comercial

Este artigo fornece informações sobre o **painel do cliente** no Partner Center. Este painel exibe informações sobre seus clientes, incluindo tendências de crescimento, apresentadas em um formato gráfico e baixável.

Para acessar o **painel do cliente**, abra o painel **[analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em Marketplace comercial.

>[!NOTE]
> Para obter definições detalhadas de terminologia de análise, consulte [perguntas e terminologia frequentes para análise do Marketplace comercial](./faq-terminology.md).

## <a name="customer-dashboard"></a>Dashboard de cliente

O **painel do cliente** do menu **analisar** exibe dados para clientes que adquiriram suas ofertas. Você pode exibir representações gráficas dos seguintes itens:

- [Resumo do cliente](#customer-summary)
- [Cliente por geografia](#customer-by-geography)
- [Tendências do cliente](#customer-trends)
- [Clientes por pedidos e uso](#customers-by-orders-and-usage)
- [Clientes por SKUs](#customers-by-skus)
- [Pedidos e uso por tipo de cliente](#orders-and-usage-by-customer-type)
- [Tabela de detalhes do cliente](#customer-details-table)
- [Filtros de página do cliente](#customer-page-filters)

### <a name="customer-summary"></a>Resumo do cliente

A seção Resumo do cliente exibe uma contagem de todos os clientes, incluindo novo, existente e com variação, durante o intervalo de datas selecionado.

- O total de clientes é definido como a contagem de todos os clientes que compraram sua oferta e têm pelo menos uma ordem que não foi cancelada.
- O percentual de crescimento dos clientes em comparação com o mês anterior é indicado pelo número e indicador ascendente no indicador verde ou inferior em vermelho.
- As tendências de crescimento são representadas por gráficos de barras e exibirão o valor de cada mês passando o mouse sobre as colunas do gráfico.

Há três **tipos de clientes**: novo, existente e com variação.

- Um novo cliente adquiriu uma ou mais de suas ofertas pela primeira vez no mês selecionado.
- Um cliente existente adquiriu uma ou mais de suas ofertas antes do mês selecionado.
- Um cliente com rotatividade cancelou todas as ofertas adquiridas anteriormente.

### <a name="customer-by-geography"></a>Cliente por geografia

O gráfico **cliente por geografia** mostra as contagens de todos os clientes e clientes adquiridos durante o intervalo de datas selecionado e são mapeados com base no país do cliente. A cor de luz para escuro no mapa representa o valor de baixo para alto da contagem de clientes. Clique em um registro na tabela para ampliar um país.

O calor exibe a contagem de clientes e% por país do cliente. Você pode mover o mapa para exibir o local exato e aplicar zoom em um local específico. Este mapa tem uma grade suplementar que permite exibir o% dos clientes por local, bem como os clientes adicionados recentemente a esse local.

### <a name="customer-trends"></a>Tendências do cliente

O gráfico de **tendências do cliente** exibe uma contagem de todos os clientes, incluindo novos, existentes e com variação, com uma tendência de crescimento mensal.

- O gráfico de linhas representa as porcentagens de crescimento geral do cliente.
- A coluna mês representa a contagem de clientes empilhados por clientes novos, existentes e com rotatividade.
- A contagem de clientes com variação é exibida na direção negativa do eixo Y.
- Você pode selecionar itens de legenda específicos para exibir exibições mais detalhadas. Por exemplo, selecione novos clientes na legenda para exibir apenas os novos clientes.
- Você pode usar o controle deslizante na parte superior do gráfico para rolar para a direita e para a esquerda no eixo x e concentrar-se em pontos de dados específicos para exibir mais detalhadamente.
- Passar o mouse sobre uma coluna do gráfico exibirá detalhes somente para esse mês.

### <a name="customers-by-orders-and-usage"></a>Clientes por pedidos e uso

O gráfico de **pedidos/uso de clientes** tem três guias, "pedidos", "uso normalizado" e "uso bruto". O **principal percentual do cliente** é exibido ao longo do eixo x, conforme determinado pelo número de pedidos. O eixo y exibe a contagem de pedidos do cliente. O eixo y secundário (grafo de linha) exibe a porcentagem cumulativa do número total de pedidos. Você pode exibir detalhes passando o mouse sobre pontos ao longo do gráfico de linhas.

Por exemplo, consulte o gráfico abaixo para obter o uso normalizado: o percentual de 30 principais de clientes está contribuindo para 87% do uso normalizado de forma cumulativa. O percentual de 30 de clientes está contribuindo apenas 1.570.000 horas de uso.

### <a name="customers-by-skus"></a>Clientes por SKUs

Os gráficos de **SKUs/uso dos clientes** são descritos abaixo.

1. O quadro líder apresenta detalhes dos principais clientes 50 classificados por contagem de pedidos. Depois de selecionar um cliente, os detalhes do cliente são apresentados nas seções 2, 3 e 4 deste quadro principal.
2. Os detalhes do perfil do cliente são exibidos nesse espaço quando os Publicadores estão conectados com uma função de proprietário. Se os Publicadores estiverem conectados com uma função de colaborador, os detalhes nesta seção não estarão disponíveis.
3. O gráfico de rosca pedidos por SKUs exibe a divisão de pedidos comprados para SKUs. Os 5 principais SKUs com a contagem de pedidos mais alto são exibidos, enquanto o restante dos pedidos é agrupado em ' Rest todos '.
4. O gráfico de rosca estações por SKUs exibe a divisão de estações solicitadas para SKUs. Os 5 principais SKUs com as estações mais altas são exibidos, enquanto o restante dos pedidos é agrupado em REST todos.

### <a name="orders-and-usage-by-customer-type"></a>Pedidos e uso por tipo de cliente

O gráfico **tipo de pedidos/uso por cliente** exibe o número de pedidos, uso normalizado e horas de uso bruto dividido entre novos clientes e clientes existentes; com base na seleção de pedidos, normalizado e uso bruto, respectivamente no gráfico.

- Um novo cliente adquiriu uma ou mais de suas ofertas ou relatou o uso pela primeira vez no mesmo mês do calendário (eixo y).
- Um cliente existente já adquiriu uma oferta de você ou relatou uso anterior ao mês do calendário relatado (no eixo y).

### <a name="customer-details-table"></a>Tabela de detalhes do cliente

A tabela **detalhes do cliente** exibe uma lista numerada dos principais clientes 1000 classificados pela data em que adquiriram primeiro uma de suas ofertas.

- As informações pessoais do cliente só estarão disponíveis se o cliente tiver fornecido consentimento. Você só poderá exibir essas informações se tiver feito logon com um nível de função de proprietário de permissões. Saiba mais sobre as funções e permissões de usuário.
- Cada coluna na grade é classificável.
- Os dados podem ser extraídos para um arquivo TSV se a contagem dos registros for menor que 1000.
- Se o número de registros for superior a 1000, os dados exportados serão colocados de forma assíncrona em uma página de downloads para os próximos 30 dias.
- Os filtros podem ser aplicados à tabela para exibir apenas os dados nos quais você está interessado. Os dados podem ser filtrados por nome da empresa, ID do cliente, ID da assinatura do Marketplace, tipo de licença do Azure, data de aquisição, data de perda, email do cliente, país/estado do cliente/cidade/zip, idioma do cliente, etc.

### <a name="customer-page-filters"></a>Filtros de página do cliente

Os filtros de **página de clientes** são aplicados no nível de página de clientes. Você pode selecionar vários filtros para renderizar o gráfico para os critérios escolhidos para exibição e os dados que deseja ver na grade/exportação de dados de pedidos detalhados. Os filtros são aplicados nos dados extraídos para o intervalo de dados selecionado no canto superior direito da página pedidos.

>[!NOTE]
> Definições detalhadas para cada um dos campos na grade de clientes, filtros de página e suas seleções possíveis estão localizadas em [perguntas frequentes e terminologia para análise do Marketplace comercial](./faq-terminology.md).

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral dos relatórios de análise disponíveis no Marketplace comercial do Partner Center, consulte [análise do Marketplace comercial no Partner Center](./analytics.md).
- Para grafos, tendências e valores de dados agregados que resumem a atividade do Marketplace para sua oferta, consulte [painel de resumo na análise do Marketplace comercial](./summary-dashboard.md).
- Para obter informações sobre seus pedidos em um formato gráfico e baixável, consulte [painel de pedidos na análise do Marketplace comercial](./orders-dashboard.md).
- Para VM (máquina virtual) oferece métricas de uso e cobrança limitada, consulte [painel de uso na análise do Marketplace comercial](./usage-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, consulte o [painel de downloads na análise do Marketplace comercial](./downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes para ofertas no Azure Marketplace e no AppSource, consulte [painel classificações e revisões na análise do Marketplace comercial](./ratings-reviews.md).
- Para perguntas frequentes sobre a análise do Marketplace comercial e um dicionário abrangente de termos de dados, consulte [perguntas e terminologia frequentes para análise do Marketplace comercial](./faq-terminology.md).
