---
title: Usar o método de avaliação offline-personalizador
titleSuffix: Azure Cognitive Services
description: Este artigo explicará como usar a avaliação offline para medir a eficácia de seu aplicativo e analisar seu loop de aprendizado.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: dec6faab0dfc7f073639186429767bbf653ceda1
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513614"
---
# <a name="offline-evaluation"></a>Avaliação offline

A avaliação offline é um método que permite testar e avaliar a eficácia do serviço personalizado sem alterar seu código nem afetar a experiência do usuário. A avaliação offline usa dados passados, enviados do seu aplicativo para as APIs de classificação e recompensa, para comparar a forma como as classificações diferentes foram executadas.

A avaliação offline é executada em um intervalo de datas. O intervalo pode terminar o mais tarde da hora atual. O início do intervalo não pode ser maior do que o número de dias especificado para a [retenção de dados](how-to-settings.md).

A avaliação offline pode ajudá-lo a responder às seguintes perguntas:

* Quão eficiente são as classificações personalizadas para personalização bem-sucedida?
    * Quais são as recompensas médias alcançadas pela política de aprendizado de máquina online do personalizador?
    * Como o personalizador se compara à eficácia do que o aplicativo teria feito por padrão?
    * O que seria a eficácia comparativa de uma escolha aleatória para personalização?
    * O que seria a eficácia comparativa de diferentes políticas de aprendizagem especificadas manualmente?
* Quais recursos do contexto estão contribuindo com uma personalização mais ou menos bem-sucedida?
* Quais recursos das ações estão contribuindo com uma personalização mais ou menos bem-sucedida?

Além disso, a avaliação offline pode ser usada para descobrir políticas de aprendizado mais otimizadas que o personalizador pode usar para melhorar os resultados no futuro.

As avaliações offline não fornecem orientações sobre a porcentagem de eventos a serem usados para exploração.

## <a name="prerequisites-for-offline-evaluation"></a>Pré-requisitos para a avaliação offline

Estas são as considerações importantes para a avaliação offline representativa:

* Ter dados suficientes. O mínimo recomendado é de pelo menos 50.000 eventos.
* Colete dados de períodos com comportamento e tráfego representativos do usuário.

## <a name="discovering-the-optimized-learning-policy"></a>Descobrindo a política de aprendizagem otimizada

O personalizador pode usar o processo de avaliação offline para descobrir uma política de aprendizado mais ideal automaticamente.

Depois de executar a avaliação offline, você pode ver a eficácia comparativa do personalizador com essa nova política em comparação com a política online atual. Em seguida, você pode aplicar essa política de aprendizado para torná-la eficaz imediatamente no Personalizador, baixando-a e carregando-a no painel modelos e políticas. Você também pode baixá-lo para análise ou uso futuro.

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Compreendendo a relevância dos resultados da avaliação offline

Quando você executa uma avaliação offline, é muito importante analisar os _limites de confiança_ dos resultados. Se eles forem largos, significa que seu aplicativo não recebeu dados suficientes para que as estimativas de recompensa sejam precisas ou significativas. À medida que o sistema acumula mais dados e você executa avaliações offline em períodos mais longos, os intervalos de confiança se tornam mais estreitos.

## <a name="how-offline-evaluations-are-done"></a>Como as avaliações offline são feitas

As avaliações offline são feitas usando um método chamado **counterfactual Evaluation**.

O personalizador se baseia na suposição de que o comportamento dos usuários (e, portanto, as recompensas) é impossível prever de forma retrospectiva (o personalizador não sabe o que teria ocorrido se o usuário tivesse mostrado algo diferente do que foi visto) e apenas para aprender com recompensas medidas.

Este é o processo conceitual usado para avaliações:

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call

        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.

    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

A avaliação offline usa apenas o comportamento de usuário observado. Esse processo descarta grandes volumes de dados, especialmente se o aplicativo classifica chamadas com um grande número de ações.


## <a name="evaluation-of-features"></a>Avaliação dos recursos

As avaliações offline podem fornecer informações sobre quanto dos recursos específicos para ações ou contexto estão pesando para recompensas mais altas. As informações são computadas usando a avaliação em relação ao período de tempo e aos dados fornecidos e podem variar com o tempo.

É recomendável observar as avaliações de recursos e perguntar:

* Que outros recursos adicionais podem ser o seu aplicativo ou sistema ao longo das linhas que são mais eficazes?
* Quais recursos podem ser removidos devido à baixa eficácia? Os recursos de baixa eficácia adicionam _ruído_ ao aprendizado de máquina.
* Há algum recurso que é incluído acidentalmente? Exemplos são: informações de identificação pessoal (PII), IDs duplicadas, etc.
* Há algum recurso indesejável que não deve ser usado para personalizar devido a considerações de uso normativo ou responsável? Há recursos que poderiam ser configurados por proxy (ou seja, espelhados ou correlacionar com) recursos indesejáveis?


## <a name="next-steps"></a>Passos seguintes

[Configurar o personalizador](how-to-settings.md)
[executar avaliações offline](how-to-offline-evaluation.md) entender [como o personalizador funciona](how-personalizer-works.md)
