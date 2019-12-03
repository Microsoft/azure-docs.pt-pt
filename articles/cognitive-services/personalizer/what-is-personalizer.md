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
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: b5d38ffeda3600fd90c4ee84acdd29ed599886ae
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707957"
---
# <a name="what-is-personalizer"></a>O que é o Personalizador?

O personalizador do Azure é um serviço de API baseado em nuvem que permite que seu aplicativo escolha a melhor experiência para mostrar aos seus usuários, aprendendo com seu comportamento em tempo real coletivo.

* Forneça informações sobre seus usuários e conteúdo e receba a ação principal para mostrar os usuários. 
* Não é necessário limpar e rotular dados antes de usar o personalizador.
* Forneça comentários para o personalizador quando for conveniente para você. 
* Exibir análise em tempo real. 

Veja uma demonstração de [como o personalizador funciona](https://personalizercontentdemo.azurewebsites.net/)

## <a name="how-does-personalizer-work"></a>Como funciona o personalizador?

O personalizador usa modelos de aprendizado de máquina para descobrir qual ação deve ser classificada mais alta em um contexto. O aplicativo cliente fornece uma lista de possíveis ações, com informações sobre elas; e informações sobre o contexto, que podem incluir informações sobre o usuário, o dispositivo, etc. O personalizador determina a ação a ser tomada. Depois que o aplicativo cliente usa a ação escolhida, ele fornece comentários para o personalizador na forma de uma pontuação de recompensa. Depois que os comentários são recebidos, o personalizador atualiza automaticamente seu próprio modelo usado para futuras classificações. Ao longo do tempo, o personalizador treinará um modelo que pode sugerir a melhor ação a ser escolhida em cada contexto com base em seus recursos.

## <a name="how-do-i-use-the-personalizer"></a>Como fazer usar o personalizador?

![Usando o personalizador para escolher qual vídeo mostrar a um usuário](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Escolha uma experiência em seu aplicativo para personalizar.
1. Crie e configure uma instância do serviço de personalização no portal do Azure. Cada instância é um loop personalizado.
1. Use a [API de classificação](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) para chamar o personalizador com informações (_recursos_) sobre seus usuários e o conteúdo (_ações_). Você não precisa fornecer dados limpos e rotulados antes de usar o personalizador. As APIs podem ser chamadas diretamente ou usando SDKs disponíveis para diferentes linguagens de programação.
1. No aplicativo cliente, mostre ao usuário a ação selecionada pelo personalizador.
1. Use a [API de recompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) para fornecer comentários ao personalizador indicando se a ação do personalizador selecionado pelo usuário. Esta é uma _[Pontuação de recompensa](concept-rewards.md)_ .
1. Veja a análise no portal do Azure para avaliar como o sistema está funcionando e como seus dados estão ajudando a personalização.

## <a name="where-can-i-use-personalizer"></a>Onde posso usar o personalizador?

Por exemplo, seu aplicativo cliente pode adicionar personalizador a:

* Personalize o artigo que está destacado em um site de notícias.    
* Otimizar o posicionamento do AD em um site.
* Exibir um "item recomendado" personalizado em um site de compras.
* Sugira elementos de interface do usuário, como filtros, a serem aplicados a uma foto específica.
* Escolha uma resposta de bot de chat para esclarecer a intenção do usuário ou sugerir uma ação.
* Priorize sugestões do que um usuário deve fazer como a próxima etapa em um processo de negócios.

O personalizador não é um serviço para persistir e gerenciar informações de perfil do usuário ou para registrar as preferências ou o histórico de usuários individuais. O personalizado aprende dos recursos de cada interação na ação de um contexto em um único modelo que pode obter recompensas máximas quando ocorrem recursos semelhantes. 

## <a name="personalization-for-developers"></a>Personalização para desenvolvedores

O serviço de personalização tem duas APIs:

* *Classificação*: Use a API de classificação para determinar qual _ação_ mostrar, no _contexto_atual. As ações são enviadas como uma matriz de objetos JSON, com uma ID e informações (_recursos_) sobre cada um; o contexto é enviado como outro objeto JSON. A API retorna a ActionID que seu aplicativo deve renderizar para o usuário.
* *Recompensa*: depois que o usuário interage com seu aplicativo, você mede o quão bem a personalização funcionou como um número entre 0 e 1 e a envia como uma [Pontuação de recompensa](concept-rewards.md). 

![Sequência básica de eventos para personalização](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Passos seguintes

* [O que há de novo no Personalizador?](whats-new.md)
* [Como funciona o personalizador?](how-personalizer-works.md)
* [O que é o reforço Learning?](concepts-reinforcement-learning.md)
* [Saiba mais sobre os recursos e as ações para a solicitação de classificação](concepts-features.md)
* [Saiba como determinar a pontuação da solicitação de recompensa](concept-rewards.md)
* [Usar a demonstração interativa](https://personalizationdemo.azurewebsites.net/)
