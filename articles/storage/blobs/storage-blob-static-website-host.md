---
title: 'Tutorial: hospedar um site estático no armazenamento de BLOBs – armazenamento do Azure'
description: Saiba como configurar uma conta de armazenamento para hospedagem de site estático e implantar um site estático no armazenamento do Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: cd3db902d713910de5a8199df85089d62569f9d7
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757571"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Tutorial: hospedar um site estático no armazenamento de BLOBs

Este tutorial é a primeira parte de uma série. Nele, você aprende a criar e implantar um site estático no armazenamento do Azure. Quando tiver terminado, você terá um site estático que os usuários podem acessar publicamente. 

Na primeira parte da série, saiba como:

> [!div class="checklist"]
> * Configurar Hospedagem de site estático
> * Implantar um site Olá, Mundo

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Certifique-se de criar uma conta de armazenamento de uso geral v2. Os sites estáticos não estão disponíveis em nenhum outro tipo de conta de armazenamento.

Este tutorial usa [Visual Studio Code](https://code.visualstudio.com/download), uma ferramenta gratuita para programadores, para criar o site estático e implantá-lo em uma conta de armazenamento do Azure.

Depois de instalar o Visual Studio Code, instale a extensão de visualização do armazenamento do Azure. Essa extensão integra a funcionalidade de gerenciamento de armazenamento do Azure com Visual Studio Code. Você usará a extensão para implantar seu site estático no armazenamento do Azure. Para instalar a extensão:

1. Abra o Visual Studio Code.
2. Na barra de ferramentas, clique em **extensões**. Pesquise o *armazenamento do Azure*e selecione a extensão de **armazenamento do Azure** na lista. Em seguida, clique no botão **instalar** para instalar a extensão.

    ![Instalar a extensão de armazenamento do Azure no VS Code](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Entre no [portal do Azure](https://portal.azure.com/) para começar.

## <a name="configure-static-website-hosting"></a>Configurar Hospedagem de site estático

A primeira etapa é configurar sua conta de armazenamento para hospedar um site estático no portal do Azure. Quando você configura sua conta para hospedagem de site estático, o armazenamento do Azure cria automaticamente um contêiner chamado *$Web*. O contêiner de *$Web* conterá os arquivos para seu site estático. 

1. Abra o [portal do Azure](https://portal.azure.com/) no navegador da Web. 
1. Localize sua conta de armazenamento e exiba a visão geral da conta.
1. Selecione **site estático** para exibir a página de configuração para sites estáticos.
1. Selecione **habilitado** para habilitar a hospedagem de site estático para a conta de armazenamento.
1. No campo **nome do documento de índice** , especifique uma página de índice padrão de *index. html*. A página de índice padrão é exibida quando um usuário navega para a raiz do seu site estático.  
1. No campo **caminho do documento de erro** , especifique uma página de erro padrão de *404. html*. A página de erro padrão é exibida quando um usuário tenta navegar para uma página que não existe no seu site estático.
1. Clique em **Guardar**. O portal do Azure agora exibe o ponto de extremidade do site estático. 

    ![Habilitar Hospedagem de site estático para uma conta de armazenamento](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Implantar um site Olá, Mundo

Em seguida, crie uma página da Web Olá, Mundo com Visual Studio Code e implante-a no site estático hospedado em sua conta de armazenamento do Azure.

1. Crie uma pasta vazia chamada *mysite* no sistema de arquivos local. 
1. Inicie o Visual Studio Code e abra a pasta que você acabou de criar no painel do **Explorer** .

    ![Abrir pasta no Visual Studio Code](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Crie o arquivo de índice padrão na pasta *mysite* e nomeie-o como *index. html*.

    ![Criar o arquivo de índice padrão no Visual Studio Code](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Abra *index. html* no editor, Cole o seguinte texto no arquivo e salve-o:

    ```
    <h1>Hello World!</h1>
    ```

1. Crie o arquivo de erro padrão e nomeie-o como *404. html*.
1. Abra *404. html* no editor, Cole o seguinte texto no arquivo e salve-o:

    ```
    <h1>404</h1>
    ```

1. Clique com o botão direito do mouse na pasta *mysite* no painel do **Explorer** e selecione **implantar no site estático...** para implantar seu site. Você será solicitado a fazer logon no Azure para recuperar uma lista de assinaturas.

1. Selecione a assinatura que contém a conta de armazenamento para a qual você habilitou a hospedagem de site estático. Em seguida, selecione a conta de armazenamento quando solicitado.

Visual Studio Code agora carregará seus arquivos no ponto de extremidade da Web e mostrará a barra de status de êxito. Inicie o site para exibi-lo no Azure.

![Exibir a implantação de site estático no Azure](media/storage-blob-static-website-host/view-static-website-endpoint.png)

Você concluiu com êxito o tutorial e implantou um site estático no Azure.

## <a name="next-steps"></a>Passos seguintes

Na parte um deste tutorial, você aprendeu como configurar sua conta de armazenamento do Azure para hospedagem de site estático e como criar e implantar um site estático para um ponto de extremidade do Azure.

Agora avance para a parte dois, em que você configura um domínio personalizado com SSL para seu site estático com a CDN do Azure.

> [!div class="nextstepaction"]
> [Usar a CDN do Azure para habilitar um domínio personalizado com SSL para um site estático](storage-blob-static-website-custom-domain.md)
