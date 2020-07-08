---
title: Configurar inscrição e iniciar sôm-in com uma conta LinkedIn
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição a clientes com contas LinkedIn nas suas aplicações utilizando o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2f1099a3564a5891a69429d78bda8177094538e4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388022"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurar inscrição e inscrição com uma conta LinkedIn utilizando o Azure Ative Directory B2C

## <a name="create-a-linkedin-application"></a>Criar uma aplicação LinkedIn

Para utilizar uma conta LinkedIn como fornecedor de [identidade](authorization-code-flow.md) no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação no seu inquilino que a represente. Se ainda não tiver uma conta LinkedIn, pode inscrever-se em [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Inscreva-se no site do [LinkedIn Developers](https://www.developer.linkedin.com/) com as suas credenciais de conta LinkedIn.
1. Selecione **as minhas aplicações**e, em seguida, clique em **Criar Aplicação**.
1. Insira **o nome da empresa**, Nome da **Aplicação, Descrição da Aplicação,** Logotipo **da Aplicação, Utilização de Aplicações,** **URL do site,** **e-mail de negócios**e **telefone comercial.** **Application Name** **Application Logo**
1. Concorde com os **Termos de Utilização da API do LinkedIn** e clique em **Enviar.**
1. Copiar os valores do **ID do Cliente** e **do Cliente Secreto.** Pode encontrá-los em **Chaves de Autenticação.** Você precisará de ambos para configurar o LinkedIn como um fornecedor de identidade no seu inquilino. **A Client Secret** é uma importante credencial de segurança.
1. Introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URLs de redirecionamento autorizados**. `your-tenant-name`Substitua-o pelo nome do seu inquilino. Você precisa usar todas as letras minúsculas ao introduzir o seu nome de inquilino, mesmo que o inquilino seja definido com letras maiúsculas em Azure AD B2C. **Selecione Adicionar**e, em seguida, clique em **Update**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configurar uma conta LinkedIn como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **os fornecedores de identidade**e, em seguida, selecione **LinkedIn**.
1. Insira um **nome**. Por exemplo, *LinkedIn*.
1. Para o ID do **Cliente,** insira o ID do Cliente da aplicação LinkedIn que criou anteriormente.
1. Para o segredo do **Cliente,** insira o Segredo de Cliente que gravou.
1. Selecione **Guardar**.

## <a name="migration-from-v10-to-v20"></a>Migração de v1.0 para v2.0

O LinkedIn atualizou recentemente [as suas APIs de v1.0 a v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Como parte da migração, o Azure AD B2C só é capaz de obter o nome completo do utilizador linkedIn durante a inscrição. Se um endereço de e-mail for um dos atributos recolhidos durante a inscrição, o utilizador deve introduzir manualmente o endereço de e-mail e validá-lo.
