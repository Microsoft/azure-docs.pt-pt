---
title: Adicione análises à API de pesquisa web bing
titleSuffix: Azure Cognitive Services
description: Bing Statistics fornece análise sonorizadora para a API de Pesquisa de Imagem Bing. A análise inclui volume de chamadas, cordas de consulta superior, distribuição geográfica, e muito mais.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68882787"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Adicione análises às APIs de Pesquisa de Bing

Bing Statistics fornece análise para as APIs de pesquisa bing. Estas análises incluem volume de chamadas, cordas de consulta de topo, distribuição geográfica, e muito mais. Pode ativar as estatísticas bing no [portal Azure](https://ms.portal.azure.com) navegando para o seu recurso Azure e clicando em **Estatísticas Enable Bing**.

> [!IMPORTANT]
> * A Bing Statistics não está disponível com subscrições de teste gratuitas, nem recursos no nível de preços gratuitos. `F0`
> * Não poderá utilizar quaisquer dados disponíveis através do painel de estatísticas bing para criar aplicações para distribuição a terceiros.
> * Ativar a Bing Statistics aumenta ligeiramente a sua taxa de subscrição. Consulte [os preços](https://aka.ms/bingstatisticspricing) para mais detalhes.


A imagem que se segue mostra a análise disponível para cada ponto final da API de Pesquisa de Bing.

![Distribuição por matriz de suporte de ponto final](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Aceda à sua análise

Bing atualiza dados de análise a cada 24 horas e mantém até 13 meses de história a que pode aceder a partir do painel de [análise](https://bingapistatistics.com). Certifique-se de que está inscrito na mesma conta da Microsoft (MSA) que usou para se inscrever para as Estatísticas Bing.

> [!NOTE]  
> * Pode levar até 24 horas para que as métricas apareçam no tablier. O painel de instrumentos mostra a data e a hora em que os dados foram atualizados pela última vez.  
> * As métricas estão disponíveis a partir do momento em que ativa o Add-in de Estatísticas bing.

## <a name="filter-the-data"></a>Filtrar os dados

Por padrão, os gráficos e gráficos exibem todas as métricas e dados a que tem acesso. Pode filtrar os dados mostrados nos gráficos e gráficos selecionando os recursos, mercados, pontos finais e período de reporte em que está interessado. Pode alterar os seguintes filtros:

- **ID de recursos**: O ID de recurso único que identifica a sua subscrição Azure. A lista contém múltiplas Identificações se subscrever mais de um nível de API bing search. Por padrão, todos os recursos são selecionados.  
  
- **Mercados**: Os mercados de onde vêm os resultados. Por exemplo, en-us (Inglês, Estados Unidos). Por padrão, todos os mercados são selecionados. O `en-WW` mercado é o mercado que a Bing utiliza se a chamada não especificar um mercado e a Bing não conseguir determinar o mercado do utilizador.  
  
- **Pontos finais**: Os pontos finais da API de pesquisa bing. A lista contém todos os pontos finais para os quais tem uma subscrição paga. Por predefinição, todos os pontos finais são selecionados.  

- **Prazo**: O período de reporte. Pode especificar:
  - **Tudo**: Inclui até 13 meses de dados  
  - **Últimas 24 horas**: Inclui análises das últimas 24 horas  
  - **Semana passada**: Inclui análisedos dos sete dias anteriores  
  - **Mês passado**: Inclui análisedos dos 30 dias anteriores  
  - Uma gama de **datas personalizada:** Inclui análises a partir da gama de datas especificada, se disponível  

## <a name="charts-and-graphs"></a>Gráficos e gráficos

O painel de instrumentos mostra gráficos e gráficos das métricas disponíveis para o ponto final selecionado. Nem todas as métricas estão disponíveis para todos os pontos finais. Os gráficos e gráficos de cada ponto final são estáticos (pode não selecionar os gráficos e gráficos para exibir). O painel de instrumentos mostra apenas gráficos e gráficos para os quais há dados.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Seguem-se possíveis métricas e restrições de pontofinal.

- **Volume de chamada**: Mostra o número de chamadas efetuadas durante o período de reporte. Se o período de reporte for por um dia, o gráfico mostra o número de chamadas feitas por hora. Caso contrário, o gráfico mostra o número de chamadas efetuadas por dia do período de reporte.  
  
  > [!NOTE]
  > O volume de chamadas pode diferir dos relatórios de faturação, que geralmente incluem apenas chamadas bem sucedidas.

- **Principais consultas**: Mostra as principais consultas e o número de ocorrências de cada consulta durante o período de reporte. Pode configurar o número de consultas mostradas. Por exemplo, pode mostrar o top 25, 50 ou 75 consultas. As principais consultas não estão disponíveis para os seguintes pontos finais:  

  - /imagens/tendência
  - /imagens/detalhes
  - /imagens/pesquisa visual
  - /vídeos/tendências
  - /vídeos/detalhes
  - /notícias
  - /notícias/tópicos de tendência
  - /sugestões  
  
  > [!NOTE]  
  > Alguns termos de consulta podem ser suprimidos para remover informações confidenciais, tais como e-mails, números de telefone, SSN, etc.

- **Distribuição Geográfica**: Os mercados onde os resultados da pesquisa são originados. Por exemplo, `en-us` (Inglês, Estados Unidos). Bing usa `mkt` o parâmetro de consulta para determinar o mercado, se especificado. Caso contrário, bing usa sinais como o endereço IP do chamador para determinar o mercado.

- **Distribuição do Código de Resposta**: Os códigos de estado HTTP de todas as chamadas durante o período de reporte.

- **Chamada Distribuição de Origem**: Os tipos de navegadores utilizados pelos utilizadores. Por exemplo, Microsoft Edge, Chrome, Safari e FireFox. As chamadas feitas de fora de um navegador (como bots, Carteiro ou uso de caracóis de uma aplicação de consola) são agrunadas nas Bibliotecas. A origem é determinada utilizando o valor do cabeçalho do utilizador-agente do pedido. Se o pedido não incluir o cabeçalho user-agent, Bing tenta obter a origem de outros sinais.  

- **Distribuição de pesquisa segura**: A distribuição de valores de pesquisa seguros. Por exemplo, fora, moderado ou rigoroso. O `safeSearch` parâmetro de consulta contém o valor, se especificado. Caso contrário, bing predefini o valor para moderar.  

- **Respostas Distribuição Solicitada**: A API de pesquisa `responseFilter` web responde que solicitou no parâmetro de consulta.  

- **Respostas Distribuição Devolvida**: As respostas que a Web Search API devolveu na resposta.

- **Distribuição do Servidor de Resposta**: O servidor de aplicações que serviu os seus pedidos de API. Os valores possíveis são Bing.com (para o tráfego servido a partir de dispositivos de secretária e portáteis) e Bing.com-mobile (para tráfego servido a partir de dispositivos móveis). O servidor é determinado utilizando o valor do cabeçalho do utilizador-agente do pedido. Se o pedido não incluir o cabeçalho user-agent, Bing tenta derivar o servidor de outros sinais.

## <a name="next-steps"></a>Passos seguintes

* [O que são as APIs de Pesquisa bing?](bing-api-comparison.md)
* [Requisitos de utilização e apresentação da API de Pesquisa do Bing](use-display-requirements.md)
