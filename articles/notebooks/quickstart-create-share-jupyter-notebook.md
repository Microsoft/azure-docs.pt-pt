---
title: Criar e partilhar um bloco de notas do Jupyter no Azure
description: Rapidamente criar e executar um bloco de notas do Jupyter nos blocos de notas do Azure, em seguida, partilhar o bloco de anotações com outras pessoas.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 5add60ad-0b4b-4fd5-adf5-eb50ce072d00
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 3f03202d0f4416b3bf08a33e5d997d7149eda9f0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104408"
---
# <a name="quickstart-create-and-share-a-notebook"></a>Início rápido: Criar e partilhar um bloco de notas

1. Aceda a [blocos de notas do Azure](https://notebooks.azure.com) e iniciar sessão. (Para obter detalhes, consulte [início rápido - início de sessão para blocos de notas do Azure](quickstart-sign-in-azure-notebooks.md)).

1. Na sua página de perfil público, selecione **meus projetos** na parte superior da página:

    ![Meu link de projetos na parte superior da janela do browser](media/quickstarts/my-projects-link.png)

1. Sobre o **meus projetos** página, selecione **+ novo projeto** (atalho de teclado: n); o botão poderá aparecer apenas como **+** se a janela do browser estreita:

    ![Comando de novo projeto na página meus projetos](media/quickstarts/new-project-command.png)

1. Na **criar novo projeto** pop-up apresentado, introduza ou defina os seguintes detalhes, em seguida, selecione **criar**:

   - **Nome do projeto**: Olá, mundo no Python
   - **ID do projeto**: hello-world-python
   - **Projeto público**: (limpo)
   - **Criar um README.md**: (limpo)

     ![Novo projeto pop-up com detalhes preenchidas](media/quickstarts/new-project-popup.png)

1. Após alguns instantes, o Azure blocos de notas navega para o novo projeto. Adicionar um bloco de notas para o projeto, selecionando o **+ novo** pendente (que podem aparecer apenas como **+**), em seguida, selecionar **bloco de notas**:

    [![](media/quickstarts/empty-project-new-notebook-button.png "Um novo, vazio do projeto e adicionar o comando de bloco de notas")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. Na **criar novo bloco de notas** pop-up apresentado, introduza um nome de ficheiro para o bloco de notas, tal como *HelloWorldInPython.ipynb* (*.ipynb* significa que o bloco de notas do IronPython (Jupyter) ) e selecione **Python 3.6** para o idioma (também conhecido como o *kernel*):

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

1. Selecione o **arquivo** > **fechar e parar** comando de menu para parar o servidor e fechar a janela do browser.

## <a name="share-the-notebook"></a>Partilhar o bloco de notas

Para partilhar o seu bloco de notas, mude novamente para a página do projeto, se necessário, clique com o botão direito do ficheiros de bloco de notas, selecione **copiar ligação** (atalho de teclado: y) e cole o link para uma mensagem apropriada (e-mail, mensagens Instantâneas, etc.).

Na página do projeto, também pode utilizar o **partilha** código de incorporação de menu para obter uma ligação, crie uma mensagem de e-mail com a ligação ou obter o HTML e Markdown:

![Comando de partilha do projeto](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: criar uma execução de um bloco de notas do Jupyter para fazer a regressão linear](tutorial-create-run-jupyter-notebook.md)
