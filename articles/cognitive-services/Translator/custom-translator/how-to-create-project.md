---
title: Como criar um projeto? -Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Como criar um projeto no Tradutor personalizado?
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a62cc6133fe01bf7478166c526e32b3215ceebf1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595746"
---
# <a name="create-a-project"></a>Criar um projeto

Um projeto é um contêiner para modelos, documentos e testes. Cada projeto inclui automaticamente todos os documentos que são carregados nesse espaço de trabalho que têm o par de idiomas correto.

A criação de projeto é a primeira etapa para a criação de um modelo.

## <a name="create-a-project"></a>Criar um projeto:

1.  No portal do [Tradutor personalizado](https://portal.customtranslator.azure.ai) , clique em criar projeto.

    ![Criar o projeto](media/how-to/how-to-create-project.png)

2.  Insira os seguintes detalhes sobre seu projeto na caixa de diálogo:

    a.  Nome do projeto (obrigatório): Dê ao seu projeto um nome exclusivo e significativo. Não é necessário mencionar os idiomas dentro do título.

    b.  Descrição: Um breve resumo sobre o projeto. Essa descrição não tem influência sobre o comportamento do tradutor personalizado ou do seu sistema personalizado resultante, mas pode ajudá-lo a diferenciar entre projetos diferentes.

    c.  Par de idiomas (obrigatório): Selecione o idioma do qual você está convertendo e para.

    d.  Categoria (obrigatório): Selecione a categoria mais apropriada para seu projeto. A categoria descreve a terminologia e o estilo dos documentos que você pretende traduzir.

    e.  Descrição da categoria: Use esse campo para descrever melhor o campo ou setor específico em que você está trabalhando. Por exemplo, se sua categoria estiver em medicina, você poderá adicionar um documento específico, como cirurgia ou Pediatrics. A descrição não tem influência sobre o comportamento do tradutor personalizado ou seu sistema personalizado resultante.

    f.  Rótulo do projeto: O [rótulo do projeto](workspace-and-project.md#project-labels) distingue entre projetos com o mesmo par de linguagem e categoria. Como prática recomendada, use um rótulo *somente* se você estiver planejando criar vários projetos para o mesmo par de idiomas e a mesma categoria e desejar acessar esses projetos com uma CategoryID diferente. Não use esse campo se você estiver criando sistemas apenas para uma categoria. Um rótulo de projeto não é necessário e não é útil para distinguir entre os pares de idiomas. Você pode usar o mesmo rótulo para vários projetos.

    ![Caixa de diálogo Criar projeto](media/how-to/how-to-create-project-dialog.png)

3.  Clique em Criar

## <a name="view-project-details"></a>Ver detalhes do projeto

A página de aterrissagem personalizada do tradutor mostra os 10 primeiros projetos em seu espaço de trabalho. Ele exibe o nome do projeto, o par de idiomas, a categoria, o status e a pontuação de BLEU.

Depois de selecionar um projeto, você verá o seguinte na página do projeto:

- Categoria Um CategoryID é criado concatenando o Workspaceid, o rótulo do projeto e o código de categoria. Use o CategoryID com a API de conversão de texto para obter traduções personalizadas.

- Botão treinar: Use este botão para iniciar um [modelo de treinamento](how-to-train-model.md).

- Botão adicionar documentos: Use este botão para [carregar documentos](how-to-upload-document.md).

- Botão filtrar documentos: Use esse botão para filtrar e procurar documentos específicos.

    ![Ver detalhes do projeto](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba [como Pesquisar, editar e excluir o projeto](how-to-search-edit-delete-projects.md).
- Saiba [como carregar o documento](how-to-upload-document.md) para criar modelos de tradução.
