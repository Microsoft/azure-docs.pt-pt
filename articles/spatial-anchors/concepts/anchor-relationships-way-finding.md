---
title: Relações de âncora e orientação espacial
description: Conheça o modelo conceptual por trás das relações de âncora. Aprenda a ligar âncoras dentro de um espaço e a usar a API próxima para cumprir um cenário de descoberta de formas.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: ce79fe88b33659241a226af7bbb7a966ede41abb
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696170"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Relações de âncora e descoberta de formas em Âncoras Espaciais Azure

Ao utilizar relações de âncora, pode criar âncoras conectadas num espaço e, em seguida, fazer perguntas como estas:

* Há âncoras por perto?
* Estão muito longe?

## <a name="examples"></a>Exemplos

Pode utilizar âncoras ligadas em casos como este:

* Um trabalhador precisa de completar uma tarefa que envolve visitar vários locais de uma fábrica industrial. A fábrica tem âncoras espaciais em cada local. Um HoloLens ou uma aplicação móvel ajuda a guiar o trabalhador de um local para outro. A aplicação primeiro pede as âncoras espaciais próximas e, em seguida, guia o trabalhador para o local seguinte. A aplicação mostra visualmente a direção geral e distância para o local seguinte.

* Um museu cria âncoras espaciais em exposições públicas. Juntos, estas âncoras formam uma visita de uma hora às exposições públicas essenciais do museu. Numa exposição pública, os visitantes podem abrir a aplicação de realidade mista do museu no seu dispositivo móvel. Depois apontam a câmara do telemóvel ao redor do espaço para ver a direção geral e a distância para os outros expositores públicos no passeio. À medida que um utilizador caminha em direção a um ecrã público, a aplicação atualiza a direção e distância gerais para ajudar a guiar o utilizador.

## <a name="set-up-way-finding"></a>Configurar a descoberta de formas

Uma aplicação que usa a direção de linha de visão e distância entre âncoras para fornecer orientação está a usar *a forma de encontrar.* A descoberta de caminhos é diferente da navegação turn-by-turn. Na navegação por turnos, os utilizadores são guiados em torno de paredes, através de portas e entre pisos. Com a descoberta de caminho, o utilizador recebe pistas sobre a direção geral do destino. Mas a inferência ou conhecimento do espaço também ajuda o utilizador a navegar através da estrutura para o destino.

Para construir uma experiência de descoberta de formas, primeiro prepare um espaço para a experiência e desenvolva uma app com a quais os utilizadores interagirão. Estes são os passos conceptuais:

1. **Planear o espaço**: Decida quais locais dentro do espaço farão parte da experiência de descoberta de caminho. Nos nossos cenários, o supervisor da fábrica ou o coordenador do tour do museu podem decidir quais os locais a incluir na experiência de descoberta de maneiras.
2. **Ligue as âncoras**: Visite os locais escolhidos para criar âncoras espaciais. Pode fazê-lo num modo de administração da aplicação do utilizador final ou numa aplicação totalmente diferente. Vai ligar ou relacionar cada âncora com as outras. O serviço mantém estas relações.
3. **Inicie a experiência do utilizador final**: Os utilizadores executam a aplicação para localizar uma âncora, que pode estar em qualquer um dos locais escolhidos. O seu design geral deve determinar os locais onde os utilizadores podem entrar na experiência.
4. **Encontre âncoras nas proximidades**: Depois de o utilizador encontrar uma âncora, a aplicação pode solicitar âncoras próximas. Este procedimento devolve uma pose entre o dispositivo e estas âncoras.
5. **Guia o utilizador**: A aplicação pode utilizar a pose de cada uma destas âncoras para dar orientações sobre a direção e distância gerais do utilizador. Por exemplo, o feed da câmara na aplicação pode mostrar um ícone e seta para representar cada destino potencial, como mostra a imagem a seguir.
6. **Refinar a orientação**: À medida que o utilizador anda, a aplicação pode calcular periodicamente uma nova pose entre o dispositivo e a âncora de destino. A aplicação continua a aperfeiçoar as dicas de orientação que ajudam o utilizador a chegar ao destino.

    ![Um exemplo de como uma aplicação pode mostrar orientação de descoberta de maneiras](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Ligar âncoras

Para construir uma experiência de descoberta de formas, primeiro precisa colocar as âncoras nos locais escolhidos. Nesta secção, vamos assumir que o administrador da app já terminou este trabalho.

### <a name="connect-anchors-in-a-single-session"></a>Ligue âncoras numa única sessão

Para ligar âncoras:

1. Caminhe até ao primeiro local e crie âncora A usando uma CloudSpatialAnchorSession.
2. Caminhe até o segundo local. A plataforma MR/AR subjacente acompanha o movimento.
3. Crie âncora B utilizando a mesma CloudSpatialAnchorSession. As âncoras A e B estão agora ligadas. O serviço Spatial Anchors mantém esta relação.
4. Continue o procedimento para as âncoras restantes.

### <a name="connect-anchors-in-multiple-sessions"></a>Ligue âncoras em várias sessões

Pode ligar âncoras espaciais em várias sessões. Utilizando este método, pode criar e ligar algumas âncoras de uma só vez e, mais tarde, criar e ligar mais âncoras.

Para ligar âncoras em várias sessões:

1. A aplicação cria algumas âncoras (Âncoras A e B) numa CloudSpatialAnchorSession.
2. Num momento diferente, a aplicação localiza uma destas âncoras (por exemplo, Âncora A) utilizando uma nova CloudSpatialAnchorSession.
3. Caminhe para um novo local. A plataforma de realidade mista subjacente ou de realidade aumentada acompanha o movimento.
4. Crie âncora C utilizando a mesma CloudSpatialAnchorSession. As âncoras A, B e C estão agora ligadas. O serviço Spatial Anchors mantém esta relação.

Pode continuar este procedimento para mais âncoras e mais sessões ao longo do tempo.

### <a name="verify-anchor-connections"></a>Verificar ligações de âncora

A aplicação pode verificar se duas âncoras estão ligadas através da emissão de uma consulta para âncoras próximas, o que faz ao definir o `NearAnchorCriteria` num `CloudSpatialAnchorWatcher` . Quando o resultado da consulta contiver a âncora do alvo, a ligação de ancoragem é verificada. Se as âncoras não estiverem ligadas, a aplicação pode tentar conectá-las novamente.

Eis algumas razões pelas quais as âncoras podem não conseguir ligar-se:

* A plataforma de realidade mista subjacente ou de realidade aumentada perdeu o rastreio durante o processo de ligação das âncoras.
* Devido a um erro de rede durante a comunicação com o serviço De âncora espacial, a ligação de âncora não pôde ser persistiu.

### <a name="find-sample-code"></a>Encontre o código de amostra

Para encontrar o código de amostra que mostra como ligar âncoras e fazer consultas nas proximidades, consulte [as aplicações de amostras de Âncoras Espaciais](https://github.com/Azure/azure-spatial-anchors-samples).
