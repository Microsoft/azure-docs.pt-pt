---
title: O que é um espaço de trabalho e um projeto? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo explicará as diferenças entre um espaço de trabalho e um projeto, bem como categorias e rótulos de projeto para o serviço de Tradutor personalizado.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d2f7903fa85c645357e46a753d1cb043e0893254
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647308"
---
# <a name="what-is-a-custom-translator-workspace"></a>O que é um espaço de trabalho de Tradutor personalizado?

Um espaço de trabalho é uma área de trabalho para compor e criar seu sistema de tradução personalizado. Um espaço de trabalho pode conter vários projetos, modelos e documentos. Todo o trabalho que você faz no Tradutor personalizado está dentro de um espaço de trabalho específico.

O espaço de trabalho é privado para você e as pessoas que você convida em seu espaço de trabalho. Pessoas não convidadas não têm acesso ao conteúdo do seu espaço de trabalho. Você pode convidar quantas pessoas quiser em seu espaço de trabalho e modificar ou remover o acesso a qualquer momento. Você também pode criar um novo espaço de trabalho. Por padrão, um espaço de trabalho não conterá projetos ou documentos que estejam dentro de outros espaços de trabalho.

## <a name="what-is-a-custom-translator-project"></a>O que é um projeto de Tradutor personalizado?

Um projeto é um wrapper para um modelo, documentos e testes. Cada projeto inclui automaticamente todos os documentos que são carregados nesse espaço de trabalho que têm o par de idiomas correto. Por exemplo, se você tiver um projeto em inglês para espanhol e um projeto de espanhol para inglês, os mesmos documentos serão incluídos em ambos os projetos. Cada projeto tem um CategoryID associado a ele que é usado ao consultar a [API v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) para traduções. CategoryID é um parâmetro usado para obter traduções de um sistema personalizado criado com o tradutor personalizado.

## <a name="project-categories"></a>Categorias de projeto

A categoria identifica o domínio – a área de terminologia e estilo que você deseja usar – para seu projeto. Escolha a categoria mais relevante para seus documentos. Em alguns casos, sua escolha da categoria influencia diretamente o comportamento do tradutor personalizado.

Temos dois conjuntos de modelos de linha de base. Elas são gerais e tecnológicas. Se a **tecnologia** de categoria estiver selecionada, os modelos de linha de base de tecnologia serão usados. Para qualquer outra seleção de categoria, os modelos de linha de base gerais são usados. O modelo de linha de base de tecnologia funciona bem no domínio de tecnologia, mas ele mostra uma qualidade inferior, se as frases usadas para tradução não estiverem dentro do domínio de tecnologia. Sugerimos que os clientes selecionem a tecnologia de categoria somente se as frases ficarem estritamente dentro do domínio de tecnologia.

No mesmo espaço de trabalho, você pode criar projetos para o mesmo par de idiomas em categorias diferentes. O tradutor personalizado impede a criação de um projeto duplicado com o mesmo par de idiomas e categoria. A aplicação de um rótulo ao seu projeto permite evitar essa restrição. Não use rótulos, a menos que você esteja criando sistemas de tradução para vários clientes, pois adicionar um rótulo exclusivo ao seu projeto será refletido em seu CategoryID de projetos.

## <a name="project-labels"></a>Rótulos do projeto

O tradutor personalizado permite atribuir um rótulo de projeto ao seu projeto. O rótulo do projeto distingue entre vários projetos com o mesmo par de linguagem e categoria. Como prática recomendada, evite usar rótulos de projeto, a menos que necessário.

O rótulo do projeto é usado como parte da CategoryID. Se o rótulo do projeto for à esquerda desconfigurado ou for definido de forma idêntica entre os projetos, os projetos com a mesma categoria e pares de idiomas *diferentes* compartilharão a mesma CategoryID. Essa abordagem é vantajosa porque permite que você ou seu cliente alterne entre linguagens ao usar a API de tradução de texto sem se preocupar com uma CategoryID exclusiva a cada projeto.

Por exemplo, se eu quisesse habilitar traduções no domínio de tecnologia de Inglês para francês e de francês para inglês, criaria dois projetos: um para o inglês-\> francês e outro para o francês-\> em inglês. Eu poderia especificar a mesma categoria (tecnologia) para ambos e deixar o rótulo do projeto em branco. A CategoryID para ambos os projetos seria correspondente, portanto, eu poderia consultar a API para conversões em inglês e francês sem precisar modificar minha CategoryID.

Se você for um provedor de serviços de linguagem e quiser atender a vários clientes com modelos diferentes que retêm a mesma categoria e par de idiomas, usar um rótulo de projeto para diferenciar os clientes seria uma decisão inteligente.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre [treinamento e modelo](training-and-model.md) para saber, como criar com eficiência um modelo de tradução.
