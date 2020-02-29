---
title: 'Tutorial: Personalize a interface do utilizador'
titleSuffix: Azure AD B2C
description: Saiba como personalizar a interface de utilizador (UI) das suas aplicações no Azure Ative Directory B2C utilizando o portal Azure.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b2b2bc8dd4e60348553228b8b418df252a8c426a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186255"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Tutorial: Personalize a interface das experiências do utilizador no Diretório Ativo Azure B2C

Para experiências de utilizador mais comuns, tais como inscrição, inscrição e edição de perfis, pode utilizar [fluxos](user-flow-overview.md) de utilizadores em Azure Ative Directory B2C (Azure AD B2C). A informação neste tutorial ajuda-o a aprender a personalizar a interface de [utilizador (UI)](customize-ui-overview.md) destas experiências utilizando os seus próprios ficheiros HTML e CSS.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar ficheiros de personalização ui
> * Atualize o fluxo do utilizador para utilizar os ficheiros
> * Teste o UI personalizado

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Crie um fluxo de utilizador](tutorial-create-user-flows.md) para permitir que os utilizadores se inscrevam e inscrevam-se na sua aplicação.

## <a name="create-customization-files"></a>Criar ficheiros de personalização

Cria uma conta de armazenamento e um recipiente Azure e, em seguida, coloca ficheiros BÁSICOS HTML e CSS no recipiente.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Embora possa armazenar os seus ficheiros de muitas formas, para este tutorial, guarde-os no [armazenamento Azure Blob.](../storage/blobs/storage-blobs-introduction.md)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém a sua subscrição Azure. Selecione o filtro de **subscrição Diretório +** no menu superior e escolha o diretório que contenha a sua subscrição. Este diretório é diferente daquele que contém o seu inquilino Azure B2C.
3. Escolha todos os serviços no canto superior esquerdo do portal Azure, procure e selecione contas de **Armazenamento.**
4. Selecione **Adicionar**.
5. No **grupo Recursos**, selecione Criar **novo,** introduza um nome para o novo grupo de recursos e, em seguida, clique OK .
6. Introduza um nome para a conta de armazenamento. O nome que escolher tem de ser exclusivo no Azure, deve ter entre 3 e 24 carateres de comprimento e apenas pode conter números e letras minúsculas.
7. Selecione a localização da conta de armazenamento ou aceite a localização predefinida.
8. Aceite todos os outros valores predefinidos, selecione **Rever + criar,** e depois clique em **Criar**.
9. Depois da criação da conta de armazenamento, selecione **Ir para o recurso**.

### <a name="create-a-container"></a>Criar um contentor

1. Na página geral da conta de armazenamento, selecione **Blobs**.
2. Selecione **Recipiente,** introduza um nome para o recipiente, escolha **Blob (acesso de leitura anónimo apenas para bolhas)** e, em seguida, clique em **OK**.

### <a name="enable-cors"></a>Ativar o CORS

 O código Azure AD B2C num browser utiliza uma abordagem moderna e padrão para carregar conteúdo personalizado a partir de um URL que especifica num fluxo de utilizador. A partilha de recursos de origem cruzada (CORS) permite que recursos restritos numa página web sejam solicitados a partir de outros domínios.

1. No menu, selecione **CORS**.
2. Para **origens permitidas,** introduza `https://your-tenant-name.b2clogin.com`. Substitua `your-tenant-name` pelo nome do seu inquilino Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Você precisa usar todas as letras minúsculas ao inserir o nome do locatário.
3. Para **métodos permitidos**, selecione `GET`,`PUT`e `OPTIONS`.
4. Para **cabeçalhos permitidos,** introduza um asterisco (*).
5. Para **cabeçalhos expostos,** introduza um asterisco (*).
6. Para **a idade máxima,** insira 200.

    ![Página de configuração CORS no armazenamento Azure Blob no portal Azure](./media/tutorial-customize-ui/enable-cors.png)

5. Clique em **Guardar**.

### <a name="create-the-customization-files"></a>Criar os ficheiros de personalização

Para personalizar o UI da experiência de inscrição, começa por criar um simples ficheiro HTML e CSS. Pode configurar o seu HTML da forma que quiser, mas deve ter um elemento de **mergulho** com um identificador de `api`. Por exemplo, `<div id="api"></div>`. O Azure AD B2C injeta elementos no recipiente `api` quando a página é apresentada.

1. Numa pasta local, crie o seguinte ficheiro e certifique-se de que muda `your-storage-account` para o nome da conta de armazenamento e `your-container` o nome do recipiente que criou. Por exemplo, `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    A página pode ser projetada da forma que quiser, mas o elemento **api** div é necessário para qualquer ficheiro de personalização HTML que você cria.

3. Guarde o ficheiro como *custom-ui.html*.
4. Crie o seguinte CSS simples que centra todos os elementos na página de inscrição ou inscrição, incluindo os elementos que o Azure AD B2C injeta.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Guarde o ficheiro como *style.css*.

### <a name="upload-the-customization-files"></a>Faça upload dos ficheiros de personalização

Neste tutorial, armazena os ficheiros que criou na conta de armazenamento para que o Azure AD B2C possa aceder aos mesmos.

1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure, procure e selecione contas de **Armazenamento.**
2. Selecione a conta de armazenamento que criou, selecione **Blobs**e, em seguida, selecione o recipiente que criou.
3. Selecione **Upload,** navegue para e selecione o ficheiro *custom-ui.html* e, em seguida, clique em **Carregar**.

    ![Carregue a página blob no portal com botão upload e ficheiros destacados](./media/tutorial-customize-ui/upload-blob.png)

4. Copie o URL para o ficheiro que carregou para utilizar mais tarde no tutorial.
5. Repita os passos 3 e 4 para o ficheiro *style.css.*

## <a name="update-the-user-flow"></a>Atualizar o fluxo do utilizador

1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
2. Selecione **fluxos de utilizador (políticas)** e, em seguida, selecione o *fluxo de utilizador B2C_1_signupsignin1.*
3. Selecione **os layouts da página**, e, em seguida, sob a página unificada de **inscrição ou de inscrição,** clique **em Sim** para Usar conteúdo de **página personalizada**.
4. Na **página personalizada URI,** introduza o URI para o ficheiro *custom-ui.html* que gravou anteriormente.
5. No topo da página, selecione **Guardar**.

## <a name="test-the-user-flow"></a>Testar o fluxo do utilizador

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador** e selecione o fluxo *de utilizador B2C_1_signupsignin1.*
2. No topo da página, clique em **Executar o fluxo do utilizador**.
3. Clique no botão de fluxo do **utilizador Executar.**

    ![Executar página de fluxo do utilizador para o fluxo de utilizador de inscrição ou de entrada](./media/tutorial-customize-ui/run-user-flow.png)

    Deve ver uma página semelhante ao seguinte exemplo com os elementos centrados com base no ficheiro CSS que criou:

    ![Navegador web mostrando inscrever-se ou iniciar sessão na página com elementos UI personalizados](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a:

> [!div class="checklist"]
> * Criar ficheiros de personalização ui
> * Atualize o fluxo do utilizador para utilizar os ficheiros
> * Teste o UI personalizado

> [!div class="nextstepaction"]
> [Personalização linguística no Diretório Ativo Azure B2C](user-flow-language-customization.md)
