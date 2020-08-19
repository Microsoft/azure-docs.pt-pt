---
title: Crie e partilhe um caderno Jupyter na pré-visualização de cadernos Azure
description: Crie e execute rapidamente um Caderno Jupyter na Pré-visualização de Cadernos Azure e, em seguida, partilhe esse caderno com outros.
ms.topic: quickstart
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: 77575b5b9e006ac9881d03dc95509d55c53c26d7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589338"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Quickstart: Criar e partilhar um caderno em Pré-visualização de Cadernos Azure

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Neste quickstart, você cria e executou um Caderno Jupyter em Cadernos Azure, em seguida, partilhe esse caderno com outros. O Jupyter permite-lhe combinar facilmente texto markdown, código executável, dados persistentes, gráficos e visualizações numa tela sharable, o caderno. AZure Notebooks é um serviço gratuito para desenvolver e executar Cadernos Jupyter na nuvem sem instalação.

## <a name="prerequisites"></a>Pré-requisitos
Nenhum.

## <a name="create-a-new-project-and-notebook"></a>Criar um novo projeto e caderno

1. Vá ao [site Azure https://notebooks.azure.com) Notebooks (](https://notebooks.azure.com) e inscreva-se. Para mais detalhes, consulte [Quickstart - Inicie súm em Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Na sua página de perfil público, selecione **Os Meus Projetos** no topo da página:

    ![O meu link de projetos no topo da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **My Projects,** selecione **+ Novo Projeto** (atalho de teclado: n). O botão só pode aparecer como **+** se a janela do navegador fosse estreita:

    ![Novo comando do projeto na página My Projects](media/quickstarts/new-project-command.png)

1. No popup **Create New Project** que aparece, introduz ou define os seguintes detalhes e, em seguida, selecione **Criar**:

   - **Nome do projeto**: Hello World in Python
   - **ID do projeto**: olá-mundo-python
   - **Projeto público**: (limpo)
   - **Criar um README.md:**(limpo)

     ![Novo projeto popup com detalhes povoados](media/quickstarts/new-project-popup.png)

1. Após alguns momentos, a Azure Notebooks navega para o novo projeto. Adicione um caderno ao projeto selecionando o **+ Novo** drop-down (que pode aparecer como apenas **+** ), selecionando então **o Caderno**:

    :::image type="content" source="media/quickstarts/empty-project-new-notebook-button.png" alt-text="Um novo projeto vazio e adicionar comando de caderno." lightbox="media/quickstarts/empty-project-new-notebook-button.png":::

1. No popup **Create New Notebook** que aparece, introduza um nome de arquivo para o seu portátil, como *HelloWorldInPython.ipynb* *(.ipynb* significa IronPython (Jupyter) Notebook), e selecione **Python 3.6** para o idioma (também referido como *o núcleo):*

    ![O popup Create New Notebook](media/quickstarts/new-notebook-popup.png)

1. Selecione **Novo** para terminar a criação do caderno, que depois aparece na lista de ficheiros do seu projeto:

    ![Novo caderno que aparece na lista de ficheiros do projeto](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Executar o bloco de notas

1. Selecione o novo caderno para executá-lo no editor; o núcleo selecionado (Python 3.6 neste exemplo) é automaticamente ativado para este caderno:

    ![Vista de um novo caderno em Cadernos Azure](media/quickstarts/create-notebook-first-open.png)

1. Por predefinição, o caderno tem uma célula de código vazia. Para alterar o tipo de célula para **Markdown,** utilize o tipo de pilha drop-down para selecionar **Markdown**:

    ![Mudar o tipo de célula num novo caderno](media/quickstarts/create-notebook-cell-type.png)

1. Introduza ou cole o seguinte texto de posição na célula:

    ```markdown
    # Hello World in Python
    ```

1. Porque estás a editar o Markdown, o texto aparece como um cabeçalho com o "#". Para tornar o Markdown em HTML, selecione o botão **Executar.** Os Cadernos Azure criam automaticamente uma nova célula de código:

    ![O botão de execução para uma célula e renderizado Markdown](media/quickstarts/run-cell-markdown-render.png)

1. Na célula de código, introduza o seguinte código Python:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Selecione **Executar** (atalho de teclado: Shift+Enter) para executar o código. Abaixo da célula deve ver uma saída bem sucedida semelhante ao seguinte texto:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Selecione o ícone de poupança para salvar o seu trabalho:

    ![Guarde o ícone na barra de ferramentas do Jupyter Notebook](media/quickstarts/hello-results-save-icon.png)

1. Selecione o comando do menu **File**  >  **'Arquivar's Close e Suspender** para parar o servidor e fechar a janela do navegador.

## <a name="share-the-notebook"></a>Partilhe o caderno

Para partilhar o seu caderno, volte para a página do projeto, se necessário, clique com o botão direito no ficheiro do portátil, selecione **Copy Link** (atalho de teclado: y) e cole esse link numa mensagem apropriada (e-mail, IM, etc.).

Na página do projeto, também pode utilizar o menu **Partilhar** para obter um link, criar uma mensagem de e-mail com o link ou obter o código incorporado HTML e Markdown:

![Comando de partilha de projetos](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Criar e executar um Caderno Jupyter para fazer regressão linear](tutorial-create-run-jupyter-notebook.md)
