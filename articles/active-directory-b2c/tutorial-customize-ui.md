---
title: 'Tutorial: Personalizar a interface do utilizador'
titleSuffix: Azure AD B2C
description: Siga este tutorial para saber como personalizar a interface de utilizador (UI) das suas aplicações no Azure Ative Directory B2C utilizando o portal Azure.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9ca6d91be95bfb1a47f85b20f3775a57518ffcf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87922137"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Tutorial: Personalize a interface das experiências do utilizador no Azure Ative Directory B2C

Para experiências mais comuns do utilizador, tais como inscrição, inscrição e edição de perfis, pode utilizar [fluxos de utilizador](user-flow-overview.md) em Azure Ative Directory B2C (Azure AD B2C). As informações neste tutorial ajudam-no a aprender a [personalizar a interface do utilizador (UI)](customize-ui-overview.md) destas experiências utilizando os seus próprios ficheiros HTML e CSS.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar ficheiros de personalização de UI
> * Atualizar o fluxo do utilizador para utilizar os ficheiros
> * Teste a UI personalizada

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Crie um fluxo de utilizador](tutorial-create-user-flows.md) para permitir que os utilizadores se inscrevam e se inscrevam na sua aplicação.

## <a name="create-customization-files"></a>Criar ficheiros de personalização

Cria uma conta de armazenamento Azure e um recipiente e, em seguida, coloca ficheiros básicos html e CSS no recipiente.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Embora possa armazenar os seus ficheiros de várias formas, para este tutorial, guarde-os no [armazenamento Azure Blob.](../storage/blobs/storage-blobs-introduction.md)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém a sua assinatura Azure. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém a sua subscrição. Este diretório é diferente daquele que contém o seu inquilino Azure B2C.
3. Escolha todos os serviços no canto superior esquerdo do portal Azure, procure e selecione **contas de Armazenamento.**
4. Selecione **Adicionar**.
5. No **grupo de Recursos**, selecione Criar **novo,** insira um nome para o novo grupo de recursos e, em seguida, clique em **OK**.
6. Introduza um nome para a conta de armazenamento. O nome que escolher tem de ser exclusivo no Azure, deve ter entre 3 e 24 carateres de comprimento e apenas pode conter números e letras minúsculas.
7. Selecione a localização da conta de armazenamento ou aceite a localização predefinitiva.
8. Aceite todos os outros valores predefinidos, **selecione Rever + criar**e, em seguida, clique em **Criar**.
9. Depois de criar a conta de armazenamento, selecione **Ir para o recurso**.

### <a name="create-a-container"></a>Criar um contentor

1. Na página geral da conta de armazenamento, selecione **Blobs**.
2. Selecione **O Recipiente**, introduza um nome para o recipiente, escolha **Blob (acesso de leitura anónima apenas para bolhas)** e, em seguida, clique em **OK**.

### <a name="enable-cors"></a>Ativar o CORS

 O código AD B2C do Azure em um navegador usa uma abordagem moderna e padrão para carregar conteúdo personalizado a partir de um URL que você especifica em um fluxo de utilizador. A partilha de recursos de origem cruzada (CORS) permite que os recursos restritos numa página web sejam solicitados a partir de outros domínios.

1. No menu, selecione **CORS**.
2. Para **origens permitidas,** insira `https://your-tenant-name.b2clogin.com` . `your-tenant-name`Substitua-o pelo nome do seu inquilino Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Você precisa usar todas as letras minúsculas ao inserir o nome do seu inquilino.
3. Para **Métodos Permitidos**, selecione `GET` , e `PUT` `OPTIONS` .
4. Para **cabeçalhos permitidos,** introduza um asterisco (*).
5. Para **cabeçalhos expostos,** introduza um asterisco (*).
6. Para **a idade máxima,** insira 200.

    ![Página de configuração CORS no armazenamento de Azure Blob no portal Azure](./media/tutorial-customize-ui/enable-cors.png)

5. Clique em **Guardar**.

### <a name="create-the-customization-files"></a>Criar os ficheiros de personalização

Para personalizar a UI da experiência de inscrição, começa por criar um ficheiro HTML e CSS simples. Pode configurar o seu HTML da forma que quiser, mas deve ter um elemento **de div** com um identificador de `api` . Por exemplo, `<div id="api"></div>`. Azure AD B2C injeta elementos no `api` recipiente quando a página é apresentada.

1. Numa pasta local, crie o seguinte ficheiro e certifique-se de que muda `your-storage-account` para o nome da conta de armazenamento e para o nome do recipiente que `your-container` criou. Por exemplo, `https://store1.blob.core.windows.net/b2c/style.css`.

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

    A página pode ser desenhada da forma que quiser, mas o elemento de div **api** é necessário para qualquer ficheiro de personalização HTML que criar.

3. Guarde o ficheiro como *custom-ui.html*.
4. Crie o CSS simples que centra todos os elementos na página de inscrição ou de inscrição, incluindo os elementos que o Azure AD B2C injeta.

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

### <a name="upload-the-customization-files"></a>Faça o upload dos ficheiros de personalização

Neste tutorial, armazena os ficheiros que criou na conta de armazenamento para que o Azure AD B2C possa aceder aos mesmos.

1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure, procure e selecione **contas de Armazenamento.**
2. Selecione a conta de armazenamento que criou, selecione **Blobs**e, em seguida, selecione o recipiente que criou.
3. Selecione **Upload**, navegue para e selecione o ficheiro *custom-ui.html* e, em seguida, clique em **Upload**.

    ![Enviar página blob no portal com botão de upload e ficheiros em destaque](./media/tutorial-customize-ui/upload-blob.png)

4. Copie o URL para o ficheiro que carregou para utilizar mais tarde no tutorial.
5. Repita os passos 3 e 4 para o ficheiro *style.css.*

## <a name="update-the-user-flow"></a>Atualizar o fluxo do utilizador

1. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
2. Selecione **os fluxos do Utilizador**e, em seguida, selecione o fluxo do utilizador *B2C_1_signupsignin1.*
3. Selecione **os layouts de página**e, em seguida, na página de **inscrição ou de inscrição unificada**, clique em **Sim** para Utilizar o conteúdo da **página personalizada**.
4. Na **página personalizada URI**, insira o URI para o * ficheirocustom-ui.html* que gravou anteriormente.
5. No topo da página, **selecione Guardar**.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador** e selecione o fluxo de utilizador *B2C_1_signupsignin1.*
2. No topo da página, clique no fluxo do **utilizador executar**.
3. Clique no botão **de fluxo do utilizador Executar.**

    ![Executar a página de fluxo do utilizador para o fluxo de utilizador de inscrição ou de entrada](./media/tutorial-customize-ui/run-user-flow.png)

    Deve ver uma página semelhante ao exemplo a seguir com os elementos centrados no ficheiro CSS que criou:

    ![Navegador web mostrando inscrição ou sinal na página com elementos de UI personalizados](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a:

> [!div class="checklist"]
> * Criar ficheiros de personalização de UI
> * Atualizar o fluxo do utilizador para utilizar os ficheiros
> * Teste a UI personalizada

> [!div class="nextstepaction"]
> [Personalize a UI em Azure Ative Directory B2C](customize-ui-overview.md)
