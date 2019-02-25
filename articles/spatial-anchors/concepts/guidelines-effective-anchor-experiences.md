---
title: Diretrizes para experiências de âncora eficaz em âncoras espaciais do Azure | Documentos da Microsoft
description: Diretrizes e considerações para criar e localizar as âncoras efetivamente com âncoras espaciais do Azure.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 196958e4818251bd7f2ee78ca472e6f28292d908
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753153"
---
# <a name="guidelines-for-an-effective-anchor-experience-with-azure-spatial-anchors"></a>Diretrizes para uma experiência de âncora efetiva com âncoras espaciais do Azure

Este artigo fornece orientações e considerações para criar e localizar as âncoras efetivamente com âncoras espaciais do Azure.

## <a name="creating-anchors"></a>Criar âncoras

Criar âncoras bom é um dos aspectos mais influenciam de trabalhar com as âncoras espaciais do Azure. É importante investir tempo na treinar ou a orientação dos usuários em sua experiência do Usuário como criar âncoras boa. Ao investir na criação de âncora com antecedência, permite que os utilizadores finais com fiabilidade localizar as âncoras de:

- Em dispositivos
- Em vários momentos
- Em condições de iluminação diferentes
- As perspetivas desejadas dentro do espaço do
- Etc.

Seguem-se as considerações e diretrizes para criar a sua experiência de criação e a localização de âncora.

## <a name="static-and-dynamic-locations"></a>Localizações estáticas e dinâmicas

Parte de projetar a experiência de âncora irá ser a escolha dos locais que participar. As localizações será estático e definido por um administrador do espaço? Ou eles serão dinâmicos e definido pelo utilizador?

Gerente de loja de varejo pode querer uma experiência na loja estática para convencer usuários a visitar. Ao passo que o desenvolvedor de um jogo de quadro de realidade mista provavelmente quer permitir aos utilizadores escolher onde a reproduzir.

Para localizações estáticas, pode instruir os administradores gastar tempo moderação o espaço com boa âncoras.

Para os locais dinâmicos, deve pensar em como dar formação aos ou orientam os utilizadores na sua experiência do Usuário para criar âncoras boa.

## <a name="stable-visual-features"></a>Recursos do visual estáveis

Visual utilizados os sistemas de controlo na realidade mista e realidade aumentada dispositivos contam com recursos visuais do ambiente. Para obter a experiência mais confiável:  

- Crie âncoras em locais com recursos visuais estáveis (ou seja, os recursos que não são alterados frequentemente).

- Não crie âncoras nas grandes superfícies em branco com nenhuma distinção características.

- Não crie âncoras em materiais altamente reflexiva.

- Não crie âncoras nas superfícies em que o padrão apenas for repetida, como o tapete ou o papel de parede.

![Imagens de exemplo que representa um ambiente de bons e mau](./media/stable-visual.png)

## <a name="consider-various-viewing-perspectives"></a>Considere várias perspectivas de visualização

Ao criar uma âncora, considere as pessoas que visitam mais tarde para localizar a âncora.

Considere, por exemplo, uma âncora no meio de uma sala com duas portas. É provável que deseja permitir que os utilizadores que entrarem na sala a partir de qualquer porta de entrada para localizar a âncora. Ao criar a âncora, terá de analisar sua posição de ambos os doorways. Isso-captura de dados de ambiente em torno de âncora de ambas as perspectivas para que os utilizadores possam localizar a âncora de qualquer porta.

Em geral, ao criar uma âncora, devem ser sujeitos a locais diferentes ou perspectivas que espera que as pessoas ser alocado ao tentar localizá-la.  

Se estiver colocando o conteúdo virtual num sculpture exterior, faz sentido andando sculpture, ao olhar para ele, à medida que cria a âncora.  

Por outro lado, se sua âncora é no canto de uma sala, há apenas uma direção de abordá-lo a partir. Ao criar a âncora, ele simplesmente precisa ser analisados a partir dessa perspectiva.

## <a name="multiple-anchors"></a>Vários âncoras

Iluminação pode fazer uma diferença nos recursos visual que são detetados. Âncoras criadas no forte light natural, talvez seja difícil localizar após escuro em artificial iluminação e vice-versa.  

Se ocorrer este problema, pode ajudar a criar duas âncoras – um da hora de Verão e outro em iluminação artificial – no mesmo lugar. A aplicação, em seguida, pode consultar as duas âncoras. Se qualquer um estão localizado, a aplicação terá uma posição para a âncora. 

Da mesma forma, em ambientes onde os recursos visual alterar porque a maioria dos objetos mover, as âncoras de vários podem ajudar. Quando uma âncora se torna muito difícil localizar devido a alterações significativas no ambiente, pode substituí-la com uma nova. Isso pode ser o caso, por exemplo, numa loja de revenda em que o esquema é atualizado todos os meses.

## <a name="targets-and-rooms"></a>Destinos e salas

Em muitos casos, localizar uma âncora representa um ponto de entrada à experiência de exclusivo da sua aplicação. Vai querer dar este passo, o mais rapidamente e fiável para que os utilizadores podem introduzir a sua experiência. Perderem tempo em como os utilizadores irão encontrar seu âncoras é uma etapa de design importantes. É útil pensar sobre isso em termos de dois cenários ampla: **Destinos** e **salas**.

### <a name="targets"></a>Destinos

![Comece por aqui edição](./media/start-here-edit.png)

No cenário de destino, a localização de uma âncora é bem conhecida. Por exemplo, num aplicativo de pintura MR fictício, um usuário coloca uma tela virtual na parede. Ela instrui os outros utilizadores na sala para apontar os respetivos dispositivos no mesmo lugar na parede para localizar a âncora e comece a experiência do utilizador.  

Outro exemplo de um cenário de destino pode ser um sinal de que diz "verificar a existência de negócios" enquanto espera na linha num café. Aqui, café anteriormente tiver efetuado uma âncora. Como o início de sessão de análise de utilizadores, eles localizar a âncora e introduza a experiência de AR de negociações no café.

No cenário de destino, podem ajudar a fotos. Se pode mostrar os utilizadores uma fotografia no respetivo dispositivo do destino pretendido, eles podem identificar rapidamente o que analise no mundo real. Por exemplo, pode ajudar os utilizadores que são recebidos dentro de proximidade geral de um destino pretendido com o GPS. Assim que o usuário chega, a aplicação mostra uma fotografia do destino. O utilizador procura em todo o espaço, localiza o destino e continua para procurar a âncora.

### <a name="rooms"></a>ambientes

![Sala de análise](./media/scan-room.png)

No cenário de espaço, os utilizadores introduzir um espaço apenas saber que há uma âncora aqui em algum lugar. Os utilizadores analisar o espaço com o respetivo dispositivo e localize rapidamente a âncora.

Alcançar essa experiência, normalmente, requer a criação de âncoras bem organizadas conforme discutido [considerar várias perspectivas de visualização](#consider-various-viewing-perspectives) anteriormente. Se sala foi analisada de muitas perspectivas, ao criar a âncora, em seguida, os utilizadores podem analisar praticamente qualquer lugar quando tentar localizá-la.

Essencialmente, a pessoa que cria a âncora gasta mais tempo a analisar o espaço, para que as pessoas que são fornecidos mais tarde podem analisar e localize a âncora de rapidamente. Esta é uma compensação importante que terá de considerar para a sua experiência.

O aplicativo de pintura MR abordado anteriormente é um exemplo que não é ideal como um cenário de espaço. Aqui, o utilizador colocar a âncora quer que os outros para associar a experiência rapidamente. Os utilizadores não pretende esperar até que o espaço é bem digitalizado para iniciar a experiência. Uma vez que todos os utilizadores sabem exatamente onde apontar o seu dispositivo para localizar as âncoras, este cenário melhor é tratado como um cenário de destino.

## <a name="effectively-locating-anchors"></a>Localização com eficiência as âncoras

Sistemas de controle de Visual contam com os recursos visual de um ambiente para a função. As funcionalidades mais visual fazem parte de uma análise, maior será a probabilidade de encontrar uma âncora.

Existem algumas Diretrizes gerais que pode seguir para criar uma experiência de utilizador que incentiva uma análise úteis do ambiente.

Em primeiro lugar, se o utilizador não localizar uma âncora dentro de alguns segundos, a aplicação deve incentivar os utilizadores movam o dispositivo para que mais perspetivas são capturadas.  A aplicação também pode incentivar os próprios usuários para mover todo o ambiente durante a análise de âncora de mais perspetivas. As mais perspetivas do qual o dispositivo vê os mesmos pontos melhor.

Para cenários de destino, peça ao utilizador para mover o destino, ao mesmo tempo visualizá-lo a partir de diferentes perspectivas. Em outras palavras, peça ao utilizador para mover seus pés para capturar o destino de novas perspectivas, até que a âncora está localizada.

Para cenários de espaço, peça ao utilizador para analisar lentamente sala. Por exemplo, peça ao utilizador para girar para capturar 180 ou até mesmo de 360 graus da sala. Ou pedir ao utilizador para mover para uma nova perspetiva na sala. É o meio mais significativo de análise analisar na sala. Esta captura mais recursos do visual do ambiente que indiquem uma parede próximas de verificação. Análise de uma parede próximas não captura como recursos visuais muito útil do ambiente.

Não é útil mover a dispositivo lado-a-lado repetidamente durante a procura de uma âncora. Isso simplesmente capturar os mesmos pontos da perspectiva do mesmo.

## <a name="testing-the-experience"></a>A experiência de teste

O procedimento acima é Diretrizes gerais. Com o Azure geográficos as âncoras, estiver a escrever aplicações que interagem com o mundo real. Como tal, é importante dedicar tempo a cenários de âncora da sua aplicação de teste em ambientes reais. Isso é especialmente verdadeiro para ambientes que são representativos de onde espera que os utilizadores a utilizar a aplicação.