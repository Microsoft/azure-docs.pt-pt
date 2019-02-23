---
title: Como criar um projeto? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Como criar um projeto no Translator personalizado?
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: dde1bfbaf810d07a2b992258dd8674efec302dc6
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737166"
---
# <a name="create-a-project"></a>Criar um projeto

Um projeto é um contentor para um modelos, documentos e testes. Cada projeto inclui automaticamente todos os documentos são carregados dessa área de trabalho que tenham o par de idioma correto.

A criação de projeto é o primeiro passo para criar um modelo.

## <a name="create-a-project"></a>Crie um projeto:

1.  Na [Translator personalizado](https://portal.customtranslator.azure.ai) portal, clique em criar projeto.

    ![Criar o projeto](media/how-to/how-to-create-project.png)

2.  Introduza os seguintes detalhes sobre o seu projeto na caixa de diálogo:

    a.  Nome do projeto (obrigatório): Dê um nome exclusivo e significativo ao seu projeto. Não é necessário mencionar os idiomas dentro do título.

    b.  Descrição: Um breve resumo sobre o projeto. Esta descrição não influenciam sobre o comportamento do seu sistema de personalizado resultante ou o tradutor personalizado, mas pode ajudar a diferenciar entre projetos diferentes.

    c.  Par de idioma (obrigatório): Selecione o idioma que está a tradução de e para.

    d.  Categoria (obrigatória): Selecione a categoria que é mais adequada para seu projeto. A categoria descreve a terminologia e o estilo dos documentos que pretende converter.

    e.  Descrição de categoria: Utilize este campo para descrever melhor o campo específico ou do setor em que está trabalhando. Por exemplo, se a categoria do seu medicina, pode adicionar um documento em particular, tais cirurgia ou pediatrics. A descrição tem não influenciam sobre o comportamento do seu sistema de personalizado resultante ou o tradutor personalizado.

    f.  Etiqueta de projeto: O [etiqueta de projeto](workspace-and-project.md#project-labels) distingue entre projetos com o mesmo par de idioma e a categoria. Como melhor prática, utilize uma etiqueta *apenas* se estiver a planear criar vários projetos para o mesmo par de idioma e a mesma categoria e deseja acessar esses projetos com uma CategoryID diferente. Não utilize este campo se estiver a criar sistemas para apenas uma categoria. Uma etiqueta de projeto não é necessário e não é útil para distinguir entre pares de idiomas. Pode utilizar o mesmo rótulo de vários projetos.

    ![Criar caixa de diálogo do projeto](media/how-to/how-to-create-project-dialog.png)

3.  Clique em Criar

## <a name="view-project-details"></a>Ver detalhes do projeto

A página de destino do Translator personalizada mostra os primeiros 10 projetos em sua área de trabalho. Apresenta o nome do projeto, o par de linguagem, a categoria, o estado e a pontuação BLEU.

Depois de selecionar um projeto, verá o seguinte na página do projeto:

- CategoryID: Uma CategoryID é criada pela concatenação o WorkspaceID, a etiqueta de projeto e o código de categoria. Utilize o CategoryID com a API de tradutor de texto para obter as traduções personalizadas.

- Botão de Train: Utilize este botão para iniciar uma [preparar um modelo](how-to-train-model.md).

- Adicione botão de documentos: Utilize este botão para [carregar documentos](how-to-upload-document.md).

- Botão de documentos de filtro: Utilize este botão para filtrar e pesquisar para document(s) específico.

    ![Ver detalhes do projeto](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais [como pesquisar, editar, eliminar o projeto](how-to-search-edit-delete-projects.md).
- Saiba mais [como carregar documentos](how-to-upload-document.md) para criar modelos de tradução.
