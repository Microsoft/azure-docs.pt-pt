---
title: 'Tutorial: Hospedar um site estático no armazenamento blob - Armazenamento Azure'
description: Saiba como configurar uma conta de armazenamento para hospedagem de websiteestático e implemente um site estático para o Armazenamento Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 03850315a05f569d2c6ba9405b6ec38bb6b1305d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78330400"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Tutorial: Hospedar um site estático no Armazenamento blob

Neste tutorial, você vai aprender a construir e implementar um site estático para o Armazenamento Azure. Quando terminar, terá um site estático a que os utilizadores podem aceder publicamente. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar o alojamento do site estático
> * Implementar um site da Hello World

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Certifique-se de criar uma conta de armazenamento padrão v2 de uso geral . Os sites estáticos não estão disponíveis em qualquer outro tipo de conta de armazenamento.

Este tutorial utiliza o [Visual Studio Code](https://code.visualstudio.com/download), uma ferramenta gratuita para programadores, para construir o site estático e implementá-lo numa conta de Armazenamento Azure.

Depois de instalar o Código do Estúdio Visual, instale a extensão de pré-visualização do Armazenamento Azure. Esta extensão integra a funcionalidade de gestão de armazenamento azure com o Visual Studio Code. Utilizará a extensão para implementar o seu website estático no Armazenamento Azure. Para instalar a extensão:

1. Abra o Visual Studio Code.
2. Na barra de ferramentas, clique em **Extensões**. Procure o *Armazenamento Azure*e selecione a extensão de **Armazenamento Azure** da lista. Em seguida, clique no botão **Instalar** para instalar a extensão.

    ![Instale a extensão de armazenamento Azure no Código VS](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal Azure](https://portal.azure.com/) para começar.

## <a name="configure-static-website-hosting"></a>Configurar o alojamento do site estático

O primeiro passo é configurar a sua conta de armazenamento para alojar um site estático no portal Azure. Quando configura a sua conta para hospedagem estátática do site, o Azure Storage cria automaticamente um recipiente chamado *$web*. O recipiente *$web* conterá os ficheiros do seu website estático. 

1. Abra o [portal Azure](https://portal.azure.com/) no seu navegador web. 
1. Localize a sua conta de armazenamento e exiba a visão geral da conta.
1. Selecione **site estático** para exibir a página de configuração para sites estáticos.
1. Selecione **Habilitado** para permitir o alojamento estático do site para a conta de armazenamento.
1. No campo de nome do **documento Index,** especifique uma página de índice predefinido do *index.html*. A página de índice predefinido é apresentada quando um utilizador navega para a raiz do seu website estático.  
1. No campo de trajetória do **erro,** especifique uma página de erro padrão de *404.html*. A página de erro predefinida é apresentada quando um utilizador tenta navegar para uma página que não existe no seu site estático.
1. Clique em **Guardar**. O portal Azure apresenta agora o seu ponto final do site estático. 

    ![Ativar hospedagem de site estático para uma conta de armazenamento](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Implementar um site da Hello World

Em seguida, crie uma página web Hello World com Visual Studio Code e implemente-a para o site estático hospedado na sua conta de Armazenamento Azure.

1. Crie uma pasta vazia chamada *mywebsite* no seu sistema de ficheiros local. 
1. Lance o Código do Estúdio Visual e abra a pasta que acabou de criar a partir do painel **Explorer.**

    ![Pasta aberta no Código do Estúdio Visual](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Crie o ficheiro de índice predefinido na pasta *do mywebsite* e nomeie-o *index.html*.

    ![Criar o ficheiro de índice padrão no Código do Estúdio Visual](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Abra o *index.html* no editor, cola o seguinte texto no ficheiro e guarda-o:

    ```
    <h1>Hello World!</h1>
    ```

1. Crie o ficheiro de erro predefinido e nomeie-o *404.html*.
1. Abra *404.html* no editor, cola o seguinte texto no ficheiro e guarde-o:

    ```
    <h1>404</h1>
    ```

1. Clique à direita sob a pasta do *mywebsite* no painel **Explorer** e selecione **Implementar para site estático...** para implementar o seu website. Será solicitado a iniciar sessão no Azure para recuperar uma lista de subscrições.

1. Selecione a subscrição que contém a conta de armazenamento para a qual permitiu hospedagem de website estático. Em seguida, selecione a conta de armazenamento quando solicitada.

O Visual Studio Code irá agora enviar os seus ficheiros para o seu ponto final da web e mostrar a barra de estado de sucesso. Lance o site para vê-lo em Azure.

Completou o tutorial com sucesso e implementou um site estático para o Azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a configurar a sua conta de Armazenamento Azure para hospedagem de websites estáticos, e como criar e implementar um site estático para um ponto final do Azure.

Em seguida, aprenda a configurar um domínio personalizado com o seu site estático.

> [!div class="nextstepaction"]
> [Mapear um domínio personalizado para um ponto final de armazenamento de Blob Azure](storage-custom-domain-name.md)
