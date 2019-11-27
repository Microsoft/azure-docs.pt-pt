---
title: Migrar de um bloco de notas Jupyter local para blocos de notas do Azure
description: Rapidamente, transferir um bloco de notas do Jupyter para blocos de notas do Azure a partir do seu computador local ou um URL de web, em seguida, partilhá-lo para colaboração.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 3b7d5aab05923fb4356e0d45aaf21a77076a870a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277471"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Início rápido: Migrar um bloco de notas Jupyter local

Blocos de notas do Jupyter que criar localmente no seu próprio computador estão acessíveis apenas para si. Pode compartilhar seus arquivos por meio de diversas maneiras, mas, em seguida, os destinatários têm sua própria cópia local do bloco de notas e é difícil para incorporar todas as alterações que podem fazer. Pode também armazenar blocos de notas num repositório partilhado online, como o GitHub, mas ainda fazer isso requer que cada funcionário tem sua própria instalação local do Jupyter com a mesma configuração como sua.

Ao migrar seus blocos de notas do locais ou baseada no repositório para blocos de notas do Azure, armazená-las na cloud a partir do qual pode instantaneamente partilhá-los com seus colaboradores. Esses colaboradores precisam apenas de um navegador para exibir e executar o bloco de anotações e, se [entrarem no](quickstart-sign-in-azure-notebooks.md) Azure notebooks eles também podem fazer alterações.

Este início rápido demonstra o processo de migração de um bloco de notas do seu computador local ou outra URL do arquivo acessível. Para migrar blocos de anotações de um repositório GitHub, consulte [início rápido: clonar um bloco de anotações](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Criar um projeto em blocos de notas do Azure

1. Vá para [Azure notebooks](https://notebooks.azure.com) e entre. (Para obter detalhes, consulte [início rápido-entrar no Azure notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na página de seu perfil público, selecione **meus projetos** na parte superior da página:

    ![Meu link de projetos na parte superior da janela do browser](media/quickstarts/my-projects-link.png)

1. Na página **meus projetos** , selecione **+ novo projeto** (atalho de teclado: n); o botão pode aparecer somente como **+** se a janela do navegador for estreita:

    ![Comando de novo projeto na página meus projetos](media/quickstarts/new-project-command.png)

1. No pop-up **criar novo projeto** que aparece, insira os valores apropriados para o bloco de anotações que você está migrando nos campos **nome do projeto** e **ID do projeto** , desmarque as opções para **projeto público** e **crie um README.MD**e, em seguida, selecione **criar**.

## <a name="upload-the-local-notebook"></a>Carregar o bloco de notas local

1. Na página projeto, selecione **carregar** (que pode aparecer como uma seta para cima somente se a janela do navegador for pequena) e, em seguida, selecione 1. No pop-up que aparece, selecione **do computador** se o seu notebook estiver localizado no sistema de arquivos local ou **na URL** se o seu bloco de anotações estiver localizado online:

    ![Comando para carregar um bloco de notas de um URL ou o computador local](media/quickstarts/upload-from-computer-url-command.png)

   (Novamente, se o seu notebook estiver em um repositório GitHub, siga as etapas em [início rápido: clonar um bloco de anotações](quickstart-clone-jupyter-notebook.md) em vez disso.)

   - Se estiver usando o **do computador**, arraste e solte os arquivos *. ipynb* para o pop-up, ou selecione **escolher arquivos**, navegue até e selecione os arquivos que deseja importar. Em seguida, selecione **carregar**. Os ficheiros carregados recebem o mesmo nome que os ficheiros locais. (Você não precisa carregar o conteúdo de nenhuma pasta *. ipynb_checkpoints* .)

     ![Carregar a partir do pop-up de computador](media/quickstarts/upload-from-computer-popup.png)

   - Se estiver usando **da URL**, insira o endereço de origem no campo **URL do arquivo** e o nome do arquivo para atribuir ao bloco de anotações em seu projeto no campo nome do **arquivo** . Em seguida, selecione **carregar**. Se você tiver vários arquivos com URLs separadas, use o comando **+ Adicionar arquivo** para verificar a primeira URL inserida, após a qual o pop-up fornece novos campos para outro arquivo.

     ![Carregar a partir do pop-up de URL](media/quickstarts/upload-from-url-popup.png)

1. Abra e execute seu bloco de notas recém-carregada para verificar o seu conteúdo e a operação. Quando terminar, selecione **arquivo** > **parar e fechar** para fechar o bloco de anotações.

1. Para compartilhar um link para o bloco de anotações carregado, clique com o botão direito do mouse no arquivo no projeto e selecione **Copiar link** (atalho de teclado: y) e cole esse link na mensagem apropriada. Como alternativa, você pode compartilhar o projeto como um todo usando o controle de **compartilhamento** na página do projeto.

1. Para editar arquivos que não sejam blocos de anotações, clique com o botão direito do mouse no arquivo no projeto e selecione **Editar arquivo** (atalho de teclado: i). A ação padrão, **executar** (atalho de teclado: r), mostra apenas o conteúdo do arquivo e não permite edição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: criar uma execução de um bloco de anotações Jupyter para fazer a regressão linear](tutorial-create-run-jupyter-notebook.md)
