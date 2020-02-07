---
title: Migrar um bloco de anotações Jupyter local para Azure Notebooks visualização
description: Transfira rapidamente um notebook Jupyter para Azure Notebooks visualização do seu computador local ou de uma URL da Web e, em seguida, compartilhe-o para colaboração.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: baf05d7adb1340d712ff0fc87436d5bbac51bc8f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064330"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Início rápido: migrar um bloco de anotações Jupyter local no Azure Notebooks versão prévia

Neste arranque rápido, migra-se um caderno Jupyter do seu computador local ou outro URL de ficheiro acessível para Os Cadernos Azure. 

Os cadernos jupyter no seu próprio computador só estão acessíveis a si. Pode partilhar os seus ficheiros, mas os destinatários têm as suas próprias cópias locais do caderno, e é difícil incorporar as suas alterações. Mesmo que você armazene cadernos num repositório online partilhado como gitHub, cada colaborador deve ter uma instalação jupyter local configurada como a sua.

Ao migrar os seus cadernos locais ou repositórios para os Cadernos Azure, pode partilhá-los instantaneamente com os seus colaboradores, que precisam apenas de um navegador para visualizar e executar os seus cadernos. Se assinarem nos Cadernos Azure, também podem fazer alterações.

## <a name="prerequisites"></a>Pré-requisitos

- Um [caderno Jupyter](https://jupyter-notebook.readthedocs.io) no seu computador local ou em outro URL de ficheiro acessível. 

## <a name="create-a-project-on-azure-notebooks"></a>Criar um projeto em blocos de notas do Azure

Este quickstart demonstra a migração de um caderno do seu computador local ou de outro URL de ficheiro acessível. Para migrar cadernos de um repositório GitHub, consulte [Quickstart: Clone um caderno](quickstart-clone-jupyter-notebook.md).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Vá aos [Cadernos Azure](https://notebooks.azure.com) e inscreva-se. (Para mais detalhes, consulte [Quickstart - Inscreva-se nos Cadernos Azure).](quickstart-sign-in-azure-notebooks.md)

1. Na sua página de perfil público, selecione **My Projects** no topo da página:

    ![Meu link de projetos na parte superior da janela do browser](media/quickstarts/my-projects-link.png)

1. Na página **My Projects,** selecione **New Project** (atalho de teclado: n). O botão só pode aparecer como **+** se a janela do navegador for estreita:

    ![Comando de novo projeto na página meus projetos](media/quickstarts/new-project-command.png)

1. No popup **Create New Project** que aparece, introduza valores apropriados para o caderno que está a migrar nos campos de Identificação do Projeto e Do **Projeto,** limpe as opções para o projeto **Público** e **Crie um README.md,** em seguida, selecione **Criar**.

## <a name="upload-the-local-notebook"></a>Carregar o bloco de notas local

1. Na página do projeto, selecione **Upload** (que só pode aparecer como uma seta up se a janela do seu navegador for pequena), em seguida, selecione 1. No popup que aparece, selecione **A partir do computador** se o seu caderno estiver localizado no seu sistema de ficheiros local, ou no **URL** se o seu caderno estiver localizado online:

    ![Comando para carregar um bloco de notas de um URL ou o computador local](media/quickstarts/upload-from-computer-url-command.png)

   Mais uma vez, se o seu caderno estiver num repositório GitHub, siga os passos em [Quickstart: Clone um caderno](quickstart-clone-jupyter-notebook.md) em vez disso.

   - Se utilizar **o From Computer,** arraste e deixe cair os seus ficheiros *.ipynb* no popup, ou selecione **'Escolher Ficheiros',** então navegue e selecione os ficheiros que pretende importar. Em seguida, selecione **Carregar**. Os ficheiros carregados recebem o mesmo nome que os ficheiros locais. Não precisa de carregar o conteúdo de quaisquer pastas *.ipynb_checkpoints.*

     ![Carregar a partir do pop-up de computador](media/quickstarts/upload-from-computer-popup.png)

   - Se utilizar **o URL**, introduza o endereço de origem no campo URL do **Ficheiro** e o nome de ficheiro para atribuir ao caderno no seu projeto no campo Nome de **Ficheiros.** Em seguida, selecione **Carregar**. Se tiver vários ficheiros com URLs separados, utilize o comando **Add File** para verificar o primeiro URL introduzido, após o qual o popup fornece novos campos para outro ficheiro.

     ![Carregar a partir do pop-up de URL](media/quickstarts/upload-from-url-popup.png)

1. Abra e execute seu bloco de notas recém-carregada para verificar o seu conteúdo e a operação. Quando terminar, selecione **File** > **Halt e feche perto** de fechar o caderno.

1. Para partilhar um link para o seu caderno carregado, clique no ficheiro no projeto e selecione **Copy Link** (atalho de teclado: y), em seguida, cole esse link na mensagem apropriada. Em alternativa, pode partilhar o projeto como um todo utilizando o controlo **de Partilha** na página do projeto.

1. Para editar ficheiros que não os cadernos, clique no ficheiro no projeto e selecione **o ficheiro Editar** (atalho do teclado: i). A ação predefinida, **Executar** (atalho do teclado: r), apenas mostra o conteúdo do ficheiro e não permite a edição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Crie e execute um caderno jupyter para fazer regressão linear](tutorial-create-run-jupyter-notebook.md)
