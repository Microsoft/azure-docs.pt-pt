---
title: Configurar inscrição e iniciar sôm-in com uma conta do Google
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição aos clientes com contas google nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 72e4de1473766d50512453ae38b6033ff0c5b73d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388039"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurar o sº de inscrição e o inseúbmento com uma conta google utilizando o Azure Ative Directory B2C

## <a name="create-a-google-application"></a>Criar uma aplicação google

Para utilizar uma conta google como fornecedor de [identidade](authorization-code-flow.md) no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação na consola do Google Developers. Se ainda não tiver uma conta no Google, pode inscrever-se [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) em .

1. Inscreva-se na Consola do [Google Developers](https://console.developers.google.com/) com as credenciais da sua conta Google.
1. No canto superior esquerdo da página, selecione a lista de projetos e, em seguida, selecione **New Project**.
1. Introduza um **Nome de Projeto,** selecione **Criar**.
1. Certifique-se de que está a utilizar o novo projeto selecionando a queda do projeto no canto superior esquerdo do ecrã, selecione o seu projeto pelo nome e, em seguida, selecione **Open**.
1. Selecione **o ecrã de consentimento OAuth** no menu esquerdo, selecione **External**e, em seguida, selecione **Criar**.
Insira um **Nome** para a sua inscrição. Introduza *b2clogin.com* na secção **de domínios autorizados** e selecione **Guardar**.
1. Selecione **Credenciais** no menu esquerdo e, em seguida, **selecione Criar credenciais**  >  **ID do cliente do autor daauth**.
1. Sob **o tipo de aplicação**, selecione **aplicação Web**.
1. Introduza um **Nome** para a sua aplicação, `https://your-tenant-name.b2clogin.com` introduza nas **origens Autorizadas javaScript**, e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URIs de redirecionamento autorizado**. `your-tenant-name`Substitua-o pelo nome do seu inquilino. Você precisa usar todas as letras minúsculas ao introduzir o seu nome de inquilino, mesmo que o inquilino seja definido com letras maiúsculas em Azure AD B2C.
1. Clique em **Criar**.
1. Copie os valores do **ID** do Cliente e **do segredo do Cliente.** Você precisará de ambos para configurar o Google como um fornecedor de identidade no seu inquilino. **O segredo do cliente** é uma importante credencial de segurança.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurar uma conta google como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade**e, em seguida, selecione **Google**.
1. Insira um **nome**. Por exemplo, *o Google*.
1. Para o ID do **Cliente,** insira o ID do Cliente da aplicação google que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo de Cliente que gravou.
1. Selecione **Guardar**.
