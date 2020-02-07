---
title: Clonar um notebook Jupyter do GitHub com Azure Notebooks versão prévia
description: Clonar um bloco de notas do Jupyter partir de um repositório do GitHub e executá-lo na sua conta de blocos de notas do Azure rapidamente.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d0f3a12ff04e115074c3821c5e29652484710bca
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064602"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Início rápido: clonar um bloco de anotações no Azure Notebooks Preview

Neste arranque rápido, você copia um caderno Jupyter armazenado no GitHub para uma conta Azure Notebooks. 

Os repositórios GitHub fornecem armazenamento e controlo de versão para os cadernos Jupyter. Os colaboradores mantêm cópias locais dos repositórios, e executam os cadernos dessas cópias. A clonagem de um caderno Jupyter do GitHub na sua conta Decadernos Azure cria uma cópia independente do caderno. As alterações são armazenadas apenas na sua conta Deporporlistas Azure, e não afetam o repositório original do GitHub. 

Como o seu clone de Cadernos Azure está na nuvem, pode partilhá-lo com colaboradores, que não precisam de fazer cópias locais ou ter o Jupyter instalado nos seus computadores. Também pode clonar um caderno simplesmente como ponto de partida para um projeto próprio, ou para obter ficheiros de dados. 

## <a name="prerequisites"></a>Pré-requisitos
Nenhum.

## <a name="clone-azure-cognitive-services-notebooks"></a>Clonar blocos de notas de serviços cognitivos do Azure

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Vá aos [Cadernos Azure](https://notebooks.azure.com) e inscreva-se. Para mais detalhes, consulte [Quickstart - Inscreva-se nos Cadernos Azure](quickstart-sign-in-azure-notebooks.md).

1. Na sua página de perfil público, selecione **My Projects** no topo da página:

    ![Meu link de projetos na parte superior da janela do browser](media/quickstarts/my-projects-link.png)

1. Na página **My Projects,** selecione o botão de seta up (atalho de teclado: U; o botão aparece como **Upload GitHub Repo** quando a janela do navegador é suficientemente larga):

    ![Comando carregar repositório do GitHub na página meus projetos](media/quickstarts/upload-github-repo-command.png)

1. No **Repositório Upload GitHub** que aparece, introduza ou detetete os seguintes detalhes e, em seguida, selecione **Import:**

   - **Repositório GitHub**: Microsoft/cognitive-services-notebooks (este nome clona os cadernos Jupyter para Serviços Cognitivos Azure em [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Clone recursivamente:** (ilibado)
   - **Nome**do projeto : Clone de Serviços Cognitivos
   - **Id do projeto**: cognitivo-serviços-clone
   - **Público**: (ilibado)

     ![Carregar pop-up do repositório do GitHub para coletar informações do repositório](media/quickstarts/upload-github-repo-popup.png)

1. Seja paciente, enquanto o processo for concluído; um repositório de clonagem pode demorar alguns minutos.

1. Após a conclusão da clonagem, blocos de notas do Azure leva-o para o novo projeto onde pode ver as cópias de todos os arquivos.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Partilhar um bloco de notas

1. Para partilhar a sua cópia do projeto clonado, utilize o controlo **de partilha** ou obtenha um link, obtenha código HTML ou Markdown que contenha o link, ou crie uma mensagem de e-mail com o link:

    ![Comando de partilha do projeto](media/quickstarts/share-project-command.png)

1. Como abriste a opção **do Público** ao clonar o projeto, o clone é privado. Para tornar a sua cópia pública, selecione **Definições**de Projeto, detete a opção **do projeto Público** no popup e, em seguida, selecione **Guardar**.

1. Selecione um bloco de notas no projeto para executá-lo. Cada bloco de anotações no repositório de serviços cognitivos do Azure, por exemplo, é sua própria guia de introdução autossuficiente. A imagem abaixo mostra o resultado da utilização do bloco de notas BingImageSearchAPI, depois de adicionar uma chave de assinatura de API dos serviços cognitivos e alterando o termo de pesquisa "puppies" para "bunnies":

    ![A executar o bloco de notas do Jupyter clonou do GitHub](media/quickstarts/clone-notebook-result.png)

1. Quando terminar de executar o caderno, selecione **File** > **Fechar e pare** para fechar o caderno e a janela do seu navegador.

1. Para partilhar um portátil individual no projeto, clique no caderno e selecione **link Copy** (atalho de teclado: y):

    ![Comando de menu de contexto para copiar uma ligação para um bloco de anotações individual](media/quickstarts/copy-link-to-individual-notebook.png)

1. Para editar ficheiros que não os cadernos, clique no ficheiro no projeto e selecione **o ficheiro Editar** (atalho do teclado: i). A ação predefinida, **Executar** (atalho do teclado: r), apenas mostra o conteúdo do ficheiro e não permite a edição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Crie e execute um caderno jupyter para fazer regressão linear](tutorial-create-run-jupyter-notebook.md)
