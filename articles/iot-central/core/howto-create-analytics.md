---
title: Analise os dados do dispositivo na sua aplicação Azure IoT Central [ Microsoft Docs
description: Analise os dados do dispositivo na sua aplicação Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 40460b58ede0ca0da8fe25475906bdbe41bfffe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158287"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Como usar a análise para analisar dados do dispositivo

*Este artigo aplica-se a operadores, construtores e administradores.*



A Azure IoT Central fornece capacidades de análise ricas para analisar tendências históricas e correlacionar várias telemetrias dos seus dispositivos. Para começar, visite **o Analytics** no painel esquerdo.

## <a name="understanding-the-analytics-ui"></a>Compreender a UI analítica
A interface de utilizador analítico é feita de três componentes principais:
- **Painel de configuração** de dados: No painel de configuração, comece por selecionar o grupo do dispositivo para o qual pretende analisar os dados. Em seguida, selecione a telemetria que pretende analisar e selecione o método de agregação para cada telemetria. **Split By** control ajuda a agrupar os dados utilizando as propriedades do dispositivo como dimensões.

- **Controlo do tempo:** O controlo de tempo é utilizado para selecionar a duração para a qual pretende analisar os dados. Pode arrastar qualquer extremidade do intervalo de tempo para selecionar o tempo. O controlo de tempo também tem um slider **de tamanho intervalo** que controla o balde ou o tamanho do intervalo utilizado para agregar os dados. 

- **Controlo de gráficos:** O controlo de gráficos visualiza os dados como um gráfico de linha. Pode alternar a visibilidade de linhas específicas interagindo com a lenda do gráfico. 


  ![Visão geral da UI analytics](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Consulta dos seus dados

Terá de começar por escolher um grupo de **dispositivos**e a telemetria que pretende analisar. Uma vez feito, selecione **Analisar** para começar a visualizar os seus dados.

- **Grupo de dispositivos:** Um grupo de [dispositivos](tutorial-use-device-groups.md) é um grupo definido pelo utilizador dos seus dispositivos. Por exemplo, todos os frigoríficos em Oakland, ou todas as turbinas eólicas da versão 2.0.

- **Telemetria:** Selecione a telemetria que pretende analisar e explorar. Pode selecionar várias telemetrias para analisar em conjunto. O método de agregação predefinida é definido em média para numérico e contagem para tipo de dados de corda, respectivamente. Os métodos de agregação suportados para tipos de dados numéricos são médios, máximos, mínimos, condes e, soma.  Os métodos de agregação suportados para o tipo de dados de cordas são contados.

- **Dividido por:** O controlo 'Split by' ajuda a agrupar os dados utilizando as propriedades do dispositivo como dimensões. Os valores do dispositivo e das propriedades da nuvem juntam-se juntamente com a telemetria como e quando é enviado pelo dispositivo. Se a propriedade da nuvem ou do dispositivo tiver sido atualizada, então verá a telemetria agruparada por diferentes valores na tabela.

    > [!TIP]
    > Para visualizar os dados de cada dispositivo separadamente, selecione Id do dispositivo no comando 'Split by'.

## <a name="interacting-with-your-data"></a>Interagindo com os seus dados

Depois de questionado sobre os seus dados, pode começar a visualizá-lo na tabela de linhas. Pode mostrar/ocultar a telemetria, alterar a duração do tempo, ver a telemetria numa grelha de dados.

- **Painel de editor de tempo:** Por padrão, vamos recuperar dados do último dia. Pode arrastar qualquer extremidade do slider para alterar a duração do tempo. Também pode utilizar o controlo de calendário para selecionar um dos baldes de tempo pré-definidos ou selecionar um intervalo de tempo personalizado. O controlo de tempo também tem um slider **de tamanho intervalo** que controla o balde ou o tamanho do intervalo utilizado para agregar os dados.

    ![Editor de Tempo](media/howto-create-analytics/timeeditorpanel.png)

    - Ferramenta de deslize de alcance de **data interior:** Utilize os dois comandos de ponto final arrastando-os durante o período de tempo que desejar. Esta gama de datas interiores é limitada pelo controlo de slider de intervalo de data exterior.
    
   
    - Controlo de **slider**de intervalo de data externa : Utilize os comandos de ponto final para selecionar o intervalo de data exterior, que estará disponível para o seu controlo de alcance de data interior.

    - **Aumentar e diminuir os botões**de intervalo de datas : Aumente ou diminua o seu tempo selecionando qualquer um dos botões para o intervalo que desejar.

    - **Deslizador de intervalos:** Utilize-o para ampliar para dentro e para fora de intervalos ao longo do mesmo período de tempo. Esta ação proporciona um controlo mais preciso do movimento entre grandes fatias de tempo. Pode usá-lo para ver vistas granulares e de alta resolução dos seus dados, mesmo até milissegundos. O ponto de partida padrão do slider é definido como a visão mais ideal dos dados da sua seleção, que equilibra a resolução, a velocidade de consulta e a granularidade.
    
    - **Picker**de gama de datas: Com este controlo web, pode selecionar facilmente as gamas de data e hora que deseja. Também pode utilizar o comando para alternar entre diferentes fusos horários. Depois de efazer as alterações para se aplicar ao seu espaço de trabalho atual, selecione Save.

    > [!TIP]
    > O tamanho do intervalo é determinado dinamicamente com base no tempo selecionado. Períodos de tempo mais pequenos permitirão agregar os dados em intervalos muito granulares de até alguns segundos.


- **Lenda do Gráfico:** A lenda do gráfico mostra a telemetria selecionada na tabela. Pode sondar cada item da lenda para o focar na tabela. Ao utilizar 'Split By', a telemetria é agrunada pelos respetivos valores da dimensão selecionada. Pode alternar a visibilidade de cada telemetria específica ou de todo o grupo clicando no nome do grupo.  


- **Controlo do formato do eixo Y:** ciclos de modo de eixo y através das opções disponíveis de visão de eixo y. Este controlo só está disponível quando se visualizam diferentes telemetrias. Pode definir o eixo y escolhendo de um de três modos:

    - **Empilhado:** Um gráfico para cada telemetria é empilhado e cada um dos gráficos tem o seu próprio eixo y. Este modo é definido como padrão.
    - **Partilhado:** Um gráfico para cada telemetria é traçado contra o mesmo eixo y.
    - **Sobreposição:** Use-o para empilhar várias linhas no mesmo eixo y, com os dados do eixo y a mudarem com base na linha selecionada.

  ![Disponha dados através do eixo y com diferentes modos de visualização](media/howto-create-analytics/yaxiscontrol.png)

- **Controlo de zoom:** O zoom permite perfurar mais nos seus dados. Se encontrar um período de tempo em que gostaria de se concentrar dentro do seu conjunto de resultados, use o ponteiro do rato para agarrar a área e, em seguida, arraste-o para o ponto final da sua escolha. Em seguida, clique à direita na área selecionada e clique em Zoom.

  ![Zoom para os dados](media/howto-create-analytics/zoom.png)

Sob a elipse, há mais controlos de gráficos para interagir com os dados.

- **Grelha de visualização:** Os seus resultados estão disponíveis num formato de tabela, permitindo-lhe visualizar o valor específico de cada ponto de dados.

- **Largue um marcador:** O controlo 'Drop Marker' fornece uma forma de ancorar certos pontos de dados na tabela. É útil quando se está a tentar comparar dados para várias linhas em diferentes períodos de tempo.

  ![Mostrando a vista da grelha para a sua análise](media/howto-create-analytics/additionalchartcontrols.png)