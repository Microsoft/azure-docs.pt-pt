---
title: Clonar um bloco de notas Jupyter do GitHub com blocos de notas do Azure
description: Clonar um bloco de notas do Jupyter partir de um repositório do GitHub e executá-lo na sua conta de blocos de notas do Azure rapidamente.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: d7122b78-6daa-4bea-883b-ff832cfecef3
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: cfce5765cda1e3a1dfb7eb073719191b13b0696e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089204"
---
# <a name="quickstart-clone-a-notebook"></a>Início rápido: Clonar um bloco de notas

Muitos programadores e os cientistas de dados armazenam seus blocos de notas na [repositórios do GitHub](https://github.com), um gratuitas de serviço que fornece controle de versão e de armazenamento para muitos tipos diferentes de projetos. GitHub é frequentemente utilizado como um meio de colaboração em blocos de notas do Jupyter que são executados localmente. Nesses casos, cada colaborador mantém uma cópia local do repositório e executa os blocos de notas dessa cópia.

A clonagem, cria uma cópia de um bloco de notas do GitHub na sua conta de blocos de notas do Azure em vez disso. Este clone é independente do seu repositório original; as alterações são armazenadas na sua conta de blocos de notas do Azure apenas e não afetam o original. Como seu clone é na cloud, pode partilhar o projeto com outros funcionários que não precisam fazer quaisquer cópias locais ou até mesmo ter Jupyter instalado em seus próprios computadores. Também pode clonar um bloco de notas simplesmente como um ponto de partida para um projeto de seu próprio ou para obter ficheiros de dados.

## <a name="clone-azure-cognitive-services-notebooks"></a>Clonar blocos de notas de serviços cognitivos do Azure

1. Aceda a [blocos de notas do Azure](https://notebooks.azure.com) e iniciar sessão. (Para obter detalhes, consulte [início rápido - início de sessão para blocos de notas do Azure](quickstart-sign-in-azure-notebooks.md)).

1. Na sua página de perfil público, selecione **meus projetos** na parte superior da página:

    ![Meu link de projetos na parte superior da janela do browser](media/quickstarts/my-projects-link.png)

1. Sobre o **meus projetos** página, selecione o botão de seta para cima (atalho de teclado: U; o botão aparece como **carregar repositório do GitHub** quando a janela do navegador é amplo o bastante):

    ![Carregar o comando de repositório do GitHub na página meus projetos](media/quickstarts/upload-github-repo-command.png)

1. Na **carregar repositório do GitHub** que aparece, introduza ou defina os seguintes detalhes, em seguida, selecione **importação**:

   - **Repositório do GitHub**: Microsoft/cognitivos-services-blocos de notas (este nome clona os blocos de notas do Jupyter para os serviços cognitivos do Azure no [ https://github.com/Microsoft/cognitive-services-notebooks ](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Clonar recursivamente**: (limpo)
   - **Nome do projeto**: Clone de serviços cognitivos
   - **ID do projeto**: clone de serviços cognitivos
   - **Público**: (limpo)

     ![Carregar o pop-up de repositório do GitHub para recolher informações do repositório](media/quickstarts/upload-github-repo-popup.png)

1. Seja paciente, enquanto o processo for concluído; um repositório de clonagem pode demorar alguns minutos.

1. Após a conclusão da clonagem, blocos de notas do Azure leva-o para o novo projeto onde pode ver as cópias de todos os arquivos.

    [![](media/quickstarts/completed-clone.png "Vista de um clone concluída")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Partilhar um bloco de notas

1. Para partilhar a sua cópia do projeto clonado, utilize o **partilhar** controlar ou obter uma ligação, obter o código HTML ou de Markdown que contém a ligação ou criar uma mensagem de e-mail com a ligação:

    ![Comando de partilha do projeto](media/quickstarts/share-project-command.png)

1. Uma vez que limpou a **público** opção ao clonar o projeto, o clone é privada. Para fazer a cópia pública, selecione **definições do projeto**, defina o **projeto público** opção no popup e, em seguida, selecione **guardar**.

1. Selecione um bloco de notas no projeto para executá-lo. Cada bloco de anotações no repositório de serviços cognitivos do Azure, por exemplo, é sua própria guia de introdução autossuficiente. A imagem abaixo mostra o resultado da utilização do bloco de notas BingImageSearchAPI, depois de adicionar uma chave de assinatura de API dos serviços cognitivos e alterando o termo de pesquisa "puppies" para "bunnies":

    ![A executar o bloco de notas do Jupyter clonou do GitHub](media/quickstarts/clone-notebook-result.png)

1. Quando terminar a executar o bloco de notas, selecione **arquivo** > **fechar e parar** para fechar o bloco de notas e sua janela do browser.

1. Para partilhar um bloco de notas individual no projeto, o bloco de notas com o botão direito e selecione **copiar ligação** (atalho de teclado: y):

    ![Comando de menu de contexto para copiar uma ligação para um bloco de anotações individual](media/quickstarts/copy-link-to-individual-notebook.png)

1. Para editar ficheiros diferentes dos blocos de notas, clique com botão direito no projeto e selecione o ficheiro de **Editar ficheiro** (atalho de teclado: eu). A ação de predefinição **executar** (atalho de teclado: r), apenas mostra o conteúdo do ficheiro e não permite a edição.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: criar uma execução de um bloco de notas do Jupyter para fazer a regressão linear](tutorial-create-run-jupyter-notebook.md)
