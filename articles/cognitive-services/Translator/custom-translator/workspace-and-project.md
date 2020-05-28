---
title: O que é um espaço de trabalho e um projeto? - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo explicará as diferenças entre um espaço de trabalho e um projeto, bem como categorias de projetos e etiquetas para o serviço Tradutor Personalizado.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: b6d43ff535fd45c73a80290442102dd8034d3903
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997031"
---
# <a name="what-is-a-custom-translator-workspace"></a>O que é um espaço de trabalho de Tradutor Personalizado?

Um espaço de trabalho é uma área de trabalho para compor e construir o seu sistema de tradução personalizada. Um espaço de trabalho pode conter vários projetos, modelos e documentos. Todo o trabalho que faz no Custom Tradutor está dentro de um espaço de trabalho específico.

O espaço de trabalho é privado para si e para as pessoas que convida para o seu espaço de trabalho. Pessoas não convidadas não têm acesso ao conteúdo do seu espaço de trabalho. Pode convidar quantas pessoas quiserem para o seu espaço de trabalho e modificar ou remover o seu acesso a qualquer momento. Também pode criar um novo espaço de trabalho. Por defeito, um espaço de trabalho não conterá quaisquer projetos ou documentos que estejam dentro dos seus outros espaços de trabalho.

## <a name="what-is-a-custom-translator-project"></a>O que é um projeto de Tradutor Personalizado?

Um projeto é um invólucro para um modelo, documentos e testes. Cada projeto inclui automaticamente todos os documentos que são enviados para aquele espaço de trabalho que têm o par de idiomas correto. Por exemplo, se tiver um projeto inglês para espanhol e um projeto espanhol para inglês, os mesmos documentos serão incluídos em ambos os projetos. Cada projeto tem um ID de categoria associado a ele que é usado na consulta da [V3 API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) para traduções. A categoria ID é um parâmetro usado para obter traduções de um sistema personalizado construído com Tradutor Personalizado.

## <a name="project-categories"></a>Categorias de projetos

A categoria identifica o domínio – a área de terminologia e estilo que pretende utilizar – para o seu projeto. Escolha a categoria mais relevante para os seus documentos. Em alguns casos, a sua escolha da categoria influencia diretamente o comportamento do Tradutor Personalizado.

Temos dois conjuntos de modelos de base. São gerais e tecnológicos. Se a categoria **Tecnologia** for selecionada, serão utilizados os modelos de base da Tecnologia. Para qualquer outra seleção de categorias, são utilizados os modelos de base geral. O modelo de base da tecnologia sai-se bem no domínio tecnológico, mas mostra uma menor qualidade, se as frases usadas para tradução não se enquadrarem no domínio tecnológico. Sugerimos que os clientes selecionem a categoria Tecnologia apenas se as frases se enquadrarem estritamente no domínio tecnológico.

No mesmo espaço de trabalho, pode criar projetos para o mesmo par de idiomas em diferentes categorias. O Tradutor Personalizado impede a criação de um projeto duplicado com o mesmo par de idiomas e categoria. Aplicar uma etiqueta no seu projeto permite-lhe evitar esta restrição. Não utilize etiquetas a menos que esteja a construir sistemas de tradução para vários clientes, uma vez que adicionar um rótulo único ao seu projeto será refletido na categoria de projetos.

## <a name="project-labels"></a>Rótulos do projeto

O Custom Tradutor permite-lhe atribuir uma etiqueta de projeto ao seu projeto. O rótulo do projeto distingue entre vários projetos com o mesmo par de línguas e categoria. Como uma boa prática, evite utilizar etiquetas de projeto a menos que seja necessário.

A etiqueta do projeto é utilizada como parte da categoriaID. Se o rótulo do projeto for deixado por definir ou for definido de forma idêntica entre projetos, então os projetos com a mesma categoria e *diferentes* pares de idiomas partilharão a mesma categoria ID. Esta abordagem é vantajosa porque permite que você ou o seu cliente alternam entre idiomas ao utilizar o Tradutor sem se preocupar com um Id de categoria único em cada projeto.

Por exemplo, se quisesse permitir traduções no domínio tecnológico do inglês para o francês e do francês para o inglês, criaria dois projetos: um para inglês - \> francês, e outro para francês - \> inglês. Especificaria a mesma categoria (Tecnologia) para ambos e deixaria o rótulo do projeto em branco. O CategoryID para ambos os projetos corresponderia, para que eu pudesse consultar a API para traduções em inglês e francês sem ter de modificar o meu Id de categoria.

Se é um fornecedor de serviços linguísticos e quer servir vários clientes com diferentes modelos que mantêm a mesma categoria e par de idiomas, então usar um rótulo de projeto para diferenciar os clientes seria uma decisão sábia.

## <a name="next-steps"></a>Próximos passos

- Leia sobre [Formação e modelo](training-and-model.md) para saber, como construir eficientemente um modelo de tradução.
