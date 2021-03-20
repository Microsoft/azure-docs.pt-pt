---
title: seu nome OpenID Connect com Azure Ative Directory B2C
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação Azure AD B2C com o seu OIDC utilizando client_secret política de fluxo de utilizador. itsme é uma aplicação digital de identificação. Permite fazer login de forma segura sem leitores de cartões, palavras-passe, autenticação de dois fatores e vários códigos PIN.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba7875caa6a1db7638bfeafcfea1efa7b2462152
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87489520"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>Configure o seu nome OpenID Connect (OIDC) com o Azure Ative Directory B2C

A sua aplicação de ID digital deme permite-lhe iniciar sedutação segura sem leitores de cartões, palavras-passe, autenticação de dois fatores ou vários códigos PIN. A sua aplicação de nome fornece uma forte autenticação do cliente com uma identidade verificada. Neste artigo, aprenda a integrar a autenticação Azure AD B2C com o seu nome OpenID Connect (OIDC) utilizando uma política de fluxo secreto do utilizador do cliente.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* [Um inquilino Azure AD B2C](tutorial-create-tenant.md) que está ligado à sua assinatura Azure.
* O seu ID do Cliente, também conhecido como Código Parceiro, fornecido pelo seu nome.
* O seu código de serviço fornecido pelo seu nome.
* O segredo do seu cliente para a sua conta de mim.

## <a name="scenario-description"></a>Descrição do cenário

![diagrama de arquitetura sua](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| Passo | Descrição |
|------|------|
|1     | No seu website ou aplicação, inclua o Login com o **botão de me,** adaptando-se no fluxo de utilizador Azure AD B2C. O fluxo de interação começa quando o utilizador clica neste botão.  |
|2     | A Azure AD B2C inicia o fluxo de ligação OpenID enviando um pedido autorizado à API secreta do seu cliente. Um ponto final de configuração conhecido/OpenID está disponível contendo informações sobre os pontos finais.  |
|3     | O ambiente do seu nome redireciona o utilizador para a sua página de identificação, permitindo ao utilizador preencher o seu número de telefone.  |
|4     | O ambiente do seu nome recebe o número de telefone do utilizador e valida a correção.  |
|5     | Se o número de telefone pertencer a um utilizador ativo, é criada uma Ação para a sua aplicação.  |
|6     | O utilizador abre a sua app de nome, verifica o pedido e confirma a ação.  |
|7     |  A aplicação informa o seu ambiente de forma a ação foi confirmada. |
|8     |  O seu ambiente devolve o código autorizado OAuth ao Azure AD B2C. |
|9     |  Utilizando o código autorizado, o Azure AD B2C faz um pedido simbólico. |
| 10 | O ambiente do seu nome verifica o pedido simbólico e, se ainda válido, devolve o token de acesso OAuth e o token de ID contendo as informações do utilizador solicitadas. |
| 11 | Finalmente, o utilizador é redirecionado para o url de redirecionamento como um utilizador autenticado.  |
|   |   |

## <a name="onboard-with-itsme"></a>A bordo com o seu nome

1. Para criar uma conta com o seu nome, visite o seu nome no [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace)

2. Ative a sua conta de identificação enviando um e-mail para onboarding@itsme.be . Receberá um **código de parceiro** e código de **serviço** que será necessário para a sua configuração B2C.

3. Após a ativação da sua conta de parceiro, receberá um e-mail com um link único para o segredo do **cliente.**

4. Siga as instruções na [sua forma](https://business.itsme.be/en) para completar a configuração.

## <a name="integrate-with-azure-ad-b2c"></a>Integrar com o Azure AD B2C

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>Criar um novo Fornecedor de Identidade em Azure AD B2C

> [!NOTE]
> Se ainda não tiver um, [crie um inquilino Azure AD B2C](tutorial-create-tenant.md) que esteja ligado à sua assinatura Azure.

1. Certifique-se de que está a usar o diretório que contém o inquilino Azure AD B2C. Selecione o filtro **de subscrição Diretório +** no menu superior e escolha o diretório que contém o seu inquilino Azure AD B2C.

2. Nos **serviços Azure**, selecione **Azure AD B2C** (ou selecione **Mais serviços** e use a caixa de pesquisa **de todos os serviços** para procurar *Azure AD B2C).*

3. Selecione **os fornecedores de identidade** e, em seguida, selecione novo fornecedor **OpenID Connect**.

4. Preencha o formulário com as seguintes informações:

   |Propriedade | Valor |
   |------------ |------- |
   | Nome | itsme |
   | URL de metadados | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>onde `<environment>` esteja `e2e` (ambiente de ensaio) ou `prd` (produção)  |
   | ClientID     | O seu **ID do Cliente,** também conhecido como **código parceiro**  |
   | Segredo do Cliente | O seu **client_secret** |
   | Âmbito  | serviço openid:YOURSERVICECODE profile email [telefone] [endereço]  |
   |Tipo de Resposta | code |
   |Modo de Resposta | consulta |
   |Dica de domínio | *Pode deixar isto vazio.* |
   |UserID | sub |
   |Nome a Apresentar | name |
   |Nome próprio | given_name |
   |Apelido | family_name |
   |E-mail | e-mail|

5. Selecione **Guardar**.

### <a name="configure-a-user-flow"></a>Configure um fluxo de utilizador

1. No seu inquilino Azure AD B2C, em **Políticas,** selecione **fluxos de utilizador**.

2. Selecione **novo fluxo de utilizador**.

3. Selecione **Iniciar Sessão e iniciar sessão,** selecione uma versão e, em seguida, selecione **Criar**.

4. Insira um **nome**.

5. Na secção **de fornecedores de identidade,** selecione **o seu nome.**

6. Selecione **Criar**.

7. Abra o fluxo de utilizador recém-criado selecionando o nome de fluxo do utilizador.

8. Selecione **Propriedades** e ajuste os seguintes valores:

   * Alterar **o acesso & as vidas de id simbólicas (minutos)** para **5**.
   * Alterar **A vida útil da janela de deslizamento do token para** não **expirar**.

### <a name="register-an-application"></a>Registar uma aplicação

1. No seu inquilino B2C, em **Manage,** selecione **Registos de Aplicações**  >  **Novo registo.**

2. Forneça um **Nome** para a aplicação e **insira o seu URI de redirecionamento.** Para efeitos de teste, insira `https://jwt.ms` .

3. Certifique-se de que a autenticação de vários fatores está **desativada**.

4. Selecione **Registar**.

   a. Para efeitos de teste, selecione **Autenticação**, e sob **o Implicit Grant**, selecione as caixas de verificação De **Tokens** de Acesso e **ID Tokens.**  

   b. Selecione **Guardar**.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. No seu inquilino B2C, ao abrigo de **Políticas** selecione **fluxos de utilizador**.

2. Selecione o fluxo de utilizador criado anteriormente.

3. Selecione **Executar o fluxo do utilizador**.

   a. **Aplicação**: *selecione a aplicação registada*

   b. **URL de resposta**: *selecione o URL de redirecionamento*

4. A página **de identificação do** seu nome aparece.  

5. Introduza o seu número de telemóvel e selecione **enviar**.

6. Confirme a ação na sua aplicação de nome.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

* [Políticas personalizadas no Azure AD B2C](custom-policy-overview.md)

* [Começar com políticas personalizadas em Azure AD B2C](custom-policy-get-started.md?tabs=applications)