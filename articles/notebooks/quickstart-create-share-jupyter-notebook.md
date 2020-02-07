---
title: Criar e compartilhar um Jupyter Notebook no Azure Notebooks visualização
description: Crie e execute rapidamente um notebook Jupyter em Azure Notebooks visualização e compartilhe esse notebook com outras pessoas.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d3310444fa28240b8fb1344199514a9601a2c615
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064449"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Início rápido: criar e compartilhar um bloco de anotações em Azure Notebooks visualização

Neste arranque rápido, cria-se e executa um caderno Jupyter nos Cadernos Azure, e depois partilha-se esse caderno com outros. Jupyter permite combinar facilmente texto Markdown, código executável, dados persistentes, gráficos e visualizações numa tela sharable, o caderno. O Azure Notebooks é um serviço alojado gratuito que serve para desenvolver e executar blocos de notas Jupyter na cloud sem instalação.

## <a name="prerequisites"></a>Pré-requisitos
Nenhum.

## <a name="create-a-new-project-and-notebook"></a>Criar um novo projeto e um bloco de anotações

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Vá ao [site azure Notebooks (https://notebooks.azure.com)](https://notebooks.azure.com) e inscreva-se. Para mais detalhes, consulte [Quickstart - Inscreva-se nos Cadernos Azure](quickstart-sign-in-azure-notebooks.md).

1. Na sua página de perfil público, selecione **My Projects** no topo da página:

    ![Meu link de projetos na parte superior da janela do browser](media/quickstarts/my-projects-link.png)

1. Na página **My Projects,** selecione **+ Novo Projeto** (atalho de teclado: n). O botão só pode aparecer como **+** se a janela do navegador for estreita:

    ![Comando de novo projeto na página meus projetos](media/quickstarts/new-project-command.png)

1. No popup **Create New Project** que aparece, introduza ou detetete os seguintes detalhes, em seguida, selecione **Criar:**

   - **Nome do projeto**: Hello World in Python
   - **Id do projeto**: olá-world-python
   - **Projeto público**: (apurado)
   - **Criar um README.md:** (limpo)

     ![Novo projeto pop-up com detalhes preenchidas](media/quickstarts/new-project-popup.png)

1. Após alguns instantes, o Azure blocos de notas navega para o novo projeto. Adicione um caderno ao projeto selecionando o **+ Novo** drop-down (que pode aparecer como apenas **+** ), e, em seguida, selecionando **O Caderno:**

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. No popup **Create New Notebook** que aparece, introduza um nome de ficheiro para o seu caderno, como *HelloWorldInPython.ipynb* ( *.ipynb* significa IronPython (Jupyter) Notebook), e selecione **Python 3.6** para o idioma (também referido como *o kernel*):

    ![O pop-up de criar um novo Notebook](media/quickstarts/new-notebook-popup.png)

1. Selecione **Novo** para terminar a criação do caderno, que aparece na lista de ficheiros do seu projeto:

    ![Novo bloco de notas que aparecem na lista de ficheiros do projeto](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Executar o bloco de notas

1. Selecione o novo bloco de notas para executá-lo no editor de; o kernel que selecionou (Python 3.6 neste exemplo) é automaticamente ativado para este bloco de notas:

    ![Vista de um novo bloco de notas em blocos de notas do Azure](media/quickstarts/create-notebook-first-open.png)

1. Por predefinição, o bloco de notas tem uma célula de código vazio. Para alterar o tipo de célula para **Markdown,** utilize o tipo de pilha drop-down para selecionar **Markdown:**

    ![Alterar o tipo de célula num novo notebook](media/quickstarts/create-notebook-cell-type.png)

1. Introduza ou cole o seguinte texto do cabeçalho para a célula:

    ```markdown
    # Hello World in Python
    ```

1. Uma vez que está a editar o Markdown, o texto é apresentado como um cabeçalho com "#". Para tornar o Markdown em HTML, selecione o botão **Executar.** Blocos de notas do Azure, em seguida, cria automaticamente uma nova célula de código posteriormente:

    ![Botão de execução para uma célula e composto de Markdown](media/quickstarts/run-cell-markdown-render.png)

1. A célula de código, introduza o seguinte código de Python:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Selecione **Executar** (atalho do teclado: Shift+Enter) para executar o código. Abaixo da célula deverá ver um resultado bem-sucedido semelhante ao seguinte texto:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Selecione o salvamento ícone para guardar o seu trabalho:

    ![Guardar o ícone na barra de ferramentas de bloco de notas do Jupyter](media/quickstarts/hello-results-save-icon.png)

1. Selecione o comando de menu **'Fechar e Parar'** **do Ficheiro** > para parar o servidor e fechar a janela do navegador.

## <a name="share-the-notebook"></a>Partilhar o bloco de notas

Para partilhar o seu caderno, mude para a página do projeto se necessário, clique no ficheiro do portátil, selecione **Copy Link** (atalho de teclado: y) e cole esse link numa mensagem apropriada (e-mail, IM, etc.).

Na página do projeto, também pode utilizar o menu **Partilhar** para obter um link, criar uma mensagem de e-mail com o link, ou obter código de incorporação HTML e Markdown:

![Comando de partilha do projeto](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Crie e execute um caderno jupyter para fazer regressão linear](tutorial-create-run-jupyter-notebook.md)
