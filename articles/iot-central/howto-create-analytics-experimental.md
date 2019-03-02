---
title: Analise os seus dados de dispositivo na sua aplicação do Azure IoT Central | Documentos da Microsoft
description: Analise os seus dados de dispositivo na sua aplicação do Azure IoT Central.
author: lmasieri
ms.author: lmasieri
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 83225af7cb2cbdff4a264693e30b2a813f6ec9c7
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214934"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Como utilizar a análise para analisar os dados de dispositivo

*Este artigo aplica-se a operadores, construtores e administradores.*

O Azure IoT Central fornece capacidades de análises avançadas que fazem sentido de grandes quantidades de dados dos seus dispositivos. Para começar, visite **Analytics** no menu de navegação esquerdo.

## <a name="querying-your-data"></a>Consultar dados

Terá de escolher uma **dispositivos conjunto**, adicione um **filtro** (opcional) e selecione um **período de tempo** para começar a utilizar. Quando tiver terminado, clique em *Mostrar resultados* para começar a visualizar seus dados.

* **Conjuntos de dispositivo:** R [dispositivos conjunto](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) é um grupo definido pelo utilizador dos seus dispositivos. Por exemplo, todos os refrigeradores no Oakland ou todos passe 2.0 turbines de vento.

* **Filtros:** Opcionalmente, pode adicionar filtros a sua pesquisa para alcançar seus dados. Pode adicionar até 10 filtros ao mesmo tempo. Por exemplo, dentro de todos os refrigeradores no Oakland, encontre as que tenha tido a temperatura passar de 60 graus.
* **Período de tempo:** Por predefinição, irá obter dados de após 10 minutos. Pode alterar este valor para um dos intervalos de tempo predefinido ou selecione um período de tempo personalizado.

 ![Consulta do Analytics](media/howto-create-analytics-experimental/analytics-query.png)

## <a name="visualizing-your-data"></a>Visualizar seus dados

Depois de consultados a seus dados, poderá começar a visualizá-lo. Pode mostrar/ocultar medidas, alterar a forma como os dados são agregados e mais dividir os dados por propriedades de dispositivo diferentes.  

* **Divida por:** A divisão de dados por propriedades do dispositivo permite-lhe desagregar ainda mais para baixo sobre os seus dados. Por exemplo, pode dividir os resultados por ID de dispositivo ou localização.

* **Medidas:** Pode optar por mostrar/ocultar até 10 itens de telemetria diferente está a ser comunicados pelos seus dispositivos ao mesmo tempo. Medidas são coisas como temperatura e humidade.

* **Agregação de:** Por predefinição, agregar dados pelo respetiva média, mas pode optar por alterar a agregação de dados para outro local para se ajustar às suas necessidades.

   ![Visualização de análise dividir por](media/howto-create-analytics-experimental/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interagir com os seus dados

Existem várias formas para alterar os resultados de consulta para atender às suas necessidades de visualização. Pode se alternam entre uma vista de gráfico e uma exibição de grade, ampliar e reduzir, atualize o seu conjunto de dados e alterar a forma como as linhas são mostradas.

* **Mostra grelha:** Os resultados estão disponíveis num formato de tabela, permitindo-lhe ver o valor específico para cada ponto de dados. Esta vista também cumpre os padrões de acessibilidade.
* **Mostra gráfico:** Os resultados são exibidos num formato de linha para o ajudar a identificar para cima ou para baixo tendências e anomalias.

 ![Mostrando a exibição de grade para análise](media/howto-create-analytics-experimental/analytics-showgrid.png)

Zoom permite-lhe em casa nos seus dados. Se encontrar um período de tempo, gostaria de se concentrar em dentro do seu conjunto de resultados, utilize o cursor para capturar a área que pretende ampliar e usar os controles disponíveis para executar uma das seguintes ações:

* **Zoom:** Depois de selecionar um período de tempo, o zoom em está ativado e permite-lhe ampliar aos seus dados.
* **Reduzir:** Esse controle permite-lhe um nível de zoom da sua última zoom. Por exemplo, se tiver o zoom aos seus dados três vezes, zoom horizontalmente leva fazer uma etapa por vez.
* **Reposição de zoom:** Assim que já realizou vários níveis de zoom, pode utilizar o controle de reposição do zoom para retornar ao seu conjunto de resultados original.

 ![Executar de zoom nos seus dados](media/howto-create-analytics-experimental/analytics-zoom.png)

Pode alterar o estilo de linha para atender às suas necessidades. Tem quatro opções:

* **Linha:** Uma simples linha entre cada um dos pontos de dados.
* **Uniforme:** Uma linha curva entre cada ponto.
* **Passo:** Linha entre cada ponto no gráfico é um passo.
* **Dispersão:** Todos os pontos são desenhados no gráfico, sem linhas ligá-las.

 ![Tipos de linha diferentes disponíveis no Analytics](media/howto-create-analytics-experimental/analytics-linetypes.png)

Por último, pode organizar seus dados entre o eixo y ao selecionar um dos três modos:

* **Empilhadas:** Um gráfico para cada medição é empilhado e cada um dos gráficos têm suas próprias eixo y. Gráficos empilhados são úteis quando tem várias medidas selecionadas e quer ter uma visão distinta dessas medidas.
* **Unstacked:** Um gráfico para cada medida é desenhado no eixo y de um, mas os valores do eixo y são alterados com base na medida realçada. Gráficos unstacked são úteis quando deseja sobrepor várias medidas e quiser ver padrões entre essas medidas para o mesmo intervalo de tempo.
* **Eixo y partilhado:** Todos os gráficos compartilham o mesmo eixo y e não altere os valores para o eixo. Gráficos de eixo y partilhados são úteis quando deseja examinar uma única medida ao dividir os dados com dividir-by.

 ![Dispor os dados entre o eixo y com modos de visualização diferente](media/howto-create-analytics-experimental/analytics-yaxis.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como criar uma análise personalizada para a sua aplicação do Azure IoT Central, eis o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Preparar e ligue-se uma aplicação node. js](howto-connect-nodejs-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)