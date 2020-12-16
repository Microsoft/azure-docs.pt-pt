---
title: Adicionar Acesso Condicional a um fluxo de utilizador em Azure AD B2C
description: Saiba como adicionar acesso condicional aos fluxos de utilizador Azure AD B2C. Configure as definições de autenticação multi-factor (MFA) e as políticas de acesso condicional nos fluxos do utilizador para impor políticas e remediar os acessos de risco.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d6d5ab13c8997dffee42a053ba498376ccbcb6d8
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585263"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Adicionar Acesso Condicional aos fluxos de utilizadores no Azure Ative Directory B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

O Acesso Condicional pode ser adicionado aos fluxos de utilizador do seu Azure Ative Directory B2C para gerir as entradas de risco nas suas aplicações. A integração da Proteção de Identidade e acesso condicional no Azure AD B2C permite-lhe definir políticas que identifiquem comportamentos de acesso de risco e apliquem políticas que exijam mais ação do utilizador ou administrador. Estes são os componentes que permitem o acesso condicional nos fluxos de utilizador Azure AD B2C:

- **Fluxo do utilizador**. Crie um fluxo de utilizador que guie o utilizador através do processo de inscrição e inscrição. Nas definições de fluxo do utilizador, indique se deve ativar as políticas de Acesso Condicional quando um utilizador segue o fluxo do utilizador.
- **Aplicação que direciona os utilizadores para o fluxo do utilizador.** Configure a sua aplicação para direcionar os utilizadores para o fluxo de utilizador de inscrição e inscrição apropriado, especificando o ponto final do fluxo do utilizador na aplicação.
- **Política de Acesso Condicional**. [Crie uma política de Acesso Condicional](conditional-access-identity-protection-setup.md) e especifique as aplicações a que pretende aplicar a política. Quando o utilizador segue o fluxo de utilizador de entrada ou inscrição para a sua aplicação, a política de Acesso Condicional utiliza sinais de Proteção de Identidade para identificar inserções de risco e apresenta as medidas de reparação adequadas, se necessário.

O Acesso Condicional é suportado nas versões mais recentes dos fluxos dos utilizadores. Pode adicionar políticas de Acesso Condicional aos novos fluxos de utilizador à medida que as cria, ou pode adicioná-las aos fluxos de utilizador existentes, desde que a versão suporte o Acesso Condicional. Ao adicionar Acesso Condicional a um fluxo de utilizador existente, existem duas definições que terá de rever:

- **Autenticação multi-factor (MFA)**: Os utilizadores podem agora utilizar um código único via SMS ou voz, ou uma palavra-passe única via e-mail para autenticação multi-factor. As definições de MFA são independentes das definições de Acesso Condicional. Pode definir MFA para **Always On** de modo a que o MFA seja sempre necessário independentemente da sua configuração de Acesso Condicional. Ou, pode definir O MFA como **Condicional** de modo a que o MFA seja necessário apenas quando uma Política de Acesso Condicional ativa o exija.

- **Acesso Condicional**: Esta definição deve estar sempre **acesa**. Normalmente, você só desligaria esta **definição** durante a resolução de problemas ou migração, ou para implementações antigas.

Saiba mais sobre [a Proteção de Identidade e Acesso Condicional](conditional-access-identity-protection-overview.md) em Azure AD B2C, ou veja como [coneccioná-lo](conditional-access-identity-protection-setup.md).

## <a name="prerequisites"></a>Pré-requisitos

- O Azure AD B2C Premium 2 é necessário para criar políticas de inscrição arriscadas. Os inquilinos Premium P1 podem criar políticas de localização, app ou grupo.
- Para efeitos de teste, pode [registar a aplicação web](tutorial-register-applications.md) de teste , que é uma `https://jwt.ms` aplicação web detida pela Microsoft que exibe o conteúdo descodificado de um símbolo (o conteúdo do token nunca sai do seu navegador). 
- Para simular um insusição de risco, descarregue o Navegador TOR e tente entrar no ponto final do fluxo do utilizador.
- Utilizando as seguintes definições, [crie uma política de acesso condicional:](conditional-access-identity-protection-setup.md)
   
  - Para **Utilizadores e grupos**, selecione o utilizador de teste (não selecione **Todos os utilizadores** ou pode bloquear-se a iniciar sessão).
  - Para **aplicações ou ações cloud**, escolha **apps Selecione** e, em seguida, escolha a sua aplicação de partido em gestão.
  - Para as condições, selecione **o risco de inscrição** e os níveis de risco **elevados,** **médios** e **baixos.**
  - Para **Grant,** escolha **o acesso ao Bloco.**

      ![Deteções de riscos](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

::: zone pivot="b2c-user-flow"

## <a name="add-conditional-access-to-a-new-user-flow"></a>Adicionar Acesso Condicional a um novo fluxo de utilizador

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **os fluxos do Utilizador** e, em seguida, selecione Novo fluxo de **utilizador**.
1. Na página De fluxo do **utilizador,** selecione o tipo de fluxo do utilizador.
1. Em **Selecione uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**. (Saiba mais sobre[as](user-flow-versions.md) versões de fluxo de utilizador.)

    ![Crie a página de fluxo do utilizador no portal Azure com propriedades destacadas](./media/tutorial-create-user-flows/select-version.png)

1. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *signo de inscrição em 1*.
1. Na secção **de fornecedores de identidade,** selecione os fornecedores de identidade que pretende permitir este fluxo de utilizador.
2. Na secção de **autenticação multifactor,** selecione o **método MFA** pretendido e, em seguida, sob **a seleção de MFA** **Conditional (Recomendado)**.
 
   ![Configurar a autenticação multifactor](media/conditional-access-user-flow/configure-mfa.png)

1. Na secção **acesso condicional,** selecione a caixa de verificação **de políticas de acesso condicional.**

   ![Configurar configurações de acesso condicional](media/conditional-access-user-flow/configure-conditional-access.png)

1. Na secção **de atributos e reclamações** do Utilizador, escolha as reclamações e atributos que pretende recolher e enviar do utilizador durante a inscrição. Por exemplo, selecione **Mostrar mais** e, em seguida, escolha atributos e reclamações para **País/Região** e **Nome de Exibição**. Selecione **OK**.

    ![Página de seleção de atributos e reclamações com três reclamações selecionadas](./media/conditional-access-user-flow/configure-user-attributes-claims.png)

1. Clique **em Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente preparado para o nome.

## <a name="add-conditional-access-to-an-existing-user-flow"></a>Adicionar Acesso Condicional a um fluxo de utilizador existente

> [!NOTE]
> O fluxo de utilizador existente deve ser uma versão que suporte o Acesso Condicional. Estas versões de fluxo do utilizador estão rotuladas **Recomendadas.**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

1. No portal Azure, procure e selecione **Azure AD B2C**.

1. Em **Políticas**, selecione **fluxos de utilizador**. Em seguida, selecione o fluxo do utilizador.

1. Selecione **Propriedades** e certifique-se de que o fluxo do utilizador suporta o Acesso Condicional selecionando **propriedades** e procurando a definição de **Acesso Condicional** com rótulo .
 
   ![Configure MFA e Acesso Condicional em Imóveis](media/conditional-access-user-flow/add-conditional-access.png)

1. Na secção de **autenticação multifactor,** selecione o **método MFA** pretendido e, em seguida, sob **a seleção de MFA** **Conditional (Recomendado)**.
 
1. Na secção **acesso condicional,** selecione a caixa de verificação **de políticas de acesso condicional.**

1. Selecione **Guardar**.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

Para testar o Acesso Condicional no fluxo do utilizador, [crie uma política de acesso condicional](conditional-access-identity-protection-setup.md) e permita o Acesso Condicional no fluxo do seu utilizador, conforme descrito acima. 


### <a name="run-the-user-flow"></a>Executar o fluxo do utilizador

1. Selecione o fluxo de utilizador criado para abrir a sua página de visão geral e, em seguida, selecione **Executar o fluxo do utilizador**. Em **Aplicação**, selecione *webapp1*. A **URL de resposta** deve mostrar `https://jwt.ms` .

   ![Executar a página de fluxo do utilizador no portal com o botão de fluxo do utilizador Executar realçado](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Copie o URL no **ponto final do fluxo do utilizador executar**.

1. Para simular um insusitado arriscado, abra o [Tor Browser](https://www.torproject.org/download/) e use o URL que copiou no passo de pré-visualização para iniciar seduca na aplicação registada.

1. Insira as informações solicitadas na página de inscrição e, em seguida, tente entrar. O símbolo é devolvido `https://jwt.ms` e deve ser exibido para si. No jwt.ms ficha descodificada, deve ver que o sinal foi bloqueado:

   ![Teste um sinal bloqueado](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Adicionar Acesso Condicional à sua política

Pode encontrar um exemplo de uma política de acesso condicional no [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access).

::: zone-end

## <a name="next-steps"></a>Passos seguintes

[Personalize a interface do utilizador num fluxo de utilizador Azure AD B2C](customize-ui-with-html.md)
