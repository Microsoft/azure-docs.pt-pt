---
title: Orientações para experiências de âncora eficazes
description: Diretrizes e considerações para criar e localizar as âncoras de forma eficaz utilizando âncoras espaciais Azure.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: a6bc500516de7e554c38a335ea57519a39c8f602
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "95487508"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Crie uma experiência de âncora eficaz usando âncoras espaciais Azure

Este artigo fornece diretrizes e considerações para ajudá-lo a criar e localizar âncoras de forma eficaz usando âncoras espaciais Azure.

## <a name="anchor-improvement-over-time"></a>Melhoria da âncora ao longo do tempo

Com âncoras espaciais Azure, cada vez que localiza âncoras, tentamos melhorar a qualidade das futuras operações de localização. Fazemos isso usando os dados ambientais recolhidos para aumentar a informação visual sobre as âncoras que procuramos. Este processo é executado sob o capot e é uma otimização offline gerida pelo serviço Azure Spatial Anchors para otimizar para o seu ambiente. Os dados adicionais recolhidos durante cada operação constroem uma compreensão mais forte do ambiente. Isto melhora a qualidade e permite-lhe localizar melhor as âncoras através da mudança de ambiente, da passagem do tempo e dos utilizadores que olham para âncoras de diferentes ângulos e perspetivas.

## <a name="good-anchors"></a>Boas âncoras

Embora as Âncoras Espaciais Azure tentem melhorar a qualidade das âncoras ao longo do tempo, também é importante investir tempo na educação ou orientação dos utilizadores na sua experiência de utilizador (UX) para criar boas âncoras. Ao investir na criação de boas âncoras à frente, ajuda os utilizadores finais a encontrar âncoras de forma fiável:

- Através de diferentes dispositivos.
- Em vários momentos.
- Em diferentes condições de iluminação.
- Das perspetivas desejadas dentro do espaço.

## <a name="static-and-dynamic-locations"></a>Localizações estáticas e dinâmicas

Parte de desenhar a experiência de âncora é escolher os locais. As localizações serão estáticas e definidas por um administrador do espaço? Ou serão dinâmicos e definidos pelo utilizador?

Um gerente de loja de retalho pode querer uma experiência estática na loja para cativar os utilizadores a visitar. Mas o desenvolvedor de um jogo de tabuleiro de realidade mista provavelmente quereria deixar os utilizadores escolherem onde jogar.

Para localizações estáticas, pode ensinar os administradores a passar o tempo a curar o espaço com boas âncoras.

Para locais dinâmicos, deve pensar em como ensina ou guia os utilizadores no seu UX para criar boas âncoras.

## <a name="stable-visual-features"></a>Características visuais estáveis

Os sistemas de rastreio visual utilizados em dispositivos de realidade mista e de realidade aumentada dependem de características visuais do ambiente. Para obter a experiência mais fiável:

- *Crie* âncoras em locais que tenham características visuais estáveis (isto é, características que não mudam frequentemente).

- *Não* crie âncoras em grandes superfícies em branco que não tenham características distintivas.

- *Não* crie âncoras em materiais altamente reflexivos.

- *Não* crie âncoras em superfícies onde o padrão se repita, como tapete ou papel de parede.

![Exemplos de um bom ambiente para âncoras e um mau ambiente para âncoras](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Várias perspetivas de visualização

Ao criar uma âncora, pense nas pessoas que mais tarde tentarão localizar a âncora.

Considere, por exemplo, uma âncora no meio de uma sala que tem duas portas. É provável que queira permitir que os utilizadores entrem na sala a partir de qualquer porta. Ao criar a âncora, terá de digitalizar a sua posição a partir de ambas as portas. Altera-se as perspetivas para capturar dados ambientais em torno da âncora para que os utilizadores possam localizar a âncora a partir de qualquer porta.

Em geral, ao criar uma âncora, digitalize-a a partir das perspetivas das pessoas que tentarão localizá-la. Por isso, se está a colocar conteúdo virtual numa escultura ao ar livre, faz sentido andar à volta da escultura, enquanto a digitaliza, enquanto cria a âncora. Se a sua âncora está no canto de uma sala, só há uma direção para se aproximar. Ao criar esta âncora, pode digitalizá-la apenas a partir desta perspetiva.

## <a name="multiple-anchors"></a>Múltiplas âncoras

A iluminação pode fazer a diferença nas funcionalidades visuais que uma aplicação deteta. Âncoras criadas com luz natural forte podem ser difíceis de localizar em luz artificial, e vice-versa.

Se tiver este problema, pode ajudar a criar duas âncoras. No mesmo local, crie uma âncora à luz do dia e outra à luz artificial. A sua aplicação pode então consultar ambas as âncoras. Quando uma das âncoras estiver localizada, a aplicação terá uma pose para a âncora.

Da mesma forma, em ambientes onde as características visuais mudam porque a maioria dos objetos se movem, várias âncoras podem ajudar. Quando uma âncora se torna demasiado difícil de encontrar devido a mudanças significativas no ambiente, pode substituir a âncora por uma nova. Você pode fazê-lo, por exemplo, em uma loja de retalho onde o layout é atualizado a cada poucos meses.

## <a name="targets-and-rooms"></a>Alvos e quartos

Em muitos casos, uma âncora é um ponto de entrada para a experiência da sua aplicação. Você vai querer passar por este passo de forma rápida e fiável para que os utilizadores possam entrar na sua experiência. Passar tempo na forma como os utilizadores vão encontrar as suas âncoras é um passo importante de design. É útil pensar em encontrar âncoras em termos de dois cenários amplos: alvos e *quartos.* 

### <a name="targets"></a>Targets

No cenário alvo, a localização de uma âncora é bem conhecida. Por exemplo, numa aplicação de pintura de realidade mista fictícia, um utilizador coloca uma tela virtual na parede. Ela instrui os outros utilizadores na sala a apontar os seus dispositivos para o mesmo lugar na parede para localizar a âncora e iniciar a experiência.

Outro exemplo de um cenário alvo pode ser um sinal num café onde se lê: "Scaneie para negócios." O café colocou uma âncora aqui. À medida que os utilizadores digitalizam o sinal, localizam a âncora e entram na experiência de realidade aumentada para encontrar ofertas no café.

No cenário alvo, as fotos podem ajudar. Se mostrar aos utilizadores uma foto do alvo pretendido no seu dispositivo, eles podem identificar rapidamente o que digitalizar no mundo real. Por exemplo, pode ajudar os seus utilizadores a chegar em geral a um alvo pretendido usando GPS. Quando o utilizador chega, a sua aplicação mostra uma foto do alvo. O utilizador olha ao redor do espaço, encontra o alvo, e digitaliza para a âncora.

![Ilustração de uma âncora, mostrando uma foto do alvo no dispositivo móvel de um utilizador](./media/start-here-edit.png)

### <a name="rooms"></a>Quartos

No cenário da sala, os utilizadores entram num espaço simplesmente sabendo que há uma âncora aqui em algum lugar. Os utilizadores digitalizam o espaço com o seu dispositivo e localizam rapidamente a âncora.

Esta experiência normalmente requer que você crie âncoras bem curadas, como discutido em Várias perspetivas de visualização. Se digitalizar a sala de muitas perspetivas quando criou a âncora, os utilizadores podem digitalizar em qualquer lugar quando tentam localizá-la.

![Ilustração de como um utilizador pode digitalizar uma sala para encontrar uma âncora](./media/scan-room.png)

Essencialmente, passa mais tempo a digitalizar o espaço quando cria a âncora para que os utilizadores posteriores possam digitalizar e localizar a âncora rapidamente. À medida que crias a tua experiência, terás de considerar esta importante troca.

O exemplo da aplicação de pintura de realidade mista que discutimos anteriormente não funciona bem como um cenário de sala. Aqui, o utilizador que coloca a âncora quer que outros se juntem à experiência rapidamente. Os utilizadores não querem esperar para iniciar a experiência até que o quarto esteja bem digitalizado. Como todos os utilizadores sabem exatamente onde apontar o seu dispositivo para localizar as âncoras, este exemplo funciona melhor como cenário alvo.

## <a name="anchor-location"></a>Localização da âncora

Os sistemas de rastreio visual dependem das características visuais num ambiente. Quanto mais características visuais uma varredura inclui, maior a probabilidade de encontrar uma âncora.

Siga as diretrizes gerais desta secção para construir um UX que incentive uma digitalização útil do ambiente.

Primeiro, se o utilizador não localizar uma âncora dentro de alguns segundos, a aplicação deve incentivar os utilizadores a mover o dispositivo para capturar mais perspetivas. A aplicação também pode incentivar os utilizadores a deslocarem-se pelo ambiente para procurarem a âncora de mais perspetivas. Quanto mais perspetivas de recurso o dispositivo vir, melhor, pois aumentará a probabilidade de uma âncora estar localizada, e também irá recolher mais dados ambientais que serão utilizados para melhorar a qualidade da âncora.

Para cenários-alvo, peça ao utilizador que se mova em torno do alvo para vê-lo de diferentes perspetivas. Por outras palavras, peça ao utilizador para capturar o alvo de novas perspetivas até que a âncora esteja localizada.

Para cenários de quartos, peça ao utilizador para digitalizar lentamente o quarto. Por exemplo, peça ao utilizador para se virar para capturar 180 graus ou mesmo 360 graus da sala. Ou peça ao utilizador para ver o quarto de uma nova perspetiva.

O método mais significativo é digitalizar através da sala. Uma varredura através da sala captura mais características visuais do ambiente do que uma varredura de uma parede próxima, por exemplo. Uma varredura de uma parede próxima não capturará tantas características visuais úteis do ambiente.

Não é útil mover repetidamente o dispositivo de um lado para o outro quando se procura uma âncora. Isto simplesmente captura os mesmos pontos da mesma perspetiva.

## <a name="experience-tests"></a>Testes de experiência

Neste artigo, discutimos orientações gerais. Com âncoras espaciais, estás a escrever apps que interagem com o mundo real. Por isso, deve dedicar tempo a testar os cenários de âncora da sua aplicação em ambientes reais. Isto é especialmente verdade para ambientes que representam onde espera que os seus utilizadores utilizem a app.
