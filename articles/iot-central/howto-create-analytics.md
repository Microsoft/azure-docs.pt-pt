---
title: Analise os seus dados de dispositivo na sua aplicação do Azure IoT Central | Documentos da Microsoft
description: Analise os seus dados de dispositivo na sua aplicação do Azure IoT Central.
author: lmasieri
ms.author: lmasieri
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d0b964c089e8aa234cd5eee56413ff56599ad5df
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307280"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Como utilizar a análise para analisar os dados de dispositivo


*Este artigo aplica-se a operadores, construtores e administradores.*


O Azure IoT Central fornece capacidades de análises avançadas que fazem sentido de grandes quantidades de dados dos seus dispositivos. Para começar, visite **Analytics** no menu de navegação esquerdo. 

  ![Navegação de IoT Central para análise](media/howto-create-analytics/analytics-navigation.png)

## <a name="querying-your-data"></a>Consultar dados

Terá de escolher uma **dispositivos conjunto**, adicione um **filtro** (opcional) e selecione um **período de tempo** para começar a utilizar. Quando tiver terminado, selecione **Mostrar resultados** para começar a visualizar seus dados.


* **Conjuntos de dispositivo:** R [dispositivos conjunto](howto-use-device-sets.md) é um grupo definido pelo utilizador dos seus dispositivos. Por exemplo, todos os refrigeradores no Oakland ou todos passe 2.0 turbines de vento.

<!---
to-do: confirm if 10 is the max number of filters
to-do: do we need to explain how fiters work?
--->

* **Filtros:** Opcionalmente, pode adicionar filtros a sua pesquisa para alcançar seus dados. Pode adicionar até 10 filtros ao mesmo tempo. Por exemplo, dentro de todos os refrigeradores no Oakland, encontre as que tenha tido a temperatura passar de 60 graus. 
* **Período de tempo:** Por predefinição, irá obter dados de após 10 minutos. Pode alterar este valor para um dos intervalos de tempo predefinido ou selecione um período de tempo personalizado. 

 ![Consulta do Analytics](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Visualizar seus dados

Depois de consultados a seus dados, poderá começar a visualizá-lo. Pode mostrar/ocultar medidas, alterar a forma como os dados são agregados e mais dividir os dados por propriedades de dispositivo diferentes.  

* **Divida por:** A divisão de dados por propriedades do dispositivo permite-lhe desagregar ainda mais para baixo sobre os seus dados. Por exemplo, pode dividir os resultados por ID de dispositivo ou localização.
<!---
to-do: confirm if 10 is the max number of measurements
--->
* **Medidas:** Pode optar por mostrar/ocultar até 10 itens de telemetria diferente está a ser comunicados pelos seus dispositivos ao mesmo tempo. Medidas são coisas como temperatura e humidade. 
* **Agregação de:** Por predefinição, agregar dados pelo respetiva média, mas pode optar por alterar a agregação de dados para outro local para se ajustar às suas necessidades. 

   ![Visualização de análise](media/howto-create-analytics/analytics-visualize.png) <br/><br/>
   ![Visualização de análise dividir por](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interagir com os seus dados

Existem várias formas em que pode alterar ainda mais os resultados de consulta para atender às suas necessidades de visualização. Pode se alternam entre uma vista de gráfico e uma exibição de grade, entrada/saída de zoom, atualize o seu conjunto de dados e alterar a forma como as linhas são mostradas.

* **Mostra grelha:** Os resultados vão estar disponíveis num formato de tabela, permitindo-lhe ver o valor específico para cada ponto de dados. Esta vista também cumpre os padrões de acessibilidade. 
* **Mostra gráfico:** Os resultados serão apresentados num formato de linha para facilmente pontos ascendente/descendente tendências e anomalias. 

 ![Mostrando a exibição de grade para análise](media/howto-create-analytics/analytics-showgrid.png)

Zoom permite-lhe alcançar seus dados. Se encontrar um período de tempo, gostaria de se concentrar em dentro do seu conjunto de resultados, utilize o cursor para capturar a área que pretende ampliar e usar os controles disponíveis para executar uma das seguintes ações:
* **Zoom:** Depois de selecionar um período de tempo, zoom será ativada e permitem-lhe ampliar aos seus dados.
* **Reduzir:** Esse controle permite-lhe um nível de zoom da sua última zoom. Por exemplo, se tiver ampliar aos seus dados três vezes, zoom horizontalmente levará fazer uma cópia de uma etapa por vez.
* **Reposição de zoom:** Assim que já realizou vários níveis de zoom, pode utilizar o controle de reposição do zoom para retornar ao seu conjunto de resultados original. 

 ![Executar de zoom nos seus dados](media/howto-create-analytics/analytics-zoom.png)


Pode alterar o estilo de linha para atender às suas necessidades. Tem quatro opções à sua escolha:
* **Linha:** Uma linha reta entre cada um dos pontos de dados será formada. 
* **Uniforme:** Uma linha curva entre cada ponto será formada
* **Passo:** Linha entre cada ponto no gráfico, irá criar um gráfico de passo
* **Dispersão:** Todos os pontos de irão ser desenhados no gráfico, sem linhas ligá-las. 

 ![Tipos de linha diferentes disponíveis no Analytics](media/howto-create-analytics/analytics-linetypes.png)

Por último, pode organizar os dados entre o eixo y ao selecionar a partir de um dos três modos:

* **Empilhadas:** Um gráfico para cada medição é empilhado e cada um dos gráficos têm suas próprias eixo y. Gráficos empilhados são úteis quando tem várias medidas selecionadas e quer ter uma visão distinta dessas medidas.
* **Unstacked:** Um gráfico para cada medida é desenhado no eixo y de um, mas os valores do eixo y são alterados com base na medida realçada. Gráficos unstacked são úteis quando deseja sobrepor várias medidas e quiser ver padrões entre essas medidas para o mesmo intervalo de tempo.
* **Eixo y partilhado:** Todos os gráficos compartilham o mesmo eixo y e não altere os valores para o eixo. Gráficos de eixo y partilhados são úteis quando deseja examinar uma única medida ao dividir os dados com dividir-by.

 ![Dispor os dados entre o eixo y com modos de visualização diferente](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como criar uma análise personalizada para a sua aplicação do Azure IoT Central, eis o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Preparar e ligue-se uma aplicação node. js](howto-connect-nodejs.md)