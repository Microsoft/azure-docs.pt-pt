---
title: Tutorial - adicionar fornecedores de identidade às suas aplicações - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como adicionar fornecedores de identidade às suas aplicações no Azure Active Directory B2C no portal do Azure.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: ef264622be1e66c98f8c7f17cf3e04b3239e8553
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338280"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Tutorial: Adicionar fornecedores de identidade às suas aplicações no Azure Active Directory B2C

Nas suas aplicações, pode querer permitir que os utilizadores iniciem sessão com diferentes provedores de identidade. Uma *fornecedor de identidade* cria, mantém e gerencia as informações de identidade, fornecendo serviços de autenticação para aplicações. Pode adicionar fornecedores de identidade que são suportadas pelo Azure Active Directory (Azure AD) B2C para sua [fluxos de utilizador](active-directory-b2c-reference-policies.md) no portal do Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar aplicativos de fornecedor de identidade
> * Adicionar os fornecedores de identidade ao seu inquilino
> * Adicionar os fornecedores de identidade para o fluxo de utilizador

Geralmente usa apenas um fornecedor de identidade em seus aplicativos, mas tem a opção para adicionar mais. Este tutorial mostra-lhe como adicionar um fornecedor de identidade do Azure AD e um fornecedor de identidade do Facebook para seu aplicativo. Adicionar ambos esses provedores de identidade para a sua aplicação é opcional. Também pode adicionar outros fornecedores de identidade, tal como [Amazon](active-directory-b2c-setup-amzn-app.md), [Github](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md), ou [Twitter](active-directory-b2c-setup-twitter-app.md). 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Criar um fluxo de utilizador](tutorial-create-user-flows.md) para permitir aos utilizadores inscrever-se e iniciar sessão na sua aplicação. 

## <a name="create-applications"></a>Criar aplicações

Aplicativos de provedores de identidade fornecem o identificador e a chave para permitir a comunicação com o seu inquilino do Azure AD B2C. Nesta secção do tutorial, vai criar uma aplicação do Azure AD e um aplicativo para Facebook partir da qual obter identificadores e chaves para adicionar os fornecedores de identidade para o seu inquilino. Se estiver a adicionar apenas um dos fornecedores de identidade, basta criar a aplicação para esse provedor.

### <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação do Azure Active Directory

Para ativar o início de sessão para utilizadores do Azure AD, terá de registar uma aplicação no inquilino do Azure AD. O inquilino do Azure AD não é o mesmo que o inquilino do Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD ao clicar o **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino do Azure AD.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **registos das aplicações**.
4. Selecione **Novo registo de aplicação**.
5. Introduza um nome para a aplicação. Por exemplo, `Azure AD B2C App`.
6. Para o **tipo de aplicação**, selecione `Web app / API`.
7. Para o **URL de início de sessão**, introduza o seguinte URL em todas as letras minúsculas, onde `your-B2C-tenant-name` é substituído pelo nome do inquilino do Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```
    
    Por exemplo, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
    
    Todos os URLs devem agora estar a utilizar [b2clogin.com](b2clogin.md).

8. Clique em **Criar**. Copiar o **ID da aplicação** a ser utilizado mais tarde.
9. Selecione a aplicação e, em seguida, selecione **definições**.
10. Selecione **chaves**, introduza a descrição da chave, selecione uma duração e, em seguida, clique em **guardar**. Copie o valor da chave, para que pode utilizá-lo mais tarde neste tutorial.

### <a name="create-a-facebook-application"></a>Criar um aplicativo do Facebook

Para utilizar uma conta do Facebook como provedor de identidade no Azure AD B2C, terá de criar uma aplicação ao Facebook. Se ainda não tiver uma conta do Facebook, pode obtê-la em [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Inicie sessão no [Facebook para os desenvolvedores](https://developers.facebook.com/) com as suas credenciais de conta do Facebook.
2. Se ainda não o fez, terá de se registrar como um desenvolvedor do Facebook. Para registar, selecione **registar** no canto canto superior direito da página, aceite as políticas do Facebook e conclua os passos de registo.
3. Selecione **as minhas aplicações** e, em seguida, clique em **adicionar uma nova aplicação**. 
4. Introduza um **nome a apresentar** e válido **E-Mail de contacto**.
5. Clique em **criar ID de aplicação**. Poderá ter de aceitar as políticas de plataforma do Facebook e concluir uma verificação de segurança online.
6. Selecione **configurações** > **básica**.
7. Escolher uma **categoria**por exemplo, `Business and Pages`. Este valor é necessária pelo Facebook, mas não é utilizado para o Azure AD B2C.
8. Na parte inferior da página, selecione **adicionar plataforma**e, em seguida, selecione **site**.
9. Na **URL do Site**, introduza `https://your-tenant-name.b2clogin.com/` substituindo `your-tenant-name` com o nome do seu inquilino. Introduza um URL para o **URL de política de privacidade**por exemplo, `http://www.contoso.com`. O URL de política é uma página que manter para fornecer informações de privacidade para a sua aplicação.
10. Selecione **guardar alterações**.
11. Na parte superior da página, copie o valor da **ID da aplicação**. 
12. Clique em **mostrar** e copie o valor de **segredo da aplicação**. Utilizar ambos para configurar o Facebook como um fornecedor de identidade no seu inquilino. **Segredo da aplicação** é uma credencial de segurança importantes.
13. Selecione **produtos**e, em seguida, selecione **configurar** sob **início de sessão do Facebook**.
14. Selecione **configurações** sob **início de sessão do Facebook**.
15. Na **URIs de redirecionamento OAuth válido**, introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua `your-tenant-name` com o nome do seu inquilino. Clique em **guardar alterações** na parte inferior da página.
16. Para disponibilizar a aplicação do Facebook para o Azure AD B2C, clique no **Status** Seletor na parte superior direita da página e defina-o como **no**. Clique em **Confirmar**. Neste momento, o estado deve ser alterado de **desenvolvimento** ao **Live**.

## <a name="add-the-identity-providers"></a>Adicionar os fornecedores de identidade

Depois de criar a aplicação para o fornecedor de identidade que pretende adicionar, adicione o fornecedor de identidade ao seu inquilino.

### <a name="add-the-azure-active-directory-identity-provider"></a>Adicionar o fornecedor de identidade do Azure Active Directory

1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino do Azure AD B2C.
2. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
3. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
4. Introduza um **nome**. Por exemplo, introduza *Contoso do Azure AD*.
5. Selecione **tipo de fornecedor de identidade**, selecione **abrir ID Connect (pré-visualização)** e, em seguida, clique em **OK**.
6. Clique em **configurar este fornecedor de identidade**
7. Para **url de metadados**, introduza o URL seguinte substituindo `your-AD-tenant-domain` com o nome de domínio de inquilino do Azure AD.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

8. Para **ID de cliente**, introduza o ID da aplicação que registou anteriormente e para **segredo do cliente**, introduza o valor da chave que registou anteriormente.
9. Opcionalmente, introduza um valor para **Domain_hint**. Por exemplo, `ContosoAD`. 
10. Clique em **OK**.
11. Selecione **mapear declarações do fornecedor de identidade** e defina as seguintes declarações:
    
    - Para **ID de utilizador**, introduza `oid`.
    - Para **nome a apresentar**, introduza `name`.
    - Para **nome fornecido**, introduza `given_name`.
    - Para **Apelido**, introduza `family_name`.
    - Para **E-Mail**, introduza `unique_name`.

12. Clique em **OK**e, em seguida, clique em **criar** para guardar a configuração.

### <a name="add-the-facebook-identity-provider"></a>Adicionar o fornecedor de identidade do Facebook

1. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
2. Introduza um **nome**. Por exemplo, introduza *Facebook*.
3. Selecione **tipo de fornecedor de identidade**, selecione **Facebook**e clique em **OK**.
4. Selecione **configurar este fornecedor de identidade** e introduza o ID da aplicação que registou anteriormente como a **ID de cliente**. Introduza o segredo de aplicação que registou como o **segredo do cliente**.
5. Clique em **OK** e, em seguida, clique em **criar** para guardar a configuração do Facebook.

## <a name="update-the-user-flow"></a>Atualizar o fluxo de utilizador

Tutorial que concluídas como parte dos pré-requisitos, criou um fluxo de utilizador para inscrição e início de sessão com o nome *B2C_1_signupsignin1*. Nesta secção, vai adicionar os fornecedores de identidade para o *B2C_1_signupsignin1* fluxo de utilizador.

1. Selecione **fluxos de utilizador (diretivas)** e, em seguida, selecione a *B2C_1_signupsignin1* fluxo de utilizador.
2. Selecione **fornecedores de identidade**, selecione a **Facebook** e **Contoso do Azure AD** fornecedores de identidade que adicionou.
3. Selecione **Guardar**.

### <a name="test-the-user-flow"></a>Testar o fluxo de utilizador

1. Na página de descrição geral do fluxo de utilizador que criou, selecione **executar o fluxo de utilizador**.
2. Para **aplicativo**, selecione a aplicação web com o nome *webapp1* que registou anteriormente. O **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **executar o fluxo de utilizador**e, em seguida, inicie sessão com um fornecedor de identidade que foi adicionado anteriormente.
4. Repita os passos 1 a 3 para os outros fornecedores de identidade que adicionou.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como:

> [!div class="checklist"]
> * Criar aplicativos de fornecedor de identidade
> * Adicionar os fornecedores de identidade ao seu inquilino
> * Adicionar os fornecedores de identidade para o fluxo de utilizador

> [!div class="nextstepaction"]
> [Personalizar a interface do usuário das suas aplicações no Azure Active Directory B2C](tutorial-customize-ui.md)