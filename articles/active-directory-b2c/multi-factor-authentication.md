---
title: Autenticação multi-factor no Azure Ative Directory B2C | Microsoft Docs
description: Como ativar a autenticação multi-factor em aplicações viradas para o consumidor protegidas pelo Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b7b7f1c5fb0a7991707a26b4a7f54fb3ffaf7bab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033525"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Ativar a autenticação multifator no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

O Azure Ative Directory B2C (Azure AD B2C) integra-se diretamente com [a Autenticação Multi-Factor AD Azure para](../active-directory/authentication/concept-mfa-howitworks.md) que possa adicionar uma segunda camada de segurança às experiências de inscrição e inscrição nas suas aplicações. Pode ativar a autenticação multifator sem ter de escrever uma única linha de código. Se já criou os fluxos de entrada e de inscrição do utilizador, ainda pode ativar a autenticação de vários fatores.

Esta funcionalidade ajuda as aplicações a lidar com cenários como:

- Não é necessário autenticação multi-factor para aceder a uma aplicação, mas precisa que aceda a outra. Por exemplo, o cliente pode assinar um pedido de seguro automóvel com uma conta social ou local, mas deve verificar o número de telefone antes de aceder ao pedido de seguro de casa registado no mesmo diretório.
- Não é necessário a autenticação de vários fatores para aceder a uma aplicação em geral, mas exige que aceda às partes sensíveis dentro dela. Por exemplo, o cliente pode iniciar scontabilidade bancária com uma conta social ou local e verificar o saldo da conta, mas deve verificar o número de telefone antes de tentar uma transferência bancária.

## <a name="set-multi-factor-authentication"></a>Definir a autenticação de vários fatores

::: zone pivot="b2c-user-flow"

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Utilize o filtro **de subscrição Diretório +** no menu superior para selecionar o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **fluxos de utilizador**.
1. Selecione o fluxo do utilizador para o qual deseja ativar o MFA. Por exemplo, *B2C_1_signinsignup.*
1. Selecione **Propriedades**.
1. Na secção de **autenticação multifactor,** selecione o **método MFA** pretendido e, em seguida, sob **a aplicação do MFA** selecione **Sempre em**, ou **Condicional (Recomendado)**.
   > [!NOTE]
   >
   > - Se selecionar **Conditional (Recomendado)**, também terá de [adicionar Acesso Condicional aos fluxos do utilizador](conditional-access-user-flow.md), e especificar as aplicações a que pretende aplicar a política.
   > - A autenticação de vários fatores (MFA) é desativada por padrão para fluxos de utilizador de inscrição. Pode ativar o MFA nos fluxos de utilizador com inscrição no telefone, mas como um número de telefone é usado como identificador principal, a senha de e-mail é a única opção disponível para o segundo fator de autenticação.

1. Selecione **Guardar**. O MFA está agora ativado para este fluxo de utilizador.

Pode utilizar **o fluxo do utilizador executar** para verificar a experiência. Confirme o seguinte cenário:

Uma conta de cliente é criada no seu inquilino antes do passo de autenticação multi-factor. Durante o passo, o cliente é solicitado a fornecer um número de telefone e verificar. Se a verificação for bem sucedida, o número de telefone é anexado à conta para posterior utilização. Mesmo que o cliente cancele ou desa caia, o cliente pode ser solicitado a verificar um número de telefone novamente durante a próxima entrada com autenticação multi-factor ativada.

::: zone-end

::: zone pivot="b2c-custom-policy"

Para ativar a autenticação multi-factor obtenha os pacotes de iniciação de políticas personalizadas do GitHub, em seguida, atualize os ficheiros XML no pacote de arranque **SocialAndLocalAccountsWithMFA** com o seu nome de inquilino Azure AD B2C. O **SocialAndLocalAccountsWithMFA**  permite opções de autenticação social, local e multi-fatores. Para obter mais informações, consulte [Começar com políticas personalizadas no Ative Directory B2C](custom-policy-get-started.md). 

::: zone-end
