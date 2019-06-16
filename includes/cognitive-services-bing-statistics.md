---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: scottwhi-msft
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2018
ms.author: scottwhi
ms.custom: include file
ms.openlocfilehash: 6016b13fe7d3e1f3b673bd2446d2f68b04878cd6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124741"
---
Estatísticas do Bing fornece análises para APIs de pesquisa do Bing. Análise inclui o volume de chamadas, cadeias de consulta superior, distribuição geográfica e muito mais. Para ativar as estatísticas do Bing na pesquisa do Bing subscrição paga, navegue até à sua [dashboard do Azure](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7), selecione a subscrição paga e clique em Ativar estatísticas do Bing. Ativar as estatísticas do Bing aumenta um pouco a sua taxa de subscrição (consulte [preços](https://aka.ms/bingstatisticspricing)).

> [!NOTE]
> Estatísticas do Bing está disponível com as assinaturas pagas apenas - não está disponível com subscrições de avaliação gratuitas.

> [!NOTE]
> Não pode utilizar qualquer disponíveis através do dashboard de estatísticas do Bing os dados para criar aplicativos para distribuição a terceiros.

Bing dados de análise de atualizações a cada 24 horas e mantém a cópia de segurança para o valor de 13 mês do histórico.

## <a name="accessing-your-analytics"></a>Aceder à sua análise

Para aceder ao seu dashboard de análise, aceda a https://bingapistatistics.com. Certifique-se de que tem sessão iniciada com a mesma conta de Microsoft (MSA) que utilizou para obter a subscrição paga.

## <a name="filtering-the-data"></a>Filtrar os dados

Por predefinição, as tabelas e gráficos refletem todos os dados de métricas que tem acesso. Pode filtrar os dados mostrados nas tabelas e gráficos ao selecionar os recursos, a mercados, a pontos de extremidade e período de relatório que está interessado. As tabelas e gráficos alterar para refletir os filtros que se aplicam. A seguir descreve os filtros que podem ser alteradas.

- **ID de recurso**: O ID de recurso exclusivo que identifica a sua subscrição do Azure. A lista contém vários IDs se subscrever mais do que uma camada de API de pesquisa Bing. Por predefinição, são selecionados todos os recursos.  
  
- **Mercados**: Os mercados de onde vêm os resultados. Por exemplo, en-us (inglês, Estados Unidos). Por predefinição, são selecionados todos os mercados. Tenha em conta que o mercado en-WW do mercado que o Bing utiliza se a chamada não especificar um mercado e o Bing é não é possível determinar o mercado do utilizador.  
  
- **Pontos de extremidade**: Os pontos finais de API de pesquisa Bing. A lista contém todos os pontos finais para os quais tem uma subscrição paga. Por predefinição, são selecionados todos os pontos finais.  

- **Período de tempo**: O período de relatório. Pode especificar:
  - Todos os&mdash;inclui até ao valor de 13 meses de dados  
  - Últimas 24 horas&mdash;inclui a análise das últimas 24 horas  
  - Última semana&mdash;inclui a análise dos sete dias anteriores  
  - Último mês&mdash;inclui a análise de 30 dias anteriores  
  - Um intervalo de datas personalizadas&mdash;inclui a análise do período de tempo especificado, se disponível  

  > [!NOTE]  
  > Pode demorar até 24 horas para métricas trazer à tona no dashboard. O dashboard mostra a data e hora que da última atualização de dados.  

  > [!NOTE]  
  > As métricas estão disponíveis a partir da hora de que ativar o suplemento de estatísticas do Bing.

## <a name="charts-and-graphs"></a>Tabelas e gráficos

O dashboard mostra gráficos de métricas disponíveis para o ponto de extremidade selecionado. Nem todas as métricas estão disponíveis para todos os pontos finais. As tabelas e gráficos para cada ponto de extremidade são estáticos (não pode selecionar as tabelas e gráficos para apresentar). O dashboard mostra apenas de gráficos para os quais há dados.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Seguem-se as métricas de possíveis. Cada restrições de ponto final de métrica de notas.

- **Volume de chamadas**: Mostra o número de chamadas efetuadas durante o período de relatório. Se for o período de relatório para um dia, o gráfico mostra o número de chamadas efetuadas por hora. Caso contrário, o gráfico mostra o número de chamadas efetuadas por dia do período de relatório.  
  
  > [!NOTE]
  > O volume de chamadas pode ser diferente do faturas relatórios, que geralmente inclui apenas as chamadas com êxito.

- **Principais consultas**: Mostra as principais consultas e o número de ocorrências de cada consulta durante o período de relatório. Pode configurar o número de consultas mostrado. Por exemplo, pode mostrar as consultas principais 25, 50 ou 75. Principais consultas não está disponível para os seguintes pontos finais:  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - / vídeos/detalhes
  - /news
  - / notícias/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > Alguns termos de consulta podem ser suprimidos para remover as informações confidenciais, tais como mensagens de correio eletrónico, números de telefone, CPF, etc.

- **Distribuição geográfica**: Os mercados de onde vêm os resultados. Por exemplo, en-us (inglês, Estados Unidos). O Bing utiliza o `mkt` parâmetro para determinar o mercado de consulta, se for especificado. Caso contrário, o Bing utiliza sinais, como o endereço IP do chamador para determinar o mercado.

- **Distribuição de código de resposta**: Os códigos de estado HTTP de todas as chamadas durante o período de relatório.

- **Chamar a distribuição de origem**: Os tipos de browsers utilizados pelos utilizadores. Por exemplo, Microsoft Edge, Chrome, Safari e FireFox. As chamadas feitas a partir de fora de um navegador, como bots, o Postman ou com o curl a partir de uma aplicação de consola, são agrupadas em bibliotecas. A origem é determinada através do valor de cabeçalho do agente do usuário da solicitação. Se o pedido não inclui o cabeçalho do agente do usuário, o Bing tenta derivar a origem de outros sinais.  

- **Distribuição de pesquisa segura**: A distribuição dos valores de pesquisa segura. Por exemplo, desativar, moderado, ou strict. O `safeSearch` consulta parâmetro contém o valor, se for especificado. Caso contrário, o Bing padrão é o valor a moderadas.  

- **Distribuição de pedidos de respostas**: A API de pesquisa Web responde a pedido no `responseFilter` parâmetro de consulta.  

- **Respostas devolvidas distribuição**: As respostas de API de pesquisa Web devolvido na resposta.

- **Distribuição do servidor de resposta**: O servidor de aplicações que serviu os pedidos de API. Os valores possíveis são Bing.com (para o tráfego está a ser atendido a partir de dispositivos de desktop e laptop) e Bing.com-mobile (para o tráfego está a ser atendido a partir de dispositivos móveis). O servidor é determinado através do valor de cabeçalho do agente do usuário da solicitação. Se o pedido não inclui o cabeçalho do agente do usuário, o Bing tenta derivar o servidor de outros sinais.

O código a seguir mostra o analytics que estão disponível para cada ponto de extremidade.

![Distribuição por matriz de suporte de ponto final](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)
