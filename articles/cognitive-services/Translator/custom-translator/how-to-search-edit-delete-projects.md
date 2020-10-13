---
title: Como pesquisar, editar e apagar projeto - Custom Tradutor
titleSuffix: Azure Cognitive Services
description: O Custom Tradutor fornece várias formas de gerir os seus projetos de forma eficiente. Pode criar vários projetos, pesquisar com base nos seus critérios, editar os seus projetos. A eliminação de um projeto também é possível no Custom Tradutor.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 4c48a8a18a3ad0487b4c3840847c7f5edbb6d29f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510381"
---
# <a name="search-edit-and-delete-projects"></a>Pesquisar, editar e apagar projetos

O Custom Tradutor fornece várias formas de gerir os seus projetos de forma eficiente. Pode criar muitos projetos, pesquisar com base nos seus critérios e editar os seus projetos. A eliminação de um projeto também é possível no Custom Tradutor.  

## <a name="search-and-filter-projects"></a>Projetos de pesquisa e filtragem

A ferramenta de filtro permite-lhe pesquisar projetos por diferentes condições de filtro. Filtra como nome de projeto, estado, origem e linguagem-alvo, e categoria do projeto.

1. Clique no botão do filtro.

    ![Projeto de pesquisa](media/how-to/how-to-search-project.png)

2. Pode filtrar por qualquer (ou todos) dos seguintes campos: nome do projeto, linguagem de origem, linguagem-alvo, categoria e disponibilidade de projeto.

3. Clique em aplicar.

    ![Opções de filtro de projeto de pesquisa](media/how-to/how-to-search-project-filters.png)

4. Limpe o filtro para visualizar todos os seus projetos tocando "Clear".

## <a name="edit-a-project"></a>Editar um projeto

O Custom Tradutor dá-lhe a capacidade de editar o nome e descrição de um projeto. Outros metadados de projeto como a categoria, a linguagem de origem e a linguagem-alvo não estão disponíveis para edição. Os passos abaixo descrevem como editar um projeto.

1. Clique no ícone do lápis que aparece quando paira sobre um projeto.

    ![Projeto de edição](media/how-to/how-to-edit-project.png)

2. No diálogo, pode modificar o nome do projeto, a descrição do projeto, a descrição da categoria e o rótulo do projeto se nenhum modelo for implementado. Não é possível modificar a categoria ou o par de idiomas uma vez que o projeto é criado.

    ![Editar o diálogo do projeto](media/how-to/how-to-edit-project-dialog.png)

3. Clique no botão "Guardar".

## <a name="delete-a-project"></a>Excluir um projeto

Pode apagar um projeto quando já não precisar dele. A Make the Project não tem modelos em estado ativo, tais como implantados, formação submetida, processamento de dados, implantação, etc., caso contrário, a operação de eliminação falharia. Abaixo os passos descrevem como apagar um projeto.

1. Paire em qualquer registo do projeto e clique no ícone do lixo.

   ![Excluir projeto](media/how-to/how-to-delete-project.png)

2. Confirme a eliminação. A eliminação de um projeto irá eliminar todos os modelos que foram criados dentro desse projeto. A eliminação do projeto não afetará os seus documentos.

   ![Apagar diálogo de confirmação](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>Passos seguintes

- [Faça upload de documentos](how-to-upload-document.md) para começar a construir o seu modelo de tradução personalizado.
