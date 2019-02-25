---
title: Relações de âncora e localizar de forma no âncoras espaciais do Azure | Documentos da Microsoft
description: Descreva o modelo conceitual por trás de relações de âncora. Descrevem o processo de ligação de âncoras dentro de um espaço e o processo de utilização da API de próximos para atender a um cenário de forma a localizar. Depois de explicar o modelo conceitual, aponte os desenvolvedores para nossas aplicações de exemplo que fazê-lo nas proximidades eles podem começar a implementar este cenário nas suas próprias aplicações.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 56f59a76ac3d11677d5b1f76904cf74e933fa7f6
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753344"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Relações de âncora e localizar de forma no âncoras espaciais do Azure

Relações de âncora permitem-lhe criar âncoras conectadas num espaço e, em seguida, fazer perguntas sobre, como:

* Existem as âncoras nas proximidades?
* Distância são?

## <a name="examples"></a>Exemplos

Alguns casos-utilização de exemplo, pode ativar com âncoras ligadas incluem:

1. Uma função de trabalho tem de executar um procedimento que envolve a visita a vários locais de uma fábrica de industrial. A fábrica colocou âncoras geográficos em cada site envolvidos no procedimento. Uma aplicação móvel ou HoloLens ajuda a orientar a função de trabalho de uma localização para a próxima. Em primeiro lugar, perguntará para as âncoras geográficos que estão próximos e orientam, em seguida, a função de trabalho para a localização seguinte. A aplicação mostra indicadores visuais sobre a direção geral e a distância para a localização seguinte para concluir a tarefa.

2. Um museu cria as âncoras espaciais ao exibe pública que juntos criam um Tour pelo específico o museu, como "Uma apresentação de uma hora de essencial apresenta pública". Quando os visitantes estão numa exibição pública, pode abrir aplicação de realidade mista o Museu no respetivo dispositivo móvel. Em seguida, eles seriam ponto o telefone em todo o espaço e por meio de um feed da câmara e ver a direção geral e a distância para a outra apresenta pública na visita guiada. Como o utilizador começa a orientá-lo para um do apresenta público, a aplicação atualiza progressivamente a direção geral e a distância para ajudar os utilizadores de guia lá.

## <a name="way-finding"></a>Forma a localizar

Imagine que a aplicação está a utilizar "de linha de visão" direção e a distância entre as âncoras de fornecer sugestões de orientação para os utilizadores. Fazemos referência a esse cenário geral como de forma a localizar. É importante ter em atenção de que forma a localizar é diferente no painel de navegação de mão por vez. No painel de navegação, ative a folheio os usuários são direcionados em torno de paredes, através de portas e entre o chão. Com a forma-localizar, o utilizador é fornecido sugestões sobre a direção geral do destino. Como inferência de tipos ou dados de conhecimento do espaço do usuário também ajuda a navegar pela estrutura para o destino.

A criação de uma experiência de forma a localizar envolve a preparação de um espaço para a experiência e desenvolver uma aplicação que os utilizadores finais irão interagir com. As etapas conceituais envolvidas incluem:

1. O espaço de planejamento: Determine as localizações dentro do espaço de que participam na experiência de forma a localizar. Nos exemplos anteriores, esta atividade pode ser concluída pelo supervisor de fábrica ou o coordenador de apresentação de museu.
2. A ligar as âncoras de: Alguém visita as localizações escolhidas e cria geográficos âncoras lá. Esta tarefa pode ser feita com um modo de administrador da aplicação pelo utilizador final ou um aplicativo diferente inteiramente. Este processo, cada âncora é ligada ou relacionados com os outros. Esses relacionamentos são mantidos no serviço.
3. A iniciar a experiência de utilizador final: É a primeira etapa para os utilizadores finais localizar um das âncoras de utilizar a aplicação, o que pode ser em qualquer uma das localizações escolhidas. Determinar as localizações onde os utilizadores finais podem introduzir a experiência é parte de projetar a experiência geral.
4. Localizar nas proximidades âncoras: Depois do utilizador tem de localizar uma âncora, a aplicação pode pedir nas proximidades âncoras. Este procedimento devolve uma posição entre o dispositivo e estes âncoras.
5. Orientar o usuário: A aplicação pode aproveitar a posição para cada um desses âncoras para processar as sugestões de orientações úteis sobre a direção geral e a distância. Por exemplo, poderá haver um ícone e seta uma câmera feed numa aplicação móvel que representa cada destino potencial conforme a imagem abaixo.
6. Refinar as diretrizes: Como guia-o utilizador, a aplicação periodicamente calcular uma nova posição entre o dispositivo e a âncora de destino. A aplicação continua a refinar as sugestões de orientações para ajudam o utilizador que são recebidos no destino.

![Ponto de reunião](./media/meeting-spot.png)

## <a name="connecting-anchors"></a>Ligar as âncoras

Para criar uma experiência de forma a localizar, precisa colocar âncoras ligadas nas localizações escolhidas. Veja a seguir vamos supor que este trabalho é feito por um administrador da aplicação.

### <a name="connecting-anchors-in-a-single-session"></a>Ligar as âncoras numa única sessão

Os passos envolvidos na conexão âncoras são:

1. O administrador leva-o para a primeira localização e cria uma âncora de uma CloudSpatialAnchorSession a utilizar.
2. O administrador leva-o para a localização do segundo enquanto a plataforma subjacente do MR/AR continua a controlar o utilizador.
3. O administrador cria B de âncora com o mesmo CloudSpatialAnchorSession. Âncoras A e B estão agora ligadas e esta relação é mantida pelo serviço âncoras espaciais do Azure.
4. Continue o procedimento para todas as âncoras de que pretende ligar.

### <a name="multiple-sessions"></a>Várias sessões

Também pode ligar as âncoras geográficos através de várias sessões. Este método permite-lhe criar e ligar-se alguns âncoras de uma só vez e mais tarde criar e ligar mais âncoras. Para ligar as âncoras com várias sessões:

1. A aplicação cria algumas âncoras no um CloudSpatialAnchorSession.
2. Mais tarde, por exemplo em outro dia, a aplicação localiza um destes âncoras com um novo CloudSpatialAnchorSession (por exemplo âncora A).
3. O utilizador leva-o para uma nova localização, enquanto a plataforma subjacente do MR/AR continua a controlar o utilizador.
4. Utilizar o mesmo CloudSpatialAnchorSession, o utilizador cria as âncoras de C. de âncora A, B e C estão agora ligados e esta relação é mantida pela âncoras espaciais do Azure.
5. Pode continuar a este procedimento para âncoras mais e mais sessões ao longo do tempo.

### <a name="verifying-anchor-connections"></a>A verificar ligações de âncora

A aplicação pode verificar que duas âncoras estão ligadas através da emissão de uma consulta para âncoras próximas. Quando o resultado da consulta contém a âncora de destino pretendida, a aplicação tem a confirmação de que as âncoras estão ligadas. Se não estão ligados, a aplicação pode repetir o procedimento de ligação novamente. Aqui estão algumas razões âncoras falhar para ligar:

1. O controlador de MR/AR subjacente perdida o controle durante o processo de ligação de âncoras.
2. Ocorreu um erro de rede ao comunicar com o serviço de âncoras espaciais do Azure e não foi possível manter-se a ligação de âncora.

### <a name="sample-code"></a>Código de exemplo

Pode ver o código de exemplo que mostra como ligar as âncoras e fazer nas proximidades de consultas. Consulte as aplicações de exemplo de âncoras espaciais do Azure no GitHub.
