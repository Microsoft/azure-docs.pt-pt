---
title: O que é o Personalizador?
titleSuffix: Azure Cognitive Services
description: O personalizador é um serviço de API baseado em nuvem que permite que você escolha a melhor experiência para mostrar aos seus usuários, aprendendo com o comportamento em tempo real.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 01/21/2020
ms.author: diberry
ms.openlocfilehash: 685e4f841ba077c81f46a27af8040fa6c57c0021
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846209"
---
# <a name="what-is-personalizer"></a>O que é o Personalizador?

O personalizador do Azure é um serviço de API baseado em nuvem que ajuda o aplicativo cliente a escolher o melhor item de _conteúdo_ único para mostrar cada usuário. O serviço seleciona o melhor item, de itens de conteúdo, com base em informações em tempo real coletiva que você fornece sobre conteúdo e contexto.

Depois de apresentar o item de conteúdo ao seu usuário, o sistema monitora o comportamento do usuário e relata uma pontuação de recompensa de volta ao personalizador para melhorar sua capacidade de selecionar o melhor conteúdo com base nas informações de contexto que ele recebe.

O **conteúdo** pode ser qualquer unidade de informações, como texto, imagens, URLs ou emails que você deseja selecionar para mostrar ao usuário.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Como o personalizador seleciona o melhor item de conteúdo?

O personalizador usa o **reforço Learning** para selecionar o melhor item (_ação_) com base no comportamento coletivo e recompensar pontuações em todos os usuários. As ações são itens de conteúdo, como artigos de notícias, filmes específicos ou produtos dos quais escolher.

A chamada de **classificação** usa o item de ação, juntamente com os recursos da ação e os recursos de contexto para selecionar o item de ação superior:

* **Ações com recursos** -itens de conteúdo com recursos específicos para cada item
* **Recursos de contexto** -recursos de seus usuários, seu contexto ou seu ambiente ao usar seu aplicativo

A chamada de classificação retorna a ID da qual item de conteúdo, __Action__, para mostrar ao usuário, no campo **ID da ação de recompensa** .
A __ação__ mostrada para o usuário é escolhida com modelos de aprendizado de máquina, tentando maximizar a quantidade total de recompensas ao longo do tempo.

Vários cenários de exemplo são:

|Tipo de conteúdo|**Ações (com recursos)**|**Recursos de contexto**|ID da ação de recompensa retornada<br>(exibir este conteúdo)|
|--|--|--|--|
|Lista de notícias|a. `The president...` (nacional, política, [texto])<br>b. `Premier League ...` (global, desporto, [texto, imagem, vídeo])<br> c. `Hurricane in the ...` (regional, clima, [texto,imagem]|As notícias do dispositivo são lidas de<br>Mês ou temporada<br>|um `The president...`|
|Lista de filmes|1. `Star Wars` (1977, [ação, aventura, fantasia], George Lucas)<br>2. `Hoop Dreams` (1994, [documentário, desporto], Steve James<br>3. `Casablanca` (1942, [romance, drama, guerra], Michael Curtiz)|O filme do dispositivo é observado em<br>tamanho da tela<br>Tipo de usuário<br>|3. `Casablanca`|
|Lista de produtos|i. `Product A` (3 kg, US $ $ $ $ $, entrega em 24 horas)<br>ii. `Product B` (20 kg, US $ $, 2 semanas entregues com alfândega)<br>iii. `Product C` (3 kg, US $ $ $, entrega em 48 horas)|A compra do dispositivo é lida<br>Camada de gastos do usuário<br>Mês ou temporada|ii. `Product B`|

O personalizador usou o reforço Learning para selecionar a melhor ação, conhecida como _ID de ação de recompensa_, com base em uma combinação de:
* Modelo treinado – informações antigas do serviço personalizado recebido
* Ações específicas de dados atuais com recursos e recursos de contexto

## <a name="when-to-call-personalizer"></a>Quando chamar o personalizador

A [API](https://go.microsoft.com/fwlink/?linkid=2092082) de **classificação** do personalizador é chamada _toda vez_ que você apresenta conteúdo, em tempo real. Isso é conhecido como um **evento**, anotado com uma _ID de evento_.

A [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) de **recompensa** do personalizador pode ser chamada em tempo real ou atrasada para se adequar melhor à sua infraestrutura. Você determina a pontuação de recompensa com base em suas necessidades de negócios. Isso pode ser um valor único, como 1 para bom, e 0 para ruim, ou um número produzido por um algoritmo criado considerando suas metas e métricas de negócios.

## <a name="personalizer-content-requirements"></a>Requisitos de conteúdo do personalizador

Use o personalizador quando seu conteúdo:

* Tem um conjunto limitado de itens (máximo de ~ 50) para selecionar. Se você tiver uma lista maior, [use um mecanismo de recomendação](where-can-you-use-personalizer.md#use-personalizer-with-recommendation-engines) para reduzir a lista até 50 itens.
* Tem informações que descrevem o conteúdo que você deseja classificar: _ações com_ recursos e _recursos de contexto_.
* Tem um mínimo de ~ 1K/dia de eventos relacionados ao conteúdo para que o personalizado seja eficaz. Se o personalizador não receber o tráfego mínimo necessário, o serviço levará mais tempo para determinar o único item de conteúdo mais adequado.

Como o personalizador usa informações coletivamente quase em tempo real para retornar o único item de conteúdo mais adequado, o serviço não:
* Manter e gerenciar informações de perfil do usuário
* Registrar preferências ou histórico de usuários individuais
* Exigir conteúdo limpo e rotulado

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Como projetar e implementar o personalizador para seu aplicativo cliente

1. [Projete](concepts-features.md) e planeje o conteúdo, as **_ações_** e o **_contexto_** . Determine o algoritmo de recompensa da Pontuação de **_recompensa_** .
1. Cada [recurso personalizado](how-to-settings.md) que você cria é considerado 1 loop de aprendizado. O loop receberá as chamadas Rank e recompensa para o conteúdo ou a experiência do usuário.
1. Adicione o personalizador ao seu site ou ao sistema de conteúdo:
    1. Adicione uma chamada de **classificação** ao personalizador no seu aplicativo, site ou sistema para determinar o melhor item de _conteúdo_ único antes de o conteúdo ser mostrado ao usuário.
    1. Exiba o melhor item de _conteúdo_ único, que é a _ID da ação de recompensa_retornada, para o usuário.
    1. Aplique o _algoritmo_ para obter informações coletadas sobre como o usuário se comporta, para determinar a pontuação de **recompensa** , como:

        |Comportamento|Pontuação de recompensa calculada|
        |--|--|
        |Melhor selecionado pelo usuário, item de _conteúdo_ único (ID da ação de recompensa)|**1**|
        |Outro conteúdo foi selecionado pelo usuário|**0**|
        |O usuário está em pausa, rolando de forma indecisiva, antes de selecionar melhor, item de _conteúdo_ único (ID da ação de recompensa)|**0,5**|

    1. Adicionar uma chamada de **recompensa** enviando uma pontuação de recompensa entre 0 e 1
        * Imediatamente após mostrar seu conteúdo
        * Ou algum tempo depois em um sistema offline
    1. [Avalie seu loop](concepts-offline-evaluation.md) com uma avaliação offline após um período de uso. Uma avaliação offline permite testar e avaliar a eficácia do serviço personalizado sem alterar seu código nem afetar a experiência do usuário.

## <a name="next-steps"></a>Passos seguintes


* [Como o personalizador funciona](how-personalizer-works.md)
* [O que é o reforço Learning?](concepts-reinforcement-learning.md)
* [Saiba mais sobre os recursos e as ações para a solicitação de classificação](concepts-features.md)
* [Saiba como determinar a pontuação da solicitação de recompensa](concept-rewards.md)
* [Inícios rápidos](sdk-learning-loop.md)
* [Tutorial](tutorial-use-azure-notebook-generate-loop-data.md)
* [Usar a demonstração interativa](https://personalizationdemo.azurewebsites.net/)
