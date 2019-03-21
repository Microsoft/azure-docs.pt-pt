---
title: Relações de âncora e localizar de forma no âncoras espaciais do Azure | Documentos da Microsoft
description: Saiba mais sobre o modelo conceitual por trás de relações de âncora. Saiba para ligar as âncoras de dentro de um espaço e utilizar a API de próximos para atender a um cenário de forma a localizar.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 619cd051eccce3434469ae909f69496a254d0d9a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863343"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Relações de âncora e localizar de forma no âncoras espaciais do Azure

Ao utilizar relações de âncora, pode criar âncoras conectadas num espaço e, em seguida, fazer perguntas como essas:

* Existem as âncoras nas proximidades?
* Distância são?

## <a name="examples"></a>Exemplos

Poderia usar as âncoras ligadas em casos como esses:

* Uma função de trabalho tem de concluir uma tarefa que envolve a visita a vários locais de uma fábrica de industrial. O factory tem âncoras geográficos em cada localização. Uma aplicação móvel ou HoloLens ajuda a orientar a função de trabalho de uma localização para a próxima. A aplicação pela primeira vez pede-lhe as âncoras geográficos próximas e, em seguida, orienta a função de trabalho para a localização seguinte. A aplicação visualmente mostra a direção geral e a distância para a localização seguinte.

* Um museu cria as âncoras espaciais ao apresenta pública. Juntos, esses âncoras formam uma apresentação de uma hora de, apresenta o Museu essencial pública. Numa exibição pública, visitantes podem abrir aplicações de realidade mista o Museu no respetivo dispositivo móvel. Em seguida, apontam sua câmera de telefone em todo o espaço para ver a direção geral e a distância para a outra apresenta pública sobre a visita guiada. Como um utilizador percorre na direção de uma exibição pública, a aplicação atualiza a direção geral e a distância para ajudar a orientar o usuário.

## <a name="set-up-way-finding"></a>Configurar de forma a localizar

Está a utilizar uma aplicação que utiliza a direção de linha de visão e a distância entre as âncoras para fornecer orientações *forma a localizar*. Localizar de maneira é diferente no painel de navegação de mão por vez. No painel de navegação, ative a folheio os utilizadores são orientados em torno de paredes, através de portas e entre o chão. Com a forma-localizar, o utilizador obtém as sugestões sobre a direção geral do destino. Mas a inferência de tipos ou dados de conhecimento do espaço também ajudam o usuário navegar pela estrutura para o destino.

Para criar uma experiência de forma a localizar, preparar um espaço para a experiência e desenvolver uma aplicação que os utilizadores irão interagir com. Estes são os passos conceituais:

1. **Planear o espaço**: Decida que localizações dentro do espaço de fará parte da experiência de forma a localizar. Em nossos cenários, o supervisor de fábrica ou o coordenador de apresentação de museu poderá decidir que localizações a incluir a experiência de forma a localizar.
2. **Ligar as âncoras**: Visite as localizações escolhidas criar âncoras espaciais. Pode fazê-lo num modo de administrador da aplicação pelo utilizador final ou num aplicativo diferente inteiramente. Irá ligar ou se relacionam com cada âncora para os outros. O serviço mantém estas relações.
3. **Iniciar a experiência de utilizador final**: Os utilizadores executarem a aplicação para localizar uma âncora, que pode estar em qualquer uma das localizações escolhidas. O design geral deve determinar as localizações onde os utilizadores podem introduzir a experiência.
4. **Encontre nas proximidades âncoras**: Depois do utilizador encontra uma âncora, a aplicação pode pedir nas proximidades âncoras. Este procedimento devolve uma posição entre o dispositivo e estes âncoras.
5. **Orientar o usuário**: A aplicação pode utilizar o representam a cada um desses âncoras de fornecer orientações sobre a direção geral do utilizador e a distância. Por exemplo, o feed na aplicação da câmara poderá mostrar um ícone e uma seta para representar cada destino potencial, como mostra a imagem seguinte.
6. **Refinar as orientações**: Como guia-o utilizador, a aplicação periodicamente calcular uma nova posição entre o dispositivo e a âncora de destino. A aplicação continua a refinar as sugestões de orientações para ajudam o utilizador que são recebidos no destino.

    ![Um exemplo de como uma aplicação pode mostrar a orientação de forma a localizar](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Ligar as âncoras

Para criar uma experiência de forma a localizar, primeiro tem de colocar as âncoras nas localizações escolhidas. Nesta secção, vamos supor que administrador da aplicação já concluiu a esse trabalho.

### <a name="connect-anchors-in-a-single-session"></a>Ligar as âncoras numa única sessão

Para ligar as âncoras de:

1. Orientá-lo para a primeira localização e criar âncora um com um CloudSpatialAnchorSession.
2. Orientá-lo para a localização do segundo. A plataforma subjacente do MR/AR controla o movimento.
3. Crie B de âncora com o mesmo CloudSpatialAnchorSession. Âncoras A e B estão agora ligadas. O serviço de âncoras geográficos mantém esta relação.
4. Continue o procedimento para as âncoras restantes.

### <a name="connect-anchors-in-multiple-sessions"></a>Ligar as âncoras em múltiplas sessões

Pode ligar as âncoras geográficos através de várias sessões. Usando esse método, pode criar e ligar-se alguns âncoras de uma só vez e, em seguida, mais tarde criar e ligar mais âncoras. 

Para ligar as âncoras através de várias sessões:

1. A aplicação cria algumas âncoras no um CloudSpatialAnchorSession. 
2. Noutra altura, a aplicação localiza um destes âncoras (por exemplo, A de âncora) utilizando um novo CloudSpatialAnchorSession.
3. Orientá-lo para uma nova localização. A plataforma subjacente de realidade mista ou realidade aumentada controla o movimento.
4. Crie âncora C com o mesmo CloudSpatialAnchorSession. Âncoras A, B e C estão agora ligadas. O serviço de âncoras geográficos mantém esta relação.

Pode continuar a este procedimento para âncoras mais e mais sessões ao longo do tempo.

### <a name="verify-anchor-connections"></a>Verifique as ligações de âncora

A aplicação pode verificar que duas âncoras estão ligadas através da emissão de uma consulta para âncoras próximas. Quando o resultado da consulta contém a âncora de destino, a ligação de âncora é verificada. Se as âncoras não estão ligadas, pode experimentar a aplicação para ligá-los novamente. 

Aqui estão algumas razões âncoras falhar para ligar:

* Na realidade mista ou realidade aumentada plataforma subjacente perdida o controle durante o processo de ligação de âncoras.
* Devido a um erro de rede durante a comunicação com o serviço de âncoras espaciais, não foi possível persistir a ligação de âncora.

### <a name="find-sample-code"></a>Encontrar o código de exemplo

Para obter o código de exemplo que mostra como ligar as âncoras e fazer nas proximidades de consultas, consulte [aplicações de exemplo de âncoras geográficos](https://github.com/Azure/azure-spatial-anchors-samples).
