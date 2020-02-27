---
title: Utilize o método de Avaliação Offline - Personalizer
titleSuffix: Azure Cognitive Services
description: Este artigo irá explicar como usar a avaliação offline para medir a eficácia da sua app e analisar o seu ciclo de aprendizagem.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: f8ceef5e80bf15f0ba52a9c289e617018febfb5c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623593"
---
# <a name="offline-evaluation"></a>Avaliação offline

A avaliação offline é um método que lhe permite testar e avaliar a eficácia do Serviço personalizador sem alterar o seu código ou afetar a experiência do utilizador. A avaliação offline utiliza dados passados, enviados da sua aplicação para as APIs de Rank e Reward, para comparar o desempenho de diferentes classificações.

A avaliação offline é realizada num intervalo de data. O alcance pode terminar tão tarde quanto o tempo atual. O início da gama não pode ser superior ao número de dias especificados para a [retenção](how-to-settings.md)de dados .

A avaliação offline pode ajudá-lo a responder às seguintes perguntas:

* Quão eficazes são as classificações da Personalizer para uma personalização bem sucedida?
    * Quais são as recompensas médias alcançadas pela política de machine learning online Personalizer?
    * Como é que o Personalizer se compara à eficácia do que a aplicação teria feito por defeito?
    * Qual seria a eficácia comparativa de uma escolha aleatória para personalização?
    * Qual seria a eficácia comparativa das diferentes políticas de aprendizagem especificadas manualmente?
* Que características do contexto estão a contribuir mais ou menos para uma personalização bem sucedida?
* Quais as características das ações que estão a contribuir mais ou menos para uma personalização bem sucedida?

Além disso, a Avaliação Offline pode ser usada para descobrir políticas de aprendizagem mais otimizadas que o Personalizer pode usar para melhorar os resultados no futuro.

As avaliações offline não fornecem orientações sobre a percentagem de eventos a utilizar para exploração.

## <a name="prerequisites-for-offline-evaluation"></a>Pré-requisitos para avaliação offline

Seguem-se considerações importantes para a avaliação offline representativa:

* Tenho dados suficientes. O mínimo recomendado é de pelo menos 50.000 eventos.
* Recolher dados de períodos com comportamento representativo do utilizador e tráfego.

## <a name="discovering-the-optimized-learning-policy"></a>Descobrir a política de aprendizagem otimizada

O personalizer pode usar o processo de avaliação offline para descobrir automaticamente uma política de aprendizagem mais ótima.

Após a realização da avaliação offline, pode ver a eficácia comparativa do Personalizer com essa nova política em comparação com a política online atual. Em seguida, pode aplicar essa política de aprendizagem para a tornar eficaz imediatamente no Personalizer, baixando-a e enviando-a no painel De Modelos e Políticas. Também pode descarregá-lo para futuraanálise ou utilização.

Políticas atuais incluídas na avaliação:

| Definições de aprendizagem | Objetivo|
|--|--|
|**Política Online**| A atual Política de Aprendizagem utilizada no Personalizer |
|**Linha de base**|O padrão do pedido (conforme determinado pela primeira Ação enviada em chamadas Rank)|
|**Política Aleatória**|Um comportamento imaginário de Rank que sempre devolve escolha aleatória de Ações dos fornecidos.|
|**Políticas Personalizadas**|Políticas adicionais de aprendizagem enviadas no início da avaliação.|
|**Política Otimizada**|Se a avaliação foi iniciada com a opção de descobrir uma política otimizada, ela também será comparada, e você será capaz de descarregá-la ou torná-la a política de aprendizagem on-line, substituindo a atual.|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Compreender a relevância dos resultados da avaliação offline

Quando se faz uma avaliação offline, é muito importante analisar os limites de confiança dos _resultados._ Se forem amplas, significa que a sua aplicação não recebeu dados suficientes para que as estimativas de recompensa sejam precisas ou significativas. À medida que o sistema acumula mais dados, e executa avaliações offline durante períodos mais longos, os intervalos de confiança tornam-se mais estreitos.

## <a name="how-offline-evaluations-are-done"></a>Como as avaliações offline são feitas

As avaliações offline são feitas utilizando um método chamado **Avaliação Contrafactual**.

O personalizer baseia-se no pressuposto de que o comportamento dos utilizadores (e, portanto, recompensas) são impossíveis de prever retrospetivamente (Personalizer não pode saber o que teria acontecido se o utilizador tivesse mostrado algo diferente do que viu), e apenas para aprender com recompensas medidas.

Este é o processo conceptual utilizado para avaliações:

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

A avaliação offline apenas utiliza o comportamento observado do utilizador. Este processo descarta grandes volumes de dados, especialmente se a sua aplicação fizer chamadas de Rank com um grande número de ações.


## <a name="evaluation-of-features"></a>Avaliação de funcionalidades

Avaliações offline podem fornecer informações sobre quanto das funcionalidades específicas para ações ou contexto estão a pesar para recompensas mais elevadas. A informação é calculada utilizando a avaliação contra o período e os dados do tempo dado, e pode variar com o tempo.

Recomendamos que se analise as avaliações de funcionalidades e pergunte:

* Que outras funcionalidades adicionais poderiam fornecer a sua aplicação ou sistema na linha daqueles que são mais eficazes?
* Que funcionalidades podem ser removidas devido à baixa eficácia? As características de baixa eficácia adicionam _ruído_ à aprendizagem automática.
* Há alguma característica que esteja acidentalmente incluída? Exemplos destes são: informação identificável do utilizador, identificações duplicadas, etc.
* Existem funcionalidades indesejáveis que não devam ser usadas para personalizar devido a considerações de uso regulamentar ou responsável? Existem características que possam proxy (isto é, espelhar ou correlacionar com) características indesejáveis?


## <a name="next-steps"></a>Passos seguintes

[Configurar personalizer](how-to-settings.md)
[executar avaliações offline](how-to-offline-evaluation.md) entender [como o personalizer funciona](how-personalizer-works.md)
