---
title: O que é uma área de trabalho e projeto? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Uma área de trabalho é uma área de trabalho para compor e criação de seu sistema de tradução personalizadas. Uma área de trabalho pode conter vários projetos, modelos e documentos. Um projeto é um wrapper para um modelo, documentos e testes. Cada projeto inclui automaticamente todos os documentos são carregados dessa área de trabalho que tenham o par de idioma correto.
services: cognitive-services
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: dc45847f3dc2e11af9b7cf566fdd6780ec2b9dac
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762165"
---
# <a name="what-is-a-custom-translator-workspace"></a>O que é uma área de trabalho do Translator personalizado?

Uma área de trabalho é uma área de trabalho para compor e criação de seu sistema de tradução personalizadas. Uma área de trabalho pode conter vários projetos, modelos e documentos. Todo o trabalho que fizer no Translator personalizado está dentro de uma área de trabalho específica.

Área de trabalho é privada para e as pessoas que convidar na área de trabalho. As pessoas desconvidadas não tem acesso ao conteúdo da área de trabalho. Pode convidar tantas pessoas como, como na área de trabalho e modifica ou remover o acesso em qualquer altura. Também pode criar uma nova área de trabalho. Por predefinição uma área de trabalho não irá conter quaisquer projetos ou documentos que estão dentro de suas outras áreas de trabalho.

## <a name="what-is-a-custom-translator-project"></a>O que é um projeto de tradutor de personalizado?

Um projeto é um wrapper para um modelo, documentos e testes. Cada projeto inclui automaticamente todos os documentos são carregados dessa área de trabalho que tenham o par de idioma correto. Por exemplo, se tiver um inglês para espanhol projeto e um espanhol para o projeto em inglês, os documentos mesmo serão incluídos nos dois projetos. Cada projeto possui uma CategoryID associada, pelo que é utilizada ao consultar o [V3 API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) para traduções. CategoryID é o parâmetro utilizado para obter as traduções de um sistema personalizado criado com o Translator personalizado.

## <a name="project-categories"></a>Categorias de projeto

A categoria identifica o domínio – a área de terminologia e estilo de que pretende utilizar – para o seu projeto. Escolha a categoria mais relevante para os seus documentos. Em alguns casos, à sua escolha da categoria influencia diretamente o comportamento do tradutor personalizado.

Temos dois conjuntos de modelos de linha de base. Elas são gerais e tecnologia. Se a categoria **tecnologia** é selecionado, os modelos de linha de base da tecnologia serão utilizados. Para outra seleção de categoria, são utilizados os modelos de linha de base geral. O modelo de linha de base da tecnologia faz bem no domínio de tecnologia, mas mostra mais baixa qualidade, se as frases utilizadas para a tradução não se enquadram dentro do domínio de tecnologia. Sugerimos que aos clientes selecionar categoria tecnologia apenas se as frases enquadram-se estritamente dentro do domínio de tecnologia.

Na mesma área de trabalho, pode criar projetos para o mesmo par de idioma em diferentes categorias. Tradutor personalizado impede a criação de um projeto de duplicados com o mesmo par de idioma e a categoria. Aplicar uma etiqueta ao seu projeto permite-lhe evitar esta restrição. Não utilize etiquetas, a menos que esteja a criar sistemas de tradução para vários clientes, como adicionar que uma etiqueta exclusiva ao seu projeto será apresentada nos seus projetos CategoryID.

## <a name="project-labels"></a>Etiquetas de projeto

Tradutor personalizado permite-lhe atribuir uma etiqueta de projeto ao seu projeto. A etiqueta de projeto distingue entre vários projetos com o mesmo par de idioma e a categoria. Como melhor prática, evite utilizar projeto das etiquetas, a menos que necessário.

A etiqueta de projeto é utilizada como parte do CategoryID. Se a etiqueta de projeto é deixada não definida ou é definida de forma idêntica entre projetos, em seguida, projetos com a mesma categoria e *diferentes* pares de idiomas irão partilhar o mesmo CategoryID. Essa abordagem é vantajosa porque ela permite que ou seu cliente, para alternar entre idiomas ao usar a API de tradutor de texto sem se preocupar sobre uma CategoryID exclusiva para cada projeto.

Por exemplo, se quiser ativar traduções em tecnologia domínio inglês de francês e francês para inglês, eu criaria dois projetos: um para inglês -\> francês e outro para o francês -\> inglês. Eu especificar da mesma categoria (tecnologia) para ambos e deixe a etiqueta de projeto em branco. O CategoryID para ambos os projetos corresponderia, poderia consultar a API para inglês e francês traduções sem ter de modificar meu CategoryID.

Se for um fornecedor de serviços de linguagem e deve servir vários clientes com diferentes modelos de mantém a mesma categoria e um par de linguagem, em seguida, usando uma etiqueta de projeto para diferenciar entre os clientes seria uma sábia decisão.

## <a name="next-steps"></a>Passos Seguintes

- Leia sobre [treinamento e o modelo](training-and-model.md) saber como criar um modelo de tradução de forma eficaz.
