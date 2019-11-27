---
title: Criar um projeto de blocos de notas do Azure com um ambiente personalizado
description: Crie um novo projeto em blocos de notas do Azure que está configurado com um conjunto específico de pacotes instalados e scripts de inicialização.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: f167d1bf6064cde535d17fa4a336ee13424baba7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277487"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>Início rápido: Criar um projeto com um ambiente personalizado

Um projeto em blocos de notas do Azure é uma coleção de arquivos, como blocos de notas, ficheiros de dados, documentação, imagens e assim por diante, juntamente com um ambiente que pode ser configurado com os comandos de configuração específicos. Ao definir o ambiente com o projeto, qualquer pessoa que clona o projeto em sua própria conta de blocos de notas do Azure tem todas as informações que precisam para recriar o ambiente necessário.

## <a name="create-a-project"></a>Criar um projeto

1. Vá para [Azure notebooks](https://notebooks.azure.com) e entre. (Para obter detalhes, consulte [início rápido-entrar no Azure notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na página de seu perfil público, selecione **meus projetos** na parte superior da página:

    ![Meu link de projetos na parte superior da janela do browser](media/quickstarts/my-projects-link.png)

1. Na página **meus projetos** , selecione **+ novo projeto** (atalho de teclado: n); o botão pode aparecer somente como **+** se a janela do navegador for estreita:

    ![Comando de novo projeto na página meus projetos](media/quickstarts/new-project-command.png)

1. No pop-up **criar novo projeto** que aparece, insira ou defina os seguintes detalhes e, em seguida, selecione **criar**:

    - **Nome do projeto**: projeto com um ambiente personalizado
    - **ID do projeto**: projeto-personalizado-ambiente
    - **Projeto público**: (desmarcado)
    - **Criar um README.MD**: (desmarcado)

1. Após alguns instantes, o Azure blocos de notas navega para o novo projeto. Adicione um bloco de anotações ao projeto selecionando a lista suspensa **+ novo** (que pode aparecer apenas **+** ) e, em seguida, selecionando **Notebook**.

1. Dê um nome ao bloco de anotações como *ambiente personalizado. ipynb*, selecione **Python 3,6** para o idioma e selecione **novo**.

## <a name="add-a-custom-setup-step"></a>Adicione um passo de configuração personalizada

1. Na página projeto, selecione **configurações do projeto**.

    ![Comando de definições do projeto](media/quickstarts/project-settings-command.png)

1. No pop-up **configurações do projeto** , selecione a guia **ambiente** e, em seguida, em etapas de **configuração do ambiente**, selecione **+ Adicionar**:

    ![Comando para adicionar um novo passo de configuração do ambiente](media/quickstarts/environment-add-command.png)

1. O comando **+ Add** cria uma etapa que é definida por uma operação e um arquivo de destino que é selecionado nos arquivos em seu projeto. São suportadas as seguintes operações:

    | Operação | Descrição |
    | --- | --- |
    | Requirements. txt | Projekty v Pythonu define as respetivas dependências num ficheiro Requirements. txt. Com esta opção, selecione o ficheiro adequado na lista de ficheiros do projeto e selecione também a versão de Python em adicional pendente que é apresentada. Se necessário, selecione **Cancelar** para retornar ao projeto, carregar ou criar o arquivo e, em seguida, retornar para a guia **configurações do projeto** > **ambiente** e criar uma nova etapa. Com essa etapa em vigor, executar um bloco de anotações no projeto é executado automaticamente `pip install -r <file>` |
    | Script de shell | Use para indicar um script de shell bash (normalmente um arquivo com a extensão *. sh* ) que contém os comandos que você deseja executar para inicializar o ambiente. |
    | Environment.yml | Um projeto Python que usa o Conda para gerenciar um ambiente usa um arquivo *Environments. yml* para descrever as dependências. Com esta opção, selecione o ficheiro adequado da lista de ficheiros do projeto. |

1. Para remover qualquer etapa de instalação, selecione o **X** à direita da etapa.

1. Quando todas as etapas de instalação estiverem em vigor, selecione **salvar**. (Selecione **Cancelar** para descartar as alterações).

1. Para testar seu ambiente, crie e execute um novo bloco de anotações e, em seguida, crie uma célula de código com instruções que dependam de um pacote no ambiente, como usar uma instrução de `import` do Python. Se a instrução for bem-sucedida, em seguida, o pacote necessário foi instalado com êxito no ambiente.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerenciar e configurar projetos no Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Tutorial: criar uma execução de um bloco de anotações Jupyter para fazer a regressão linear](tutorial-create-run-jupyter-notebook.md)
