---
title: 'Tutorial: Alojar um Web site estático no armazenamento de BLOBs, armazenamento do Azure'
description: Saiba como configurar uma conta de armazenamento para alojamento de Web sites estáticos e implementar um Web site estático no armazenamento do Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 69acb9152d1dc30d986276939e29e8c183095b18
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539608"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Tutorial: Alojar um Web site estático no armazenamento de BLOBs

Neste tutorial, você vai aprender a construir e implementar um site estático para o Armazenamento Azure. Quando tiver terminado, terá um Web site estático que os utilizadores podem aceder ao público. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar o alojamento de Web site estático
> * Implementar um Web site do Hello World

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Certifique-se de criar uma conta de armazenamento padrão v2 de uso geral . Os sites estáticos não estão disponíveis em qualquer outro tipo de conta de armazenamento.

Este tutorial utiliza o [Visual Studio Code](https://code.visualstudio.com/download), uma ferramenta gratuita para programadores, para construir o site estático e implementá-lo numa conta de Armazenamento Azure.

Depois de instalar o Visual Studio Code, instale a extensão de pré-visualização do armazenamento do Azure. Esta extensão integra-se a funcionalidade de gestão de armazenamento do Azure com o Visual Studio Code. Irá utilizar a extensão para implementar o seu Web site estático no armazenamento do Azure. Para instalar a extensão:

1. Abra o Visual Studio Code.
2. Na barra de ferramentas, clique em **Extensões**. Procure o *Armazenamento Azure*e selecione a extensão de **Armazenamento Azure** da lista. Em seguida, clique no botão **Instalar** para instalar a extensão.

    ![Instalar a extensão de armazenamento do Azure no VS Code](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal Azure](https://portal.azure.com/) para começar.

## <a name="configure-static-website-hosting"></a>Configurar o alojamento de Web site estático

A primeira etapa é configurar a sua conta de armazenamento para alojar um Web site estático no portal do Azure. Quando configura a sua conta para hospedagem estátática do site, o Azure Storage cria automaticamente um recipiente chamado *$web*. O recipiente *$web* conterá os ficheiros do seu website estático. 

1. Abra o [portal Azure](https://portal.azure.com/) no seu navegador web. 
1. Localize a sua conta de armazenamento e exibir a descrição geral da conta.
1. Selecione **site estático** para exibir a página de configuração para sites estáticos.
1. Selecione **Habilitado** para permitir o alojamento estático do site para a conta de armazenamento.
1. No campo de nome do **documento Index,** especifique uma página de índice predefinido do *index.html*. A página de índice de predefinida é apresentada quando um utilizador navega para a raiz do seu Web site estático.  
1. No campo de trajetória do **erro,** especifique uma página de erro padrão de *404.html*. A página de erro padrão é apresentada quando um usuário tenta navegar para uma página que não existe no seu Web site estático.
1. Clique em **Guardar**. O portal do Azure agora apresenta o ponto de final do Web site estático. 

    ![Permitir o alojamento de Web site estático para uma conta de armazenamento](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Implementar um Web site do Hello World

Em seguida, crie uma página da web de Hello World com o Visual Studio Code e implementá-la para o Web site estático alojado na sua conta de armazenamento do Azure.

1. Crie uma pasta vazia chamada *mywebsite* no seu sistema de ficheiros local. 
1. Lance o Código do Estúdio Visual e abra a pasta que acabou de criar a partir do painel **Explorer.**

    ![Abrir pasta no Visual Studio Code](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Crie o ficheiro de índice predefinido na pasta *do mywebsite* e nomeie-o *index.html*.

    ![Criar o ficheiro de índice predefinido no Visual Studio Code](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Abra o *index.html* no editor, cola o seguinte texto no ficheiro e guarda-o:

    ```
    <h1>Hello World!</h1>
    ```

1. Crie o ficheiro de erro predefinido e nomeie-o *404.html*.
1. Abra *404.html* no editor, cola o seguinte texto no ficheiro e guarde-o:

    ```
    <h1>404</h1>
    ```

1. Clique à direita sob a pasta do *mywebsite* no painel **Explorer** e selecione **Implementar para site estático...** para implementar o seu website. Será solicitado a iniciar sessão no Azure para obter uma lista de subscrições.

1. Selecione a subscrição que contém a conta de armazenamento para o qual ativou o alojamento de Web site estático. Em seguida, selecione a conta de armazenamento quando lhe for pedido.

Visual Studio Code agora irá carregar os ficheiros para o ponto final de web e mostrar a barra de estado de êxito. Inicie o Web site para vê-la no Azure.

![Ver a implementação de Web site estático no Azure](media/storage-blob-static-website-host/view-static-website-endpoint.png)

Tiver concluído o tutorial e implementar um Web site estático para o Azure com êxito.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a configurar a sua conta de Armazenamento Azure para hospedagem de websites estáticos, e como criar e implementar um site estático para um ponto final do Azure.

Em seguida, aprenda a configurar um domínio personalizado com o seu site estático.

> [!div class="nextstepaction"]
> [Mapear um domínio personalizado para um ponto final de armazenamento de Blob Azure](storage-custom-domain-name.md)
