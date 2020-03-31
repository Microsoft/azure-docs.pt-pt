---
title: Clone um caderno Jupyter do GitHub com pré-visualização de cadernos Azure
description: Clone rapidamente um caderno Jupyter de um repositório GitHub e execute-o na sua conta Decadernos Azure.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d0f3a12ff04e115074c3821c5e29652484710bca
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064602"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Quickstart: Clone um caderno em Pré-visualização de cadernos Azure

Neste arranque rápido, você copia um caderno Jupyter armazenado no GitHub para uma conta Azure Notebooks. 

Os repositórios GitHub fornecem armazenamento e controlo de versão para os cadernos Jupyter. Os colaboradores mantêm cópias locais dos repositórios, e executam os cadernos dessas cópias. A clonagem de um caderno Jupyter do GitHub na sua conta Decadernos Azure cria uma cópia independente do caderno. As alterações são armazenadas apenas na sua conta Deporporlistas Azure, e não afetam o repositório original do GitHub. 

Como o seu clone de Cadernos Azure está na nuvem, pode partilhá-lo com colaboradores, que não precisam de fazer cópias locais ou ter o Jupyter instalado nos seus computadores. Também pode clonar um caderno simplesmente como ponto de partida para um projeto próprio, ou para obter ficheiros de dados. 

## <a name="prerequisites"></a>Pré-requisitos
Nenhum.

## <a name="clone-azure-cognitive-services-notebooks"></a>Cadernos dos Serviços Cognitivos Clone Azure

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Vá aos [Cadernos Azure](https://notebooks.azure.com) e inscreva-se. Para mais detalhes, consulte [Quickstart - Inscreva-se nos Cadernos Azure](quickstart-sign-in-azure-notebooks.md).

1. Na sua página de perfil público, selecione **My Projects** no topo da página:

    ![My Projects link no topo da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **My Projects,** selecione o botão de seta up (atalho de teclado: U; o botão aparece como **Upload GitHub Repo** quando a janela do navegador é suficientemente larga):

    ![Carregue o comando GitHub Repo na página my projects](media/quickstarts/upload-github-repo-command.png)

1. No **Repositório Upload GitHub** que aparece, introduza ou detetete os seguintes detalhes e, em seguida, selecione **Import:**

   - **Repositório GitHub**: Microsoft/cognitive-services-notebooks (este nome clona os cadernos [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)Jupyter para Serviços Cognitivos Azure em ).
   - **Clone recursivamente:**(ilibado)
   - **Nome**do projeto : Clone de Serviços Cognitivos
   - **Id do projeto**: cognitivo-serviços-clone
   - **Público**: (ilibado)

     ![Upload GitHub Repo popup para recolher informações repo](media/quickstarts/upload-github-repo-popup.png)

1. Seja paciente enquanto o processo termina; clonagem de um repositório pode levar alguns minutos.

1. Uma vez terminada a clonagem, os Cadernos Azure leva-o ao novo projeto onde pode ver as cópias de todos os ficheiros.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Partilhar um caderno

1. Para partilhar a sua cópia do projeto clonado, utilize o controlo **de partilha** ou obtenha um link, obtenha código HTML ou Markdown que contenha o link, ou crie uma mensagem de e-mail com o link:

    ![Comando de partilha de projeto](media/quickstarts/share-project-command.png)

1. Como abriste a opção **do Público** ao clonar o projeto, o clone é privado. Para tornar a sua cópia pública, selecione **Definições**de Projeto, detete a opção **do projeto Público** no popup e, em seguida, selecione **Guardar**.

1. Selecione um caderno no projeto para executá-lo. Cada caderno no repositório dos Serviços Cognitivos Azure, por exemplo, é o seu próprio Quickstart independente. A imagem abaixo mostra o resultado da utilização do caderno BingImageSearchAPI, depois de adicionar uma chave de subscrição de API de Serviços Cognitivos e alterar o termo de pesquisa "filhotes" para "coelhinhos":

    ![Caderno jupyter em execução clonado do GitHub](media/quickstarts/clone-notebook-result.png)

1. Quando terminar de executar o caderno, selecione **File** > **Close e pare** para fechar o caderno e a janela do seu navegador.

1. Para partilhar um portátil individual no projeto, clique no caderno e selecione **link Copy** (atalho de teclado: y):

    ![Comando de menu de contexto para copiar um link para um caderno individual](media/quickstarts/copy-link-to-individual-notebook.png)

1. Para editar ficheiros que não os cadernos, clique no ficheiro no projeto e selecione **o ficheiro Editar** (atalho do teclado: i). A ação predefinida, **Executar** (atalho do teclado: r), apenas mostra o conteúdo do ficheiro e não permite a edição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Crie e execute um caderno jupyter para fazer regressão linear](tutorial-create-run-jupyter-notebook.md)
