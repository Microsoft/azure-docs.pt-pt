---
title: Avaliação de cenário-personalizador
titleSuffix: Azure Cognitive Services
description: O personalizador pode ser aplicado em qualquer situação em que seu aplicativo possa selecionar o item, a ação ou o produto certo para ser exibido, a fim de melhorar a experiência, obter melhores resultados de negócios ou aumentar a produtividade.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: diberry
ms.openlocfilehash: 87065709b16a5a0c7076584e8fc33c3830319e4f
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999636"
---
# <a name="where-can-you-use-personalizer"></a>Onde pode utilizar o Personalizador?

Use o personalizador em qualquer situação em que seu aplicativo precise selecionar o item, a ação ou o produto certo para ser exibido, a fim de melhorar a experiência, obter melhores resultados de negócios ou aumentar a produtividade. 

O personalizador usa o aprendizado de máquina para selecionar qual ação mostrar o usuário. A seleção pode variar drasticamente dependendo da quantidade, da qualidade e da distribuição dos dados enviados ao serviço.

### <a name="checklist-for-applying-personalizer"></a>Lista de verificação para aplicação do personalizador


Você pode aplicar o personalizador em situações em que:

* Você tem uma meta comercial ou de usabilidade para seu aplicativo.
* Você tem um local em seu aplicativo, em que tomar uma decisão contextual do que mostrar aos usuários melhorará essa meta.
* A melhor opção pode e deve ser aprendida do comportamento do usuário coletivo e da Pontuação de recompensa total.
* O uso do aprendizado de máquina para personalização segue as diretrizes e as escolhas de [uso responsáveis](ethics-responsible-use.md) .
* A decisão contextual pode ser expressa como a classificação da melhor opção (ação) de um conjunto limitado de opções.
* O quão bem a escolha classificada para seu aplicativo pode ser determinada medindo algum aspecto do comportamento do usuário e expressando-o em uma _Pontuação de recompensa_. Este é um número entre-1 e 1.
* A pontuação de recompensa não traz muitos fatores de constatação ou externos. A duração do experimento é baixa o suficiente para que a pontuação de recompensa possa ser computada enquanto ainda for relevante.
* Você pode expressar o contexto para a classificação como uma lista de pelo menos 5 [recursos](concepts-features.md) que você imagina que ajudarão a fazer a escolha certa e que não incluem informações de identificação pessoal. (PII).
* Você tem informações sobre cada opção de conteúdo, _ação_, como uma lista de pelo menos 5 [recursos](concepts-features.md) que você acredita que ajudarão o personalizado a fazer a escolha certa.
* Seu aplicativo pode reter dados por tempo suficiente para acumular um histórico de pelo menos 100.000 interações.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Considerações de aprendizado de máquina para aplicar personalizador

O personalizador se baseia no reforço Learning, uma abordagem ao aprendizado de máquina que é ensinada pelos comentários que você fornece. 

O personalizador aprenderá melhor em situações em que:

* Há eventos suficientes para se manter em cima da personalização ideal se o problema se desconectar ao longo do tempo (como preferências de notícias ou de moda). O personalizado se adaptará à mudança contínua no mundo real, mas os resultados não serão ideais se não houver eventos e dados suficientes para aprender com a descoberta e a liquidação de novos padrões. Você deve escolher um caso de uso que ocorra com frequência suficiente. Considere procurar casos de uso que acontecem pelo menos 500 vezes por dia.
* O contexto e as ações têm [recursos](concepts-features.md) suficientes para facilitar o aprendizado.
* Há menos de 50 ações a serem classificadas por chamada.
* Suas configurações de retenção de dados permitem que o personalizado colete dados suficientes para executar avaliações offline e otimização de política. Normalmente, são pelo menos 50.000 pontos de dados.

## <a name="monitor-effectiveness-of-personalizer"></a>Monitorar a eficácia do personalizador

Você pode monitorar a eficácia do personalizado periodicamente executando [avaliações offline](concepts-offline-evaluation.md).

## <a name="use-personalizer-with-recommendation-engines"></a>Usar personalizador com mecanismos de recomendação

Muitas empresas usam mecanismos de recomendação, ferramentas de marketing e de campanha, segmentação de público e clustering, filtragem colaborativa e outros meios para recomendar produtos de um catálogo grande para os clientes.

O [repositório GitHub de recomendados da Microsoft](https://github.com/Microsoft/Recommenders) fornece exemplos e práticas recomendadas para a criação de sistemas de recomendação, fornecidos como notebooks Jupyter. Ele fornece exemplos funcionais de preparação de dados, criação de modelos, avaliação, ajuste e operacionalização dos mecanismos de recomendação, para muitas abordagens comuns, incluindo xDeepFM, SAR, ALS, RBM, DKN.

O personalizador pode funcionar com um mecanismo de recomendação quando ele estiver presente.

* Os mecanismos de recomendação usam grandes quantidades de itens (por exemplo, 500.000) e recomendam um subconjunto (como os 20 principais) de centenas ou milhares de opções.
* O personalizador usa um pequeno número de ações com muitas informações sobre elas e as classifica em tempo real para um determinado contexto avançado, enquanto a maioria dos mecanismos de recomendação usa apenas alguns atributos sobre usuários, produtos e suas interações.
* O personalizador foi projetado para explorar de modo autônomo as preferências do usuário o tempo todo, o que produzirá resultados melhores onde o conteúdo está mudando rapidamente, como notícias, eventos ao vivo, conteúdo da Comunidade ao vivo, conteúdo com atualizações diárias ou conteúdo sazonal.

Um uso comum é pegar a saída de um mecanismo de recomendação (por exemplo, os 20 principais produtos para um determinado cliente) e usá-lo como as ações de entrada para o personalizador.

## <a name="next-steps"></a>Passos Seguintes

[Ética & uso responsável](ethics-responsible-use.md).