---
title: Configurar o início e iniciar sessão com uma conta LinkedIn
titleSuffix: Azure AD B2C
description: Forneça inscrição e inscrição aos clientes com contas LinkedIn nas suas aplicações utilizando o Diretório Ativo Azure B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: edd6bc2bbad218a1ed0a25bdcb763bde656a589d
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847722"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurar o início e iniciar sessão com uma conta LinkedIn utilizando o Diretório Ativo Azure B2C

## <a name="create-a-linkedin-application"></a>Criar uma aplicação LinkedIn

Para utilizar uma conta LinkedIn como fornecedor de [identidade](authorization-code-flow.md) no Azure Ative Directory B2C (Azure AD B2C), precisa de criar uma aplicação no seu inquilino que o represente. Se ainda não tiver uma conta LinkedIn, pode inscrever-se na [https://www.linkedin.com/](https://www.linkedin.com/).

1. Inscreva-se no [website do LinkedIn Developers](https://www.developer.linkedin.com/) com as suas credenciais de conta LinkedIn.
1. Selecione **As minhas apps**e, em seguida, clique em Criar **Aplicação**.
1. Insira **o nome da empresa,** nome da **aplicação,** **descrição da aplicação,** logotipo da **aplicação,** utilização da **aplicação,** URL do **site,** **email de negócios**e **telefone comercial.**
1. Concorde com os **termos de utilização da API LinkedIn** e clique em **Submeter**.
1. Copie os valores da **ID de cliente** e **segredo do cliente**. Pode encontrá-las em Teclas de **Autenticação.** Você precisará de ambos para configurar linkedIn como um fornecedor de identidade no seu inquilino. **Client Secret** é uma importante credencial de segurança.
1. Introduza `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` em **URLs de Redirecionamento Autorizados**. Substitua `your-tenant-name` pelo nome do seu locatário. Você precisa usar todas as letras minúsculas ao inserir o nome do locatário, mesmo que o locatário seja definido com letras maiúsculas no Azure AD B2C. Selecione **Adicionar**, e, em seguida, clique em **Atualizar**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configure uma conta LinkedIn como fornecedor de identidade

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **fornecedores de Identidade**e, em seguida, selecione **LinkedIn**.
1. Insira um **nome**. Por exemplo, *LinkedIn*.
1. Para o ID do **Cliente,** insira o ID do Cliente da aplicação LinkedIn que criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do cliente que você registrou.
1. Selecione **Guardar**.

## <a name="migration-from-v10-to-v20"></a>Migração de v1.0 para v2.0

O LinkedIn atualizou recentemente [as suas APIs de v1.0 para v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Como parte da migração, o Azure AD B2C só é capaz de obter o nome completo do utilizador LinkedIn durante a inscrição. Se um endereço de e-mail for um dos atributos recolhidos durante o arranque, o utilizador deve introduzir manualmente o endereço de e-mail e validá-lo.
