---
title: Tutorial para configurar Azure Ative Directy B2C com Nevis
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação Azure AD B2C com o Nevis para autenticação sem palavras-passe
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 282ec6a25dc381dc51f28534d272bae57d2e792e
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98674998"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Tutorial para configurar Nevis com Azure Ative Directory B2C para autenticação sem palavras-passe

Neste tutorial de amostra, aprenda a estender o Azure AD B2C com  [o Nevis](https://www.nevis.net/solution/authentication-cloud) para permitir a autenticação sem palavras-passe. A Nevis fornece uma experiência de utilizador final de primeira e totalmente marcada com a aplicação Nevis Access para fornecer autenticação forte do cliente e cumprir os requisitos de transação da Direção 2 (PSD2) dos Serviços de Pagamento.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma [conta de julgamento](https://www.nevis-security.com/aadb2c/) de Nevis

- Uma assinatura AD Azure. Se não tiver uma, obtenha uma [conta gratuita.](https://azure.microsoft.com/free/)

- Um [inquilino Azure AD B2C](./tutorial-create-tenant.md) que está ligado à sua assinatura Azure.

- Ambiente Azure AD B2C configurado para a utilização de [políticas personalizadas,](./custom-policy-get-started.md)se desejar integrar o Nevis no seu fluxo de políticas de inscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste cenário, adicione uma aplicação de acesso totalmente marcada à sua aplicação back-end para autenticação sem palavras-passe. Os seguintes componentes compõem a solução:

- Um inquilino Azure AD B2C, com uma política combinada de entrada e inscrição para o seu back-end
- Caso Nevis e sua API REST para melhorar Azure AD B2C
- A sua própria app de acesso à marca

O diagrama mostra a implementação.

![Fluxo de entrada de senha de alto nível com Azure AD B2C e Nevis](./media/partner-nevis/nevis-architecture-diagram.png)

|Passo | Description |
|:-----| :-----------|
| 1. | Um utilizador tenta iniciar seduca ou inscrever-se numa aplicação através da política de inscrição e inscrição do Azure AD B2C.
| 2. | Durante a inscrição, a Aplicação de Acesso Nevis está registada no dispositivo do utilizador utilizando um código QR. Uma chave privada é gerada no dispositivo do utilizador e é usada para assinar os pedidos do utilizador.
| 3. |  A Azure AD B2C utiliza um perfil técnico RESTful para iniciar o login com a solução Nevis.
| 4. | O pedido de login é enviado para a aplicação de acesso, seja como uma mensagem de impulso, código QR ou como uma ligação profunda.
| 5. | O utilizador aprova a tentativa de inscrição com a sua biometria. Uma mensagem é então devolvida a Nevis, que verifica o login com a chave pública armazenada.
| 6. | A Azure AD B2C envia um último pedido à Nevis para confirmar que o login foi concluído com sucesso.
| 7. |Com base na mensagem de sucesso/falha do utilizador Azure AD B2C é concedido/negado acesso à aplicação.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Integre o seu inquilino Azure AD B2C

### <a name="onboard-to-nevis"></a>A bordo de Nevis 

[Inscreva-se numa conta Nevis.](https://www.nevis-security.com/aadb2c/)
Receberá dois e-mails:

1. Uma notificação de conta de gestão

2. Um convite para aplicativos móveis.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Adicione o seu inquilino Azure AD B2C à sua conta Nevis

1. A partir do e-mail de teste da conta de gestão Nevis, copie a sua chave de gestão para a sua área de transferência.

2. Abra https://console.nevis.cloud/ num browser.

3. Inscreva-se na consola de gestão com a sua chave.

4. Selecione **adicionar instância**

5. Selecione o caso recém-criado para abri-lo.

6. Na barra de navegação lateral, selecione **Integrações Personalizadas**

7. **Selecione Adicionar integração personalizada.**

8. Para nome de integração, insira o nome do seu inquilino Azure AD B2C.

9. Para URL/Domínio, insira `https://yourtenant.onmicrosoft.com`

10. Selecione **Seguinte**

>[!NOTE]
>Vai precisar do sinal de acesso nevis mais tarde.

11. Selecione **Concluído**.

### <a name="install-the-nevis-access-app-on-your-phone"></a>Instale a aplicação Nevis Access no seu telefone

1. A partir do e-mail de teste da aplicação móvel Nevis, abra o convite **da aplicação Test Flight.**

2. Instale a aplicação.

3. Siga as instruções dadas para instalar a aplicação Nevis Access.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Integre a Azure AD B2C com Nevis

1. Abra o [portal do Azure](https://portal.azure.com/).

2. Mude para o seu inquilino Azure AD B2C. Certifique-se de que selecionou o inquilino certo, já que o inquilino Azure AD B2C normalmente está em um inquilino separado.

3. No menu, selecione **Identity Experience Framework (IEF)**

4. Selecione **teclas de política**

5. **Selecione Adicionar** e crie uma nova chave com as seguintes definições:

      a. Selecione **Manual** em Opções

      b. Definir Nome para **AuthCloudAccessToken**

      c. Cole o **token de acesso de Nevis** anteriormente armazenado no campo Secreto

      d. Para a utilização da chave **selecione encriptação**

      e. Selecione **Criar**

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>Configure e carreguma o nevis.html para o armazenamento de bolhas Azure

1. No seu Ambiente de Identidade (IDE), vá à pasta [**da política.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Abra o [**ficheironevis.html.**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html)

3. Substitua o  **authentication_cloud_url** pelo URL da sua consola Nevis Admin - `https://<instance_id>.mauth.nevis.cloud` .

4. **Guarde** as alterações no ficheiro.

5. Siga as [instruções](./customize-ui-with-html.md#2-create-an-azure-blob-storage-account) e carrehe o ficheiro **nevis.html** para o seu armazenamento de bolhas Azure.

6. Siga as [instruções](./customize-ui-with-html.md#3-configure-cors) e ative a partilha de recursos de origem cruzada (CORS) para este ficheiro.

7. Uma vez que o upload esteja concluído e o CORS esteja ativado, selecione o ficheiro **nevis.html** na lista.

8. No **separador Visão Geral,** junto ao **URL,** selecione o ícone do link de **cópia.**

9. Abra o link num novo separador de navegador para se certificar de que apresenta uma caixa cinzenta.

>[!NOTE]
>Vai precisar da ligação blob mais tarde.

### <a name="customize-your-trustframeworkbasexml"></a>Personalize o seu TrustFrameworkBase.xml

1. No seu IDE, vá à pasta da [**política.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Abra o ficheiro [**TrustFrameworkBase.xml.**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml)

3. Substitua **o seutenante** pelo nome da sua conta de inquilino Azure no **TenantId**.

4. Substitua **o seutenant** pelo nome da sua conta de inquilino Azure em **PublicPolicyURI**.

5. Substitua todas as **instâncias authentication_cloud_url** pelo URL da sua consola Nevis Admin

6. **Guarde** as alterações no seu ficheiro.

### <a name="customize-your-trustframeworkextensionsxml"></a>Personalize o seu TrustFrameworkExtensions.xml

1. No seu IDE, vá à pasta da [**política.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Abra o ficheiro [**TrustFrameworkExtensions.xml.**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml)

3. Substitua **o seutenante** pelo nome da sua conta de inquilino Azure no **TenantId**.

4. Substitua **o seutenant** pelo nome da sua conta de inquilino Azure em **PublicPolicyURI**.

5. Sob **a BasePolicy,** no **TenantId,** também _substitua o seutenant_ pelo nome da sua conta de inquilino Azure.

6. Em **BuildingBlocks,** substitua **loaduri** pelo URL de ligação blob do seu _nevis.html_ na sua conta de armazenamento de bolhas.

7. **Guarde** o ficheiro.

### <a name="customize-your-signuporsigninxml"></a>Personalize o seu SignUpOrSignin.xml

1. No seu IDE, vá à pasta da [**política.**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)

2. Abra o ficheiro [**SignUpOrSignin.xml.**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml)

3. Substitua **o seutenante** pelo nome da sua conta de inquilino Azure no **TenantId**.

4. Substitua **o seutenant** pelo nome da sua conta de inquilino Azure em **PublicPolicyUri**.

5. Sob **a BasePolicy,** em **TenantId,** também **substitua o seutenant** pelo nome da sua conta de inquilino Azure.

6. **Guarde** o ficheiro.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Faça o upload das suas políticas personalizadas para Azure AD B2C

1. Abra a sua casa [de inquilinoS Azure AD B2C.](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview)

2. Selecione **o Quadro de Experiência de Identidade.**

3. Selecione **a política personalizada do Upload**.

4. Selecione o ficheiro **TrustFrameworkBase.xml** modificado.

5. Selecione a **política de substituição de substituição se já existir** caixa de verificação.
6. Selecione **Carregar**.

7. Repita os passos 5 e 6 para **TrustFrameworkExtensions.xml**.

8. Repita os passos 5 e 6 para **SignUpOrSignin.xml**.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Criação de conta de teste e configuração de aplicações nevis Access

1. Abra a sua casa [de inquilinoS Azure AD B2C.](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview)

2. Selecione **o Quadro de Experiência de Identidade.**

3. Percorra as políticas personalizadas e selecione **B2C_1A_signup_signin**.

4. Selecione **Executar agora**.

5. Na janela popup, **selecione Inscreva-se agora**.

6. Adicione o seu endereço de e-mail.

7. Selecione **Enviar código de verificação**.

8. Copiar o código de verificação do e-mail.

9. Selecione **Verificar**.

10. Preencha o formulário com a sua nova senha e nome de Exibição.

11. Selecione **Criar**.

12. Serão levados para a página de verificação do código QR.

13. No seu telefone, abra a **aplicação Nevis Access**.

14. Selecione **Face ID**.

15. Quando o ecrã diz que o **registo do Autenticador foi bem sucedido,** selecione **Continue**.

16. No seu telefone, autentica com o rosto novamente.

17. Você será levado para a página de aterragem [jwt.ms](https://jwt.ms) que mostra os seus detalhes descodificados.

### <a name="test-the-pure-passwordless-sign-in"></a>Teste o sign-in sem palavra-passe puro

1. No quadro **de experiência de** identidade, selecione o **B2C_1A_signup_signin**.

2. Selecione **Executar agora**.

3. Na janela popup, selecione **Autenticação Sem Palavras-Passe**.

4. Introduza o seu endereço de e-mail.

5. Selecione **Continuar**.

6. No seu telefone, em notificações, selecione **a notificação da aplicação Nevis Access**.

7. Autenticar com o seu rosto.

8. Será automaticamente levado para a página de aterragem [jwt.ms](https://jwt.ms) que exibe os seus tokens.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Começar com políticas personalizadas em Azure AD B2C](./custom-policy-get-started.md?tabs=applications)