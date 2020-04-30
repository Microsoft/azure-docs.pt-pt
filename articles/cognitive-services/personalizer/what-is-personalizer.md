---
title: O que é o Personalizador?
description: Personalizer é um serviço de API baseado na nuvem que lhe permite escolher a melhor experiência para mostrar aos seus utilizadores, aprendendo com o seu comportamento em tempo real.
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 3ae425479d764c0a6bf6c63bdd54a964c48af8b6
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687265"
---
# <a name="what-is-personalizer"></a>O que é o Personalizador?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O Azure Personalizer é um serviço de API baseado na nuvem que ajuda a aplicação do seu cliente a escolher o melhor item de _conteúdo_ único para mostrar a cada utilizador. O serviço seleciona o melhor item, a partir de itens de conteúdo, com base em informações coletivas em tempo real que fornece sobre conteúdo e contexto.

Depois de apresentar o item de conteúdo ao utilizador, o seu sistema monitoriza o comportamento do utilizador e reporta uma pontuação de recompensa ao Personalizer para melhorar a sua capacidade de selecionar o melhor conteúdo com base na informação de contexto que recebe.

**O conteúdo** pode ser qualquer unidade de informação, como texto, imagens, urls ou e-mails que pretende selecionar para mostrar ao seu utilizador.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Como é que o Personalizer seleciona o melhor item de conteúdo?

O Personalizer utiliza a **aprendizagem** de reforço para selecionar o melhor item _(ação)_ com base no comportamento coletivo e pontuações de recompensa em todos os utilizadores. As ações são os itens de conteúdo, como artigos noticiosos, filmes específicos ou produtos para escolher.

A chamada **Rank** leva o item de ação, juntamente com as características da ação, e características de contexto para selecionar o item de ação superior:

* **Ações com funcionalidades** - itens de conteúdo com funcionalidades específicas de cada item
* **Características de contexto** - funcionalidades dos seus utilizadores, do seu contexto ou do seu ambiente ao utilizar a sua app

A chamada Rank devolve o ID de que conteúdo, __ação,__ para mostrar ao utilizador, no campo **DeAção recompensa.**
A __ação__ mostrada ao utilizador é escolhida com modelos de machine learning, tentando maximizar a quantidade total de recompensas ao longo do tempo.

Vários exemplos são:

|Tipo do conteúdo|**Ações (com características)**|**Características do contexto**|Id de ação de recompensa devolvida<br>(exibir este conteúdo)|
|--|--|--|--|
|Lista de notícias|a. `The president...`(nacional, política, [texto])<br>b. `Premier League ...`(global, desporto, [texto, imagem, vídeo])<br> c. `Hurricane in the ...`(regional, clima, [texto,imagem]|As notícias do dispositivo são lidas a partir de<br>Mês, ou temporada<br>|um`The president...`|
|Lista de filmes|1. `Star Wars` (1977, [ação, aventura, fantasia], George Lucas)<br>2. `Hoop Dreams` (1994, [documentário, desporto], Steve James<br>3. `Casablanca` (1942, [romance, drama, guerra], Michael Curtiz)|Filme de dispositivo é assistido a partir de<br>tamanho do ecrã<br>Tipo de utilizador<br>|3.`Casablanca`|
|Lista de produtos|i. `Product A`(3 kg, $$$$$$, entrega em 24 horas)<br>ii. `Product B`(20 kg, $$, 2 semanas de envio com alfândega)<br>iii. `Product C`(3 kg, $$$$, entrega em 48 horas)|As compras de dispositivos são lidas a partir de<br>Nível de utilização<br>Mês, ou temporada|ii. `Product B`|

O personalizer usou a aprendizagem de reforço para selecionar a melhor ação, conhecida como ID de ação de _recompensa,_ com base numa combinação de:
* Modelo treinado - informação passada que o serviço Personalizer recebeu
* Dados atuais - Ações específicas com funcionalidades e características de contexto

## <a name="when-to-call-personalizer"></a>Quando ligar para personalizar

A [API](https://go.microsoft.com/fwlink/?linkid=2092082) do Personalizer's **Rank** é chamada _sempre_ que apresentar conteúdo, em tempo real. Isto é conhecido como um **evento,** notado com um ID de _evento._

A [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) **de Recompensa** personalizada pode ser chamada em tempo real ou atrasada para melhor se adaptar à sua infraestrutura. Determina a pontuação da recompensa com base nas necessidades do seu negócio. O resultado da recompensa é entre 0 e 1. Isso pode ser um único valor, como 1 para o bem, e 0 para o mal, ou um número produzido por um algoritmo que criatendo os seus objetivos e métricas de negócio.

## <a name="personalizer-content-requirements"></a>Requisitos de conteúdo personalizado

Utilize o Personalizer quando estiver satisfeito:

* Tem um conjunto limitado de itens (máx. Se tiver uma lista maior, [utilize um motor de recomendação](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) para reduzir a lista para 50 itens.
* Tem informações que descrevem o conteúdo que deseja classificado: _ações com funcionalidades_ e características de _contexto._
* Tem um mínimo de ~1k/dia de eventos relacionados com conteúdo para personalizar para ser eficaz. Se o Personalizer não receber o tráfego mínimo necessário, o serviço demora mais tempo a determinar o único melhor item de conteúdo.

Uma vez que a Personalizer utiliza informação coletiva em quase tempo real para devolver o único melhor item de conteúdo, o serviço não:
* Persistir e gerir informações sobre o perfil do utilizador
* Registar as preferências ou histórico dos utilizadores individuais
* Exigir conteúdo limpo e rotulado

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Como conceber e implementar o Personalizer para a sua aplicação de cliente

1. [Design](concepts-features.md) e plano para conteúdos, **_ações_** e **_contexto._** Determine o algoritmo de recompensa para a pontuação da **_recompensa._**
1. Cada [Recurso Personalizer](how-to-settings.md) que cria é considerado 1 Learning Loop. O loop receberá as chamadas Rank e Reward para esse conteúdo ou experiência do utilizador.
1. Adicione personalizer ao seu website ou sistema de conteúdo:
    1. Adicione uma chamada **de Rank** ao Personalizer na sua aplicação, website ou sistema para determinar o melhor item de _conteúdo_ único antes de o conteúdo ser mostrado ao utilizador.
    1. Exiba o melhor item de _conteúdo_ único, que é o ID de ação de _recompensa_devolvido, ao utilizador.
    1. Aplique _algoritmo_ suplicou informações recolhidas sobre como o utilizador se comportou, para determinar a pontuação da **recompensa,** tais como:

        |Comportamento|Pontuação de recompensa calculada|
        |--|--|
        |Utilizador selecionado melhor, único item de _conteúdo_ (ID de ação de recompensa)|**1**|
        |Utilizador selecionado outros conteúdos|**0**|
        |O utilizador fez uma pausa, percorrendo indecisamente, antes de selecionar o melhor item de _conteúdo_ único (Id de ação de recompensa)|**0,5**|

    1. Adicione uma chamada **de recompensa** enviando uma pontuação de recompensa entre 0 e 1
        * Imediatamente após mostrar o seu conteúdo
        * Ou algum tempo mais tarde num sistema offline
    1. [Avalie o seu loop](concepts-offline-evaluation.md) com uma avaliação offline após um período de utilização. Uma avaliação offline permite-lhe testar e avaliar a eficácia do Serviço personalizador sem alterar o seu código ou afetar a experiência do utilizador.

## <a name="next-steps"></a>Passos seguintes


* [Como funciona o Personalizador](how-personalizer-works.md)
* [O que é a Aprendizagem de Reforço?](concepts-reinforcement-learning.md)
* [Conheça as funcionalidades e ações para o pedido de Rank](concepts-features.md)
* [Saiba determinar a pontuação para o pedido de Recompensa](concept-rewards.md)
* [Guias de Início Rápido](sdk-learning-loop.md)
* [Tutorial](tutorial-use-azure-notebook-generate-loop-data.md)
* [Use a demonstração interativa](https://personalizationdemo.azurewebsites.net/)
