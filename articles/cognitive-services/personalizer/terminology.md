---
title: Terminologia - Personalizer
description: O personalizer usa terminologia a partir da aprendizagem de reforço. Estes termos são utilizados no portal Azure e nas APIs.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: f75437c5afd5d3fd7f7570079be410d3db1ca8db
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624277"
---
# <a name="terminology"></a>Terminologia

O personalizer usa terminologia a partir da aprendizagem de reforço. Estes termos são utilizados no portal Azure e nas APIs.

## <a name="conceptual-terminology"></a>Terminologia conceptual

* **Learning Loop**: Cria um recurso Personalizer, chamado ciclo de _aprendizagem,_ para cada parte da sua aplicação que pode beneficiar da personalização. Se tiver mais do que uma experiência para personalizar, crie um loop para cada um.

* **Modelo**: Um modelo Personalizer captura todos os dados aprendidos sobre o comportamento do utilizador, obtendo dados de formação a partir da combinação dos argumentos que envia para chamadas De Classificação e Recompensa, e com um comportamento de treino determinado pela Política de Aprendizagem.

## <a name="personalizer-configuration"></a>Configuração personalizada

O personalizer está configurado a partir do [portal Azure.](https://portal.azure.com)

* **Recompensas**: configure os valores predefinidos para o tempo de espera da recompensa, recompensa por defeito e política de agregação de recompensas.

* **Exploração**: configure a percentagem de chamadas rank para usar para exploração

* **Frequência de atualização**do modelo : Com que frequência o modelo é retreinado.

* **Retenção de dados**: Quantos dias valem dados para armazenar. Isto pode ter impacto em avaliações offline, que são usadas para melhorar o seu ciclo de aprendizagem.

## <a name="use-rank-and-reward-apis"></a>Use AFIs de Classificação e Recompensa

* **Classificação**: Dadas as ações com funcionalidades e as características de contexto, utilize explorar ou explorar para devolver a ação de topo (produto de conteúdo).

    * **Ações**: As ações são os itens de conteúdo, como produtos ou promoções, para escolher. O Personalizer escolhe a ação de topo (ID de ação de recompensa devolvida) para mostrar aos seus utilizadores através da Rank API.

    * **Contexto**: Para fornecer uma classificação mais precisa, forneça informações sobre o seu contexto, por exemplo:
        * O seu utilizador.
        * O dispositivo em que estão.
        * O tempo atual.
        * Outros dados sobre a situação atual.
        * Dados históricos sobre o utilizador ou contexto.

        A sua aplicação específica pode ter diferentes informações de contexto.

    * **[Características](concepts-features.md)** : Uma unidade de informação sobre um item de conteúdo ou um contexto de utilizador. Certifique-se de que utiliza apenas as funcionalidades agregadas. Não utilize tempos específicos, iDs de utilizador ou outros dados não agregados como funcionalidades.

        * Uma _funcionalidade de ação_ são metadados sobre o conteúdo.
        * Uma _característica de contexto_ são metadados sobre o contexto em que o conteúdo é apresentado.

* **Exploração**: O serviço Personalizer está a explorar quando, em vez de devolver a melhor ação, escolhe uma ação diferente para o utilizador. O serviço Personalizer evita deriva, estagnação e pode adaptar-se ao comportamento contínuo do utilizador explorando.

* **Exploração**: O serviço Personalizer utiliza o modelo atual para decidir a melhor ação com base em dados anteriores.

* **Duração da Experiência**: O tempo que o serviço Personalizer espera por uma recompensa, a partir do momento em que a chamada do Rank aconteceu para esse evento.

* **Eventos Inativos**: Um evento inativo é aquele em que chamou Rank, mas não tem a certeza se o utilizador alguma vez verá o resultado, devido a decisões de aplicação do cliente. Os eventos inativos permitem-lhe criar e armazenar resultados de personalização, depois decide descartá-los mais tarde sem afetar o modelo de aprendizagem automática.


* **Recompensa**: Uma medida de como o utilizador reagiu ao ID de ação de recompensa devolvido da Rank API, como uma pontuação entre 0 a 1. O valor de 0 a 1 é definido pela sua lógica de negócio, com base na forma como a escolha ajudou a alcançar os seus objetivos de negócio de personalização. O ciclo de aprendizagem não armazena esta recompensa como histórico individual de utilizadores.

## <a name="offline-evaluations"></a>Avaliações offline

* **Avaliação**: Uma avaliação offline determina a melhor política de aprendizagem para o seu loop com base nos dados do seu loop.

* **Política de Aprendizagem**: Como o Personalizer treina um modelo em cada evento será determinado por alguns parâmetros que afetam o funcionamento do algoritmo de aprendizagem automática. Um novo ciclo de aprendizagem começa com uma Política de **Aprendizagem**padrão, que pode produzir um desempenho moderado. Ao executar [Avaliações,](concepts-offline-evaluation.md)o Personalizer cria novas políticas de aprendizagem especificamente otimizadas para os casos de utilização do seu loop. O personalizer terá um desempenho significativamente melhor com as políticas otimizadas para cada loop específico, gerada durante a Avaliação. A política de aprendizagem é nomeada _definições_ de aprendizagem no **Modelo e definições** de aprendizagem para o recurso Personalizer no portal Azure.