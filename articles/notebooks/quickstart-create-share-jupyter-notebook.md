---
title: Criar e compartilhar um Jupyter Notebook no Azure Notebooks visualização
description: Crie e execute rapidamente um notebook Jupyter em Azure Notebooks visualização e compartilhe esse notebook com outras pessoas.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 7b983d927bfd789cddb8d00675294cefc9f55e08
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647089"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Início rápido: criar e compartilhar um bloco de anotações em Azure Notebooks visualização

Neste guia de início rápido, você aprende a criar e executar rapidamente um notebook Jupyter em Azure Notebooks e, em seguida, compartilhar esse notebook com outras pessoas.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-new-project-and-notebook"></a>Criar um novo projeto e um bloco de anotações

1. Vá para o [site do Azure notebooks (https://notebooks.azure.com)](https://notebooks.azure.com) e entre. (Para obter detalhes, consulte [início rápido - início de sessão para blocos de notas do Azure](quickstart-sign-in-azure-notebooks.md)).

1. Na sua página de perfil público, selecione **meus projetos** na parte superior da página:

    ![Meu link de projetos na parte superior da janela do browser](media/quickstarts/my-projects-link.png)

1. Sobre o **meus projetos** página, selecione **+ novo projeto** (atalho de teclado: n); o botão poderá aparecer apenas como **+** se a janela do browser estreita:

    ![Comando de novo projeto na página meus projetos](media/quickstarts/new-project-command.png)

1. Na **criar novo projeto** pop-up apresentado, introduza ou defina os seguintes detalhes, em seguida, selecione **criar**:

   - **Nome do projeto**: Hello World no Python
   - **ID do projeto**: hello-world-python
   - **Projeto público**: (limpo)
   - **Criar um README.md**: (limpo)

     ![Novo projeto pop-up com detalhes preenchidas](media/quickstarts/new-project-popup.png)

1. Após alguns instantes, o Azure blocos de notas navega para o novo projeto. Adicionar um bloco de notas para o projeto, selecionando o **+ novo** pendente (que podem aparecer apenas como **+** ), em seguida, selecionar **bloco de notas**:

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. Na **criar novo bloco de notas** pop-up apresentado, introduza um nome de ficheiro para o bloco de notas, tal como *HelloWorldInPython.ipynb* ( *.ipynb* significa que o bloco de notas do IronPython (Jupyter) ) e selecione **Python 3.6** para o idioma (também conhecido como o *kernel*):

    ![O pop-up de criar um novo Notebook](media/quickstarts/new-notebook-popup.png)

1. Selecione **New** para concluir a criação de bloco de notas, o que, em seguida, aparece na lista de ficheiros do seu projeto:

    ![Novo bloco de notas que aparecem na lista de ficheiros do projeto](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Executar o bloco de notas

1. Selecione o novo bloco de notas para executá-lo no editor de; o kernel que selecionou (Python 3.6 neste exemplo) é automaticamente ativado para este bloco de notas:

    ![Vista de um novo bloco de notas em blocos de notas do Azure](media/quickstarts/create-notebook-first-open.png)

1. Por predefinição, o bloco de notas tem uma célula de código vazio. Para alterar o tipo de célula para **Markdown**, utilize a célula tipo pendente para selecionar **Markdown**:

    ![Alterar o tipo de célula num novo notebook](media/quickstarts/create-notebook-cell-type.png)

1. Introduza ou cole o seguinte texto do cabeçalho para a célula:

    ```markdown
    # Hello World in Python
    ```

1. Uma vez que está a editar o Markdown, o texto é apresentado como um cabeçalho com "#". Para compor o Markdown em HTML, selecione o **executar** botão. Blocos de notas do Azure, em seguida, cria automaticamente uma nova célula de código posteriormente:

    ![Botão de execução para uma célula e composto de Markdown](media/quickstarts/run-cell-markdown-render.png)

1. A célula de código, introduza o seguinte código de Python:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Selecione **executar** (atalho de teclado: Shift + Enter) para executar o código. Abaixo da célula deverá ver um resultado bem-sucedido semelhante ao seguinte texto:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Selecione o salvamento ícone para guardar o seu trabalho:

    ![Guardar o ícone na barra de ferramentas de bloco de notas do Jupyter](media/quickstarts/hello-results-save-icon.png)

1. Selecione o **comando** de menu > **fechar e parar** para parar o servidor e fechar a janela do navegador.

## <a name="share-the-notebook"></a>Partilhar o bloco de notas

Para partilhar o seu bloco de notas, mude novamente para a página do projeto, se necessário, clique com o botão direito do ficheiros de bloco de notas, selecione **copiar ligação** (atalho de teclado: y) e cole o link para uma mensagem apropriada (e-mail, mensagens Instantâneas, etc.).

Na página do projeto, também pode utilizar o **partilha** código de incorporação de menu para obter uma ligação, crie uma mensagem de e-mail com a ligação ou obter o HTML e Markdown:

![Comando de partilha do projeto](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: criar uma execução de um bloco de notas do Jupyter para fazer a regressão linear](tutorial-create-run-jupyter-notebook.md)
