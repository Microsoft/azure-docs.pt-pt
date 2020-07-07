---
title: Migrar um caderno jupyter local para visualização de cadernos Azure
description: Transfira rapidamente um caderno Jupyter para Azure Notebooks Preview a partir do seu computador local ou um URL web e, em seguida, partilhe-o para colaboração.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 9c961ad67260cee2809e0bba1e79f2c709183dea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85832122"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Quickstart: Migrar um caderno jupyter local em Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Neste arranque rápido, migra um bloco de notas Jupyter do seu computador local ou outro URL de ficheiro acessível para Azure Notebooks. 

Os cadernos Jupyter no seu próprio computador são acessíveis apenas a si. Pode partilhar os seus ficheiros, mas os destinatários têm as suas próprias cópias locais do caderno, e é difícil incorporar as suas alterações. Mesmo que guarde cadernos num repositório online partilhado, como o GitHub, cada colaborador deve ter uma instalação jupyter local configurada como a sua.

Ao migrar os seus cadernos locais ou baseados em repositórios para os Cadernos Azure, pode partilhá-los instantaneamente com os seus colaboradores, que precisam apenas de um browser para visualizar e executar os seus cadernos. Se assinarem os Cadernos Azure, também podem fazer alterações.

## <a name="prerequisites"></a>Pré-requisitos

- Um [caderno Jupyter](https://jupyter-notebook.readthedocs.io) no seu computador local ou em outro URL de ficheiro acessível. 

## <a name="create-a-project-on-azure-notebooks"></a>Criar um projeto em Cadernos Azure

Este quickstart demonstra migrar um bloco de notas do seu computador local ou de outro URL de ficheiro acessível. Para migrar cadernos de um repositório GitHub, consulte [Quickstart: Clone a notebook](quickstart-clone-jupyter-notebook.md).

1. Vá aos [Cadernos Azure](https://notebooks.azure.com) e inscreva-se. (Para mais detalhes, consulte [Quickstart - Inicie súm em Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na sua página de perfil público, selecione **Os Meus Projetos** no topo da página:

    ![O meu link de projetos no topo da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **My Projects,** selecione **New Project** (atalho de teclado: n). O botão só pode aparecer como **+** se a janela do navegador fosse estreita:

    ![Novo comando do projeto na página My Projects](media/quickstarts/new-project-command.png)

1. No popup **Create New Project** que aparece, insira os valores apropriados para o caderno que está a migrar nos campos do nome do **Projeto** e do **Projeto ID,** limpe as opções para **o projeto público** e crie **uma README.md,** em seguida, selecione **Create**.

## <a name="upload-the-local-notebook"></a>Faça upload do caderno local

1. Na página do projeto, selecione **Upload** (que pode aparecer como uma seta para cima apenas se a janela do seu navegador for pequena), em seguida, selecione 1. No popup que aparece, selecione **From computer** if your notebook is located on your local file system, or **From URL** if your notebook is located online:

    ![Ordenar o upload de um caderno a partir de um URL ou do computador local](media/quickstarts/upload-from-computer-url-command.png)

   Mais uma vez, se o seu caderno estiver num repositório gitHub, siga os passos no [Quickstart: Clone a notebook](quickstart-clone-jupyter-notebook.md) em vez disso.

   - Se utilizar **o From Computer,** arraste e deixe cair os ficheiros *.ipynb* no popup ou selecione **Choose Files**, em seguida, navegue e selecione os ficheiros que pretende importar. Em seguida, selecione **Carregar**. Os ficheiros carregados têm o mesmo nome que os ficheiros locais. Não é necessário carregar o conteúdo de nenhuma pasta *.ipynb_checkpoints.*

     ![Upload do popup do computador](media/quickstarts/upload-from-computer-popup.png)

   - Se utilizar **a partir de URL,** insira o endereço de origem no campo URL de **ficheiro** e o nome de ficheiro para atribuir ao portátil no seu projeto no campo Nome de **Ficheiros.** Em seguida, selecione **Carregar**. Se tiver vários ficheiros com URLs separados, utilize o comando **Add File** para verificar o primeiro URL introduzido, após o qual o popup fornece novos campos para outro ficheiro.

     ![Upload do popup URL](media/quickstarts/upload-from-url-popup.png)

1. Abra e execute o seu novo caderno para verificar o seu conteúdo e funcionamento. Quando terminar, selecione **'Parada de**  >  **ficheiro' e perto** de fechar o caderno.

1. Para partilhar um link para o seu bloco de notas carregado, clique com o botão direito no ficheiro do projeto e selecione **Copy Link** (atalho de teclado: y), em seguida, cole esse link na mensagem apropriada. Em alternativa, pode partilhar o projeto como um todo usando o controlo **de Partilhar** na página do projeto.

1. Para editar ficheiros que não os cadernos, clique com o botão direito no ficheiro do projeto e selecione editar o **ficheiro Editar** (atalho de teclado: i). A ação predefinida, **Executar** (atalho de teclado: r), só mostra o conteúdo do ficheiro e não permite a edição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Criar e executar um caderno Jupyter para fazer regressão linear](tutorial-create-run-jupyter-notebook.md)
