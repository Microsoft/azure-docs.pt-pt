---
title: Como funciona o Personalizer - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer utiliza machine learning para descobrir qual a ação a utilizar um contexto. Cada loop learning tem um modelo que está preparado exclusivamente nos dados que enviou a ele por meio de classificação e chama de recompensa. Cada loop de aprendizado é completamente independente umas das outras.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 6b2237f27fba5eaf952932cd6592052649400b96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027132"
---
# <a name="how-personalizer-works"></a>Como funciona a Personalizer

Personalizer utiliza machine learning para descobrir qual a ação a utilizar um contexto. Cada loop de aprendizado tem um modelo que está preparado exclusivamente nos dados que enviou a ele via **classificação** e **recompensa** chamadas. Cada loop de aprendizado é completamente independente umas das outras. Crie um loop de aprendizagem para cada parte ou o comportamento da sua aplicação que pretende personalizar.

Para cada loop **chamar a API de classificação com** com base no contexto atual, com:

* Lista de ações possíveis: itens a partir do qual pode selecionar a ação de principal de conteúdo.
* Lista de [funcionalidades do contexto](concepts-features.md): dados relevantes contextualmente como usuário, o conteúdo e o contexto.

O **classificação** decide a utilização de uma API:

* _Explorar_: O modelo atual para decidir a melhor ação com base nos últimos dados.
* _Explore_: Selecione outra ação em vez da ação superior.

O **recompensa** API:

* Recolhe dados para preparar o modelo gravando as funcionalidades e as pontuações de recompensa de cada chamada de classificação.
* Usa esses dados para atualizar o modelo com base nas definições especificadas na _Learning política_.

## <a name="architecture"></a>Arquitetura

A imagem seguinte mostra o fluxo de arquitetura de chamar as chamadas de classificação e a recompensa:

![texto alternativo](./media/how-personalizer-works/personalization-how-it-works.png "como funciona a personalização")

1. Personalizer utiliza um modelo de IA interno para determinar a classificação da ação.
1. O serviço de decide se pretende explorar o modelo atual ou explorar novas escolhas para o modelo.  
1. O resultado de classificação é enviado para EventHub.
1. Quando Personalizer recebe a recompensa, a recompensa é enviada para EventHub. 
1. A classificação e a recompensa são correlacionados.
1. É atualizar o modelo de IA com base nos resultados de correlação.
1. O mecanismo de inferência de tipos é atualizado com o novo modelo. 

## <a name="research-behind-personalizer"></a>Pesquisa por trás Personalizer

Personalizer baseia-se a ciência de vanguarda e investigação na área de [aprendizagem por reforço](concepts-reinforcement-learning.md) incluindo documentos, atividades de pesquisa e as áreas em curso de exploração na Microsoft Research.

## <a name="terminology"></a>Terminologia

* **Ciclo de aprendizagem**: Pode criar um loop de aprendizagem para todas as partes do aplicativo que pode se beneficiar da personalização. Se tiver mais de uma experiência para personalizar, crie um loop para cada um. 

* **Ações**: As ações são os itens de conteúdo, por exemplo, produtos ou promoções, à sua escolha. Personalizer escolhe a ação para mostrar aos seus utilizadores, conhecidos como superior a _recompensar ação_, através da API de classificação. Cada ação pode ter submetidos com o pedido de classificação de recursos.

* **Contexto**: Para fornecer uma classificação mais precisa, fornecem informações sobre seu contexto, por exemplo:
    * O utilizador.
    * O dispositivo estão no. 
    * A hora atual.
    * Outros dados sobre a situação atual.
    * Dados históricos sobre o utilizador ou o contexto.

    Seu aplicativo específico pode ter informações de contexto diferente. 

* **[Funcionalidades](concepts-features.md)**: Uma unidade de informações sobre um item de conteúdo ou a um contexto de usuário.

* **Recompensa**: Uma medida de como o usuário respondesse para a API de classificação devolveu ação, como uma classificação de entre 0 e 1. O valor de 0 a 1 é definido por sua lógica de negócio, com base em como a opção ajudou a atingir suas metas de negócios de personalização. 

* **Exploração**: O serviço de Personalizer está explorando quando, em vez de retornar a melhor ação, ela escolhe uma ação diferente para o utilizador. O serviço de Personalizer evita descompassos, estagnação e pode adaptar ao comportamento do utilizador em curso ao explorar. 

* **Duração experimental**: A quantidade de tempo que o serviço de Personalizer aguarda uma recompensa, a partir do momento em que a chamada de classificação aconteceu para esse evento.

* **Eventos Inativos**: Um evento inativo é um em que é chamado de classificação, mas não tiver a certeza do que usuário nunca verá o resultado, devido a decisões de aplicação de cliente. Eventos Inativos permitem-lhe criar e armazenar os resultados de personalização, em seguida, optar por eliminá-las mais tarde sem afetar o modelo de machine learning.

* **modelo**: Um modelo de Personalizer captura todos os dados, ficou a conhecer o comportamento dos utilizadores, a obtenção de dados de treinamento da combinação dos enviar para chamadas de classificação e a recompensa e com um comportamento de treinamento, determinado pela política de aprendizagem de argumentos. 

## <a name="next-steps"></a>Passos Seguintes

Compreender [onde pode utilizar Personalizer](where-can-you-use-personalizer.md).