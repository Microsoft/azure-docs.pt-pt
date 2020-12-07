---
title: Criar um fluxo de utilizador - Azure Ative Directory B2C
description: Saiba como criar fluxos de utilizador no portal Azure para permitir a edição de inscrição, inscrição e perfil do utilizador para as suas aplicações no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fbb55d71c2ed56bac14380960f9a0777be549566
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754871"
---
# <a name="create-a-user-flow-in-azure-active-directory-b2c"></a>Criar um fluxo de utilizador em Azure Ative Directory B2C

Pode criar fluxos de utilizadores de [diferentes](user-flow-overview.md) tipos no seu inquilino Azure Ative Directory B2C (Azure AD B2C) e usá-los nas suas aplicações conforme necessário. Os fluxos dos utilizadores podem ser reutilizados através das aplicações.

> [!IMPORTANT]
> Mudamos a forma como referenciamos as versões de fluxo de utilizadores. Anteriormente, oferecemos versões V1 (prontas para produção) e versões V1.1 e V2 (pré-visualização). Agora, consolidamos os fluxos de utilizador nas versões **Recomendado** (pré-visualização da próxima geração) e **Standard** (geralmente disponível). Todos os fluxos de pré-visualização de V1.1 e V2 estão em vias de depreciação até 1 de agosto de **2021**. Para mais informações, consulte [as versões de fluxo do utilizador em Azure AD B2C](user-flow-versions.md).

## <a name="before-you-begin"></a>Before you begin

- **Registe a aplicação** que pretende utilizar para testar o novo fluxo de utilizador. Por exemplo, consulte o [Tutorial: Registe uma aplicação web em Azure AD B2C](tutorial-register-applications.md).
- **Adicione fornecedores de identidade externos** se quiser ativar o acesso do utilizador a fornecedores como Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft ou Twitter. Por exemplo, consulte [Tutorial: Adicione fornecedores de identidade às suas aplicações em Azure AD B2C](tutorial-add-identity-providers.md).
- **Configure o fornecedor local de identidade** da conta para especificar os tipos de identidade (e-mail, nome de utilizador, número de telefone) que pretende apoiar para contas locais no seu inquilino. Em seguida, pode escolher entre estes tipos de identidade suportados quando criar fluxos de utilizador individuais. Quando um utilizador completa o fluxo do utilizador, é criada uma conta local no seu diretório Azure AD B2C, e o seu fornecedor de identidade de **conta Local** autentica as informações do utilizador. Configure o fornecedor local de identidade de conta do seu inquilino com estes passos:

   1. Inicie sessão no [portal do Azure](https://portal.azure.com/). 
   2. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.
   3. Na barra de pesquisa no topo do portal Azure, procure e selecione **Azure AD B2C**.
   4. Em **Gestão**, selecione **Fornecedores de Identidade**.
   5. Na lista de fornecedores de identidade, selecione **Conta Local.**
   6. Na página **Configure local IDP,** selecione todos os tipos de identidade que pretende suportar. As opções de seleção aqui simplesmente as disponibilizam para os fluxos de utilizador que cria mais tarde:
      - **Telefone** (pré-visualização): Permite que um utilizador introduza um número de telefone, que é verificado no início da inscrição e se torna o seu ID do utilizador.
      - **E-mail** (padrão): Permite que um utilizador introduza um endereço de e-mail, que é verificado no momento da inscrição e se torna o seu ID do utilizador.
      - **Nome de utilizador**: Permite que um utilizador crie o seu próprio ID de utilizador único. Um endereço de e-mail é recolhido do utilizador e verificado.
    7. Selecione **Save** (Guardar).

## <a name="create-a-user-flow"></a>Criar um fluxo de utilizador

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

    ![Inquilina B2C, Diretório e Painel de Assinatura, Portal Azure](./media/create-user-flow/directory-subscription-pane.png)

3. No portal Azure, procure e selecione **Azure AD B2C**.
4. Em **Políticas**, selecione **os fluxos do Utilizador** e, em seguida, selecione Novo fluxo de **utilizador**.

    ![Página de fluxos de utilizador no portal com novo botão de fluxo do utilizador realçado](./media/create-user-flow/signup-signin-user-flow.png)

5. Na página De fluxo do **utilizador,** selecione o tipo de fluxo de utilizador que pretende criar (consulte os [fluxos do Utilizador em Azure AD B2C](user-flow-overview.md) para uma visão geral).

    ![Selecione uma página de fluxo de utilizador com o fluxo de inscrição e de entrada em destaque](./media/create-user-flow/select-user-flow-type.png)

6. Em **Selecione uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**. (Saiba mais sobre[as](user-flow-versions.md) versões de fluxo de utilizador.)

    ![Crie a página de fluxo do utilizador no portal Azure com propriedades destacadas](./media/create-user-flow/select-version.png)

7. Introduza um **Nome** para o fluxo do utilizador (por exemplo, *signo-assinado 1,* *perfileditação1*, *passwordreset1*).
8. Nos **fornecedores de identidade,** escolha as opções dependendo do tipo de fluxo de utilizador que está a criar:

   - **Conta local.** Se pretender permitir que os utilizadores criem contas locais no seu inquilino Azure AD B2C, selecione o tipo de identificador que pretende que utilize (por exemplo, e-mail, ID do utilizador ou telefone). Apenas os tipos de identidade configurados nas definições [do fornecedor de identidade de conta local](#before-you-begin) estão listados.

   - **Prestadores de identidade social.** Se pretender permitir o acesso ao utilizador com fornecedores de [identidade social,](tutorial-add-identity-providers.md)como a Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft ou Twitter, selecione os fornecedores da lista.

9. Para **atributos e reclamações do Utilizador,** escolha as reclamações e atributos que pretende recolher e enviar do utilizador durante a inscrição. Selecione **Mostrar mais**. Selecione os atributos e reclamações e, em seguida, selecione **OK**.

    ![Página de seleção de atributos e reclamações com três reclamações selecionadas](./media/create-user-flow/signup-signin-attributes.png)

10. Selecione **Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente preparado para o nome.

### <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Selecione **Políticas**  >  **Os fluxos do utilizador** e, em seguida, selecione o fluxo de utilizador que criou. Na página de visão geral do fluxo do utilizador, selecione **Executar o fluxo do utilizador**.
1. Para **Aplicação**, selecione a aplicação web que registou no passo 1. A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione **Executar o fluxo do utilizador**.
2. Dependendo do tipo de fluxo de utilizador que está a testar, ou se inscreva usando um endereço de e-mail válido e siga o fluxo de inscrição, ou faça o sôm.

    ![Executar a página de fluxo do utilizador no portal com o botão de fluxo do utilizador Executar realçado](./media/create-user-flow/sign-up-sign-in-run-now.png)

1. Siga as indicações de fluxo do utilizador. Quando completar o fluxo do utilizador, o token é devolvido `https://jwt.ms` e deve ser apresentado a si.

> [!NOTE]
> A experiência "Executar o fluxo do utilizador" não é atualmente compatível com o tipo URL de resposta SPA utilizando o fluxo de código de autorização. Para utilizar a experiência "Executar o fluxo do utilizador" com este tipo de aplicações, registe um URL de resposta do tipo "Web" e possibilite o fluxo implícito tal como descrito [aqui](tutorial-register-spa.md).

## <a name="next-steps"></a>Passos seguintes

- [Adicionar acesso condicional aos fluxos de utilizadores AZure AD B2C](conditional-access-user-flow.md)
- [Personalize a interface do utilizador num fluxo de utilizador Azure AD B2C](customize-ui-overview.md)
