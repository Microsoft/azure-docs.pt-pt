---
title: Migrar de um bloco de notas Jupyter local para blocos de notas do Azure
description: Rapidamente, transferir um bloco de notas do Jupyter para blocos de notas do Azure a partir do seu computador local ou um URL de web, em seguida, partilhá-lo para colaboração.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 2e935425-3923-4a33-89b2-0f2100b0c0c4
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: a160bc0247d2f80dda620b67c946805926d78006
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773968"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Início rápido: Migrar um bloco de notas Jupyter local

Blocos de notas do Jupyter que criar localmente no seu próprio computador estão acessíveis apenas para si. Pode compartilhar seus arquivos por meio de diversas maneiras, mas, em seguida, os destinatários têm sua própria cópia local do bloco de notas e é difícil para incorporar todas as alterações que podem fazer. Pode também armazenar blocos de notas num repositório partilhado online, como o GitHub, mas ainda fazer isso requer que cada funcionário tem sua própria instalação local do Jupyter com a mesma configuração como sua.

Ao migrar seus blocos de notas do locais ou baseada no repositório para blocos de notas do Azure, armazená-las na cloud a partir do qual pode instantaneamente partilhá-los com seus colaboradores. Os funcionários precisam apenas um browser para ver e executar o bloco de notas e se eles [iniciar sessão](quickstart-sign-in-azure-notebooks.md) para blocos de notas do Azure também fazer alterações.

Este início rápido demonstra o processo de migração de um bloco de notas do seu computador local ou outra URL do arquivo acessível. Para migrar blocos de notas a partir de um repositório do GitHub, consulte [início rápido: Clonar um bloco de notas](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Criar um projeto em blocos de notas do Azure

1. Aceda a [blocos de notas do Azure](https://notebooks.azure.com) e iniciar sessão. (Para obter detalhes, consulte [início rápido - início de sessão para blocos de notas do Azure](quickstart-sign-in-azure-notebooks.md)).

1. Na sua página de perfil público, selecione **meus projetos** na parte superior da página:

    ![Meu link de projetos na parte superior da janela do browser](media/quickstarts/my-projects-link.png)

1. Sobre o **meus projetos** página, selecione **+ novo projeto** (atalho de teclado: n); o botão poderá aparecer apenas como **+** se a janela do browser estreita:

    ![Comando de novo projeto na página meus projetos](media/quickstarts/new-project-command.png)

1. Na **criar novo projeto** pop-up apresentado, introduza os valores adequados para o bloco de notas no qual está a migrar o **nome do projeto** e **ID de projeto** campos, desmarque a Opções de **projeto público** e **criar um README.md**, em seguida, selecione **criar**.

## <a name="upload-the-local-notebook"></a>Carregar o bloco de notas local

1. Na página do projeto, selecione **carregar** (que podem aparecer como um se seta apenas se a janela do navegador é pequeno), em seguida, selecione 1. Na caixa que aparece, selecione **do computador** se o bloco de notas estiver localizado no sistema de arquivos local, ou **de URL** se seu bloco de notas se encontra online:

    ![Comando para carregar um bloco de notas de um URL ou o computador local](media/quickstarts/upload-from-computer-url-command.png)

   (Novamente, se seu bloco de notas estiver num repositório do GitHub, siga os passos no [início rápido: Clonar um bloco de notas](quickstart-clone-jupyter-notebook.md) em vez disso.)

    - Se utilizar **de computador**, arraste e largue sua *.ipynb* ficheiros para o pop-up ou selecione o **escolher ficheiros**, em seguida, procure e selecione os ficheiros que pretende importar. Em seguida, selecione **carregar**. Os ficheiros carregados recebem o mesmo nome que os ficheiros locais. (Não precisa de carregar o conteúdo de qualquer *.ipynb_checkpoints* pastas.)

    ![Carregar a partir do pop-up de computador](media/quickstarts/upload-from-computer-popup.png)

    - Se utilizar **de URL**, introduza o endereço de origem na **URL de ficheiro** campo e o nome de ficheiro para atribuir ao bloco de notas no seu projeto no **nome de ficheiro** campo. Em seguida, selecione **carregar**. Se tiver vários ficheiros com URLs separados, utilize o **+ adicionar ficheiro** comando para verificar o primeiro URL que introduziu, após o qual o pop-up fornece novos campos para outro ficheiro.

    ![Carregar a partir do pop-up de URL](media/quickstarts/upload-from-url-popup.png)

1. Abra e execute seu bloco de notas recém-carregada para verificar o seu conteúdo e a operação. Quando tiver terminado, selecione **arquivo** > **interrompido e fechar** para fechar o bloco de notas.

1. Para partilhar uma ligação para o bloco de notas carregado, clique com botão direito no projeto e selecione o ficheiro de **copiar ligação** (atalho de teclado: y), em seguida, cole o link para a mensagem apropriada. Em alternativa, pode partilhar o projeto como um todo utilizando o **partilhar** controle na página do projeto.

1. Para editar ficheiros diferentes dos blocos de notas, clique com botão direito no projeto e selecione o ficheiro de **Editar ficheiro** (atalho de teclado: eu). A ação de predefinição **executar** (atalho de teclado: r), apenas mostra o conteúdo do ficheiro e não permite a edição.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: criar uma execução de um bloco de notas do Jupyter para fazer a regressão linear](tutorial-create-run-jupyter-notebook.md)
