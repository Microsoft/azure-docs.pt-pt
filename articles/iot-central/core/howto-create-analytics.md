---
title: Analise os dados do dispositivo na sua aplicação Azure IoT Central / Microsoft Docs
description: Analise os dados do dispositivo na sua aplicação Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f329b6377915a3df6e3f3e212cbd8d41f9ed80d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90017665"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Como usar a análise para analisar dados do dispositivo

*Este artigo aplica-se a operadores, construtores e administradores.*

O Azure IoT Central fornece ricas capacidades de análise para analisar tendências históricas e correlacionar várias telemetrias dos seus dispositivos. Para começar, visite **Analytics** no painel esquerdo.

## <a name="understanding-the-analytics-ui"></a>Compreender a UI analítica
A interface do utilizador analítico é feita de três componentes principais:
- **Painel de configuração de dados:** No painel de configuração, comece por selecionar o grupo de dispositivos para o qual pretende analisar os dados. Em seguida, selecione a telemetria que pretende analisar e selecione o método de agregação para cada telemetria. **O** controlo Split By ajuda a agrupar os dados utilizando as propriedades do dispositivo como dimensões.

- **Controlo do tempo:** O controlo de tempo é utilizado para selecionar a duração para a qual pretende analisar os dados. Pode arrastar qualquer extremidade do deslize de tempo para selecionar o intervalo de tempo. O controlo de tempo também tem um deslizador **de tamanho de intervalo** que controla o balde ou o tamanho do intervalo utilizado para agregar os dados. 

- **Controlo de gráficos:** O controlo de gráficos visualiza os dados como um gráfico de linha. Pode alternar a visibilidade de linhas específicas interagindo com a lenda do gráfico. 


  ![Visão geral da UI analítica](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Consulta dos seus dados

Terá de começar por escolher um grupo de **dispositivos**e a telemetria que pretende analisar. Uma vez feito, **selecione Analisar** para começar a visualizar os seus dados.

- **Grupo de dispositivos:** Um [grupo de dispositivos](tutorial-use-device-groups.md) é um grupo definido pelo utilizador dos seus dispositivos. Por exemplo, todos os Frigoríficos em Oakland, ou Todas as turbinas eólicas da versão 2.0.

- **Telemetria:** Selecione a telemetria que pretende analisar e explorar. Pode selecionar várias telemetrias para analisar em conjunto. O método de agregação predefinido é definido como médio para numérico e contagem para o tipo de dados de cadeia, respectivamente. Os métodos de agregação suportados para tipos de dados numéricos são médios, máximos, mínimos, contagens e, Sum.  Os métodos de agregação suportados para o tipo de dados de cadeia são contados.

- **Dividido por:** O controlo 'Split by' ajuda a agrupar os dados utilizando as propriedades do dispositivo como dimensões. Os valores do dispositivo e das propriedades da nuvem juntam-se juntamente com a telemetria como e quando é enviada pelo dispositivo. Se a propriedade da nuvem ou do dispositivo tiver sido atualizada, verá a telemetria agrupada por diferentes valores na tabela.

    > [!TIP]
    > Para visualizar os dados de cada dispositivo separadamente, selecione O Id do dispositivo no controlo 'Split by'.

## <a name="interacting-with-your-data"></a>Interagindo com os seus dados

Uma vez consultados os seus dados, pode começar a visualizá-lo na tabela de linhas. Pode mostrar/ocultar telemetria, alterar a duração do tempo, ver a telemetria numa grelha de dados.

- **Painel de editores de tempo:** Por defeito, vamos recuperar dados do passado um dia. Pode arrastar qualquer extremidade do deslize de tempo para alterar a duração do tempo. Também pode utilizar o controlo do calendário para selecionar um dos baldes de tempo predefinidos ou selecionar um intervalo de tempo personalizado. O controlo de tempo também tem um deslizador **de tamanho de intervalo** que controla o balde ou o tamanho do intervalo utilizado para agregar os dados.

    ![Editor do Tempo](media/howto-create-analytics/timeeditorpanel.png)

    - **Ferramenta de deslizamento de gama de datas interior**: Utilize os dois comandos de ponto final arrastando-os durante o período de tempo que pretende. Este intervalo de data interior é limitado pelo controlo do deslizamento de datas exteriores.
    
   
    - **Controlo do deslizamento de gama de datas exteriores**: Utilize os comandos de ponto final para selecionar o intervalo de datas exteriores, que estará disponível para o controlo da gama de datas interiores.

    - **Aumentar e diminuir os botões de intervalo**de datas : Aumente ou diminua o seu tempo selecionando qualquer um dos botões para o intervalo que deseja.

    - **Slider de tamanho de intervalo : Use-o**para ampliar dentro e fora de intervalos ao longo do mesmo período de tempo. Esta ação proporciona um controlo mais preciso do movimento entre grandes fatias de tempo. Você pode usá-lo para ver visões granulares e de alta resolução dos seus dados, mesmo até milissegundos. O ponto de partida padrão do slider é definido como a visão mais ideal dos dados da sua seleção, que equilibra a resolução, a velocidade de consulta e a granularidade.
    
    - **Selecionador de datas**: Com este controlo web, pode selecionar facilmente as faixas de data e hora desejada. Também pode utilizar o controlo para alternar entre diferentes fusos horários. Depois de escoar as alterações a aplicar no seu espaço de trabalho atual, selecione Save.

    > [!TIP]
    > O tamanho do intervalo é determinado dinamicamente com base no tempo de tempo selecionado. Períodos de tempo mais pequenos permitirão agregar os dados em intervalos muito granulares de até alguns segundos.


- **Lenda do gráfico:** A lenda do gráfico mostra a telemetria selecionada na tabela. Pode pairar sobre cada item da lenda para o focar na tabela. Ao utilizar 'Split By', a telemetria é agrupada pelos respetivos valores da dimensão selecionada. Pode alternar a visibilidade de cada telemetria específica ou de todo o grupo clicando no nome de grupo.  


- **Controlo do formato do eixo Y:** ciclos de modo y-eixo através das opções de visualização do eixo y disponível. Este controlo só está disponível quando se visualizam diferentes telemetrias. Pode definir o eixo y escolhendo entre um dos três modos:

    - **Empilhado:** Um gráfico para cada telemetria é empilhado e cada um dos gráficos tem o seu próprio eixo y. Este modo está definido como padrão.
    - **Partilhado:** Um gráfico para cada telemetria é traçado contra o mesmo eixo y.
    - **Sobreposição:** Utilize-o para empilhar várias linhas no mesmo eixo y, com os dados do eixo y a mudarem com base na linha selecionada.

  ![Organize dados através do eixo y com diferentes modos de visualização](media/howto-create-analytics/yaxiscontrol.png)

- **Controlo de zoom:** O Zoom permite-lhe aprofundar os seus dados. Se encontrar um período de tempo em que gostaria de se concentrar dentro do conjunto de resultados, use o ponteiro do rato para agarrar a área e, em seguida, arraste-a para o ponto final à sua escolha. Em seguida, clique à direita na área selecionada e clique em Zoom.

  ![Ampliar os dados](media/howto-create-analytics/zoom.png)

Sob a elipse, há mais controlos de gráficos para interagir com os dados:

- **Grelha de exibição:** Os seus resultados estão disponíveis num formato de tabela, permitindo-lhe visualizar o valor específico de cada ponto de dados.

- **Descarregue como CSV:** Os seus resultados estão disponíveis para exportar como um ficheiro de valores separados por vírgula (CSV). O ficheiro CSV contém dados para cada dispositivo. Os resultados são exportados utilizando o intervalo e o prazo especificados. 

- **Deixe cair um marcador:** O controlo 'Drop Marker' fornece uma forma de ancorar certos pontos de dados na tabela. É útil quando se está a tentar comparar dados para várias linhas em diferentes períodos de tempo.

  ![Mostrando a vista da grelha para a sua análise](media/howto-create-analytics/additionalchartcontrols.png)
