---
title: O que é um espaço de trabalho e um projeto? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo explicará as diferenças entre um espaço de trabalho e um projeto, bem como categorias de projetos e etiquetas para o serviço De Tradutor Personalizado.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41586b2ec96d30fd583cbc9a746493ed9206548b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510747"
---
# <a name="what-is-a-custom-translator-workspace"></a>O que é um espaço de trabalho de tradutor personalizado?

Um espaço de trabalho é uma área de trabalho para compor e construir o seu sistema de tradução personalizado. Um espaço de trabalho pode conter vários projetos, modelos e documentos. Todo o trabalho que faz no Custom Tradutor está dentro de um espaço de trabalho específico.

O espaço de trabalho é privado para si e para as pessoas que convida para o seu espaço de trabalho. Pessoas não convidadas não têm acesso ao conteúdo do seu espaço de trabalho. Pode convidar o máximo de pessoas que quiser para o seu espaço de trabalho e modificar ou remover o seu acesso a qualquer momento. Também pode criar um novo espaço de trabalho. Por predefinição, um espaço de trabalho não conterá quaisquer projetos ou documentos que estejam dentro dos seus outros espaços de trabalho.

## <a name="what-is-a-custom-translator-project"></a>O que é um projeto de Tradutor Personalizado?

Um projeto é um invólucro para um modelo, documentos e testes. Cada projeto inclui automaticamente todos os documentos que são enviados para esse espaço de trabalho que têm o par de linguagem correto. Por exemplo, se tiver um projeto inglês para espanhol e um projeto espanhol para inglês, os mesmos documentos serão incluídos em ambos os projetos. Cada projeto tem uma CategoriaID associada a ele que é usada na consulta da [API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) para traduções. CategoriaID é o parâmetro usado para obter traduções de um sistema personalizado construído com Tradutor Personalizado.

## <a name="project-categories"></a>Categorias de projetos

A categoria identifica o domínio – a área de terminologia e estilo que pretende utilizar – para o seu projeto. Escolha a categoria mais relevante para os seus documentos. Em alguns casos, a sua escolha da categoria influencia diretamente o comportamento do Tradutor Personalizado.

Temos dois conjuntos de modelos de base. São gerais e tecnológicos. Se a categoria **Tecnologia** for selecionada, os modelos de base de tecnologia serão utilizados. Para qualquer outra seleção de categorias, são utilizados os modelos de base geral. O modelo de base de tecnologia sai-se bem no domínio da tecnologia, mas mostra menor qualidade, se as frases utilizadas para a tradução não se enquadrarem no domínio tecnológico. Sugerimos que os clientes selecionem a categoria Tecnologia apenas se as frases se enquadrarem estritamente no domínio tecnológico.

No mesmo espaço de trabalho, pode criar projetos para o mesmo par de línguas em diferentes categorias. O Tradutor Personalizado impede a criação de um projeto duplicado com o mesmo par e categoria linguística. A aplicação de uma etiqueta no seu projeto permite evitar esta restrição. Não utilize rótulos a não ser que esteja a construir sistemas de tradução para vários clientes, uma vez que adicionar uma etiqueta única ao seu projeto será refletida nos seus projetos CategoriaID.

## <a name="project-labels"></a>Rótulos de projeto

O Custom Tradutor permite-lhe atribuir uma etiqueta de projeto ao seu projeto. O rótulo do projeto distingue vários projetos com o mesmo par e categoria linguística. Como boas práticas, evite utilizar etiquetas de projeto a menos que seja necessário.

A etiqueta do projeto é utilizada como parte da categoriaID. Se a etiqueta do projeto for deixada sem design ou for definida de forma idêntica em todos os projetos, então os projetos com a mesma categoria e *diferentes* pares linguísticos partilharão a mesma categoriaID. Esta abordagem é vantajosa porque permite que você ou o seu cliente alterem entre idiomas ao utilizar a API do Tradutor de Texto sem se preocupar com uma CategoriaID que seja única para cada projeto.

Por exemplo, se quisesse permitir traduções no domínio tecnológico do inglês para o francês e do francês para o inglês, criaria dois projetos: um para inglês - \> francês e outro para francês - \> inglês. Especificaria a mesma categoria (Tecnologia) para ambos e deixaria a etiqueta do projeto em branco. A categoriaID para ambos os projetos corresponderia, para que eu pudesse consultar a API para traduções em inglês e francês sem ter de modificar a minha categoriaID.

Se você é um prestador de serviços linguísticos e quer servir vários clientes com diferentes modelos que mantêm a mesma categoria e par de idiomas, então usar um rótulo de projeto para diferenciar entre clientes seria uma decisão sábia.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre [Formação e modelo](training-and-model.md) para saber, como construir eficientemente um modelo de tradução.
