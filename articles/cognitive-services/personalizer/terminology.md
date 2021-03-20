---
title: Terminologia - Personalizante
description: O personalizar usa terminologia a partir da aprendizagem do reforço. Estes termos são utilizados no portal Azure e nas APIs.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: cd0d85be5447aad0f2a3c37041e7d5d5d047a468
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91777297"
---
# <a name="personalizer-terminology"></a>Terminologia personalizada

O personalizar usa terminologia a partir da aprendizagem do reforço. Estes termos são utilizados no portal Azure e nas APIs.

## <a name="conceptual-terminology"></a>Terminologia conceptual

* **Learning Loop**: Cria-se um recurso Personalizer, chamado ciclo de _aprendizagem,_ para cada parte da sua aplicação que pode beneficiar da personalização. Se tiver mais do que uma experiência para personalizar, crie um loop para cada um.

* **Modelo**: Um modelo personalizante captura todos os dados aprendidos sobre o comportamento do utilizador, obtendo dados de formação a partir da combinação dos argumentos que envia para as chamadas Rank and Reward, e com um comportamento de formação determinado pela Política de Aprendizagem.

* **Modo online**: O comportamento de [aprendizagem](#learning-behavior) padrão para Personalizer onde o seu ciclo de aprendizagem, utiliza machine learning para construir o modelo que prevê a **ação de topo** para o seu conteúdo.

* **Modo aprendiz**: Um comportamento de [aprendizagem](#learning-behavior) que ajuda a iniciar um modelo Personalizer para treinar sem afetar os resultados e ações das aplicações.

## <a name="learning-behavior"></a>Comportamento de aprendizagem:

* **Modo online**: Devolva a melhor ação. O seu modelo irá responder às chamadas rank com a melhor ação e usará chamadas Reward para aprender e melhorar as suas seleções ao longo do tempo.
* **[Modo aprendiz](concept-apprentice-mode.md)**: Aprenda como aprendiz. O seu modelo aprenderá observando o comportamento do seu sistema existente. As chamadas de classificação retornarão sempre a **ação padrão** da aplicação (linha de base).

## <a name="personalizer-configuration"></a>Configuração personalizada

O personalizador é configurado a partir do [portal Azure](https://portal.azure.com).

* **Recompensas**: configurar os valores predefinidos para o tempo de espera de recompensa, recompensa por defeito e política de agregação de recompensas.

* **Exploração**: configurar a percentagem de chamadas rank a usar para exploração

* **Frequência de atualização** do modelo : Com que frequência o modelo é retreinado.

* **Retenção de** dados : Quantos dias de dados podem armazenar. Isto pode ter impacto em avaliações offline, que são usadas para melhorar o seu ciclo de aprendizagem.

## <a name="use-rank-and-reward-apis"></a>Use APIs de classificação e recompensa

* **Classificação**: Dadas as ações com funcionalidades e as funcionalidades de contexto, utilize explorar ou explorar para devolver a ação de topo (item de conteúdo).

    * **Ações**: As ações são os itens de conteúdo, como produtos ou promoções, para escolher. O personalização escolhe a ação de topo (ID de ação de recompensa devolvida) para mostrar aos seus utilizadores através da Rank API.

    * **Contexto**: Para fornecer uma classificação mais precisa, forneça informações sobre o seu contexto, por exemplo:
        * O seu utilizador.
        * O dispositivo em que estão.
        * A hora atual.
        * Outros dados sobre a situação atual.
        * Dados históricos sobre o utilizador ou contexto.

        A sua aplicação específica pode ter informações de contexto diferentes.

    * **[Características](concepts-features.md)**: Uma unidade de informação sobre um item de conteúdo ou um contexto de utilizador. Certifique-se de que utiliza apenas funcionalidades que estão agregadas. Não utilize tempos específicos, IDs do utilizador ou outros dados não agregados como funcionalidades.

        * Uma _funcionalidade de ação_ é metadados sobre o conteúdo.
        * Uma _característica de contexto_ é metadados sobre o contexto em que o conteúdo é apresentado.

* **Exploração**: O serviço Personalizar está a explorar quando, em vez de devolver a melhor ação, escolhe uma ação diferente para o utilizador. O serviço Personalizer evita deriva, estagnação e pode adaptar-se ao comportamento do utilizador em curso explorando.

* **Exploração**: O serviço Personalizar utiliza o modelo atual para decidir a melhor ação com base em dados passados.

* **Duração da Experiência**: A quantidade de tempo que o serviço Personaler espera por uma recompensa, a partir do momento em que a chamada do Rank aconteceu para esse evento.

* **Eventos Inativos**: Um evento inativo é aquele em que chamou Rank, mas não tem certeza se o utilizador alguma vez verá o resultado, devido às decisões de aplicação do cliente. Eventos inativos permitem criar e armazenar resultados de personalização, e depois decidir deitá-los fora mais tarde sem afetar o modelo de machine learning.


* **Recompensa**: Uma medida de como o utilizador reagiu ao ID de ação de recompensa devolvido da Rank API, como uma pontuação entre 0 a 1. O valor de 0 a 1 é definido pela sua lógica de negócio, com base na forma como a escolha ajudou a atingir os seus objetivos de personalização. O ciclo de aprendizagem não armazena esta recompensa como histórico de utilizadores individuais.

## <a name="evaluations"></a>Avaliações

### <a name="offline-evaluations"></a>Avaliações offline

* **Avaliação**: Uma avaliação offline determina a melhor política de aprendizagem para o seu loop com base nos dados da sua aplicação.

* **Política de Aprendizagem**: Como o Personaler treina um modelo em cada evento será determinado por alguns parâmetros que afetam o funcionamento do algoritmo de aprendizagem automática. Um novo ciclo de aprendizagem começa com uma Política de **Aprendizagem** predefinido, que pode produzir um desempenho moderado. Ao executar [Avaliações,](concepts-offline-evaluation.md)o Personalizer cria novas políticas de aprendizagem especificamente otimizadas para os casos de utilização do seu loop. O personalizador irá realizar-se significativamente melhor com políticas otimizadas para cada ciclo específico, gerado durante a Avaliação. A política de aprendizagem é nomeada _definições de aprendizagem_ no **Modelo e configurações de aprendizagem** para o recurso Personalizer no portal Azure.

### <a name="apprentice-mode-evaluations"></a>Avaliações do modo de aprendiz

O modo aprendiz fornece as seguintes **métricas de avaliação:**
* **Linha de base – recompensa média**: Recompensas médias do padrão da aplicação (linha de base).
* **Personalização – recompensa média**: Média de recompensas totais O Personalizador teria potencialmente atingido.
* **Recompensa média de rolamento**: Relação de base e recompensa personalizante – normalizada ao longo dos mais recentes 1000 eventos.

## <a name="next-steps"></a>Passos seguintes

* Conheça [a ética e o uso responsável](ethics-responsible-use.md)