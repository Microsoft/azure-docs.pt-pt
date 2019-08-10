---
title: Adicionar análise ao API de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: As estatísticas do Bing fornecem análises para o API de Pesquisa de Imagem do Bing. A análise inclui o volume de chamadas, as principais cadeias de caracteres de consulta, a distribuição geográfica e muito mais.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 5d1b0b19523eb37aa83aa59b24114be9f76ffa55
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882787"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Adicionar análise ao APIs de Pesquisa do Bing

As estatísticas do Bing fornecem análises para o APIs de Pesquisa do Bing. Essas análises incluem o volume de chamada, as principais cadeias de caracteres de consulta, a distribuição geográfica e muito mais. Você pode habilitar as estatísticas do Bing no [portal do Azure](https://ms.portal.azure.com) navegando até o recurso do Azure e clicando em **habilitar estatísticas do Bing**.

> [!IMPORTANT]
> * As estatísticas do Bing não estão disponíveis com assinaturas de avaliação gratuitas ou recursos no tipo `F0` de preço gratuito.
> * Você não pode usar nenhum dado disponível por meio do painel de estatísticas do Bing para criar aplicativos para distribuição a terceiros.
> * A habilitação das estatísticas do Bing aumenta um pouco sua taxa de assinatura. Consulte os [preços](https://aka.ms/bingstatisticspricing) para obter detalhes.


A imagem a seguir mostra a análise disponível para cada ponto de extremidade de API Pesquisa do Bing.

![Distribuição por matriz de suporte de ponto de extremidade](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Acessar sua análise

O Bing atualiza dados de análise a cada 24 horas e mantém um histórico de até 13 meses que você pode acessar por meio do [painel de análise](https://bingapistatistics.com). Verifique se você está conectado usando o mesmo conta Microsoft (MSA) usado para se inscrever nas estatísticas do Bing.

> [!NOTE]  
> * Pode levar até 24 horas para que as métricas apareçam no painel. O painel mostra a data e a hora em que os dados foram atualizados pela última vez.  
> * As métricas estão disponíveis no momento em que você habilita o suplemento de estatísticas do Bing.

## <a name="filter-the-data"></a>Filtrar os dados

Por padrão, os gráficos exibem todas as métricas e os dados aos quais você tem acesso. Você pode filtrar os dados mostrados nos gráficos, selecionando os recursos, os mercados, os pontos de extremidade e o período de relatório em que está interessado. Você pode alterar os seguintes filtros:

- **ID do recurso**: A ID de recurso exclusiva que identifica sua assinatura do Azure. A lista conterá várias IDs se você assinar mais de uma Pesquisa do Bing camada de API. Por padrão, todos os recursos são selecionados.  
  
- **Mercados**: Os mercados dos quais vêm os resultados. Por exemplo, en-US (Inglês, Estados Unidos). Por padrão, todos os mercados são selecionados. O `en-WW` mercado é o mercado que o Bing usa se a chamada não especificar um mercado e o Bing não puder determinar o mercado do usuário.  
  
- **Pontos de extremidade**: Os pontos de extremidade da API Pesquisa do Bing. A lista contém todos os pontos de extremidade para os quais você tem uma assinatura paga. Por padrão, todos os pontos de extremidade são selecionados.  

- **Período de tempo**: O período de relatório. Você pode especificar:
  - **Todos**: Inclui um valor de dados de até 13 meses  
  - **Últimas 24 horas**: Inclui análise das últimas 24 horas  
  - **Última semana**: Inclui análise dos sete dias anteriores  
  - **Mês passado**: Inclui análise dos últimos 30 dias  
  - **Um intervalo de datas personalizado**: Inclui a análise do intervalo de datas especificado, se disponível  

## <a name="charts-and-graphs"></a>Gráficos

O painel mostra gráficos das métricas disponíveis para o ponto de extremidade selecionado. Nem todas as métricas estão disponíveis para todos os pontos de extremidade. Os gráficos para cada ponto de extremidade são estáticos (você não pode selecionar os gráficos a serem exibidos). O painel mostra apenas gráficos e grafos para os quais há dados.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

A seguir estão as possíveis métricas e restrições de ponto de extremidade.

- **Volume de chamada**: Mostra o número de chamadas feitas durante o período de relatório. Se o período de relatório for de um dia, o gráfico mostrará o número de chamadas feitas por hora. Caso contrário, o gráfico mostrará o número de chamadas feitas por dia do período de relatório.  
  
  > [!NOTE]
  > O volume de chamada pode ser diferente dos relatórios de cobrança, o que geralmente inclui apenas chamadas bem-sucedidas.

- **Principais consultas**: Mostra as principais consultas e o número de ocorrências de cada consulta durante o período de relatório. Você pode configurar o número de consultas mostradas. Por exemplo, você pode mostrar as principais 25, 50 ou 75 consultas. As principais consultas não estão disponíveis para os seguintes pontos de extremidade:  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > Alguns termos de consulta podem ser suprimidos para remover informações confidenciais, como emails, números de telefone, SSN etc.

- **Distribuição geográfica**: Os mercados nos quais os resultados da pesquisa se originam. Por exemplo, `en-us` (Inglês, Estados Unidos). O Bing usa `mkt` o parâmetro de consulta para determinar o mercado, se especificado. Caso contrário, o Bing usa sinais como o endereço IP do chamador para determinar o mercado.

- **Distribuição de código de resposta**: Os códigos de status HTTP de todas as chamadas durante o período de relatório.

- **Distribuição de origem da chamada**: Os tipos de navegadores usados pelos usuários. Por exemplo, Microsoft Edge, Chrome, Safari e FireFox. As chamadas feitas de fora de um navegador (como bots, postmaster ou uso de ondulação de um aplicativo de console) são agrupadas em bibliotecas. A origem é determinada usando o valor do cabeçalho do agente do usuário da solicitação. Se a solicitação não incluir o cabeçalho User-Agent, o Bing tentará derivar a origem de outros sinais.  

- **Distribuição de pesquisa segura**: A distribuição de valores de pesquisa segura. Por exemplo, desativado, moderado ou estrito. O `safeSearch` parâmetro de consulta contém o valor, se especificado. Caso contrário, o Bing padroniza o valor como moderado.  

- **Responde à distribuição solicitada**: O pesquisa na Web API responde que você solicitou no `responseFilter` parâmetro de consulta.  

- **Respostas retornadas distribuição**: As respostas que Pesquisa na Web API retornou na resposta.

- **Distribuição do servidor de resposta**: O servidor de aplicativos que atendeu suas solicitações de API. Os valores possíveis são Bing.com (para o tráfego servido por dispositivos de desktop e laptop) e Bing.com-Mobile (para o tráfego servido por meio de dispositivos móveis). O servidor é determinado usando o valor do cabeçalho User-Agent da solicitação. Se a solicitação não incluir o cabeçalho User-Agent, o Bing tentará derivar o servidor de outros sinais.

## <a name="next-steps"></a>Passos seguintes

* [Quais são os APIs de Pesquisa do Bing?](bing-api-comparison.md)
* [Requisitos de exibição e uso de API Pesquisa do Bing](use-display-requirements.md)
