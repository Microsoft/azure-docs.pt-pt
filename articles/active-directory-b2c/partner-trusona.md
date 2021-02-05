---
title: Diretório Ativo Trusona e Azure B2C
titleSuffix: Azure AD B2C
description: Saiba como adicionar Trusona como fornecedor de identidade no Azure AD B2C para permitir a autenticação sem palavras-passe.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 99368da8b4018e93ad537e4722ffefd476e61291
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573692"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>Integrando Trusona com Azure Ative Directory B2C

A Trusona é um fornecedor independente de fornecedores de software (ISV) que ajuda a garantir o acesso ao sing-in, permitindo a autenticação sem palavras-passe, a autenticação de vários fatores e a digitalização da licença digital. Neste artigo, você vai aprender a adicionar Trusona como um fornecedor de identidade em Azure AD B2C para ativar a autenticação sem palavras-passe.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* [Um inquilino Azure AD B2C](tutorial-create-tenant.md) que está ligado à sua assinatura Azure.
* Uma [conta de julgamento](https://www.trusona.com/) em Trusona

## <a name="scenario-description"></a>Descrição do cenário

Neste cenário, Trusona atua como um fornecedor de identidade para a Azure AD B2C para permitir a autenticação sem palavras-passe. Os seguintes componentes compõem a solução:

* Uma política de entrada combinada AD B2C da Azure AD B2C
* Trusona adicionada ao Azure AD B2C como fornecedor de identidade
* A aplicação trusona transferível

![Diagrama de arquitetura de Trusona](media/partner-trusona/trusona-architecture-diagram.png)

| Passo | Descrição |
|------|------|
|1     | Um utilizador tenta iniciar sção ou inscrever-se na aplicação. O utilizador é autenticado através da política de inscrição e inscrição Azure AD B2C. Durante a inscrição, é utilizado o endereço de e-mail previamente verificado do utilizador a partir da aplicação Trusona.     |
|2     | O Azure B2C redireciona o utilizador para o fornecedor de identidade Trusona OpenID Connect (OIDC) utilizando o fluxo implícito.     |
|3     | Para logins baseados em PC no ambiente de trabalho, Trusona exibe um código QR único, apátrida, animado e dinâmico para digitalizar com a app Trusona. Para logins móveis, Trusona usa um "link profundo" para abrir a app Trusona. Estes dois métodos são utilizados para o dispositivo e, em última análise, para a descoberta do utilizador.     |
|4     | O utilizador digitaliza o código QR apresentado com a aplicação Trusona.     |
|5     | A conta do utilizador encontra-se no serviço de nuvem Trusona e a autenticação está preparada.     |
|6     | O serviço de nuvem Trusona emite um desafio de autenticação ao utilizador através de uma notificação push enviada para a app Trusona:<br>a. O utilizador é solicitado com o desafio de autenticação. <br> b. O utilizador opta por aceitar ou rejeitar o desafio. <br> c. Pede-se ao utilizador que utilize a segurança do SO (por exemplo, biométrica, senha, PIN ou padrão) para confirmar e assinar o desafio com uma chave privada no ambiente Secure Enclave/Execução Fidedigna. <br> d. A aplicação Trusona gera uma carga útil anti-repetição dinâmica baseada nos parâmetros da autenticação em tempo real. <br> e. Toda a resposta é assinada (pela segunda vez) por uma chave privada no ambiente Secure Enclave/Execução Fidedigna e devolvida ao serviço de nuvem de Trusona para verificação.      |
|7     |  O serviço de nuvem Trusona redireciona o utilizador de volta para a aplicação de início com um id_token. Azure AD B2C verifica a id_token usando a configuração aberta publicada de Trusona como configurada durante a configuração do fornecedor de identidade.    |
|  |  |

## <a name="onboard-with-trusona"></a>A bordo com Trusona

1. Preencha o [formulário](https://www.trusona.com/) para criar uma conta Trusona e começar.

2. Descarregue a aplicação móvel Trusona da loja de aplicações. Instale a aplicação e registe o seu email.

3. Verifique o seu e-mail através do "link mágico" seguro enviado pelo software.  

4. Vá ao [painel de desenvolvimento da Trusona](https://dashboard.trusona.com) para se autosserviço.

5. **Selecione I'm Ready** e autenha-se com a sua app Trusona.

6. A partir do painel de navegação à esquerda, escolha **integrações OIDC.**

7. Selecione **Criar Integração de Ligação OpenID**.

8. Forneça um **Nome** à sua escolha e utilize as informações de domínio previamente fornecidas (por exemplo, Contoso) no **campo Anfitrião de Redirecionamento de Clientes**.  

   > [!NOTE]
   > O nome de domínio inicial do Azure Ative Directory é usado como anfitrião de redirecionamento de clientes.

9. Siga as instruções no guia de [integração de Trusona.](https://docs.trusona.com/integrations/aad-b2c-integration/) Quando solicitado, utilize o nome de domínio inicial (por exemplo, Contoso) referido no passo anterior.  

## <a name="integrate-with-azure-ad-b2c"></a>Integrar com o Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Adicionar um novo fornecedor de identidade

> [!NOTE]
> Se ainda não tiver um, [crie um inquilino Azure AD B2C](tutorial-create-tenant.md) que esteja ligado à sua assinatura Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.

2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.

4. Navegue para  >  os fornecedores de identidade **B2C do Tablier Azure Ative**  >  **Directory**.

3. Selecione **fornecedores de identidade**.

4. Selecione **Adicionar**.

### <a name="configure-an-identity-provider"></a>Configure um fornecedor de identidade  

1. **Selecione o tipo de fornecedor de identidade**  >  **OpenID Connect (Pré-visualização)**.

2. Preencha o formulário para configurar o fornecedor de identidade:  

   | Propriedade | Valor  |
   | :--- | :--- |
   | URL de metadados | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | ID de Cliente | Será enviado por e-mail de Trusona |
   | Âmbito | E-mail de perfil OpenID |
   | Tipo de resposta | Id_token |
   | Modo de resposta  | Form_post |

3. Selecione **OK**.  

4. Selecione **Mapear as reclamações deste fornecedor de identidade.**  

5. Preencha o formulário para mapear o fornecedor de identidade:

   | Propriedade | Valor  |
   | :--- | :--- |
   | UserID | Submarino  |
   | Nome a apresentar | apelido |
   | Nome próprio | given_name |
   | Apelido | Family_name |
   | Modo de resposta | e-mail |

6. Selecione **OK** para completar a configuração para o seu novo Fornecedor de identidade OIDC.

### <a name="create-a-user-flow-policy"></a>Criar uma política de fluxo de utilizador

Deverá agora ver a Trusona como um **novo Fornecedor de Identidade OpenID Connect** listado nos seus fornecedores de identidade B2C.

1. No seu inquilino Azure AD B2C, em **Políticas,** selecione **fluxos de utilizador**.

1. Selecione **novo fluxo de utilizador**.

1. Selecione **Iniciar Sessão e iniciar sessão,** selecione uma versão e, em seguida, selecione **Criar**.

1. Insira um **Nome** para a sua política.

1. Na secção **de fornecedores de identidade,** selecione o seu recém-criado **Fornecedor de Identidade Trusona.**

   > [!NOTE]
   > Como Trusona é inerentemente multi-factor, é melhor deixar a autenticação multi-factor desativada.

1. Selecione **Criar**.

1. Em **Atributos e Reclamações do Utilizador,** escolha **Mostrar mais**. No formulário, selecione pelo menos um atributo que especificou durante a configuração do seu fornecedor de identidade na secção anterior.

1. Selecione **OK**.  

### <a name="test-the-policy"></a>Testar a Política

1. Selecione a sua política recém-criada.

2. Selecione **Executar o fluxo do utilizador**.

3. No formulário, insira o URL de Resposta.

4. Selecione **Executar o fluxo do utilizador**. Devias ser redirecionado para a porta de entrada da Trusona OIDC. No gateway de Trusona, digitalize o código Secure QR exibido com a app Trusona ou com uma aplicação personalizada usando o SDK móvel Trusona.

5. Depois de digitalizar o código Secure QR, deverá ser redirecionado para o URL de resposta definido no passo 3.

## <a name="next-steps"></a>Passos seguintes  

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](custom-policy-overview.md)

- [Começar com políticas personalizadas em AAD B2C](custom-policy-get-started.md?tabs=applications)
