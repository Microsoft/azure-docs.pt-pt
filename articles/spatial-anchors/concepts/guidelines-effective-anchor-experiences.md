---
title: Diretrizes para experiências de âncora eficaz que utilizam as âncoras espaciais do Azure | Documentos da Microsoft
description: Diretrizes e as considerações para criar e localizar as âncoras com eficiência usando âncoras espaciais do Azure.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4714ea7aa4bf55f7cbd4500977b09505788233dd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895809"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Criar uma experiência de âncora efetiva com âncoras espaciais do Azure

Este artigo fornece diretrizes e considerações para o ajudar a efetivamente cria e localizar as âncoras, utilizando as âncoras espaciais.

## <a name="good-anchors"></a>Boa âncoras

Âncoras geográficos ajuda-o a criar âncoras boa. É importante investir tempo na treinar ou a orientação dos usuários na sua experiência de usuário (UX) criar âncoras boa. Ao investir na criação de boa âncoras com antecedência, ajudar os utilizadores finais, de forma fiável encontrar as âncoras de:

- Em diferentes dispositivos.
- Em vários momentos.
- Em condições de iluminação diferentes.
- Das perspectivas desejadas dentro do espaço de.

## <a name="static-and-dynamic-locations"></a>Localizações estáticas e dinâmicas

Parte de projetar a experiência de âncora é a escolha dos locais. As localizações será estático e definido por um administrador do espaço? Ou eles serão dinâmicos e definidos pelo utilizador?

Gerente de loja de varejo pode querer uma experiência na loja estática para convencer usuários a visitar. Mas o desenvolvedor de um jogo de quadro de realidade mista seria aconselhável permitir aos utilizadores escolher onde a reproduzir.

Para localizações estáticas, pode te ensinar administradores gastar tempo moderação o espaço com boa âncoras.

Para os locais dinâmicos, deve pensar em como ensinar ou orientam os utilizadores na sua experiência do Usuário para criar âncoras boa.

## <a name="stable-visual-features"></a>Recursos do visual estáveis

Sistemas de controle de Visual utilizado na realidade mista e dispositivos de realidade aumentada contam com recursos visuais do ambiente. Para obter a experiência mais confiável:  

- *Fazer* criar âncoras em locais com recursos visuais estáveis (ou seja, os recursos que não são alterados frequentemente).

- *Não* criar âncoras em superfícies em branco grandes que não tenham nenhuma distinção características.

- *Não* criar âncoras em materiais altamente reflexiva.

- *Não* criar âncoras nas superfícies em que o padrão se repete como alcatifa ou imagem de fundo.

![Exemplos de um ambiente de bom para âncoras e um ambiente incorreto para âncoras](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Várias perspectivas de visualização

Ao criar uma âncora, considere as pessoas que mais tarde irão tentar localizar a âncora.

Considere, por exemplo, uma âncora no meio de uma sala com duas portas. É provável que deseja permitir que os utilizadores entrarem na sala a partir de qualquer porta. Ao criar a âncora, terá de analisar sua posição de ambos os doorways. Alterar perspetivas para capturar dados de ambiente em torno de âncora para que os utilizadores possam localizar a âncora de qualquer porta.

Em geral, ao criar uma âncora, analise-o de perspectivas das pessoas que tentarão localizá-la. Portanto, se estiver colocando o conteúdo virtual num sculpture equipamentos esportivos, faz sentido andando sculpture, ao procurar, à medida que cria a âncora. Se sua âncora no canto de uma sala, há apenas uma direção de abordá-lo a partir. Ao criar este âncora, pode lê-lo apenas dessa perspectiva.

## <a name="multiple-anchors"></a>Vários âncoras

Iluminação pode fazer uma diferença nos recursos visual que Deteta uma aplicação. Âncoras criadas no forte light natural talvez seja difícil localizar sob a perspectiva artificial e vice-versa.  

Se tiver este problema, pode ajudar a criar duas âncoras. No mesmo lugar, crie uma âncora na hora de Verão e outro sob a perspectiva artificial. A aplicação, em seguida, pode consultar as duas âncoras. Quando qualquer um dos âncora é localizada, a aplicação terá uma posição para a âncora. 

Da mesma forma, em ambientes onde os recursos visual alterar porque a maioria dos objetos mover, as âncoras de vários podem ajudar. Quando uma âncora se torna muito difícil encontrar devido a alterações significativas no ambiente, pode substituir a âncora de por um novo. Pode fazer isso, por exemplo, numa loja de revenda em que o esquema é atualizado todos os meses.

## <a name="targets-and-rooms"></a>Destinos e salas

Em muitos casos, uma âncora é um ponto de entrada para a experiência da sua aplicação. Vai querer dar este passo, o mais rapidamente e fiável para que os utilizadores podem introduzir a sua experiência. Perderem tempo em como os utilizadores irão encontrar seu âncoras é uma etapa de design importantes. É útil pensar sobre como localizar as âncoras em termos de dois cenários ampla: *destinos* e *salas*.

### <a name="targets"></a>Destinos

No cenário de destino, a localização de uma âncora é bem conhecida. Por exemplo, num aplicativo de pintura de realidade mista fictícia, um usuário coloca uma tela virtual na parede. Ela instrui os outros utilizadores na sala para apontar os respetivos dispositivos no mesmo lugar na parede para localizar a âncora e comece a experiência do utilizador.  

Outro exemplo de um cenário de destino pode ser um início de sessão numa cafeteria que lê, "Análise de negócios". O café tiver efetuado uma âncora aqui. Como o início de sessão de análise de utilizadores, eles localizar a âncora e introduza a experiência de realidade aumentada para encontrar ofertas no café.

No cenário de destino, podem ajudar a fotos. Se a mostrar os utilizadores de uma fotografia do destino pretendido no respetivo dispositivo, eles podem identificar rapidamente o que analise no mundo real. Por exemplo, pode ajudar os utilizadores que são recebidos dentro da área geral de um destino pretendido com o GPS. Quando o usuário chega, a aplicação mostra uma fotografia do destino. O utilizador procura em todo o espaço, localiza o destino e verifica a existência de âncora.

![Ilustração de uma âncora, que mostra uma fotografia do destino no dispositivo móvel de um utilizador](./media/start-here-edit.png)

### <a name="rooms"></a>ambientes

No cenário de espaço, os utilizadores introduzir um espaço apenas saber que há uma âncora aqui em algum lugar. Os utilizadores analisar o espaço com o respetivo dispositivo e localize rapidamente a âncora.

Esta experiência normalmente exige que criar âncoras bem organizadas, conforme discutido [várias perspectivas de visualização](#consider-various-viewing-perspectives). Se analisados a sala de muitas perspectivas quando criou a âncora, os utilizadores podem analisar praticamente qualquer lugar quando tentarem a localizá-la.

![Ilustração de como um utilizador pode analisar um espaço para encontrar uma âncora](./media/scan-room.png)

Essencialmente, gastar mais tempo a analisar o espaço ao criar a âncora para que os utilizadores posteriores podem analisar e localize a âncora rapidamente. Como criar a sua experiência, precisará considerar desta compensação importante.

O exemplo do aplicativo de pintura de realidade mista que abordamos anteriormente não funciona bem como um cenário de espaço. Aqui, o utilizador que coloca a âncora quer que outras pessoas para associar a experiência rapidamente. Os utilizadores não desejam esperar para começar a experiência até que o espaço é bem analisado. Uma vez que todos os utilizadores sabem exatamente onde apontar o seu dispositivo para localizar as âncoras, este exemplo funciona melhor como um cenário de destino.

## <a name="anchor-location"></a>Localização de âncora

Sistemas de controle de Visual contam com os recursos visual num ambiente. As funcionalidades mais visual que inclui uma análise, maior será a probabilidade de encontrar uma âncora.

Siga as diretrizes gerais desta secção para criar uma experiência do Usuário que incentiva uma análise úteis do ambiente.

Em primeiro lugar, se o utilizador não localizar uma âncora dentro de alguns segundos, a aplicação deve incentivar os utilizadores movam o dispositivo para capturar mais perspetivas. A aplicação também pode incentivar os utilizadores movam próprios em todo o ambiente para procurar a âncora de mais perspetivas. As mais perspetivas de funcionalidade que o dispositivo vê, melhor.

Para cenários de destino, peça ao utilizador para mover o destino para vê-la a partir de diferentes perspectivas. Em outras palavras, peça ao utilizador capturar o destino de novas perspectivas, até que a âncora está localizada.

Para cenários de espaço, peça ao utilizador para analisar lentamente sala. Por exemplo, pedir ao utilizador para ativar para capturar a 180 graus ou até mesmo de 360 graus da sala. Ou pedir ao utilizador para ver o espaço de uma perspectiva de novo. 

O método mais significativo é analisar na sala. Uma análise na sala captura mais recursos do visual do ambiente de que uma análise de uma parede próximas, por exemplo. Uma análise de uma parede próximas não capture tantos recursos úteis de visual do ambiente.

Não é útil para repetidamente mover o dispositivo de lado a lado ao procurar uma âncora. Isso simplesmente captura os mesmos pontos da perspectiva do mesmo.

## <a name="experience-tests"></a>Testes de experiência

Neste artigo, discutimos as diretrizes gerais. Com as âncoras espaciais, estiver escrevendo aplicativos que interagem com o mundo real. Por isso, deve dedicar tempo a cenários de âncora da sua aplicação de teste em ambientes reais. Isso é especialmente verdadeiro para ambientes que representam a onde espera que os seus utilizadores para utilizar a aplicação.