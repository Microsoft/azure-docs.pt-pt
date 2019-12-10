---
title: Configurar a inscrição e a entrada com uma conta do LinkedIn
titleSuffix: Azure AD B2C
description: Forneça inscrição e entrada para clientes com contas do LinkedIn em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fc6537b56b26d6becfe0851a09d3bfe0f13050ab
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947720"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com uma conta do LinkedIn usando Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Criar um aplicativo do LinkedIn

Para usar uma conta do LinkedIn como um [provedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure Active Directory B2C (Azure ad B2C), você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do LinkedIn, poderá se inscrever em [https://www.linkedin.com/](https://www.linkedin.com/).

1. Entre no site de [desenvolvedores do LinkedIn](https://www.developer.linkedin.com/) com suas credenciais de conta do LinkedIn.
1. Selecione **meus aplicativos**e, em seguida, clique em **criar aplicativo**.
1. Insira o **nome da empresa**, **nome do aplicativo**, descrição do **aplicativo**, **logotipo do aplicativo**, uso do **aplicativo**, **URL do site**, **email comercial**e **telefone comercial**.
1. Concorde com os **termos de uso da API do LinkedIn** e clique em **Enviar**.
1. Copie os valores da **ID de cliente** e **segredo do cliente**. Você pode encontrá-los em **chaves de autenticação**. Você precisará de ambos para configurar o LinkedIn como um provedor de identidade em seu locatário. O **segredo do cliente** é uma credencial de segurança importante.
1. Insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URLs de redirecionamento autorizadas**. Substitua `your-tenant-name` pelo nome do seu locatário. Você precisa usar todas as letras minúsculas ao inserir o nome do locatário, mesmo que o locatário seja definido com letras maiúsculas no Azure AD B2C. Selecione **Adicionar**e, em seguida, clique em **Atualizar**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configurar uma conta do LinkedIn como um provedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **LinkedIn**.
1. Insira um **nome**. Por exemplo, *LinkedIn*.
1. Para a **ID do cliente**, insira a ID do cliente do aplicativo do LinkedIn que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do cliente que você registrou.
1. Selecione **Guardar**.

## <a name="migration-from-v10-to-v20"></a>Migração de v 1.0 para v 2.0

O LinkedIn [atualizou recentemente suas APIs de v 1.0 para v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Como parte da migração, Azure AD B2C só é capaz de obter o nome completo do usuário do LinkedIn durante a inscrição. Se um endereço de email for um dos atributos coletados durante a inscrição, o usuário deverá inserir manualmente o endereço de email e validá-lo.
