---
title: Configurar e iniciar sessão com uma conta no Facebook
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição aos clientes com contas do Facebook nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cd0e19de88a6a65d72a2e7e19f7fca2a94d8da55
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188278"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurar e iniciar sessão com uma conta no Facebook utilizando o Diretório Ativo Azure B2C

## <a name="create-a-facebook-application"></a>Criar uma aplicação do Facebook

Para utilizar uma conta no Facebook como fornecedor de [identidade](authorization-code-flow.md) no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação no seu inquilino que o represente. Se ainda não tem uma conta no Facebook, pode inscrever-se na [https://www.facebook.com/](https://www.facebook.com/).

1. Inscreva-se no [Facebook para programadores](https://developers.facebook.com/) com as suas credenciais de conta no Facebook.
1. Se ainda não o fez, tem de se registar como desenvolvedor do Facebook. Para isso, selecione **Get Started** no canto superior direito da página, aceite as políticas do Facebook e complete os passos de registo.
1. Selecione **as minhas apps** e, em seguida, **crie app**.
1. Introduza um Nome de **Exibição** e um email de **contacto**válido.
1. Selecione **Criar id**de aplicativo . Isto pode exigir que você aceite as políticas da plataforma do Facebook e complete uma verificação de segurança on-line.
1. Selecione **Definições** > **Básicas**.
1. Escolha uma **categoria,** por exemplo, `Business and Pages`. Este valor é exigido pelo Facebook, mas não utilizado para O Azure AD B2C.
1. Na parte inferior da página, selecione **Adicionar Plataforma**, e, em seguida, selecione **Website**.
1. No **Site URL,** insira `https://your-tenant-name.b2clogin.com/` substituindo `your-tenant-name` pelo nome do seu inquilino. Introduza um URL para o URL da Política de **Privacidade,** por exemplo, `http://www.contoso.com`. O URL de política é uma página que mantém para fornecer informações de privacidade para a sua aplicação.
1. Selecione **Guardar Alterações**.
1. No topo da página, copie o valor do ID da **App**.
1. Selecione **Mostrar** e copiar o valor da **App Secret**. Usa-os para configurar o Facebook como fornecedor de identidade no seu inquilino. **App Secret** é uma importante credencial de segurança.
1. Selecione o sinal de mais ao lado de **PRODUTOS**, e, em seguida, selecione **Configurar** sob **o Facebook Login**.
1. Em **login no Facebook,** selecione **Definições**.
1. Em **Redirecionamento Válido de IUrIs,** introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua `your-tenant-name` pelo nome do seu inquilino. Selecione **Guardar Alterações** na parte inferior da página.
1. Para disponibilizar a sua aplicação do Facebook ao Azure AD B2C, selecione o seletor de Estado no canto superior direito da página e **ligue-o** para tornar a aplicação pública e, em seguida, selecione **Modo Switch**.  Neste momento, o Estatuto deve mudar de **Desenvolvimento** para **Live**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configure uma conta de Facebook como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de Identidade**e, em seguida, selecione **Facebook**.
1. Introduza um **Nome**. Por exemplo, *Facebook*.
1. Para o ID do **cliente**, insira o ID da app da aplicação do Facebook que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo da App que gravou.
1. Selecione **Guardar**.
