---
title: Avaliação de recursos - Personalização
titleSuffix: Azure Cognitive Services
description: Quando executa uma Avaliação no seu recurso Personalizer a partir do portal Azure, o Personalizer fornece informações sobre quais as características de contexto e ações que estão a influenciar o modelo.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: c0e47a2943cf8c934d201f76aefc41868adf0b25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87127728"
---
# <a name="feature-evaluation"></a>Avaliação de funcionalidade

Quando executa uma Avaliação no seu recurso Personalizer a partir do [portal Azure,](https://portal.azure.com)o Personalizer fornece informações sobre quais as características de contexto e ações que estão a influenciar o modelo. 

Isto é útil para:

* Imagine funcionalidades adicionais que possa utilizar, inspirando-se nas funcionalidades mais importantes do modelo.
* Veja quais as funcionalidades não são importantes e potencialmente remova-as ou analise melhor o que pode estar a afetar o uso.
* Fornecer orientações às equipas editoriais ou de curadoria sobre novos conteúdos ou produtos que valha a pena trazer para o catálogo.
* Resolver problemas comuns e erros que acontecem ao enviar funcionalidades para Personalizer.

As características mais importantes têm pesos mais fortes no modelo. Como estas características têm um peso mais forte, tendem a estar presentes quando o Personalizer obtém recompensas mais elevadas.

## <a name="getting-feature-importance-evaluation"></a>Obtenção de avaliação de importância de recurso

Para ver os resultados da importância da característica, tem de fazer uma avaliação. A avaliação cria rótulos de características legíveis pelo homem com base nos nomes de características observados durante o período de avaliação.

A informação resultante sobre a importância da funcionalidade representa o atual modelo online personalizer. A avaliação analisa a importância do modelo guardado na data final do período de avaliação, depois de ter sido submetido a toda a formação esboante durante a avaliação, com a atual política de aprendizagem online. 

Os resultados da característica não representam outras políticas e modelos testados ou criados durante a avaliação.  A avaliação não incluirá funcionalidades enviadas ao Personaler após o final do período de avaliação.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Como interpretar a avaliação de importância de recurso

Personalizer avalia funcionalidades criando "grupos" de funcionalidades que têm uma importância semelhante. Um grupo pode dizer-se que tem uma importância globalmente mais forte do que outros, mas dentro do grupo, a encomenda de características é alfabeticamente.

As informações sobre cada recurso incluem:

* Se a funcionalidade vem de Contexto ou Ações.
* Chave de recurso e valor.

Por exemplo, uma aplicação de encomenda de gelados pode ver "Context.Weather:Hot" como uma característica muito importante.

O personaler exibe correlações de funcionalidades que, quando tomadas em consideração em conjunto, produzem recompensas mais elevadas.

Por exemplo, pode ver "Context.Weather:Hot *with* Action.MenuItem:IceCream" bem como "Context.Weather:Cold *with* Action.MenuItem:WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Ações que pode tomar com base na avaliação de recursos

### <a name="imagine-additional-features-you-could-use"></a>Imagine funcionalidades adicionais que possa usar

Inspire-se nas características mais importantes do modelo. Por exemplo, se vir "Context.MobileBattery:Low" numa aplicação móvel de vídeo, pode pensar que esse tipo de ligação também pode fazer com que os clientes escolham ver um videoclip em vez de outro, e depois adicione funcionalidades sobre tipo de conectividade e largura de banda na sua app.

### <a name="see-what-features-are-not-important"></a>Veja quais as características não são importantes

Potencialmente remova funcionalidades sem importância ou analise melhor o que pode afetar o uso. As características podem ser baixas por muitas razões. Pode-se que a funcionalidade não afete genuinamente o comportamento do utilizador. Mas também pode significar que a funcionalidade não é aparente para o utilizador. 

Por exemplo, um site de vídeo poderia ver que "Action.VideoResolution=4k" é uma característica de baixa importância, contradizendo a pesquisa do utilizador. A causa pode ser que a aplicação nem sequer mencione ou mostre a resolução de vídeo, para que os utilizadores não mudem o seu comportamento com base nele.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Fornecer orientação às equipas editoriais ou de curadoria

Forneça orientações sobre novos conteúdos ou produtos que valha a pena trazer para o catálogo. O personalização é projetado para ser uma ferramenta que aumenta a perceção humana e as equipas. Uma forma de o fazer é fornecendo informações a grupos editoriais sobre o que é sobre produtos, artigos ou conteúdos que impulsionam o comportamento. Por exemplo, o cenário da aplicação em vídeo pode mostrar que existe uma característica importante chamada "Action.VideoEntities.Cat:true", levando a equipa editorial a trazer mais vídeos para gatos.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Resolver problemas e erros comuns

Problemas e erros comuns podem ser corrigidos alterando o seu código de aplicação para que não envie funcionalidades inadequadas ou formatadas incorretamente para o Personaler. 

Os erros comuns ao enviar funcionalidades incluem:

* Envio de informação pessoalmente identificável (PII). PiI específico para um indivíduo (nome, número de telefone, números de cartão de crédito, endereços IP) não deve ser usado com Personaler. Se a sua aplicação precisar de rastrear os utilizadores, utilize um UUID não identificado ou outro número userID. Na maioria dos cenários isto também é problemático.
* Com um grande número de utilizadores, é improvável que a interação de cada utilizador pese mais do que toda a interação da população, pelo que o envio de IDs de utilizador (mesmo que não PII) provavelmente irá adicionar mais ruído do que valor ao modelo.
* Envio de campos de data como semps de tempo precisos em vez de valores de tempo apresentando. Ter funcionalidades como Context.TimeStamp.Day=Monday ou "Context.TimeStamp.Hour"="13" é mais útil. Haverá no máximo 7 ou 24 valores de características para cada um. Mas "Context.TimeStamp":"1985-04-12T23:20:50.52Z" é tão preciso que não haverá forma de aprender com ele porque nunca mais vai acontecer.

## <a name="next-steps"></a>Passos seguintes

Compreender [a escalabilidade e o desempenho](concepts-scalability-performance.md) com o Personalizer.

