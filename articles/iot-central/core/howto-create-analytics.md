---
title: Analisar os dados do dispositivo em seu aplicativo de IoT Central do Azure | Microsoft Docs
description: Este artigo descreve como analisar os dados do dispositivo no aplicativo IoT Central do Azure usando consultas e visualizações.
author: lmasieri
ms.author: lmasieri
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a467e0e6e8967cf963ad099f83de6718330aa43f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827982"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Como usar a análise para analisar os dados do dispositivo

*Este artigo aplica-se a operadores, construtores e administradores.*

O Azure IoT Central fornece recursos de análise avançados para fazer sentido de grandes quantidades de dados de seus dispositivos. Para começar, visite **análise** no painel esquerdo.

## <a name="querying-your-data"></a>Consultando seus dados

Você precisará escolher um **conjunto de dispositivos**, adicionar um **filtro** (opcional) e selecionar um **período de tempo** para começar. Depois de terminar, selecione **Mostrar resultados** para começar a visualizar seus dados.

* **Conjuntos de dispositivos:** Um [conjunto de dispositivos](howto-use-device-sets.md) é um grupo definido pelo usuário de seus dispositivos. Por exemplo, todos os refrigeradores em Oakland ou todas as turbinas de vento da Rev 2,0.

* **Filtros:** Opcionalmente, você pode adicionar filtros à sua pesquisa para aprimorar seus dados. Você pode adicionar até 10 filtros de cada vez. Por exemplo, em todos os refrigeradores no Oakland, encontre aqueles que tiveram a temperatura entre 60 graus.
* **Período de tempo:** Por padrão, recuperaremos dados dos últimos 10 minutos. Você pode alterar esse valor para um dos intervalos de tempo predefinidos ou selecionar um período de tempo personalizado.

  ![Consulta do Analytics](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Visualizando seus dados

Depois de consultar seus dados, você poderá começar a Visualizar isso. Você pode mostrar/ocultar medidas, alterar a maneira como os dados são agregados e dividir ainda mais os dados por propriedades de dispositivo diferentes.  

* **Dividir por:** A divisão de dados por propriedades de dispositivo permite detalhar ainda mais os dados. Por exemplo, você pode dividir os resultados por ID do dispositivo ou local.

* **Medidas:** Você pode optar por mostrar/ocultar até 10 itens de telemetria diferentes que estão sendo relatados pelos dispositivos por vez. As medidas são coisas como temperatura e umidade.

* **Agregação:** Por padrão, agregamos dados por sua média, mas você pode optar por alterar a agregação de dados para outra coisa que atenda às suas necessidades.

   ![Visualização de análise dividida por](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interagindo com seus dados

Você tem várias maneiras de alterar os resultados da consulta para atender às suas necessidades de visualização. Você pode alternar entre uma exibição de gráfico e uma exibição de grade, ampliar e reduzir, atualizar o conjunto de dados e alterar o modo como as linhas são mostradas.

* **Mostrar grade:** Os resultados estão disponíveis em um formato de tabela, permitindo que você exiba o valor específico para cada ponto de dados. Essa exibição também atende aos padrões de acessibilidade.
* **Mostrar gráfico:** Os resultados são exibidos em um formato de linha para ajudá-lo a identificar tendências e anomalias ascendentes ou descendentes.

  ![Mostrando o modo de exibição de grade para sua análise](media/howto-create-analytics/analytics-showgrid.png)

O zoom permite que você se baseie em seus dados. Se você encontrar um período de tempo em que deseja se concentrar em seu conjunto de resultados, use o cursor para obter a área em que você gostaria de ampliar e use os controles disponíveis para executar uma das seguintes ações:

* **Ampliar:** Depois de selecionar um período de tempo, o zoom é habilitado e permite que você amplie seus dados.
* **Reduzir:** Esse controle permite que você reduza um nível do último zoom. Por exemplo, se você ampliar seus dados três vezes, reduzir o levará de volta uma etapa por vez.
* **Redefinição de zoom:** Depois de executar vários níveis de zoom, você pode usar o controle de redefinição de zoom para retornar ao conjunto de resultados original.

  ![Execute o zoom em seus dados](media/howto-create-analytics/analytics-zoom.png)

Você pode alterar o estilo da linha para atender às suas necessidades. Você tem quatro opções:

* **Linha:** Uma linha simples entre cada um dos pontos de dados.
* **Smooth:** Uma linha curva entre cada ponto.
* **Etapa:** A linha entre cada ponto no gráfico é uma etapa.
* **Dispersão:** Todos os pontos são plotados no gráfico sem linhas que os conectam.

  ![Tipos de linha diferentes disponíveis no Analytics](media/howto-create-analytics/analytics-linetypes.png)

Por fim, você pode organizar seus dados no eixo Y escolhendo um dos três modos:

* **Empilhado:** Um grafo para cada medição é empilhado e cada um dos grafos tem seu próprio eixo Y. Os gráficos empilhados são úteis quando você tem várias medições selecionadas e deseja ter uma exibição distinta dessas medidas.
* Não **empilhado:** Um grafo para cada medida é plotado em relação a um eixo Y, mas os valores para o eixo Y são alterados com base na medida realçada. Os gráficos não empilhados são úteis quando você deseja sobrepor várias medidas e deseja ver padrões entre essas medidas para o mesmo intervalo de tempo.
* **Eixo Y compartilhado:** Todos os grafos compartilham o mesmo eixo Y e os valores para o eixo não são alterados. Os gráficos do eixo Y compartilhado são úteis quando você deseja examinar uma única medida ao dividir os dados com divisão por.

  ![Organizar dados entre eixos y com modos de visualização diferentes](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como criar uma análise personalizada para seu aplicativo IoT Central do Azure, aqui a próxima etapa sugerida é:

> [!div class="nextstepaction"]
> [Preparar e conectar um aplicativo node. js](howto-connect-nodejs.md)