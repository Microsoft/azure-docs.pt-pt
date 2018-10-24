---
title: Configurar contas de início de sessão no Azure Active Directory uma política incorporada no Azure Active Directory B2C | Documentos da Microsoft
description: Configure início de sessão no Azure Active Directory contas de uma política incorporada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: beb2d618d93f4c599f946194bd483326471065f4
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944809"
---
# <a name="set-up-sign-in-azure-active-directory-accounts-a-built-in-policy-in-azure-active-directory-b2c"></a>Configurar contas de início de sessão no Azure Active Directory uma política incorporada no Azure Active Directory B2C

>[!NOTE]
> Esta funcionalidade está em pré-visualização pública. Não utilize a funcionalidade em ambientes de produção.

Este artigo mostra como ativar o início de sessão para os utilizadores de uma organização específica do Azure Active Directory (Azure AD) com uma política incorporada no Azure Active Directory (Azure AD) B2C.

## <a name="create-an-azure-ad-app"></a>Criar uma aplicação do Azure AD

Para ativar o início de sessão para que os utilizadores específicos de um organização do Azure AD, terá de registar uma aplicação organizacionais no inquilino do Azure AD, que não é o mesmo que o inquilino do Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD ao clicar no filtro de diretório e subscrição no menu superior e escolher o diretório que contém o seu inquilino do Azure AD.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **registos das aplicações**.
4. Selecione **Novo registo de aplicação**.
5. Introduza um nome para a aplicação. Por exemplo, `Azure AD B2C App`.
6. Para o **tipo de aplicação**, selecione `Web app / API`.
7. Para o **URL de início de sessão**, introduza o seguinte URL em todas as letras minúsculas, onde `your-B2C-tenant-name` é substituído pelo nome do inquilino do Azure AD B2C. Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.b2clogin.com/oauth2/authresp`:

    ```
    https://your-tenant-name.b2clogin.com/your-B2C-tenant-name.b2clogin.com/oauth2/authresp
    ```

    Todos os URLs devem agora estar a utilizar [b2clogin.com](b2clogin.md).

8. Clique em **Criar**. Copiar o **ID da aplicação** a ser utilizado mais tarde.
9. Selecione a aplicação e, em seguida, selecione **definições**.
10. Selecione **chaves**, introduza a descrição da chave, selecione uma duração e, em seguida, clique em **guardar**. Copie o valor da chave que é apresentada a ser utilizado mais tarde.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurar o Azure AD como fornecedor de identidade

1. Certifique-se de que está a utilizar o diretório que contém o inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino do Azure AD B2C.
2. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
3. Selecione **fornecedores de identidade**e, em seguida, selecione **Add**.
4. Introduza um **nome**. Por exemplo, introduza "Contoso do Azure AD".
5. Selecione **tipo de fornecedor de identidade**, selecione **abrir ID Connect (pré-visualização)** e, em seguida, clique em **OK**.
6. Clique em **configurar este fornecedor de identidade**
7. Para **url de metadados**, introduza o URL seguinte substituindo `your-AD-tenant-domain` com o nome de domínio de inquilino do Azure AD. Por exemplo `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. Para **id de cliente**, introduza o ID da aplicação que registou anteriormente e para **segredo do cliente**, introduza o valor da chave que registou anteriormente.
9. Opcionalmente, introduza um valor para **Domain_hint**. Por exemplo, `ContosoAD`. Este é o valor a utilizar quando nos Referimos a este fornecedor de identidade usando *domain_hint* no pedido. 
10. Clique em **OK**.
11. Selecione **mapear declarações do fornecedor de identidade** e defina as seguintes declarações:
    
    - Para **ID de utilizador**, introduza `oid`.
    - Para **nome a apresentar**, introduza `name`.
    - Para **nome fornecido**, introduza `given_name`.
    - Para **Apelido**, introduza `family_name`.
    - Para **E-Mail**, introduza `unique_name`.

12. Clique em **OK**e, em seguida, clique em **criar** para guardar a configuração.
