---
title: Como criar um projeto - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo explica como criar e gerir um projeto no Azure Cognitive Services Custom Tradutor.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 9e3aa52323f44e6c1407fe2a542e40ee06370043
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895803"
---
# <a name="create-a-project"></a>Criar um projeto

Um projeto é um recipiente para modelos, documentos e testes. Cada projeto inclui automaticamente todos os documentos que são enviados para esse espaço de trabalho que têm o par de linguagem correto.

Criar projeto é o primeiro passo para a construção de um modelo.

## <a name="create-a-project"></a>Criar um projeto:

1.  No portal [Do Tradutor Personalizado,](https://portal.customtranslator.azure.ai) clique em Criar projeto.

    ![Criar o projeto](media/how-to/how-to-create-project.png)

2.  Insira os seguintes detalhes sobre o seu projeto no diálogo:

    a.  Nome do projeto (obrigatório): Dê ao seu projeto um nome único e significativo. Não é necessário mencionar as línguas dentro do título.

    b.  Descrição: Um breve resumo sobre o projeto. Esta descrição não tem influência sobre o comportamento do Tradutor Personalizado ou do seu sistema personalizado resultante, mas pode ajudá-lo a diferenciar diferentes projetos.

    c.  Par de idiomas (obrigatório): Selecione o idioma que está a traduzir de e para.

    d.  Categoria (necessária): Selecione a categoria mais adequada para o seu projeto. A categoria descreve a terminologia e o estilo dos documentos que pretende traduzir.

    e.  Descrição da categoria: Utilize este campo para descrever melhor o campo ou a indústria em que está a trabalhar. Por exemplo, se a sua categoria for medicina, pode adicionar um documento específico, tal cirurgia, ou pediatria. A descrição não tem influência sobre o comportamento do Tradutor Personalizado ou do seu sistema personalizado resultante.

    f.  Etiqueta do [projeto:](workspace-and-project.md#project-labels) O rótulo do projeto distingue entre projetos com o mesmo par de línguas e categoria. Como uma boa prática, use um rótulo *apenas* se estiver planejando construir vários projetos para o mesmo par de idiomas e mesma categoria e quiser aceder a estes projetos com uma categoriaID diferente. Não use este campo se estiver a construir sistemas apenas para uma categoria. Não é necessário um rótulo de projeto e não é útil para distinguir entre pares de línguas. Pode usar a mesma etiqueta para vários projetos.

    ![Criar diálogo de projeto](media/how-to/how-to-create-project-dialog.png)

3.  Clique em Criar

## <a name="view-project-details"></a>Ver detalhes do projeto

A página de aterragem do Tradutor Personalizado mostra os primeiros 10 projetos no seu espaço de trabalho. Apresenta o nome do projeto, par de idiomas, categoria, estado e pontuação BLEU.

Depois de selecionar um projeto, verá o seguinte na página do projeto:

- CategoriaID: A CategoryID é criada através da concatenação do WorkspaceID, da etiqueta do projeto e do código de categoria. Utiliza a CategoriaID com a API do Tradutor de Texto para obter traduções personalizadas. Para copiar, clique no ícone da cópia.

- Botão do comboio: Utilize este botão para iniciar um [treino de um modelo](how-to-train-model.md).

- Adicione o botão de documentos: Utilize este botão para [carregar documentos](how-to-upload-document.md).

- Botão de filtrar documentos: Utilize este botão para filtrar e procurar documentos específicos.

    ![Ver detalhes do projeto](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Próximos passos

- Saiba [como pesquisar, editar, eliminar o projeto.](how-to-search-edit-delete-projects.md)
- Saiba [como carregar o documento](how-to-upload-document.md) para construir modelos de tradução.
