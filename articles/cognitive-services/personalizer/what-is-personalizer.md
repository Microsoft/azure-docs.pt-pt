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
ms.date: 09/19/2019
ms.author: diberry
ms.openlocfilehash: ee17e204082389db19e1ebe463352a40d7ecd5ae
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155247"
---
# <a name="what-is-personalizer"></a>O que é o Personalizador?

O Personalizador do Azure é um serviço de API com base na cloud que lhe permite escolher a melhor experiência para os seus utilizadores, ao aprender com o respetivo comportamento em tempo real.

* Forneça informações sobre seus usuários e conteúdo e receba a ação principal para mostrar os usuários. 
* Não é necessário limpar e rotular dados antes de usar o personalizador.
* Forneça comentários para o personalizador quando for conveniente para você. 
* Exibir análise em tempo real. 
* Use o personalizador como parte de um esforço de ciência de dados maior para validar experimentos existentes.

## <a name="how-does-personalizer-work"></a>Como funciona o personalizador?

O personalizador usa modelos de aprendizado de máquina para descobrir qual ação deve ser classificada mais alta em um contexto. O aplicativo cliente fornece uma lista de possíveis ações, com informações sobre elas; e informações sobre o contexto, que podem incluir informações sobre o usuário, o dispositivo, etc. O personalizador determina a ação a ser tomada. Depois que o aplicativo cliente usa a ação escolhida, ele fornece comentários para o personalizador na forma de uma pontuação de recompensa. Depois que os comentários são recebidos, o personalizador atualiza automaticamente seu próprio modelo usado para futuras classificações. Ao longo do tempo, o personalizador treinará um modelo que pode sugerir a melhor ação a ser escolhida em cada contexto com base em seus recursos.

## <a name="how-do-i-use-the-personalizer"></a>Como fazer usar o personalizador?

![Usando o personalizador para escolher qual vídeo mostrar a um usuário](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Escolha uma experiência em seu aplicativo para personalizar.
1. Crie e configure uma instância do serviço de personalização no portal do Azure. Cada instância é um loop personalizado.
1. Use o SDK para chamar o personalizador com informações (_recursos_) sobre seus usuários e o conteúdo (_ações_). Você não precisa fornecer dados limpos e rotulados antes de usar o personalizador. 
1. No aplicativo cliente, mostre ao usuário a ação selecionada pelo personalizador.
1. Use o SDK para fornecer comentários ao personalizador indicando se a ação do personalizador selecionado pelo usuário. Esta é uma _[Pontuação de recompensa](concept-rewards.md)_ .
1. Veja a análise no portal do Azure para avaliar como o sistema está funcionando e como seus dados estão ajudando a personalização.

## <a name="where-can-i-use-personalizer"></a>Onde posso usar o personalizador?

Por exemplo, seu aplicativo cliente pode adicionar personalizador a:

* Personalize o artigo que está destacado em um site de notícias.    
* Otimizar o posicionamento do AD em um site.
* Exibir um "item recomendado" personalizado em um site de compras.
* Sugira elementos de interface do usuário, como filtros, a serem aplicados a uma foto específica.
* Escolha uma resposta de bot de chat para esclarecer a intenção do usuário ou sugerir uma ação.
* Priorize sugestões do que um usuário deve fazer como a próxima etapa em um processo de negócios.

O personalizador não é um serviço para persistir e gerenciar informações de perfil do usuário ou para registrar as preferências ou o histórico de usuários individuais. O personalizado aprende com os recursos de cada interação na ação um contexto de um único modelo que pode obter recompensas máximas quando ocorrem recursos semelhantes. 

## <a name="personalization-for-developers"></a>Personalização para desenvolvedores

O serviço de personalização tem duas APIs:

* Envie informações (_recursos_) sobre seus usuários e o conteúdo (_ações_) para personalizar. O personalizador responde com a ação superior.
* Envie comentários para o personalizador sobre o quão bem a classificação funcionou como uma [Pontuação de recompensa](concept-rewards.md). 

![Sequência básica de eventos para personalização](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Passos seguintes

* [O que há de novo no Personalizador?](whats-new.md)
* [Como funciona o personalizador?](how-personalizer-works.md)
* [O que é o reforço Learning?](concepts-reinforcement-learning.md)
* [Saiba mais sobre os recursos e as ações para a solicitação de classificação](concepts-features.md)
* [Saiba como determinar a pontuação da solicitação de recompensa](concept-rewards.md)
* [Usar a demonstração interativa](https://personalizationdemo.azurewebsites.net/)
