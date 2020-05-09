---
title: Painel de clientes na análise do mercado comercial da Microsoft no Partner Center
description: Saiba como aceder a informações sobre os seus clientes, incluindo tendências de crescimento, utilizando o dashboard de clientes na análise do mercado comercial.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 4d9dc0315a5c87ed67390e8edb23d44ee55f242b
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82732772"
---
# <a name="customer-dashboard-in-commercial-marketplace-analytics"></a>Dashboard de clientes na análise do mercado comercial

Este artigo fornece informações sobre o **dashboard do Cliente** no Partner Center. Este dashboard exibe informações sobre os seus clientes, incluindo tendências de crescimento, apresentadas num formato gráfico e descarregável.

Para aceder ao **dashboard do Cliente,** abra o dashboard **[Analyze](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** no Mercado Comercial.

>[!NOTE]
> Para definições detalhadas de terminologia analítica, consulte [frequentemente perguntas e terminologia para análise](./faq-terminology.md)de mercado comercial.

## <a name="customer-dashboard"></a>Dashboard de cliente

O **dashboard cliente** do menu **Analyze** exibe dados para clientes que adquiriram as suas ofertas. Pode ver representações gráficas dos seguintes itens:

- [Resumo do cliente](#customer-summary)
- [Cliente por geografia](#customer-by-geography)
- [Tendências do cliente](#customer-trends)
- [Clientes por encomendas e utilização](#customers-by-orders-and-usage)
- [Clientes por SKUs](#customers-by-skus)
- [Encomendas e utilização por tipo de cliente](#orders-and-usage-by-customer-type)
- [Tabela de detalhes do cliente](#customer-details-table)
- [Filtros de página do cliente](#customer-page-filters)

### <a name="customer-summary"></a>Resumo do cliente

A secção de resumo do Cliente apresenta uma contagem de todos os clientes, incluindo novos, existentes e agitados, durante a gama de datas selecionadas.

- O total de clientes é definido como a contagem de todos os clientes que adquiriram a sua oferta e têm pelo menos uma encomenda que não foi cancelada.
- A percentagem de crescimento dos clientes face ao mês anterior é indicada pelo indicador de crescimento e número e subida no indicador verde ou descendente a vermelho.
- As tendências de crescimento são representadas por gráficos de barras e mostrarão o valor por cada mês pairando sobre as colunas do gráfico.

Existem três tipos de **clientes:** novos, existentes e agitados.

- Um novo cliente adquiriu uma ou mais das suas ofertas pela primeira vez dentro do mês selecionado.
- Um cliente existente adquiriu uma ou mais das suas ofertas antes do mês selecionado.
- Um cliente agitado cancelou todas as ofertas anteriormente adquiridas.

### <a name="customer-by-geography"></a>Cliente por geografia

O gráfico **de geografia do Cliente** mostra as contagens de todos os clientes e clientes adquiridos durante a gama de datas selecionadas e são mapeados com base no País de Cliente. A luz para a cor escura no mapa representa o baixo a alto valor da contagem de clientes. Clique num disco na tabela para ampliar um país.

O mapa de calor exibe a contagem do cliente e % por país cliente. Pode mover o mapa para ver a localização exata e ampliar para um local específico. Este mapa tem uma grelha suplementar que lhe permite ver a % dos clientes por localização, bem como os clientes recém-adicionados a esse local.

### <a name="customer-trends"></a>Tendências do cliente

O gráfico de tendências do **Cliente** apresenta uma contagem de todos os clientes, incluindo novos, existentes e agitados, com uma tendência de crescimento mensal.

- O gráfico de linha representa as percentagens globais de crescimento do cliente.
- A coluna do mês representa a contagem de clientes empilhados por clientes novos, existentes e agitados.
- A contagem de clientes agitada é exibida na direção negativa do Eixo Y.
- Pode selecionar itens de lenda específicos para visualizações mais detalhadas. Por exemplo, selecione novos clientes da lenda para apenas apresentar novos clientes.
- Pode utilizar o slider na parte superior da tabela para rolar para a direita e para a esquerda no eixo x e concentrar-se em pontos de dados específicos para visualizar com mais detalhes.
- Pairar sobre uma coluna da tabela mostrará detalhes apenas para esse mês.

### <a name="customers-by-orders-and-usage"></a>Clientes por encomendas e utilização

Os **Clientes por encomenda/gráfico** de utilização tem três separadores, "encomendas", "utilização normalizada" e "uso bruto". O **percentil superior** do cliente é exibido ao longo do eixo x, conforme determinado pelo seu número de encomendas. O eixo y exibe a contagem de encomendas do cliente. O eixo y secundário (gráfico de linha) apresenta a percentagem acumulada do número total de encomendas. Pode exibir detalhes pairando sobre pontos ao longo da tabela de linhas.

Como exemplo, veja o gráfico abaixo para uso normalizado: O percentil 30 mais elevado dos clientes está a contribuir para 87% do uso normalizado cumulativamente. O 30º percentil de clientes está apenas a contribuir com 1,57 milhões de horas de utilização.

### <a name="customers-by-skus"></a>Clientes por SKUs

Os **gráficos de utilização dos Clientes por SKUs/utilização** são descritos abaixo.

1. O conselho de administração da Leader apresenta detalhes dos 50 principais clientes classificados por contagem de encomendas. Depois de selecionar um cliente, os detalhes do cliente são apresentados nas secções 2, 3 e 4 deste quadro de líderes.
2. Os detalhes do perfil do Cliente são apresentados neste espaço quando as editoras são registadas com uma função de proprietário. Se os editores estiverem com uma função contributiva, os detalhes nesta secção não estarão disponíveis.
3. O gráfico de donut Encomendas da SKUs exibe a desagregação das encomendas adquiridas para SKUs. Os 5 Melhores SKUs com a contagem de encomendas mais alta são apresentados, enquanto o resto das encomendas são agruparadas em 'descanse todos'.
4. O gráfico de donut Seats by SKUs exibe a desagregação dos assentos encomendados para As SKUs. Os 5 Melhores SKUs com assentos mais altos são apresentados, enquanto o resto das encomendas são agruparadas sob repouso.

### <a name="orders-and-usage-by-customer-type"></a>Encomendas e utilização por tipo de cliente

O gráfico de **Encomendas/utilização por tipo cliente** mostra o número de encomendas, utilização normalizada e horas de utilização brutas divididas entre novos clientes e clientes existentes; com base na seleção de encomendas, normalizadas e em uso bruto, respectivamente, no gráfico.

- Um novo cliente adquiriu uma ou mais das suas ofertas ou reportou uso pela primeira vez no mesmo mês civil (y-eixo).
- Um cliente existente já adquiriu uma oferta de si ou reportou o seu uso antes do mês civil relatado (no eixo y).

### <a name="customer-details-table"></a>Tabela de detalhes do cliente

A tabela de detalhes do **Cliente** apresenta uma lista numerada dos 1000 clientes classificados pela data em que adquiriram pela primeira vez uma das suas ofertas.

- As informações pessoais do cliente só estarão disponíveis se o cliente tiver fornecido o consentimento. Só pode ver esta informação se tiver iniciado sessão com um nível de permissões. Saiba mais sobre as funções e permissões do utilizador.
- Cada coluna na grelha é ordenada.
- Os dados podem ser extraídos para um ficheiro TSV se a contagem dos registos for inferior a 1000.
- Se o número de registos for superior a 1000, os dados exportados serão colocados assincronicamente numa página de downloads durante os próximos 30 dias.
- Os filtros podem ser aplicados na tabela para visualizar apenas os dados que lhe interessam. Os dados podem ser filtrados pelo nome da empresa, ID do cliente, ID de subscrição do mercado, Tipo de Licença Azure, Data Adquirida, Data Perdida, E-mail do Cliente, País cliente/Estado/Cidade/Zip, Idioma do Cliente, etc.

### <a name="customer-page-filters"></a>Filtros de página do cliente

Os filtros da **página Clientes** são aplicados ao nível da página do Cliente. Pode selecionar vários filtros para tornar o gráfico pelos critérios que escolhe ver e os dados que pretende ver na grelha /exportação de dados de encomendas detalhadas. Os filtros são aplicados nos dados extraídos para a gama de dados selecionado no canto superior direito da página de encomendas.

>[!NOTE]
> Definições detalhadas para cada um dos campos na grelha do Cliente, filtros de página e respetivas possíveis seleções estão localizadas em [perguntas frequentes e terminologia para análise](./faq-terminology.md)do Mercado Comercial.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral dos relatórios de análise disponíveis no mercado comercial do Partner Center, consulte [o Analytics para o mercado comercial no Partner Center](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumam a atividade do mercado para a sua oferta, consulte [Summary Dashboard na análise do mercado comercial.](./summary-dashboard.md)
- Para obter informações sobre as suas encomendas num formato gráfico e descarregável, consulte [o Painel de Encomendas na análise do mercado comercial](./orders-dashboard.md).
- Para máquina virtual (VM) oferece métricas de utilização e faturação medida, consulte [o Usage Dashboard na análise do mercado comercial](./usage-dashboard.md).
- Para obter uma lista dos seus pedidos de descarregamento ao longo dos últimos 30 dias, consulte [Downloads Dashboard na análise do mercado comercial](./downloads-dashboard.md).
- Para ver uma visão consolidada do feedback dos clientes para as ofertas no Azure Marketplace e appSource, consulte [ratings e avaliações de dashboard na análise do mercado comercial.](./ratings-reviews.md)
- Para perguntas frequentes sobre análise de mercado comercial e para um dicionário abrangente de termos de dados, consulte [frequentemente perguntas e terminologia para análise](./faq-terminology.md)de mercado comercial.
