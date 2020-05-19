---
title: Como criar um projeto - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo explica como criar e gerir um projeto no Tradutor Personalizado de Serviços Cognitivos Azure.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: e01f3ddde96903716cf1fcff0426791ff3a90e07
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587668"
---
# <a name="create-a-project"></a>Criar um projeto

Um projeto é um recipiente para modelos, documentos e testes. Cada projeto inclui automaticamente todos os documentos que são enviados para aquele espaço de trabalho que têm o par de idiomas correto.

Criar projeto é o primeiro passo para a construção de um modelo.

## <a name="create-a-project"></a>Criar um projeto:

1.  No portal [Tradutor Personalizado,](https://portal.customtranslator.azure.ai) clique em Criar projeto.

    ![Criar o projeto](media/how-to/how-to-create-project.png)

2.  Introduza os seguintes detalhes sobre o seu projeto no diálogo:

    a.  Nome do projeto (obrigatório): Dê ao seu projeto um nome único e significativo. Não é necessário mencionar as línguas dentro do título.

    b.  Descrição: Um breve resumo sobre o projeto. Esta descrição não tem influência sobre o comportamento do Tradutor Personalizado ou do seu sistema personalizado resultante, mas pode ajudá-lo a diferenciar entre diferentes projetos.

    c.  Par de idiomas (obrigatório): Selecione o idioma de que está a traduzir e para.

    d.  Categoria (necessária): Selecione a categoria mais adequada para o seu projeto. A categoria descreve a terminologia e o estilo dos documentos que pretende traduzir.

    e.  Descrição da categoria: Utilize este campo para descrever melhor o campo ou a indústria em que está a trabalhar. Por exemplo, se a sua categoria for medicina, pode adicionar um documento específico, tal cirurgia, ou pediatria. A descrição não tem influência sobre o comportamento do Tradutor Personalizado ou do seu sistema personalizado resultante.

    f.  Rótulo do [projeto:](workspace-and-project.md#project-labels) O rótulo do projeto distingue entre projetos com o mesmo par de línguas e categoria. Como uma boa prática, utilize um rótulo *apenas* se estiver a planear construir vários projetos para o mesmo par de idiomas e mesma categoria e quiser aceder a estes projetos com uma categoria diferente. Não use este campo se estiver a construir sistemas para uma categoria. Não é necessário um rótulo de projeto e não é útil para distinguir entre pares de línguas. Pode usar o mesmo rótulo para vários projetos.

    ![Criar diálogo de projeto](media/how-to/how-to-create-project-dialog.png)

3.  Clique em Criar

## <a name="view-project-details"></a>Ver detalhes do projeto

A página de aterragem do Tradutor Personalizado mostra os primeiros 10 projetos no seu espaço de trabalho. Exibe o nome do projeto, par de idiomas, categoria, estado e pontuação BLEU.

Depois de selecionar um projeto, verá o seguinte na página do projeto:

- CategoriaID: Um ID de categoria é criado através da concatenação do WorkspaceID, do rótulo do projeto e do código de categoria. Utiliza o CategoryID com o Tradutor de Texto para obter traduções personalizadas.

- Botão de trem: Utilize este botão para iniciar um [treino de um modelo](how-to-train-model.md).

- Adicionar botão de documentos: Utilize este botão para [carregar documentos](how-to-upload-document.md).

- Botão de documentos de filtro: Utilize este botão para filtrar e procurar documentos específicos.

    ![Ver detalhes do projeto](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba [como pesquisar, editar, excluir projeto.](how-to-search-edit-delete-projects.md)
- Aprenda [a carregar documento](how-to-upload-document.md) para construir modelos de tradução.
