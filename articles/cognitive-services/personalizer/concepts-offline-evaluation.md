---
title: Utilize o método de avaliação offline - Personalizar
titleSuffix: Azure Cognitive Services
description: Este artigo irá explicar como usar a avaliação offline para medir a eficácia da sua app e analisar o seu ciclo de aprendizagem.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 627f511bb12c16c8f54935d1f782cb7c2c962163
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132760"
---
# <a name="offline-evaluation"></a>Avaliação offline

A avaliação offline é um método que lhe permite testar e avaliar a eficácia do Serviço Personalizar sem alterar o seu código ou afetar a experiência do utilizador. A avaliação offline utiliza dados passados, enviados da sua aplicação para as APIs Rank e Reward, para comparar como diferentes ranks têm realizado.

A avaliação offline é realizada num intervalo de datas. O alcance pode terminar tão tarde quanto o tempo atual. O início da gama não pode ser superior ao número de dias especificados para a retenção de [dados.](how-to-settings.md)

A avaliação offline pode ajudá-lo a responder às seguintes perguntas:

* Quão eficazes são as classificações do Personalizer para uma personalização bem sucedida?
    * Quais são as recompensas médias alcançadas pela política de aprendizagem automática online Personalizer?
    * Como é que o Personaler se compara à eficácia do que a aplicação teria feito por defeito?
    * Qual teria sido a eficácia comparativa de uma escolha aleatória para a Personalização?
    * Qual teria sido a eficácia comparativa das diferentes políticas de aprendizagem especificadas manualmente?
* Que características do contexto estão a contribuir mais ou menos para uma personalização bem sucedida?
* Que características das ações estão a contribuir mais ou menos para uma personalização bem sucedida?

Além disso, a Avaliação Offline pode ser usada para descobrir políticas de aprendizagem mais otimizadas que o Personalizer pode usar para melhorar os resultados no futuro.

As avaliações offline não fornecem orientações sobre a percentagem de eventos a utilizar para exploração.

## <a name="prerequisites-for-offline-evaluation"></a>Pré-requisitos para avaliação offline

Seguem-se considerações importantes para a avaliação offline representativa:

* Tenho dados suficientes. O mínimo recomendado é de pelo menos 50.000 eventos.
* Recolher dados de períodos com comportamento representativo do utilizador e tráfego.

## <a name="discovering-the-optimized-learning-policy"></a>Descobrir a política de aprendizagem otimizada

O personalização pode usar o processo de avaliação offline para descobrir uma política de aprendizagem mais ideal automaticamente.

Após a avaliação offline, pode ver a eficácia comparativa do Personaler com essa nova política em comparação com a política online atual. Em seguida, pode aplicar essa política de aprendizagem para torná-la eficaz imediatamente no Personalizer, descarregando-a e carregando-a no painel de Modelos e Políticas. Também pode descarregá-lo para análise futura ou utilização.

Políticas atuais incluídas na avaliação:

| Definições de aprendizagem | Objetivo|
|--|--|
|**Política Online**| A atual Política de Aprendizagem utilizada no Personalizar |
|**Linha de base**|O padrão da aplicação (conforme determinado pela primeira ação enviada em chamadas rank)|
|**Política Aleatória**|Um comportamento de Rank imaginário que devolve sempre a escolha aleatória das Ações dos fornecidos.|
|**Políticas Personalizadas**|Políticas adicionais de aprendizagem carregadas ao iniciar a avaliação.|
|**Política Otimizada**|Se a avaliação foi iniciada com a opção de descobrir uma política otimizada, ela também será comparada, e você será capaz de descarregá-la ou torná-la a política de aprendizagem on-line, substituindo a atual.|

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Compreender a relevância dos resultados da avaliação offline

Quando se faz uma avaliação offline, é muito importante analisar os limites de confiança dos _resultados._ Se forem largas, significa que a sua aplicação não recebeu dados suficientes para que as estimativas de recompensa sejam precisas ou significativas. À medida que o sistema acumula mais dados, e faz avaliações offline durante períodos mais longos, os intervalos de confiança tornam-se mais estreitos.

## <a name="how-offline-evaluations-are-done"></a>Como as avaliações offline são feitas

As avaliações offline são feitas utilizando um método chamado **Avaliação Contrafactual**.

O personalização baseia-se no pressuposto de que o comportamento dos utilizadores (e, portanto, as recompensas) são impossíveis de prever retrospetivamente (Personalizer não pode saber o que teria acontecido se o utilizador tivesse sido mostrado algo diferente do que viu), e apenas para aprender com recompensas medidas.

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

A avaliação offline apenas utiliza o comportamento observado do utilizador. Este processo descarta grandes volumes de dados, especialmente se a sua aplicação fizer chamadas rank com um grande número de ações.


## <a name="evaluation-of-features"></a>Avaliação de funcionalidades

As avaliações offline podem fornecer informações sobre quanto das características específicas para ações ou contexto estão a pesar para recompensas mais elevadas. A informação é calculada utilizando a avaliação contra o período de tempo e dados determinados, e pode variar com o tempo.

Recomendamos que analise as avaliações de funcionalidades e pergunte:

* Que outras funcionalidades adicionais poderiam a sua aplicação ou sistema fornecer na linha daqueles que são mais eficazes?
* Que características podem ser removidas devido à baixa eficácia? Características de baixa eficácia adicionam _ruído_ à aprendizagem automática.
* Existem alguma característica que esteja acidentalmente incluída? Exemplos destes exemplos são: informação identificável do utilizador, IDs duplicados, etc.
* Existem algumas características indesejáveis que não devam ser usadas para personalizar devido a considerações regulamentares ou responsáveis de uso? Existem funcionalidades que podem ser procuradas (isto é, espelhar ou correlacionar com) características indesejáveis?


## <a name="next-steps"></a>Passos seguintes

[Configure Personalizer](how-to-settings.md) 
 [Executar Avaliações Offline](how-to-offline-evaluation.md) Compreender [como funciona o Personaler](how-personalizer-works.md)
