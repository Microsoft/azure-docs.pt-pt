---
title: Clone um caderno Jupyter de GitHub com pré-visualização de cadernos Azure
description: Clone rapidamente um caderno Jupyter de um repositório GitHub e execute-o na sua conta de Azure Notebooks.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 267e79e7d4bf108ac3b2c72d64cee5a07ba638be
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87424482"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Quickstart: Clone um caderno em Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Neste arranque rápido, você copia um portátil Jupyter armazenado no GitHub para uma conta de Azure Notebooks. 

Os repositórios gitHub fornecem armazenamento e controlo de versão para cadernos Jupyter. Os colaboradores mantêm cópias locais dos repositórios e executam os cadernos dessas cópias. Clonar um caderno Jupyter do GitHub na sua conta Azure Notebooks cria uma cópia independente do caderno. As alterações são armazenadas apenas na sua conta de Azure Notebooks e não afetam o repositório original do GitHub. 

Como o seu clone de Azure Notebooks está na nuvem, pode partilhá-lo com colaboradores, que não precisam de fazer cópias locais ou ter o Jupyter instalado nos seus computadores. Também pode clonar um caderno simplesmente como ponto de partida para um projeto próprio, ou para obter ficheiros de dados. 

## <a name="prerequisites"></a>Pré-requisitos
Nenhum.

## <a name="clone-azure-cognitive-services-notebooks"></a>Cadernos dos Serviços Cognitivos clones da Azure

1. Vá aos [Cadernos Azure](https://notebooks.azure.com) e inscreva-se. Para mais detalhes, consulte [Quickstart - Inicie súm em Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Na sua página de perfil público, selecione **Os Meus Projetos** no topo da página:

    ![O meu link de projetos no topo da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **My Projects,** selecione o botão de seta para cima (atalho de teclado: U; o botão aparece como **Upload GitHub Repo** quando a janela do navegador é larga o suficiente):

    ![Upload GitHub Repo comando na página My Projects](media/quickstarts/upload-github-repo-command.png)

1. No **Repositório De Upload GitHub** que aparece, introduz ou define os seguintes detalhes e, em seguida, selecione **Import**:

   - **Repositório GitHub**: Microsoft/cognitive-services-notebooks (este nome clona os cadernos Jupyter para Azure Cognitive Services at [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) ).
   - **Clone recursivamente**: (limpo)
   - **Nome do projeto**: Cognitive Services Clone
   - **ID do projeto**: cognitive-services-clone
   - **Público:**(limpo)

     ![Upload GitHub Repo popup para recolher informações de repo](media/quickstarts/upload-github-repo-popup.png)

1. Seja paciente enquanto o processo termina; clonagem de um repositório pode levar alguns minutos.

1. Uma vez terminada a clonagem, a Azure Notebooks leva-o ao novo projeto onde pode ver as cópias de todos os ficheiros.

    :::image type="content" source="media/quickstarts/completed-clone.png" alt-text="Vista de um clone completo." lightbox="media/quickstarts/completed-clone.png":::

## <a name="share-a-notebook"></a>Partilhar um caderno

1. Para partilhar a sua cópia do projeto clonado, utilize o controlo **partilhar** ou obtenha um link, obtenha o código HTML ou Markdown que contenha o link ou crie uma mensagem de correio eletrónico com o link:

    ![Comando de partilha de projetos](media/quickstarts/share-project-command.png)

1. Como limpou a opção **pública** ao clonar o projeto, o clone é privado. Para tornar a sua cópia pública, selecione **Definições de Projeto,** defina a opção **de projeto Público** no pop-up e, em seguida, selecione **Save**.

1. Selecione um caderno no projeto para executá-lo. Cada caderno do repositório de Serviços Cognitivos Azure, por exemplo, é o seu próprio Quickstart autossuficiente. A imagem abaixo mostra o resultado da utilização do caderno BingImageSearchAPI, depois de adicionar uma chave de subscrição de API de Serviços Cognitivos e alterar o termo de pesquisa "filhotes" para "coelhinhos":

    ![Caderno jupyter gerido do GitHub](media/quickstarts/clone-notebook-result.png)

1. Quando terminar de executar o portátil, selecione **'Arquivar's**  >  **Close e pare** para fechar o portátil e a janela do navegador.

1. Para partilhar um caderno individual no projeto, clique com o botão direito no bloco de notas e selecione **Copy link** (atalho de teclado: y):

    ![Comando do menu de contexto para copiar um link para um caderno individual](media/quickstarts/copy-link-to-individual-notebook.png)

1. Para editar ficheiros que não os cadernos, clique com o botão direito no ficheiro do projeto e selecione editar o **ficheiro Editar** (atalho de teclado: i). A ação predefinida, **Executar** (atalho de teclado: r), só mostra o conteúdo do ficheiro e não permite a edição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Criar e executar um caderno Jupyter para fazer regressão linear](tutorial-create-run-jupyter-notebook.md)
