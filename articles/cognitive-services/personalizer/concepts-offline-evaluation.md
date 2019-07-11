---
title: Avaliação offline - Personalizer
titleSuffix: Azure Cognitive Services
description: Criar o ciclo de comentários nesta C# início rápido com o serviço de Personalizer.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 3fdedd1af9b683b221dfa4aebad7a30559b7abff
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722487"
---
# <a name="offline-evaluation"></a>Avaliação offline

Avaliação offline é um método que permite-lhe testar e avaliar a eficácia do serviço Personalizer sem alterar o seu código ou afetar a experiência do usuário. Avaliação offline usa passado executaram os dados, enviados a partir da sua aplicação para a API de classificação, para comparar diferentes classificações.

Avaliação offline é executada num intervalo de datas. O intervalo pode concluir mais tarde a hora atual. O início do intervalo não pode ser superior ao número de dias especificado para [retenção de dados](how-to-settings.md).

Avaliação offline pode ajudá-lo a responder às seguintes perguntas:

* A eficiência são classificações Personalizer para personalização concluída com êxito?
    * Quais são as recompensas média obtidas com o Personalizer de machine learning online política?
    * Como Personalizer compara para a eficiência da qual a aplicação teria feito por predefinição?
    * O que teria sido a eficácia comparativa de uma escolha aleatória para personalização?
    * O que teria sido a eficácia comparativa de políticas de aprendizagem diferente especificado manualmente?
* Quais recursos do contexto estiver a contribuir mais ou menos para personalização concluída com êxito?
* Quais recursos das ações estiver a contribuir mais ou menos para personalização concluída com êxito?

Além disso, avaliação Offline pode ser utilizada para detetar mais otimizadas para aprendizagem políticas que Personalizer pode utilizar para melhorar os resultados no futuro.

Avaliações offline não fornecem orientação sobre a percentagem de eventos a utilizar para exploração.

## <a name="prerequisites-for-offline-evaluation"></a>Pré-requisitos para avaliação offline

Seguem-se as considerações importantes para a avaliação offline representativa:

* Ter dados suficientes. O mínimo recomendado é, pelo menos, 50 000 eventos.
* Recolha dados de períodos com o comportamento do utilizador representativos e o tráfego.

## <a name="discovering-the-optimized-learning-policy"></a>Detetar a política de learning otimizado

Personalizer pode utilizar o processo de avaliação de offline para detetar automaticamente uma política de aprendizado mais ideal.

Depois de efetuar a avaliação offline, pode ver a eficácia comparativa de Personalizer com essa nova política em comparação com a atual política online. Pode, em seguida, aplicar essa política de aprendizagem para torná-lo em vigor imediatamente em Personalizer ou transferi-lo para uso ou análise futura.

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Noções básicas sobre a relevância dos resultados da avaliação offline

Quando executa uma edição de avaliação offline, é muito importante analisar _dos limites de confiança_ dos resultados. Se forem grandes, significa que seu aplicativo não recebeu dados suficientes para que as estimativas de recompensa ser preciso ou significativas. À medida que o sistema acumula mais dados e executar avaliações offline por períodos mais longos, os intervalos de confiança se tornará mais estreito.

## <a name="how-offline-evaluations-are-done"></a>Como offline são feitas avaliações

Avaliações offline são feitas através de um método chamado **avaliação Counterfactual**. 

Personalizer baseia-se no pressuposto de que usuários comportamento (e, portanto, remunerações) são impossíveis prever retrospectively (Personalizer não é possível saber o que teria acontecido se o usuário tivesse apresentado algo diferente da que vêem) e somente para aprender a partir de medida de remunerações. 

Este é o processo de conceitual utilizado para avaliações:

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

A avaliação offline utiliza apenas o comportamento do utilizador observado. Este processo descartará grandes volumes de dados, especialmente se o seu aplicativo classificar chamadas com um grande número de ações.


## <a name="evaluation-of-features"></a>Avaliação dos recursos

Avaliações offline podem fornecer informações sobre a quantidade de recursos específicos para ações ou contexto são Pesar para remunerações superior. As informações são calculadas utilizando a avaliação em relação a determinado período de tempo e os dados e podem variar de acordo com a hora.

Recomendamos olhar para as avaliações de funcionalidade e perguntar:

* Quais recursos de outros, adicionais, foi a sua aplicação ou sistema fornece ao longo das linhas os que são mais eficientes?
* Que funcionalidades podem ser removida devido a eficácia de baixa? Adicionar funcionalidades de eficácia baixa _ruído_ para o machine learning.
* Existem quaisquer funcionalidades que estão incluídas por acidente? Exemplos disso são: pessoal (PII), informações de identificação duplicar IDs, etc.
* Existem considerações de utilizar quaisquer funcionalidades indesejáveis que não devem ser usadas para personalizar devido à regulamentação ou responsável? Existem recursos que poderiam proxy (ou seja, estreitamente espelhar ou obtenha uma correlação) recursos indesejáveis?


## <a name="next-steps"></a>Passos Seguintes

[Configurar Personalizer](how-to-settings.md)
