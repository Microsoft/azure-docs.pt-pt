---
title: Relações de âncora e procura de caminhos
description: Conheça o modelo conceptual por trás das relações de âncora. Aprenda a ligar âncoras dentro de um espaço e a utilizar a API próxima para cumprir um cenário de descoberta de formas.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f2fd8f4b7d03be8822c3ec12e2be589054942ce3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74270608"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Relações de âncora e descoberta de caminhos em Âncoras Espaciais Azure

Ao usar relações de âncora, pode criar âncoras conectadas num espaço e, em seguida, fazer perguntas como estas:

* Há âncoras por perto?
* A que distância estão?

## <a name="examples"></a>Exemplos

Pode utilizar âncoras conectadas em casos como estes:

* Um trabalhador precisa de completar uma tarefa que envolve visitar vários locais numa fábrica industrial. A fábrica tem âncoras espaciais em cada local. Uma aplicação HoloLens ou móvel ajuda a guiar o trabalhador de um local para o outro. A aplicação primeiro pede as âncoras espaciais próximas e, em seguida, guia o trabalhador para o local seguinte. A aplicação mostra visualmente a direção geral e a distância para o local seguinte.

* Um museu cria âncoras espaciais em exposições públicas. Juntos, estas âncoras formam uma visita de uma hora às exposições públicas essenciais do museu. Numa exposição pública, os visitantes podem abrir a aplicação de realidade mista do museu no seu dispositivo móvel. Depois apontam a câmara do telemóvel à volta do espaço para ver a direção geral e a distância até às outras exibições públicas do passeio. À medida que um utilizador caminha para um ecrã público, a aplicação atualiza a direção geral e a distância para ajudar a guiar o utilizador.

## <a name="set-up-way-finding"></a>Configurar a procura de caminhos

Uma aplicação que utiliza a direção da linha de visão e a distância entre âncoras para fornecer orientação está a utilizar a *descoberta de formas.* A procura de caminhos é diferente da navegação turn-by-turn. Na navegação por turnos, os utilizadores são guiados em torno de paredes, através de portas e entre pisos. Com a procura de caminhos, o utilizador recebe dicas sobre a direção geral do destino. Mas a inferência ou conhecimento do espaço também ajuda o utilizador a navegar através da estrutura para o destino.

Para construir uma experiência de descoberta de formas, prepare primeiro um espaço para a experiência e desenvolva uma app com a quais os utilizadores irão interagir. Estes são os passos conceptuais:

1. **Planeie o espaço**: Decida quais as localizações dentro do espaço que farão parte da experiência de localização. Nos nossos cenários, o supervisor da fábrica ou o coordenador do museu podem decidir quais os locais a incluir na experiência de localização.
2. **Ligue as âncoras**: Visite os locais escolhidos para criar âncoras espaciais. Pode fazê-lo num modo de administração da aplicação de utilizador final ou numa aplicação totalmente diferente. Ligará ou relacionará cada âncora com as outras. O serviço mantém estas relações.
3. **Inicie a experiência do utilizador final**: Os utilizadores executam a aplicação para localizar uma âncora, que pode estar em qualquer um dos locais escolhidos. O seu design geral deve determinar os locais onde os utilizadores podem entrar na experiência.
4. **Encontre âncoras próximas**: Depois de o utilizador encontrar uma âncora, a aplicação pode solicitar âncoras próximas. Este procedimento devolve uma pose entre o dispositivo e estas âncoras.
5. **Guiar o utilizador**: A aplicação pode utilizar a pose para cada uma destas âncoras para dar orientações sobre a direção geral e a distância do utilizador. Por exemplo, o feed da câmara na aplicação pode mostrar um ícone e uma seta para representar cada destino potencial, como mostra a imagem seguinte.
6. **Refinar a orientação**: À medida que o utilizador anda, a aplicação pode calcular periodicamente uma nova pose entre o dispositivo e a âncora de destino. A aplicação continua a aperfeiçoar as dicas de orientação que ajudam o utilizador a chegar ao destino.

    ![Um exemplo de como uma aplicação pode mostrar orientação de procura de caminhos](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Ligar âncoras

Para construir uma experiência de descoberta de caminhos, você primeiro precisa colocar as âncoras nos locais escolhidos. Nesta secção, assumimos que o administrador da app já terminou este trabalho.

### <a name="connect-anchors-in-a-single-session"></a>Ligar âncoras numa única sessão

Para ligar âncoras:

1. Caminhe até ao primeiro local e crie âncora A usando uma CloudSpatialAnchorSession.
2. Caminhe para o segundo local. A plataforma mr/AR subjacente acompanha o movimento.
3. Crie a âncora B utilizando a mesma CloudSpatialAnchorSession. As âncoras A e B estão agora ligadas. O serviço De Âncoras Espaciais mantém esta relação.
4. Continue o procedimento para as âncoras restantes.

### <a name="connect-anchors-in-multiple-sessions"></a>Ligar âncoras em várias sessões

Pode ligar âncoras espaciais em várias sessões. Utilizando este método, pode criar e ligar algumas âncoras de uma só vez e depois criar e ligar mais âncoras.

Para ligar âncoras em várias sessões:

1. A aplicação cria algumas âncoras numa CloudSpatialAnchorSession.
2. Em um momento diferente, a aplicação localiza uma destas âncoras (por exemplo, Anchor A) usando uma nova CloudSpatialAnchorSession.
3. Caminhe para um novo local. A plataforma subjacente de realidade mista ou realidade aumentada acompanha o movimento.
4. Crie a âncora C utilizando a mesma CloudSpatialAnchorSession. As âncoras A, B e C estão agora ligadas. O serviço De Âncoras Espaciais mantém esta relação.

Pode continuar este procedimento para mais âncoras e mais sessões ao longo do tempo.

### <a name="verify-anchor-connections"></a>Verificar ligações de âncora

A aplicação pode verificar se duas âncoras estão ligadas através da emissão de uma consulta para âncoras próximas. Quando o resultado da consulta contém a âncora-alvo, a ligação da âncora é verificada. Se as âncoras não estiverem ligadas, a aplicação pode tentar conectá-las novamente.

Aqui estão algumas razões pelas quais as âncoras podem não conseguir ligar:

* A plataforma de realidade mista subjacente ou de realidade aumentada perdeu o rastreio durante o processo de ligação das âncoras.
* Devido a um erro de rede durante a comunicação com o serviço De âncora espacial, a ligação de âncora não pôde ser persistiu.

### <a name="find-sample-code"></a>Encontrar código de amostra

Para encontrar um código de amostra que mostre como ligar âncoras e fazer consultas próximas, consulte aplicações de amostra de [Âncoras Espaciais](https://github.com/Azure/azure-spatial-anchors-samples).
