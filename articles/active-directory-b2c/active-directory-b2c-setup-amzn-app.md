---
title: Configurar a inscrição e a entrada com uma conta da Amazon-Azure Active Directory B2C
description: Forneça inscrição e entrada para clientes com contas da Amazon em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bddbd9b588d8ffc2ace3a937fa2c72876eb5e4e9
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065217"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e a entrada com uma conta do Amazon usando Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Criar um aplicativo Amazon

Para usar uma conta da Amazon como um [provedor de identidade](active-directory-b2c-reference-oauth-code.md) no Azure Active Directory B2C (Azure ad B2C), você precisa criar um aplicativo em seu locatário que o represente. Se você ainda não tiver uma conta do Amazon, poderá se inscrever em [https://www.amazon.com/](https://www.amazon.com/).

1. Entre no [Amazon Developer Center](https://login.amazon.com/) com suas credenciais de conta do Amazon.
1. Se você ainda não tiver feito isso, clique em **inscrever-** se, siga as etapas de registro do desenvolvedor e aceite a política.
1. Selecione **registrar novo aplicativo**.
1. Insira um **nome**, uma **Descrição**e uma **URL de aviso de privacidade**e, em seguida, clique em **salvar**. O aviso de privacidade é uma página que você gerencia que fornece informações de privacidade aos usuários.
1. Na seção **configurações da Web** , copie os valores da **ID do cliente**. Selecione **Mostrar segredo** para obter o segredo do cliente e, em seguida, copie-o. Você precisa de ambos para configurar uma conta do Amazon como um provedor de identidade em seu locatário. O **segredo do cliente** é uma credencial de segurança importante.
1. Na seção **configurações da Web** , selecione **Editar**e, em seguida `https://your-tenant-name.b2clogin.com` , insira em origens de `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **JavaScript permitidas** e em **URLs de retorno permitidas**. Substitua `your-tenant-name` pelo nome do seu locatário. Você precisa usar todas as letras minúsculas ao inserir o nome do locatário, mesmo que o locatário seja definido com letras maiúsculas no Azure AD B2C.
1. Clique em **Guardar**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configurar uma conta do Amazon como um provedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade**e, em seguida, selecione **Amazon**.
1. Insira um **nome**. Por exemplo, *Amazon*.
1. Para a **ID do cliente**, insira a ID do cliente do aplicativo Amazon que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do cliente que você registrou.
1. Selecione **Guardar**.
