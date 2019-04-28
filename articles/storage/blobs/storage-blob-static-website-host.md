---
title: 'Tutorial: Alojar um Web site estático no armazenamento de BLOBs, armazenamento do Azure'
description: Saiba como configurar uma conta de armazenamento para alojamento de Web sites estáticos e implementar um Web site estático no armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: tamram
ms.custom: seodec18
ms.openlocfilehash: eb472465c0d35150f2a13563058905751219411d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61428586"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Tutorial: Alojar um Web site estático no armazenamento de BLOBs

Este tutorial é a primeira parte de uma série. Nela, saiba como criar e implementar um Web site estático no armazenamento do Azure. Quando tiver terminado, terá um Web site estático que os utilizadores podem aceder ao público. 

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Configurar o alojamento de Web site estático
> * Implementar um Web site do Hello World

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Este tutorial utiliza [Visual Studio Code](https://code.visualstudio.com/download), uma ferramenta gratuita para programadores, para criar o Web site estático e implementá-la a uma conta de armazenamento do Azure.

Depois de instalar o Visual Studio Code, instale a extensão de pré-visualização do armazenamento do Azure. Esta extensão integra-se a funcionalidade de gestão de armazenamento do Azure com o Visual Studio Code. Irá utilizar a extensão para implementar o seu Web site estático no armazenamento do Azure. Para instalar a extensão:

1. Abra o Visual Studio Code.
2. Na barra de ferramentas, clique em **extensões**. Procure *armazenamento do Azure*e selecione o **armazenamento do Azure** extensão da lista. Em seguida, clique nas **instalar** botão para instalar a extensão.

    ![Instalar a extensão de armazenamento do Azure no VS Code](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão para o [portal do Azure](https://portal.azure.com/) para começar a utilizar.

## <a name="configure-static-website-hosting"></a>Configurar o alojamento de Web site estático

A primeira etapa é configurar a sua conta de armazenamento para alojar um Web site estático no portal do Azure. Quando configura a sua conta para o alojamento de Web site estático, o armazenamento do Azure cria automaticamente um contentor com o nome *$web*. O *$web* contentor irá conter os ficheiros do Web site estático. 

1. Abra o [portal do Azure](https://portal.azure.com/) no seu browser. 
1. Localize a sua conta de armazenamento e exibir a descrição geral da conta.
1. Selecione **Web site estático** para exibir a página de configuração para os Web sites estáticos.
1. Selecione **ativado** para permitir o alojamento de Web site estático para a conta de armazenamento.
1. Na **nome do documento de índice** campo, especifique uma página de índice do padrão de *Index*. A página de índice de predefinida é apresentada quando um utilizador navega para a raiz do seu Web site estático.  
1. Na **caminho do documento de erro** campo, especifique uma página de erro padrão do *404. html*. A página de erro padrão é apresentada quando um usuário tenta navegar para uma página que não existe no seu Web site estático.
1. Clique em **Guardar**. O portal do Azure agora apresenta o ponto de final do Web site estático. 

    ![Permitir o alojamento de Web site estático para uma conta de armazenamento](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Implementar um Web site do Hello World

Em seguida, crie uma página da web de Hello World com o Visual Studio Code e implementá-la para o Web site estático alojado na sua conta de armazenamento do Azure.

1. Crie uma pasta vazia com o nome *mywebsite* no seu sistema de arquivos local. 
1. Inicie o Visual Studio Code e abrir a pasta que acabou de criar a partir da **Explorer** painel.

    ![Abrir pasta no Visual Studio Code](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Criar o ficheiro de índice predefinido no *mywebsite* pasta e o nomeio *Index*.

    ![Criar o ficheiro de índice predefinido no Visual Studio Code](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Open *Index* no editor, cole o seguinte texto no ficheiro e guarde-o:

    ```
    <h1>Hello World!</h1>
    ```

1. Crie o ficheiro de erro padrão e nomeie- *404. html*.
1. Open *404. html* no editor, cole o seguinte texto no ficheiro e guarde-o:

    ```
    <h1>404</h1>
    ```

1. Com o botão direito no *mywebsite* pasta na **Explorer** painel e selecione **implementar no Web site estático...**  para implementar o seu Web site. Será solicitado a iniciar sessão no Azure para obter uma lista de subscrições.

1. Selecione a subscrição que contém a conta de armazenamento para o qual ativou o alojamento de Web site estático. Em seguida, selecione a conta de armazenamento quando lhe for pedido.

Visual Studio Code agora irá carregar os ficheiros para o ponto final de web e mostrar a barra de estado de êxito. Inicie o Web site para vê-la no Azure.

![Ver a implementação de Web site estático no Azure](media/storage-blob-static-website-host/view-static-website-endpoint.png)

Tiver concluído o tutorial e implementar um Web site estático para o Azure com êxito.

## <a name="next-steps"></a>Passos Seguintes

Na primeira parte deste tutorial, ficou a saber como configurar a sua conta de armazenamento do Azure para o alojamento de Web site estático e como criar e implementar um Web site estático para um ponto final do Azure.

Agora avance para a parte dois, onde configura um domínio personalizado com SSL do Web site estático com o CDN do Azure.

> [!div class="nextstepaction"]
> [Utilizar a CDN do Azure para ativar um domínio personalizado com SSL para um Web site estático](storage-blob-static-website-custom-domain.md)
