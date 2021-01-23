---
title: Tutorial para configurar Keyless com Azure Ative Directory B2C
titleSuffix: Azure AD B2C
description: Tutorial para configurar Keyless com Azure Ative Directory B2C para autenticação sem palavras-passe
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 2ab1b5ae87cee6265c965c45b706c7fbf10dfe3f
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98702959"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>Tutorial: Configurar Keyless com Azure Ative Directory B2C

Neste tutorial de amostra, fornecemos orientações sobre como configurar o Azure Ative Directory (AD) B2C com [Keyless](https://keyless.io/). Com o Azure AD B2C como fornecedor de Identidade, pode integrar o Keyless com qualquer uma das suas aplicações de clientes para fornecer uma autenticação verdadeiramente sem palavras-passe aos seus utilizadores.

A solução **Keyless Keyless Zero-Knowledge Biometric (ZKB™)** fornece a autenticação multifactor sem palavras-passe que elimina a fraude, phishing e reutilização credencial – tudo ao mesmo tempo que melhora a experiência do cliente e protege a sua privacidade.

## <a name="pre-requisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma subscrição do Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- Um [inquilino Azure AD B2C.](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) O inquilino deve estar ligado à sua assinatura Azure.

- Um inquilino de nuvem sem chave, obtenha uma [conta de teste](https://keyless.io/go)grátis.

- A aplicação Keyless Authenticator instalada no dispositivo do utilizador.

## <a name="scenario-description"></a>Descrição do cenário

A integração Keyless inclui os seguintes componentes:

- Azure AD B2C – O servidor de autorização, responsável pela verificação das credenciais do utilizador, também conhecido como fornecedor de identidade.

- Aplicações web e móveis – As suas aplicações móveis ou web que escolhe proteger com Keyless e Azure AD B2C.

- A aplicação móvel Keyless – A aplicação móvel Keyless será utilizada para autenticação nas aplicações ativadas AZURE AD B2C.

O seguinte diagrama de arquitetura mostra a implementação.

![Imagem mostra diagrama de arquitetura keyless](./media/partner-keyless/keyless-architecture-diagram.png)

|Passo | Descrição |
|:-----| :-----------|
| 1. | O utilizador chega a uma página de login. Os utilizadores selecionam o pré-in/inscrição e introduzem o nome de utilizador
| 2. | A aplicação envia os atributos do utilizador ao Azure AD B2C para verificação de identidade.
| 3. | O Azure AD B2C recolhe os atributos do utilizador e envia os atributos a Keyless para autenticar o utilizador através da aplicação móvel Keyless.
| 4. | A Keyless envia uma notificação push para o dispositivo móvel do utilizador registado para uma autenticação que preserva a privacidade sob a forma de uma varredura biométrica facial.
| 5. | Após o utilizador responder à notificação push, o utilizador é concedido ou negado acesso à aplicação do cliente com base nos resultados de verificação.

## <a name="integrate-with-azure-ad-b2c"></a>Integrar com o Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Adicionar um novo fornecedor de identidade

Para adicionar um novo fornecedor de identidade, siga estes passos:

1. Inscreva-se no **[portal Azure](https://portal.azure.com/#home)** como administrador global do seu inquilino Azure AD B2C.

2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.

4. Navegue para **dashboard**  >  **Azure Ative Directory B2C**  >   **Fornecedores de identidade**

5. Selecione **fornecedores de identidade**.

6. Selecione **Adicionar**.

### <a name="configure-an-identity-provider"></a>Configure um fornecedor de identidade

Para configurar um fornecedor de identidade, siga estes passos:

1. **Selecione o tipo de fornecedor de identidade**  >  **OpenID Connect (Pré-visualização)**
2. Preencha o formulário para configurar o fornecedor de identidade:

   |Propriedade | Valor |
   |:-----| :-----------|
   | Nome   | Sem chave |
   | URL de metadados | Insira o URI da app de autenticação keyless hospedada, seguido do caminho específico, como https://keyless.auth/.well-known/openid-configuration |
   | Segredo do Cliente | O segredo associado à instância de autenticação sem chave - não é o mesmo que o configurado anteriormente. Insira uma cadeia complexa à sua escolha. Este segredo será usado mais tarde na configuração do Contentor Keyless.|
   | ID de Cliente | A identificação do cliente. Este ID será usado mais tarde na configuração do Recipiente Keyless.|
   | Âmbito | openid |
   | Tipo de resposta | id_token |
   | Modo de resposta | form_post|

3. Selecione **OK**.

4. Selecione **Mapear as reclamações deste fornecedor de identidade.**

5. Preencha o formulário para mapear o fornecedor de identidade:

   |Propriedade | Valor |
   |:-----| :-----------|
   | UserID    | A partir da subscrição |
   | Nome a apresentar | A partir da subscrição |
   | Modo de resposta | A partir da subscrição |

6. **Selecione Guardar** para completar a configuração do seu novo fornecedor de identidade Open ID Connect (OIDC).

### <a name="create-a-user-flow-policy"></a>Criar uma política de fluxo de utilizador

Deverá agora ver Keyless como um novo fornecedor de identidade OIDC listado nos seus fornecedores de identidade B2C.

1. No seu inquilino Azure AD B2C, em **Políticas,** selecione **fluxos de utilizador**.

2. Selecione **Novo** fluxo de utilizador.

3. Selecione **Iniciar Sessão e iniciar sessão,** selecione uma **versão** e, em seguida, selecione **Criar**.

4. Insira um **Nome** para a sua política.

5. Na secção de fornecedores de identidade, selecione o seu fornecedor de identidade sem chave recém-criado.

6. Configurar os parâmetros do fluxo do seu Utilizador. Insira um nome e selecione o fornecedor de identidade que criou. Também pode adicionar o endereço de e-mail. Neste caso, o Azure não redirecionará o procedimento de login diretamente para Keyless, em vez disso, mostrará um ecrã onde o utilizador pode escolher a opção que gostaria de usar.

7. Deixe o campo **de autenticação multi-factor** como está.

8. **Selecione Executar políticas de acesso condicional**

9. Sob **atributos do Utilizador e reclamações simbólicas,** selecione **Endereço de E-mail** na opção atributo Recolha. Pode adicionar todos os atributos que o Azure Ative Directory pode recolher sobre o utilizador juntamente com as alegações de que o Azure AD B2C pode voltar à aplicação do cliente.

10. Selecione **Criar**.

11. Depois de uma criação bem sucedida, selecione o seu novo **fluxo de Utilizador**.

12. No painel esquerdo, selecione **Pedidos de Aplicação**. Em opções, marque a caixa de verificação **de e-mail** e selecione **Save**.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Abra o inquilino Azure AD B2C e em Políticas selecione Quadro de Experiência de Identidade.

2. Selecione o seu Signo De Assinatura de Inscrição anteriormente criado.

3. Selecione Executar o fluxo do utilizador e selecione as definições:

   a. Aplicação: selecione a aplicação registada (a amostra é JWT)

   b. URL de resposta: selecione o URL de redirecionamento

   c. Selecione Executar o fluxo do utilizador.

4. Passe pelo fluxo de inscrição e crie uma conta

5. Keyless será chamado durante o fluxo, após a criação do atributo do utilizador. Se o fluxo estiver incompleto, verifique se o utilizador não está guardado no diretório.

## <a name="next-steps"></a>Próximos passos

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Começar com políticas personalizadas em Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
