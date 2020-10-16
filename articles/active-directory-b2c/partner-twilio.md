---
title: Twilio Verificar App com Azure Ative Directory B2C
titleSuffix: Azure AD B2C
description: Saiba como integrar uma aplicação de pagamento online de amostra em Azure AD B2C com a API Twilio Check. Cumprir os requisitos de transação PSD2 (Direção de Serviços de Pagamento 2) através de ligação dinâmica e autenticação forte do cliente.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 953653a758577ed3d48ca2d81403b4cb363ea294
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259073"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>Integração da App Twilio Check com o Azure Ative Directory B2C

Nesta passagem, aprenda a integrar uma aplicação de pagamento online de amostra no Azure Ative Directory B2C (Azure AD B2C) com a API Twilio Check. Ao utilizar a App Twilio Check, os clientes Azure AD B2C podem cumprir os requisitos de transação PSD2 (Diretiva de Serviços de Pagamento 2) através de ligações dinâmicas e autenticação forte do cliente.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* [Um inquilino Azure AD B2C](tutorial-create-tenant.md) que está ligado à sua assinatura Azure.
* Uma [conta de julgamento](https://www.twilio.com/try-twilio) em Twilio.

## <a name="scenario-description"></a>Descrição do cenário

Os seguintes componentes compõem a solução Twilio:

- .NET [PSD2 demo web app](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App), que fornece a capacidade de iniciar sposição ou inscrição, e realizar uma transação de alto risco manequim.
- A política [de inscrição combinada](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy)AZURE AD B2C .
- Política Azure AD B2C integrada com Twilio Verificar API utilizando `id_token_hint` .
- .NET Web App, que acolhe um `.well-known` ponto final OpenIdConnect para permitir a validação de um `id_token_hint` .


    ![fluxo twilio](media/partner-twilio/twilio-flow.png)

| Passo | Descrição |
|------|------|
| 1     | O utilizador inicia a inscrição ou inscrição na app DEMO PSD2. O utilizador é autenticado através da política de inscrição combinada Azure AD B2C. Um símbolo é devolvido ao pedido. Na inscrição, o número de telefone do utilizador é verificado através de SMS/Telefone e gravado na sua conta Azure AD B2C.     |
| 2     | O utilizador inicia uma transação de alto risco, como uma transferência de $50,00. O token de acesso atual do utilizador é avaliado para o PolicyId para determinar se o utilizador já autenticou através de uma Step-Up política personalizada.     |
| 3     | A aplicação regista o valor da transação e o beneficiário, $50,00 e John Doe, e gera um token assinado. Este símbolo é chamado de `id_token_hint` e contém a reivindicação `amount:$500, payee:john doe` . O `id_token_hint` é enviado juntamente com o pedido à política personalizada Azure AD B2C, que está integrada com twilio.     |
| 4     | A azure AD B2C verifica a assinatura do id_token_hint verificando as aplicações `/.well-known` OpenId Connect endpoint. Após verificação, extrai as reclamações deste token, nomeadamente o `amount` e `payee` . O utilizador verá uma página para verificar o seu número de telemóvel através de sms.     |
| 5     | O utilizador solicita a verificação do seu número de telefone através de sms, e a Azure AD B2C faz um pedido de API REST para o ponto final da API de Verificação de Twilio. Também envia a transação `amount` e como parte do processo PSD2 para gerar o Código De passagem única `payee` (OTP). Twilio envia uma mensagem SMS para o número de telefone registado do utilizador.     |
| 6     |  O utilizador introduz a OTP recebida na sua mensagem SMS e submete-a ao Azure AD B2C. A Azure AD B2C faz um pedido de API com esta OTP para verificar a API de Twilio para verificar se a OTP está correta. Finalmente, é emitido um sinal para a aplicação, com um novo PolicyId a significar que o utilizador reforçou a sua autenticação.    |
|      |      |

## <a name="onboard-with-twilio"></a>A bordo com Twilio

1. Obtenha uma [conta de julgamento](https://www.twilio.com/try-twilio) em Twilio.

2. Compre um número de telefone em Twilio como descrito [neste artigo](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console)

3. Navegue para [verificar a API](https://www.twilio.com/console/verify/services) na consola Twilio e siga [as instruções](https://www.twilio.com/docs/verify/verifying-transactions-psd2) para criar um serviço e ativar a opção PSD2.  

## <a name="configure-the-psd2-demo-app"></a>Configurar a App de Demonstração PSD2

1. Abra a solução B2C-WebAPI-DotNet e substitua os seguintes valores pelos valores específicos do seu próprio arrendatário no web.config:

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. A [aplicação web](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App) também acolhe o gerador de sugestões de iD e ponto final de metadados.
   - Crie o seu certificado de assinatura como descrito nesta [descrição da amostra](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate).
   - Atualize as seguintes linhas com base no seu certificado no web.config:
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. Faça o upload da aplicação de demonstração para o seu fornecedor de hospedagem de eleição. A orientação para o Serviço de Aplicações Azure é fornecida [nesta descrição da amostra,](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service)incluindo instruções para o upload do seu certificado.

4. Atualize o seu registo de pedidos Azure AD B2C adicionando um URL de resposta equivalente ao URL no qual o pedido está hospedado.

5. Abra os [ficheiros de apólice](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy) e substitua todas as instâncias com o nome do  `contoso` seu inquilino.

6. Encontre o perfil técnico da API Twilio REST **Custom-SMS-Enroll.** Atualize o  `ServiceURL`   com o seu Twilio AccountSID e o Número De Para o seu número de telefone adquirido.

7. Encontre os perfis técnicos da API Twilio REST, **TwilioRestAPI-Check-Step1**   e **TwilioRestAPI-Check-Step2**, e atualize o com o  `ServiceURL`   seu Twilio AccountSID.

## <a name="integrate-with-azure-ad-b2c"></a>Integrar com o Azure AD B2C

Adicione os ficheiros de política ao Azure AD B2C:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) como administrador global do inquilino do Azure AD B2C.

2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.

3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.

4. Navegue para **Azure AD B2C**  >  **Identity Experience Framework**Policy  >  **Keys**.

5. Adicione uma nova chave com o nome **B2cRestTwilioClientId**. Selecione **manual**, e forneça o valor do Twilio AccountSID.

6. Adicione uma nova chave com o nome **B2cRestTwilioClientSecret**. Selecione **manual**, e forneça o valor do TOKEN Twilio AUTH.

7. Faça o upload de todos os ficheiros de apólice para o seu inquilino.

8. Personalize o string no GenerateOTPMessageEnrol para a sua inscrição de texto SMS.

## <a name="test-the-solution"></a>Testar a solução

* Navegue pela sua aplicação e teste as ações de inscrição, inscrição e envio de dinheiro.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- Consulte o GitHub para amostras de código de [integração de Twilio](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2)  

- [Políticas personalizadas em AAD B2C](custom-policy-overview.md)

- [Começar com políticas personalizadas em AAD B2C](custom-policy-get-started.md?tabs=applications)
