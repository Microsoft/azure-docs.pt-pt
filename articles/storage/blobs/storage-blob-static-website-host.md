---
title: 'Tutorial: Hospedar um website estático no armazenamento blob - Azure Storage'
description: Saiba como configurar uma conta de armazenamento para hospedagem estática do site e implementar um site estático para o Azure Storage.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 36cdaa813e0eccb23563301052aee268ab61533a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94888509"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Tutorial: Hospedar um site estático no Blob Storage

Neste tutorial, você vai aprender a construir e implementar um site estático para o Azure Storage. Quando terminar, terá um website estático a que os utilizadores possam aceder publicamente. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar hospedagem estática do site
> * Implementar um site da Hello World

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Certifique-se de criar uma conta de armazenamento v2 Standard para fins gerais . Os sites estáticos não estão disponíveis em qualquer outro tipo de conta de armazenamento.

Este tutorial utiliza [o Código do Estúdio Visual,](https://code.visualstudio.com/download)uma ferramenta gratuita para programadores, para construir o website estático e implantá-lo numa conta de Armazenamento Azure.

Depois de instalar o Código do Estúdio Visual, instale a extensão de pré-visualização do Azure Storage. Esta extensão integra a funcionalidade de gestão de Armazenamento Azure com o Código do Estúdio Visual. Utilizará a extensão para implementar o seu website estático para o Azure Storage. Para instalar a extensão:

1. Abra o Visual Studio Code.
2. Na barra de **ferramentas,** clique em Extensões . Procure *por Azure Storage* e selecione a extensão **de Armazenamento Azure** da lista. Em seguida, clique no botão **Instalar** para instalar a extensão.

    ![Instale a extensão de armazenamento Azure no Código VS](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal Azure](https://portal.azure.com/) para começar.

## <a name="configure-static-website-hosting"></a>Configurar hospedagem estática do site

O primeiro passo é configurar a sua conta de armazenamento para hospedar um website estático no portal Azure. Ao configurar a sua conta para hospedagem estática no site, o Azure Storage cria automaticamente um recipiente chamado *$web*. O recipiente *$web* conterá os ficheiros do seu website estático. 

1. Abra o [portal Azure](https://portal.azure.com/) no seu navegador web. 
1. Localize a sua conta de armazenamento e apresente a visão geral da conta.
1. Selecione **site estático** para exibir a página de configuração para sites estáticos.
1. Selecione **Ativado** para permitir hospedagem estática do site para a conta de armazenamento.
1. No campo **de nome do documento Índice,** especifique uma página de índice predefinido deindex.htm *l*. A página de índice predefinido é apresentada quando um utilizador navega para a raiz do seu website estático.  
1. No campo de trajetória do **documento Erro,** especifique uma página de erro padrão de *404.html*. A página de erro predefinida é exibida quando um utilizador tenta navegar para uma página que não existe no seu website estático.
1. Clique em **Guardar**. O portal Azure apresenta agora o seu ponto final estático do site. 

    ![Ativar hospedagem estática no site para uma conta de armazenamento](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Implementar um site da Hello World

Em seguida, crie uma página web hello world com Visual Studio Code e implemente-a para o site estático hospedado na sua conta de Armazenamento Azure.

1. Crie uma pasta vazia chamada *mywebsite* no seu sistema de ficheiros local. 
1. Lance o Código do Estúdio Visual e abra a pasta que acabou de criar a partir do painel **Explorer.**

    ![Abrir pasta no Código do Estúdio Visual](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Crie o ficheiro de índice predefinido na pasta *mywebsite* e nomeie-o *index.html*.

    ![Criar o ficheiro de índice predefinido no Código do Estúdio Visual](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Abra *index.html* no editor, cole o seguinte texto no ficheiro e guarde-o:

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>Hello World!</h1>
      </body>
    </html>
    ```

1. Crie o ficheiro de erro predefinido e nomeie-o *404.html*.
1. Abra *404.html* no editor, cole o seguinte texto no ficheiro e guarde-o:

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>404</h1>
      </body>
    </html>
    ```

1. Clique com o botão direito sob a pasta *mywebsite* no painel **Explorer** e selecione **Implementar para o Site Estático...** para implementar o seu website. Será solicitado a iniciar sessão no Azure para recuperar uma lista de subscrições.

1. Selecione a subscrição que contém a conta de armazenamento para a qual permitiu hospedar o site estático. Em seguida, selecione a conta de armazenamento quando solicitada.

O Visual Studio Code irá agora enviar os seus ficheiros para o seu ponto final web e mostrar a barra de estado de sucesso. Lance o site para vê-lo em Azure.

Completou com sucesso o tutorial e implementou um site estático para Azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a configurar a sua conta de Armazenamento Azure para hospedagem estática do site, e como criar e implementar um website estático para um ponto final do Azure.

Em seguida, aprenda a configurar um domínio personalizado com o seu website estático.

> [!div class="nextstepaction"]
> [Mapear um domínio personalizado para um ponto final de armazenamento Azure Blob](storage-custom-domain-name.md)
