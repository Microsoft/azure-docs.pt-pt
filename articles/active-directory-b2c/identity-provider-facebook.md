---
title: Configurar inscrição e iniciar sôm-in com uma conta no Facebook
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição em clientes com contas do Facebook nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62956000e143f5504d32dae26953bcf877ce96a6
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92628580"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com uma conta no Facebook utilizando o Azure Ative Directory B2C

## <a name="create-a-facebook-application"></a>Criar uma aplicação no Facebook

Para utilizar uma conta do Facebook como fornecedor de [identidade](authorization-code-flow.md) no Azure Ative Directory B2C (Azure AD B2C), é necessário criar uma aplicação no seu inquilino que a represente. Se ainda não tiver uma conta no Facebook, pode inscrever-se em [https://www.facebook.com/](https://www.facebook.com/) .

1. Inscreva-se no [Facebook para programadores](https://developers.facebook.com/) com as suas credenciais de conta no Facebook.
1. Se ainda não o fez, tem de se registar como programador do Facebook. Para isso, **selecione Começar** no canto superior direito da página, aceite as políticas do Facebook e complete as etapas de inscrição.
1. Selecione **as minhas apps** e, em seguida, **crie app.**
1. Selecione **Build Connected Experiences** .
1. Introduza um **Nome de Exibição** e um **e-mail de contacto** válido.
1. Selecione **Criar ID de aplicação.** Isto pode exigir que aceite as políticas da plataforma do Facebook e preencha uma verificação de segurança online.
1. Selecione **Definições**  >  **Básicas** .
    1. Escolha uma **categoria,** por `Business and Pages` exemplo. Este valor é exigido pelo Facebook, mas não utilizado para Azure AD B2C.
    1. Introduza um URL para os **Termos de Serviço URL,** por exemplo `http://www.contoso.com/tos` . O URL de política é uma página que mantém para fornecer termos e condições para a sua aplicação.
    1. Introduza um URL para o **URL da Política de Privacidade,** por exemplo `http://www.contoso.com/privacy` . O URL de política é uma página que mantém para fornecer informações de privacidade para a sua aplicação.
1. Na parte inferior da página, selecione **Add Platform** e, em seguida, selecione **Website** .
1. No **URL do Site,** insira o endereço do seu website, por `https://contoso.com` exemplo. 
1. Selecione **Guardar alterações** .
1. No topo da página, copie o valor do ID da **aplicação.**
1. Selecione **Mostrar** e copie o valor da **App Secret.** Usa os dois para configurar o Facebook como fornecedor de identidade no seu inquilino. **App Secret** é uma importante credencial de segurança.
1. No menu, selecione o sinal **de mais** ao lado **de PRODUTOS.** No âmbito dos **Produtos Adicionais à Sua App,** selecione **Configurar** no **Facebook Login** .
1. A partir do menu, selecione **Facebook Login** , selecione **Definições** .
1. Em **OAuth Redirecionar URIs,** insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . `your-tenant-name`Substitua-o pelo nome do seu inquilino. Selecione **Guardar Alterações** na parte inferior da página.
1. Para disponibilizar a sua aplicação do Facebook ao Azure AD B2C, selecione o seletor de Estado no topo direito da página e **ligue-o** para tornar a aplicação pública e, em seguida, selecione **o Modo Switch** .  Neste momento, o Estatuto deve mudar de **Desenvolvimento** para **Viver** .

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurar uma conta do Facebook como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C** .
1. Selecione **fornecedores de identidade** e, em seguida, selecione **Facebook** .
1. Insira um **nome** . Por exemplo, *Facebook* .
1. Para o ID do **Cliente,** insira o ID da aplicação do Facebook que criou anteriormente.
1. Para o segredo do **Cliente,** insira a App Secret que gravou.
1. Selecione **Guardar** .
