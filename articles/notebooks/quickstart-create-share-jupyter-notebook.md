---
title: Criar e partilhar um bloco de notas do Jupyter no Azure
description: Rapidamente criar e executar um bloco de notas do Jupyter nos blocos de notas do Azure, em seguida, partilhar o bloco de anotações com outras pessoas.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 71220fa5aa0367d1cb1694582b4f96459a3016e7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277510"
---
# <a name="quickstart-create-and-share-a-notebook"></a>Início rápido: Criar e partilhar um bloco de notas

1. Vá para [Azure notebooks](https://notebooks.azure.com) e entre. (Para obter detalhes, consulte [início rápido-entrar no Azure notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na página de seu perfil público, selecione **meus projetos** na parte superior da página:

    ![Meu link de projetos na parte superior da janela do browser](media/quickstarts/my-projects-link.png)

1. Na página **meus projetos** , selecione **+ novo projeto** (atalho de teclado: n); o botão pode aparecer somente como **+** se a janela do navegador for estreita:

    ![Comando de novo projeto na página meus projetos](media/quickstarts/new-project-command.png)

1. No pop-up **criar novo projeto** que aparece, insira ou defina os seguintes detalhes e, em seguida, selecione **criar**:

   - **Nome do projeto**: Olá, mundo em Python
   - **ID do projeto**: Olá-mundo-Python
   - **Projeto público**: (desmarcado)
   - **Criar um README.MD**: (desmarcado)

     ![Novo projeto pop-up com detalhes preenchidas](media/quickstarts/new-project-popup.png)

1. Após alguns instantes, o Azure blocos de notas navega para o novo projeto. Adicione um bloco de anotações ao projeto selecionando a lista suspensa **+ novo** (que pode aparecer apenas **+** ) e, em seguida, selecionando o **bloco de anotações**:

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. No pop-up **criar novo bloco de anotações** que aparece, insira um nome de arquivo para o bloco de anotações, como *HelloWorldInPython. ipynb* ( *. ipynb* significa o notebook IronPython (Jupyter)) e selecione **Python 3,6** para o idioma (também conhecido como *kernel*):

    ![O pop-up de criar um novo Notebook](media/quickstarts/new-notebook-popup.png)

1. Selecione **novo** para concluir a criação do bloco de anotações, que aparece na lista de arquivos do seu projeto:

    ![Novo bloco de notas que aparecem na lista de ficheiros do projeto](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Executar o bloco de notas

1. Selecione o novo bloco de notas para executá-lo no editor de; o kernel que selecionou (Python 3.6 neste exemplo) é automaticamente ativado para este bloco de notas:

    ![Vista de um novo bloco de notas em blocos de notas do Azure](media/quickstarts/create-notebook-first-open.png)

1. Por predefinição, o bloco de notas tem uma célula de código vazio. Para alterar o tipo de célula para **redução**, use a lista suspensa tipo de célula para selecionar **redução**:

    ![Alterar o tipo de célula num novo notebook](media/quickstarts/create-notebook-cell-type.png)

1. Introduza ou cole o seguinte texto do cabeçalho para a célula:

    ```markdown
    # Hello World in Python
    ```

1. Uma vez que está a editar o Markdown, o texto é apresentado como um cabeçalho com "#". Para renderizar a redução em HTML, selecione o botão **executar** . Blocos de notas do Azure, em seguida, cria automaticamente uma nova célula de código posteriormente:

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

Para compartilhar seu bloco de anotações, volte para a página do projeto, se necessário, clique com o botão direito do mouse no arquivo do bloco de anotações, selecione **Copiar link** (atalho do teclado: y) e cole esse link em uma mensagem apropriada (email, mensagens instantâneas, etc.).

Na página do projeto, você também pode usar o menu **compartilhar** para obter um link, criar uma mensagem de email com o link ou obter HTML e fazer a redução do código de inserção:

![Comando de partilha do projeto](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: criar uma execução de um bloco de anotações Jupyter para fazer a regressão linear](tutorial-create-run-jupyter-notebook.md)
