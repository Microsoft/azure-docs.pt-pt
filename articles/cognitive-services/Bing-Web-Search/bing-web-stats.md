---
title: Adicionar análise à API de Pesquisa web de Bing
titleSuffix: Azure Cognitive Services
description: Bing Statistics fornece análise à API de Pesquisa de Imagem de Bing. Os analytics incluem volume de chamadas, cadeias de consulta de topo, distribuição geográfica, e muito mais.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 02fea02cca5950ef8467377a866e9a765af9e2e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349559"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Adicione análise às APIs de Pesquisa de Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Bing Statistics fornece análise para as APIs de pesquisa de Bing. Estas análises incluem volume de chamadas, cadeias de consulta superior, distribuição geográfica, e muito mais. Pode ativar as Estatísticas Bing no [portal Azure](https://ms.portal.azure.com) navegando para o seu recurso Azure e clicando em **Enable Bing Statistics**.

> [!IMPORTANT]
> * A Bing Statistics não está disponível com recursos no `F0` nível de preços gratuitos.
> * Não pode utilizar quaisquer dados disponíveis através do painel Bing Statistics para criar aplicações de distribuição a terceiros.
> * Habilitar estatísticas de Bing aumenta ligeiramente a sua taxa de subscrição. Consulte [os preços](https://aka.ms/bingstatisticspricing) para mais detalhes.


A imagem a seguir mostra a análise disponível para cada ponto final da API de pesquisa de Bing.

![Distribuição por matriz de suporte de ponto final](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Aceda à sua análise

Bing atualiza dados de análise a cada 24 horas e mantém até 13 meses de história que pode aceder a partir do painel de [análise](https://bingapistatistics.com). Certifique-se de que está assinado usando a mesma conta Microsoft (MSA) que usou para se inscrever para bing Statistics.

> [!NOTE]  
> * Pode levar até 24 horas para que as métricas suram no painel de instrumentos. O painel mostra a data e a hora em que os dados foram atualizados pela última vez.  
> * As métricas estão disponíveis a partir do momento em que ativar o Add-in de Estatísticas Bing.

## <a name="filter-the-data"></a>Filtrar os dados

Por predefinição, os gráficos e gráficos exibem todas as métricas e dados a que tem acesso. Pode filtrar os dados apresentados nos gráficos e gráficos selecionando os recursos, mercados, pontos finais e período de reporte em que está interessado. Pode alterar os seguintes filtros:

- **ID de recurso**: O ID de recurso único que identifica a sua subscrição Azure. A lista contém vários IDs se subscrever mais de um nível de API de pesquisa de Bing. Por predefinição, todos os recursos são selecionados.  
  
- **Mercados**: Os mercados de onde vêm os resultados. Por exemplo, en-us (inglês, Estados Unidos). Por padrão, todos os mercados são selecionados. O `en-WW` mercado é o mercado que a Bing utiliza se a chamada não especificar um mercado e a Bing não conseguir determinar o mercado do utilizador.  
  
- **Pontos finais**: Os pontos finais da API de Pesquisa Bing. A lista contém todos os pontos finais para os quais tem uma subscrição paga. Por predefinição, todos os pontos finais são selecionados.  

- **Prazo**: O período de reporte. Pode especificar:
  - **Tudo**: Inclui dados até 13 meses  
  - **Últimas 24 horas**: Inclui análises das últimas 24 horas  
  - **Semana passada**: Inclui análises dos 7 dias anteriores  
  - **Mês passado**: Inclui análises dos 30 dias anteriores  
  - **Uma gama de datas personalizadas**: Inclui análises a partir da gama de datas especificadas, se disponível  

## <a name="charts-and-graphs"></a>Gráficos e gráficos

O painel de instrumentos mostra gráficos e gráficos das métricas disponíveis para o ponto final selecionado. Nem todas as métricas estão disponíveis para todos os pontos finais. Os gráficos e gráficos de cada ponto final são estáticos (não pode selecionar os gráficos e gráficos a exibir). O painel de instrumentos mostra apenas gráficos e gráficos para os quais há dados.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Seguem-se possíveis métricas e restrições de ponto final.

- **Volume de chamadas**: Mostra o número de chamadas efetuadas durante o período de reporte. Se o período de reporte for por um dia, o gráfico mostra o número de chamadas feitas por hora. Caso contrário, o gráfico mostra o número de chamadas efetuadas por dia do período de reporte.  
  
  > [!NOTE]
  > O volume de chamadas pode diferir dos relatórios de faturação, que geralmente inclui apenas chamadas bem sucedidas.

- **Principais consultas**: Mostra as principais consultas e o número de ocorrências de cada consulta durante o período de reporte. Pode configurar o número de consultas mostradas. Por exemplo, pode mostrar o top 25, 50 ou 75 consultas. As principais consultas não estão disponíveis para os seguintes pontos finais:  

  - /imagens/tendência
  - /imagens/detalhes
  - /imagens/visualização
  - /vídeos/tendência
  - /vídeos/detalhes
  - /notícias
  - /news/trendingtopics
  - /sugestões  
  
  > [!NOTE]  
  > Alguns termos de consulta podem ser suprimidos para remover informações confidenciais, tais como e-mails, números de telefone, SSN, etc.

- **Distribuição Geográfica**: Os mercados de origem dos resultados da pesquisa. Por exemplo, `en-us` (inglês, Estados Unidos). Bing utiliza o `mkt` parâmetro de consulta para determinar o mercado, se especificado. Caso contrário, bing utiliza sinais como o endereço IP do chamador para determinar o mercado.

- **Distribuição do Código de Resposta**: Os códigos de estado HTTP de todas as chamadas durante o período de reporte.

- **Call Origin Distribution**: Os tipos de navegadores utilizados pelos utilizadores. Por exemplo, Microsoft Edge, Chrome, Safari e FireFox. As chamadas feitas de fora de um navegador (como bots, Carteiro ou usando caracóis de uma aplicação de consola) estão agrupadas nas Bibliotecas. A origem é determinada utilizando o valor do cabeçalho User-Agent do pedido. Se o pedido não incluir o cabeçalho User-Agent, Bing tenta obter a origem de outros sinais.  

- **Distribuição de Pesquisa Segura**: Distribuição de valores de pesquisa seguros. Por exemplo, fora, moderado ou rigoroso. O `safeSearch` parâmetro de consulta contém o valor, se especificado. Caso contrário, Bing predefini o valor a moderado.  

- **Respostas Distribuição Solicitada**: A API de Pesquisa Web responde ao que solicitou no `responseFilter` parâmetro de consulta.  

- **Respostas Distribuição Devolvida**: As respostas que a API de Pesquisa Web devolveu na resposta.

- **Distribuição do Servidor de Resposta**: O servidor de aplicações que serviu os seus pedidos de API. Os valores possíveis são Bing.com (para o tráfego servido a partir de dispositivos de secretária e portátil) e Bing.com-mobile (para o tráfego servido a partir de dispositivos móveis). O servidor é determinado usando o valor do cabeçalho User-Agent do pedido. Se o pedido não incluir o cabeçalho User-Agent, Bing tenta obter o servidor de outros sinais.

## <a name="next-steps"></a>Passos seguintes

* [Quais são as APIs de Pesquisa de Bing?](bing-api-comparison.md)
* [Requisitos de utilização e apresentação da API de Pesquisa do Bing](use-display-requirements.md)