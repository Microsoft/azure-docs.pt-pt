---
title: Criar o fornecedor local de identidade de conta
titleSuffix: Azure AD B2C
description: Defina os tipos de identidade que pode utilizar (e-mail, nome de utilizador, número de telefone) para autenticação de conta local quando configurar fluxos de utilizador no seu inquilino Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1ed0ca069f29413083356a3147d4b97f1b6ed981
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525200"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows-preview"></a>Configurar a inscrição no telefone e iniciar sômposições para os fluxos do utilizador (pré-visualização)

> [!NOTE]
> As funcionalidades de inscrição e inscrição e recuperação de emails do telefone para fluxos de utilizadores estão em pré-visualização pública.

Além do e-mail e nome de utilizador, pode ativar o número de telefone como uma opção de inscrição em todo o inquilino, adicionando inscrição de telefone e inscrição no seu fornecedor de identidade de conta local. Depois de ativar a inscrição no telefone e iniciar sômposições para contas locais, pode adicionar inscrição de telefone aos fluxos do seu utilizador.

A configuração da inscrição no telefone e a inscrição num fluxo de utilizador envolvem os seguintes passos:

- [Configure a inscrição no telefone e inscreva-se em todo](#configure-phone-sign-up-and-sign-in-tenant-wide) o seu fornecedor de identidade de conta local para aceitar um número de telefone como identidade de um utilizador. 

- [Adicione a inscrição do telefone ao fluxo do utilizador](#add-phone-sign-up-to-a-user-flow) para permitir que os utilizadores se inscrevam na sua aplicação usando o seu número de telefone.

- [Ativar o pedido de e-mail de recuperação (pré-visualização)](#enable-the-recovery-email-prompt-preview) para permitir que os utilizadores especifiquem um e-mail que pode ser usado para recuperar a sua conta quando não têm o seu telemóvel.

A autenticação multi-factor (MFA) é desativada por predefinição quando configura um fluxo de utilizador com inscrição de telefone. Pode ativar o MFA nos fluxos de utilizador com inscrição no telefone, mas como um número de telefone é usado como identificador principal, a senha de e-mail é a única opção disponível para o segundo fator de autenticação.

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>Configurar inscrição de telefone e inscrição em inquilino em todo

A inscrição por e-mail é ativada por padrão nas definições do seu fornecedor de identidade de conta local. Pode alterar os tipos de identidade que irá apoiar no seu inquilino selecionando ou deselendo o registo de e-mail, nome de utilizador ou número de telefone.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD.

3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.

4. Em **Gestão**, selecione **Fornecedores de Identidade**.

5. Na lista de fornecedores de identidade, selecione **Conta Local.**

   ![Os fornecedores de identidade selecionam conta local](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. Na página **de IDP local Configure,** certifique-se de que o **Telefone** é selecionado como um dos tipos de identidade admissíveis que os consumidores podem usar para criar as suas contas locais no seu inquilino Azure AD B2C.

   ![Selecione os tipos de identidade permitidos](media/phone-authentication-user-flows/configure-local-idp.png)

1. Selecione **Guardar**.

## <a name="add-phone-sign-up-to-a-user-flow"></a>Adicione o registo do telefone a um fluxo de utilizador

Depois de ter adicionado a inscrição no telefone como uma opção de identidade para contas locais, pode adicioná-la aos fluxos de utilizador, desde que sejam as versões de fluxo **recomendado** para o utilizador mais recentes. Segue-se um exemplo que mostra como adicionar inscrição de telefone a novos fluxos de utilizador. Mas também pode adicionar inscrição de telefone aos fluxos de utilizador de versão recomendada existentes (selecione **User Flows**  >  *user flow name* Identity  >  **providers** Local Account Phone  >  **(** 

Aqui está um exemplo que mostra como adicionar inscrição de telefone a um novo fluxo de utilizador.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

    ![Inquilina B2C, Diretório e Painel de Assinatura, Portal Azure](./media/phone-authentication-user-flows/directory-subscription-pane.png)

3. No portal Azure, procure e selecione **Azure AD B2C**.
4. Em **Políticas**, selecione **os fluxos do Utilizador** e, em seguida, selecione Novo fluxo de **utilizador**.

    ![Página de fluxos de utilizador no portal com novo botão de fluxo do utilizador realçado](./media/phone-authentication-user-flows/signup-signin-user-flow.png)

5. Na página De fluxo do **utilizador,** selecione o **'Iniciar's e inscreva-se no** fluxo do utilizador.

    ![Selecione uma página de fluxo de utilizador com o fluxo de inscrição e de entrada em destaque](./media/phone-authentication-user-flows/select-user-flow-type.png)

6. Em **Selecione uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**. (Saiba mais sobre[as](user-flow-versions.md) versões de fluxo de utilizador.)

    ![Criar botão de fluxo de utilizador](./media/phone-authentication-user-flows/select-version.png)

7. Introduza um **Nome** para o fluxo do utilizador. Por exemplo, *signo de inscrição em 1*.
8. Na secção **de fornecedores de identidade,** em **contas locais,** selecione **Inscrição telefónica**.

   ![Opção de inscrição do telefone de fluxo do utilizador selecionada](media/phone-authentication-user-flows/user-flow-phone-signup.png)

9. Nos **fornecedores de identidade social,** selecione quaisquer outros fornecedores de identidade que pretenda permitir este fluxo de utilizador.

   > [!NOTE]
   > A autenticação de vários fatores (MFA) é desativada por padrão para fluxos de utilizador de inscrição. Pode ativar o fluxo de utilizador de inscrição no telefone, mas como um número de telefone é usado como identificador principal, a senha de e-mail é a única opção disponível para o segundo fator de autenticação.

1. Na secção **de atributos do Utilizador e reclamações simbólicas,** escolha as reclamações e atributos que pretende recolher e enviar do utilizador durante a inscrição. Por exemplo, selecione **Mostrar mais**, e depois escolha atributos e reclamações para **País/Região,** **Nome de Exibição** e **Código Postal**. Selecione **OK**.

1. Selecione **Criar** para adicionar o fluxo do utilizador. Um prefixo de *B2C_1* é automaticamente preparado para o nome.

## <a name="enable-the-recovery-email-prompt-preview"></a>Ativar o pedido de e-mail de recuperação (pré-visualização)

Quando ativa a inscrição no telefone e o registo dos fluxos do utilizador, também é uma boa ideia ativar a funcionalidade de e-mail de recuperação. Com esta funcionalidade, um utilizador pode fornecer um endereço de e-mail que pode ser usado para recuperar a sua conta quando não tem o seu telemóvel. Este endereço de e-mail é utilizado apenas para recuperação de conta. Não pode ser usado para se inscrever.

- Quando o pedido de e-mail de recuperação está **ligado,** um utilizador que se inscreve pela primeira vez é solicitado a verificar um e-mail de backup. Um utilizador que não tenha fornecido um e-mail de recuperação antes é solicitado a verificar um e-mail de backup durante o próximo sôm.

- Quando o e-mail de recuperação está **desligado,** um utilizador que se inscreva ou inscreva-se não é mostrado o pedido de e-mail de recuperação.
 
Pode ativar o pedido de e-mail de recuperação nas propriedades de fluxo do utilizador.

> [!NOTE]
> Antes de começar, [certifique-se de que adicionou a inscrição do telefone ao fluxo do utilizador,](#add-phone-sign-up-to-a-user-flow) conforme descrito acima.

### <a name="to-enable-the-recovery-email-prompt"></a>Para ativar o pedido de e-mail de recuperação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
3. No portal Azure, procure e selecione **Azure AD B2C**.
4. Em Azure AD B2C, em **Políticas,** selecione **fluxos de utilizador**.
5. Selecione o fluxo do utilizador da lista.
6. Em **Definições**, selecione **Propriedades**.
7. Seguinte para **Ativar o pedido de e-mail de recuperação para a inscrição do número de telefone e iniciar sing dentro (pré-visualização)**, selecione:

   - **Continuar** a mostrar o pedido de e-mail de recuperação durante a inscrição e o registo.
   - **Fora** para esconder o e-mail de recuperação.

    ![O utilizador flui propriedades com Enable email de recuperação ativado](./media/phone-authentication-user-flows/recovery-email-settings.png)

8. Selecione **Guardar**.

### <a name="to-test-the-recovery-email-prompt"></a>Para testar o pedido de e-mail de recuperação

Depois de ter ativado a inscrição no telefone e o insejo de sômis e o pedido de e-mail de recuperação no fluxo do utilizador, pode utilizar **o fluxo do utilizador Run** para testar a experiência do utilizador.

1. Selecione **Políticas**  >  **Os fluxos do utilizador** e, em seguida, selecione o fluxo de utilizador que criou. Na página de visão geral do fluxo do utilizador, selecione **Executar o fluxo do utilizador**.

2. Para **Aplicação**, selecione a aplicação web que registou no passo 1. A **URL de resposta** deve mostrar `https://jwt.ms` .

3. Selecione **Executar o fluxo do utilizador** e verificar o seguinte comportamento:

   - Um utilizador que se inscreve pela primeira vez é solicitado a fornecer um e-mail de recuperação. 
   - Um utilizador que já se inscreveu mas não forneceu um e-mail de recuperação é solicitado a fornecer um após a sua assinatura.

4. Introduza um endereço de e-mail e, em seguida, selecione **Enviar código de verificação**. Verifique se um código é enviado para a caixa de entrada de e-mail que forneceu. Recupere o código e introduza-o na caixa **de códigos de verificação.** Em seguida, **selecione Verificar código**.

## <a name="next-steps"></a>Passos seguintes

- [Adicionar fornecedores de identidade externos](tutorial-add-identity-providers.md)
- [Criar um fluxo de utilizador](tutorial-create-user-flows.md)