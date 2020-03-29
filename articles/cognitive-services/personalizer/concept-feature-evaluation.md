---
title: Avaliação de funcionalidades - Personalizer
titleSuffix: Azure Cognitive Services
description: Ao executar uma Avaliação no seu recurso Personalizer a partir do portal Azure, o Personalizer fornece informações sobre quais as características do contexto e ações que estão a influenciar o modelo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 31243b5e9da55aafbc376fa416c1b00a4499c116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70242421"
---
# <a name="feature-evaluation"></a>Avaliação de funcionalidade

Ao executar uma Avaliação no seu recurso Personalizer a partir do [portal Azure,](https://portal.azure.com)o Personalizer fornece informações sobre quais as características do contexto e ações que estão a influenciar o modelo. 

Isto é útil para:

* Imagine funcionalidades adicionais que possa utilizar, inspirando-se no que as funcionalidades são mais importantes no modelo.
* Veja quais as funcionalidades que não são importantes e potencialmente removê-las ou analisar ainda mais o que pode estar a afetar o uso.
* Fornecer orientação às equipas editoriais ou de curadoria sobre novos conteúdos ou produtos que valha a pena trazer para o catálogo.
* Problemas comuns que acontecem ao enviar funcionalidades para personalizar.

As características mais importantes têm pesos mais fortes no modelo. Como estas características têm um peso mais forte, tendem a estar presentes quando o Personalizer obtém recompensas mais elevadas.

## <a name="getting-feature-importance-evaluation"></a>Obtenção de avaliação de importância de recursos

Para ver os resultados da funcionalidade, deve fazer uma avaliação. A avaliação cria etiquetas de recurso legíveis pelo homem com base nos nomes de recurso observados durante o período de avaliação.

A informação resultante sobre a importância da funcionalidade representa o atual modelo online Personalizer. A avaliação analisa a importância do modelo guardado na data final do período de avaliação, depois de ter sido submetido a toda a formação realizada durante a avaliação, com a atual política de aprendizagem online. 

Os resultados da funcionalidade não representam outras políticas e modelos testados ou criados durante a avaliação.  A avaliação não incluirá funcionalidades enviadas ao Personalizer após o final do período de avaliação.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Como interpretar a avaliação de importância da característica

Personalizer avalia funcionalidades criando "grupos" de funcionalidades que têm uma importância semelhante. Um grupo pode dizer-se que tem uma importância globalmente mais forte do que outros, mas dentro do grupo, ordenar características é alfabeticamente.

A informação sobre cada Recurso inclui:

* Se a funcionalidade vem do Context ou Das Ações.
* Chave de recurso e valor.

Por exemplo, uma aplicação de encomenda de uma gelataria pode ver "Context.Weather:Hot" como uma característica muito importante.

O personalizer exibe correlações de características que, quando tidas em conta em conjunto, produzem recompensas mais elevadas.

Por exemplo, pode ver "Context.Weather:Hot *with* Action.MenuItem:IceCream" bem como "Context.Weather:Cold *with* Action.MenuItem:WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Ações que pode tomar com base na avaliação de recursos

### <a name="imagine-additional-features-you-could-use"></a>Imagine funcionalidades adicionais que poderia usar

Inspire-se nas características mais importantes do modelo. Por exemplo, se vir "Context.MobileBattery:Low" numa aplicação móvel de vídeo, pode pensar que o tipo de ligação também pode fazer com que os clientes escolham ver um videoclip sobre outro, em seguida, adicionar funcionalidades sobre o tipo de conectividade e largura de banda na sua aplicação.

### <a name="see-what-features-are-not-important"></a>Veja quais as funcionalidades que não são importantes

Potencialmente remova características não importantes ou analise ainda mais o que pode afetar o uso. As características podem estar baixas por muitas razões. Pode ser que, genuinamente, a funcionalidade não afete o comportamento do utilizador. Mas também pode significar que a funcionalidade não é aparente para o utilizador. 

Por exemplo, um site de vídeo poderia ver que "Action.VideoResolution=4k" é uma funcionalidade de baixa importância, contradizendo a pesquisa do utilizador. A causa pode ser que a aplicação nem sequer mencione ou mostre a resolução de vídeo, para que os utilizadores não mudem o seu comportamento com base no mesmo.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Fornecer orientação às equipas editoriais ou de curadoria

Forneça orientações sobre novos conteúdos ou produtos que valha a pena trazer para o catálogo. O personalizer é projetado para ser uma ferramenta que aumenta a perceção humana e as equipas. Uma das formas de o fazer é fornecendo informações aos grupos editoriais sobre o que é sobre produtos, artigos ou conteúdos que impulsionam o comportamento. Por exemplo, o cenário de aplicação de vídeo pode mostrar que há uma característica importante chamada "Action.VideoEntities.Cat:true", levando a equipa editorial a trazer mais vídeos de gatos.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Problemas de resolução de problemas e erros comuns

Problemas e erros comuns podem ser corrigidos alterando o seu código de aplicação para que não envie funcionalidades inadequadas ou mal formatadas para personalizar o Personalizer. 

Erros comuns ao enviar funcionalidades incluem:

* Envio de informações pessoalmente identificáveis (PII). PiI específico de um indivíduo (como nome, número de telefone, números de cartão de crédito, endereços IP) não deve ser utilizado com personalizer. Se a sua aplicação necessitar de rastrear os utilizadores, utilize um UUID não identificado ou algum outro número userID. Na maioria dos cenários, isto também é problemático.
* Com um grande número de utilizadores, é pouco provável que a interação de cada utilizador pese mais do que toda a interação da população, pelo que o envio de IDs de utilizador (mesmo que não-PII) provavelmente irá adicionar mais ruído do que valor ao modelo.
* Envio de campos de data como selos temporais precisos em vez de valores de tempo agrétes. Ter funcionalidades como Context.TimeStamp.Day=Monday ou "Context.TimeStamp.Hour="13" é mais útil. Haverá no máximo 7 ou 24 valores de características para cada um. Mas "Context.TimeStamp":"1985-04-12T23:20:50.52Z" é tão preciso que não haverá forma de aprender com ele porque nunca mais vai acontecer.

## <a name="next-steps"></a>Passos seguintes

Compreenda [a escalabilidade e desempenho](concepts-scalability-performance.md) com o Personalizer.

