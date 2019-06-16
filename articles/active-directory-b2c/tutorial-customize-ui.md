---
title: Tutorial - personalizar a interface de experiências de utilizador - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como personalizar a interface do usuário das suas aplicações no Azure Active Directory B2C no portal do Azure.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4c0fdbee2c5108dd3203217cb721576703b3faca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512085"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Tutorial: Personalizar a interface de experiências de utilizador no Azure Active Directory B2C

Para experiências de usuário mais comuns, tais como inscrição, início de sessão e edição de perfil, pode usar [fluxos de utilizador](active-directory-b2c-reference-policies.md) no Azure Active Directory (Azure AD) B2C. As informações neste tutorial ajudam-o a saber como [personalizar a interface de utilizador (IU)](customize-ui-overview.md) destas experiências com seus próprios arquivos HTML e CSS.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar arquivos de personalização da interface do Usuário
> * O fluxo de utilizador para utilizar os ficheiros de atualização
> * Testar a interface do Usuário personalizada

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Criar um fluxo de utilizador](tutorial-create-user-flows.md) para permitir aos utilizadores inscrever-se e iniciar sessão na sua aplicação.

## <a name="create-customization-files"></a>Criar arquivos de personalização

Criar uma conta de armazenamento do Azure e o contentor e, em seguida, colocar ficheiros HTML e CSS básicos no contentor.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Embora armazene seus arquivos de várias maneiras, para este tutorial, armazene-os na [armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém a sua subscrição do Azure. Selecione o **filtro de diretório e subscrição** no menu superior e selecione o diretório que contém a sua subscrição. Este diretório é diferente daquela que contém o inquilino B2C do Azure.
3. Selecione todos os serviços no canto superior esquerdo do portal do Azure, procure e selecione **contas de armazenamento**. 
4. Selecione **Adicionar**.
5. Sob **grupo de recursos**, selecione **criar nova**, introduza um nome para o novo grupo de recursos e, em seguida, clique em **OK**.
6. Introduza um nome para a conta de armazenamento. O nome que escolher tem de ser exclusivo no Azure, deve ter entre 3 e 24 carateres de comprimento e apenas pode conter números e letras minúsculas.
7. Selecione a localização da conta de armazenamento ou aceite a localização predefinida. 
8. Aceite todos os outros valores predefinidos, selecione **rever + criar**e, em seguida, clique em **criar**.
9. Depois de criar a conta de armazenamento, selecione **Ir para recurso**.

### <a name="create-a-container"></a>Criar um contentor

1. Na página de descrição geral da conta de armazenamento, selecione **Blobs**.
2. Selecione **contentor**, introduza um nome para o contentor, escolha **Blob (acesso de leitura anónimo apenas para blobs)** e, em seguida, clique em **OK**.

### <a name="enable-cors"></a>Ativar o CORS

 Azure AD B2C código num navegador usa uma abordagem moderna e padrão para carregar conteúdo personalizado a partir de uma URL que especificou num fluxo de utilizador. Recursos de várias origens (CORS) de compartilhamento permite recursos restritos numa página da web para ser pedidos a partir de outros domínios.

1. No menu, selecione **CORS**.
2. Para **permitido origens**, introduza `https://your-tenant-name.b2clogin.com`. Substitua `your-tenant-name` com o nome do seu inquilino do Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com`. Tem de utilizar todas as letras minúsculas, ao introduzir o nome do seu inquilino.
3. Para **métodos permitidos**, selecione ambos `GET` e `OPTIONS`.
4. Para **cabeçalhos permitidos**, introduza um asterisco (*).
5. Para **cabeçalhos expostos**, introduza um asterisco (*).
6. Para **idade máxima**, introduza a 200.

    ![Ativar o CORS](./media/tutorial-customize-ui/enable-cors.png)

5. Clique em **Guardar**.

### <a name="create-the-customization-files"></a>Criar os arquivos de personalização

Para personalizar a interface do Usuário da experiência de inscrição, começa criando um arquivo HTML e CSS simple. Pode configurar o HTML de forma que quiser, mas tem de ter uma **div** elemento com um identificador de `api`. Por exemplo, `<div id="api"></div>`. O Azure AD B2C injeta elementos para o `api` contentor quando a página é apresentada.

1. Numa pasta local, crie o ficheiro seguinte e certifique-se de que altere `your-storage-account` para o nome da conta de armazenamento e `your-container` para o nome do contentor que criou. Por exemplo, `https://store1.blob.core.windows.net/b2c/style.css`.

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

    A página pode ser desenvolvida qualquer forma que quiser, mas a **api** elemento div é necessário para qualquer arquivo de personalização do HTML que criar. 

3. Guarde o ficheiro como *personalizado ui.html*.
4. Crie o seguinte CSS simple que se centra-se todos os elementos na página de inscrição ou início de sessão, incluindo os elementos que injeta do Azure AD B2C.

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

5. Guarde o ficheiro como *style. CSS*.

### <a name="upload-the-customization-files"></a>Carregue os ficheiros de personalização

Neste tutorial, vai armazenar os ficheiros que criou na conta de armazenamento para que o Azure AD B2C podem aceder aos mesmos.

1. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **contas de armazenamento**.
2. Selecione a conta de armazenamento que criou, selecione **Blobs**e, em seguida, selecione o contentor que criou.
3. Selecione **carregue**, navegue até e selecione o *ui.html personalizada* ficheiro e, em seguida, clique em **carregar**.

    ![Carregar arquivos de personalização](./media/tutorial-customize-ui/upload-blob.png)

4. Copie o URL para o ficheiro que carregou para utilizar mais tarde no tutorial.
5. Repita os passos 3 e 4 para o *style. CSS* ficheiro.

## <a name="update-the-user-flow"></a>Atualizar o fluxo de utilizador

1. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
2. Selecione **fluxos de utilizador (diretivas)** e, em seguida, selecione a *B2C_1_signupsignin1* fluxo de utilizador.
3. Selecione **layouts de página**e, em **unificado a página de inscrição ou início de sessão**, clique em **Sim** para **utilizar conteúdo da página personalizada**.
4. Na **URI da página personalizada**, introduza o URI para o *personalizado ui.html* ficheiro que registou anteriormente.
5. Na parte superior da página, selecione **guardar**.

## <a name="test-the-user-flow"></a>Testar o fluxo de utilizador

1. No seu inquilino do Azure AD B2C, selecione **fluxos de utilizador** e selecione o *B2C_1_signupsignin1* fluxo de utilizador.
2. Na parte superior da página, clique em **executar o fluxo de utilizador**.
3. Clique nas **executar o fluxo de utilizador** botão.

    ![Executar o fluxo de utilizador de inscrição ou início de sessão](./media/tutorial-customize-ui/run-user-flow.png)

    Deverá ver uma página semelhante ao seguinte exemplo com os elementos centra-se com base em arquivo CSS que criou:

    ![Resultados de fluxo de utilizador](./media/tutorial-customize-ui/run-now.png) 

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como:

> [!div class="checklist"]
> * Criar arquivos de personalização da interface do Usuário
> * O fluxo de utilizador para utilizar os ficheiros de atualização
> * Testar a interface do Usuário personalizada

> [!div class="nextstepaction"]
> [Personalização de idioma no Azure Active Directory B2C](active-directory-b2c-reference-language-customization.md)
