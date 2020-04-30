---
title: Migrar um caderno de jupyter local para a pré-visualização de cadernos Azure
description: Transfira rapidamente um caderno Jupyter para Os Cadernos Azure Pré-visualização do seu computador local ou um URL web e, em seguida, partilhe-o para colaboração.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: baf05d7adb1340d712ff0fc87436d5bbac51bc8f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77064330"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Quickstart: Migrar um caderno de jupyter local em Pré-visualização de cadernos Azure

Neste arranque rápido, migra-se um caderno Jupyter do seu computador local ou outro URL de ficheiro acessível para Os Cadernos Azure. 

Os cadernos jupyter no seu próprio computador só estão acessíveis a si. Pode partilhar os seus ficheiros, mas os destinatários têm as suas próprias cópias locais do caderno, e é difícil incorporar as suas alterações. Mesmo que você armazene cadernos num repositório online partilhado como gitHub, cada colaborador deve ter uma instalação jupyter local configurada como a sua.

Ao migrar os seus cadernos locais ou repositórios para os Cadernos Azure, pode partilhá-los instantaneamente com os seus colaboradores, que precisam apenas de um navegador para visualizar e executar os seus cadernos. Se assinarem nos Cadernos Azure, também podem fazer alterações.

## <a name="prerequisites"></a>Pré-requisitos

- Um [caderno Jupyter](https://jupyter-notebook.readthedocs.io) no seu computador local ou em outro URL de ficheiro acessível. 

## <a name="create-a-project-on-azure-notebooks"></a>Criar um projeto sobre cadernos Azure

Este quickstart demonstra a migração de um caderno do seu computador local ou de outro URL de ficheiro acessível. Para migrar cadernos de um repositório GitHub, consulte [Quickstart: Clone um caderno](quickstart-clone-jupyter-notebook.md).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Vá aos [Cadernos Azure](https://notebooks.azure.com) e inscreva-se. (Para mais detalhes, consulte [Quickstart - Inscreva-se nos Cadernos Azure).](quickstart-sign-in-azure-notebooks.md)

1. Na sua página de perfil público, selecione **My Projects** no topo da página:

    ![My Projects link no topo da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **My Projects,** selecione **New Project** (atalho de teclado: n). O botão só **+** pode aparecer como se a janela do navegador fosse estreita:

    ![Novo comando do Projeto na página my projects](media/quickstarts/new-project-command.png)

1. No popup **Create New Project** que aparece, introduza valores apropriados para o caderno que está a migrar nos campos de Identificação do Projeto e Do **Projeto,** limpe as opções para o projeto **Público** e **Project ID** **Crie um README.md,** em seguida, selecione **Criar**.

## <a name="upload-the-local-notebook"></a>Faça upload do caderno local

1. Na página do projeto, selecione **Upload** (que só pode aparecer como uma seta up se a janela do seu navegador for pequena), em seguida, selecione 1. No popup que aparece, selecione **A partir do computador** se o seu caderno estiver localizado no seu sistema de ficheiros local, ou no **URL** se o seu caderno estiver localizado online:

    ![Comando para fazer upload de um caderno a partir de um URL ou do computador local](media/quickstarts/upload-from-computer-url-command.png)

   Mais uma vez, se o seu caderno estiver num repositório GitHub, siga os passos em [Quickstart: Clone um caderno](quickstart-clone-jupyter-notebook.md) em vez disso.

   - Se utilizar **o From Computer,** arraste e deixe cair os seus ficheiros *.ipynb* no popup, ou selecione **'Escolher Ficheiros',** então navegue e selecione os ficheiros que pretende importar. Em seguida, selecione **Carregar**. Os ficheiros enviados têm o mesmo nome que os ficheiros locais. Não precisa de carregar o conteúdo de quaisquer pastas *.ipynb_checkpoints.*

     ![Upload de popup de computador](media/quickstarts/upload-from-computer-popup.png)

   - Se utilizar **o URL**, introduza o endereço de origem no campo URL do **Ficheiro** e o nome de ficheiro para atribuir ao caderno no seu projeto no campo Nome de **Ficheiros.** Em seguida, selecione **Carregar**. Se tiver vários ficheiros com URLs separados, utilize o comando **Add File** para verificar o primeiro URL introduzido, após o qual o popup fornece novos campos para outro ficheiro.

     ![Upload de URL popup](media/quickstarts/upload-from-url-popup.png)

1. Abra e execute o seu caderno recém-carregado para verificar o seu conteúdo e funcionamento. Quando terminar, selecione **File** > **Halt e feche perto** de fechar o caderno.

1. Para partilhar um link para o seu caderno carregado, clique no ficheiro no projeto e selecione **Copy Link** (atalho de teclado: y), em seguida, cole esse link na mensagem apropriada. Em alternativa, pode partilhar o projeto como um todo utilizando o controlo **de Partilha** na página do projeto.

1. Para editar ficheiros que não os cadernos, clique no ficheiro no projeto e selecione **o ficheiro Editar** (atalho do teclado: i). A ação predefinida, **Executar** (atalho do teclado: r), apenas mostra o conteúdo do ficheiro e não permite a edição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Crie e execute um caderno jupyter para fazer regressão linear](tutorial-create-run-jupyter-notebook.md)
