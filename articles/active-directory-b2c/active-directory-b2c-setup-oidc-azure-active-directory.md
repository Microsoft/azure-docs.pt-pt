---
title: Configurar o início de sessão para uma organização do Azure Active Directory - Azure Active Directory B2C
description: Configure o início de sessão para uma organização específica do Azure Active Directory no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b867a7f9ffeab3d243c8c094830aa0984cffd04a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654206"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurar o início de sessão para uma organização específica do Azure Active Directory no Azure Active Directory B2C

>[!NOTE]
> Esta funcionalidade está em pré-visualização pública. Não utilize a funcionalidade em ambientes de produção.

Para utilizar um Azure Active Directory (Azure AD) como um [fornecedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure AD B2C, terá de criar uma aplicação que a representa. Este artigo mostra-lhe como ativar o início de sessão para os utilizadores de um específico do Azure AD o fluxo de organização através de um utilizador no Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Criar uma aplicação do Azure AD

Para ativar o início de sessão para que os utilizadores específicos de um organização do Azure AD, terá de registar uma aplicação organizacionais no inquilino do Azure AD, que não é o mesmo que o inquilino do Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD. Selecione o **filtro de diretório e subscrição** no menu superior e selecione o diretório que contém o seu inquilino do Azure AD. Não é o mesmo inquilino como inquilino do Azure AD B2C.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **registos das aplicações**.
4. Selecione **novo registo**.
5. Introduza um nome para a aplicação. Por exemplo, `Azure AD B2C App`.
6. Aceite a seleção de **contas neste diretório organizacional apenas** para esta aplicação.
7. Para o **URI de redirecionamento**, aceite o valor **Web**e introduza o seguinte URL em todas as letras minúsculas, onde `your-B2C-tenant-name` é substituído pelo nome do inquilino do Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Todos os URLs devem agora estar a utilizar [b2clogin.com](b2clogin.md).

8. Clique em **registar**. Copiar o **ID da aplicação (cliente)** a ser utilizado mais tarde.
9. Selecione **certificados e segredos** no menu do aplicativo e, em seguida, selecione **novo segredo do cliente**.
10. Introduza um nome para o segredo do cliente. Por exemplo, `Azure AD B2C App Secret`.
11. Selecione o período de expiração. Para esta aplicação, aceite a seleção de **em 1 ano**.
12. Selecione **adicionar** e copie o valor do novo segredo do cliente que é apresentado a ser utilizado mais tarde.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurar o Azure AD como fornecedor de identidade

1. Certifique-se de que está a utilizar o diretório que contém o inquilino do Azure AD B2C. Selecione o **filtro de diretório e subscrição** no menu superior e selecione o diretório que contém o seu inquilino do Azure AD B2C.
2. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
3. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
4. Introduza um **nome**. Por exemplo, introduza `Contoso Azure AD`.
5. Selecione **tipo de fornecedor de identidade**, selecione **abrir ID Connect (pré-visualização)** e, em seguida, clique em **OK**.
6. Selecione **configurar este fornecedor de identidade**
7. Para **url de metadados**, introduza o URL seguinte substituindo `your-AD-tenant-domain` com o nome de domínio de inquilino do Azure AD. Por exemplo `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. Para **ID de cliente**, introduza o ID da aplicação que registou anteriormente e para **segredo do cliente**, introduza o segredo do cliente que registou anteriormente.
9. Opcionalmente, introduza um valor para **Domain_hint**. Por exemplo, `ContosoAD`. Este é o valor a utilizar quando nos Referimos a este fornecedor de identidade usando *domain_hint* no pedido.
10. Clique em **OK**.
11. Selecione **mapear declarações do fornecedor de identidade** e defina as seguintes declarações:

    - Para **ID de utilizador**, introduza `oid`.
    - Para **nome a apresentar**, introduza `name`.
    - Para **nome fornecido**, introduza `given_name`.
    - Para **Apelido**, introduza `family_name`.
    - Para **E-Mail**, introduza `unique_name`.

12. Clique em **OK**e, em seguida, clique em **criar** para guardar a configuração.
