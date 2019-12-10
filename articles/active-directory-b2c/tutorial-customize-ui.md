---
title: 'Tutorial: personalizar a interface do usuário'
titleSuffix: Azure AD B2C
description: Saiba como personalizar a interface do usuário de seus aplicativos no Azure Active Directory B2C usando o portal do Azure.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: eba9919b7a1d89e6aea8fb93ef8c4b3e92960368
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950872"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Tutorial: personalizar a interface de experiências do usuário no Azure Active Directory B2C

Para experiências de usuário mais comuns, como inscrição, entrada e edição de perfil, você pode usar [fluxos de usuário](active-directory-b2c-reference-policies.md) no Azure Active Directory B2C (Azure ad B2C). As informações neste tutorial ajudam a aprender a [Personalizar a interface do usuário](customize-ui-overview.md) dessas experiências usando seus próprios arquivos HTML e CSS.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar arquivos de personalização da interface do usuário
> * Atualizar o fluxo do usuário para usar os arquivos
> * Testar a interface do usuário personalizada

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Crie um fluxo de usuário](tutorial-create-user-flows.md) para permitir que os usuários se inscrevam e entrem no seu aplicativo.

## <a name="create-customization-files"></a>Criar arquivos de personalização

Você cria uma conta de armazenamento do Azure e um contêiner e, em seguida, coloca os arquivos HTML e CSS básicos no contêiner.

### <a name="create-a-storage-account"></a>Create a storage account

Embora seja possível armazenar seus arquivos de várias maneiras, para este tutorial, você os armazena no [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém sua assinatura do Azure. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório que contém sua assinatura. Esse diretório é diferente daquele que contém o locatário do Azure B2C.
3. Escolha todos os serviços no canto superior esquerdo da portal do Azure, procure e selecione **contas de armazenamento**.
4. Selecione **Adicionar**.
5. Em **grupo de recursos**, selecione **criar novo**, insira um nome para o novo grupo de recursos e clique em **OK**.
6. Introduza um nome para a conta de armazenamento. O nome que escolher tem de ser exclusivo no Azure, deve ter entre 3 e 24 carateres de comprimento e apenas pode conter números e letras minúsculas.
7. Selecione o local da conta de armazenamento ou aceite o local padrão.
8. Aceite todos os outros valores padrão, selecione **revisar + criar**e, em seguida, clique em **criar**.
9. Depois que a conta de armazenamento for criada, selecione **ir para o recurso**.

### <a name="create-a-container"></a>Criar um contentor

1. Na página Visão geral da conta de armazenamento, selecione **BLOBs**.
2. Selecione **contêiner**, insira um nome para o contêiner, escolha **BLOB (acesso de leitura anônimo somente para BLOBs)** e clique em **OK**.

### <a name="enable-cors"></a>Ativar o CORS

 Azure AD B2C código em um navegador usa uma abordagem moderna e padrão para carregar conteúdo personalizado de uma URL que você especifica em um fluxo de usuário. O CORS (compartilhamento de recursos entre origens) permite que recursos restritos em uma página da Web sejam solicitados de outros domínios.

1. No menu, selecione **CORS**.
2. Para **origens permitidas**, insira `https://your-tenant-name.b2clogin.com`. Substitua `your-tenant-name` com o nome do seu inquilino do Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Você precisa usar todas as letras minúsculas ao inserir o nome do locatário.
3. Para **métodos permitidos**, selecione `GET`,`PUT`e `OPTIONS`.
4. Para **cabeçalhos permitidos**, insira um asterisco (*).
5. Para **cabeçalhos expostos**, insira um asterisco (*).
6. Para **idade máxima**, insira 200.

    ![Página de configuração do CORS no armazenamento de BLOBs do Azure no portal do Azure](./media/tutorial-customize-ui/enable-cors.png)

5. Clique em **Guardar**.

### <a name="create-the-customization-files"></a>Criar os arquivos de personalização

Para personalizar a interface do usuário da experiência de inscrição, comece criando um arquivo HTML e CSS simples. Você pode configurar o HTML da maneira que desejar, mas ele deve ter um elemento **div** com um identificador de `api`. Por exemplo, `<div id="api"></div>`. Azure AD B2C injeta elementos no contêiner de `api` quando a página é exibida.

1. Em uma pasta local, crie o arquivo a seguir e certifique-se de alterar `your-storage-account` para o nome da conta de armazenamento e `your-container` para o nome do contêiner que você criou. Por exemplo, `https://store1.blob.core.windows.net/b2c/style.css`.

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

    A página pode ser criada de qualquer forma que você desejar, mas o elemento da **API** div é necessário para qualquer arquivo de personalização HTML que você criar.

3. Salve o arquivo como *Custom-UI. html*.
4. Crie o CSS simples a seguir que centraliza todos os elementos na página de inscrição ou de entrada, incluindo os elementos que Azure AD B2C injeta.

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

5. Salve o arquivo como *Style. css*.

### <a name="upload-the-customization-files"></a>Carregar os arquivos de personalização

Neste tutorial, você armazena os arquivos que criou na conta de armazenamento para que Azure AD B2C possa acessá-los.

1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure, procure e selecione **contas de armazenamento**.
2. Selecione a conta de armazenamento que você criou, selecione **BLOBs**e, em seguida, selecione o contêiner que você criou.
3. Selecione **carregar**, navegue até e selecione o arquivo *Custom-UI. html* e, em seguida, clique em **carregar**.

    ![Carregar página de blob no portal com o botão carregar e os arquivos realçados](./media/tutorial-customize-ui/upload-blob.png)

4. Copie a URL do arquivo que você carregou para usar posteriormente no tutorial.
5. Repita as etapas 3 e 4 para o arquivo *Style. css* .

## <a name="update-the-user-flow"></a>Atualizar o fluxo do usuário

1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
2. Selecione **fluxos de usuário (políticas)** e, em seguida, selecione o fluxo de usuário do *B2C_1_signupsignin1* .
3. Selecione **layouts de página**e, em seguida, na **página inscrição ou entrada unificada**, clique em **Sim** para **usar o conteúdo da página personalizada**.
4. Em **URI de página personalizada**, insira o URI para o arquivo *Custom-UI. html* que você registrou anteriormente.
5. Na parte superior da página, selecione **salvar**.

## <a name="test-the-user-flow"></a>Testar o fluxo do usuário

1. No locatário do Azure AD B2C, selecione **fluxos de usuário** e selecione o *B2C_1_signupsignin1* fluxo de usuário.
2. Na parte superior da página, clique em **executar fluxo de usuário**.
3. Clique no botão **executar fluxo de usuário** .

    ![Executar a página de fluxo de usuário para o fluxo de usuário de inscrição ou de entrada](./media/tutorial-customize-ui/run-user-flow.png)

    Você deverá ver uma página semelhante ao exemplo a seguir com os elementos centralizados com base no arquivo CSS que você criou:

    ![Navegador da Web mostrando a página de inscrição ou de entrada com elementos personalizados da interface do usuário](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar arquivos de personalização da interface do usuário
> * Atualizar o fluxo do usuário para usar os arquivos
> * Testar a interface do usuário personalizada

> [!div class="nextstepaction"]
> [Personalização de idioma no Azure Active Directory B2C](active-directory-b2c-reference-language-customization.md)
