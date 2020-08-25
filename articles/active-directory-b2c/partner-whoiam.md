---
title: Tutorial para configurar Azure Ative Directory B2C com whoIam
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação Azure AD B2C com o whoIam para verificação do utilizador
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 6545c5f40004dc50904618a8ea734eb73eeee933
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817826"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Tutorial para configurar WhoIAM com Azure Ative Directory B2C

Neste tutorial de amostra, fornecemos orientações sobre como configurar o Sistema de Gestão de Identidade da Marca [WhoIAM](https://www.whoiam.ai/brims/) (BRIMS) no seu ambiente e integrá-lo com a Azure AD B2C.

O BRIMS da WhoIAM é um conjunto de aplicações e serviços que são implementados no seu ambiente. Fornece verificação de voz, SMS e e-mail da sua base de utilizadores. A BRIMS trabalha em conjunto com a sua solução de gestão de identidade e acesso existente e é agnóstica de plataforma.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- [Um inquilino Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) que está ligado à sua assinatura Azure.

- Uma [conta de julgamento](https://www.whoiam.ai/contact-us/) da WhoIAM

## <a name="scenario-description"></a>Descrição do cenário

A integração do WhoIAM inclui os seguintes componentes:

- Inquilino Azure AD B2C – O servidor de autorização, responsável pela verificação das credenciais do utilizador com base em políticas personalizadas definidas no arrendatário. Também é conhecido como o fornecedor de identidade.

- Um portal de administração para gerir clientes e suas configurações

- Um serviço API que expõe várias funcionalidades através de pontos finais  

- Azure Cosmos DB que é usado como backend tanto para o portal de administração BRIMS como para o serviço API

O seguinte diagrama de arquitetura mostra a implementação.

![screenshot para whoiam-arquitetura-diagrama](media/partner-whoiam/whoiam-architecture-diagram.png)

|Passo | Descrição |
|:-----| :-----------|
| 1. | O utilizador chega a uma página de login. O utilizador inicia um pedido de inscrição ou inscrição numa aplicação que utiliza o Azure AD B2C como seu fornecedor de identidade.
| 2. | Como parte da autenticação, o utilizador solicita para verificar a propriedade do seu e-mail ou telefone ou usar a sua voz como fator de verificação biométrica.  
| 3. | Azure AD B2C faz uma chamada para o serviço API BRIMS passando o endereço de e-mail do utilizador, número de telefone e sua gravação de voz
| 4. | A BRIMS utiliza configurações pré-definidas, tais como e-mails totalmente personalizáveis e modelos DE SMS para interagir com o utilizador no respetivo idioma que é consistente com o aspeto e a sensação da aplicação.
| 5. | Uma vez concluída a verificação de identidade de um utilizador, a BRIMS devolve um token ao Azure AD B2C indicando o resultado da verificação. O Azure AD B2C concede então ao utilizador acesso à app ou falha a sua tentativa de autenticação.  

## <a name="onboard-with-whoiam"></a>A bordo com o WhoIAM

1. Contacte [a WhoIAM](https://www.whoiam.ai/contact-us/) e crie uma conta BRIMS.

2. Uma vez criada uma conta, utilize as diretrizes de embarque disponibilizadas e configuure os seguintes serviços Azure:

    - [Cofre de Chaves Azure](https://azure.microsoft.com/services/key-vault/) - Usado para armazenamento seguro de senhas, tais como, palavras-passe do serviço de correio.

    - [Azure App Service](https://azure.microsoft.com/services/app-service/) - Usado para acolher os serviços de porta de administração da BRIMS

    - [Diretório Ativo Azure](https://azure.microsoft.com/services/active-directory/) - Usado para autenticar utilizadores administrativos para o portal de administração

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) - Usado para armazenar e recuperar configurações

    - [Insights de Aplicação](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#:~:text=Application%20Insights%2C%20a%20feature%20of%20Azure%20Monitor%2C%20is,professionals.%20Use%20it%20to%20monitor%20your%20live%20applications.) (Opcional) - Usado para iniciar sessão tanto no portal API como no portal de administração

3. Implemente a API BRIMS e o portal de administração BRIMS no seu ambiente Azure.

4. As amostras de política personalizada Azure AD B2C estão disponíveis na sua documentação de bordo BRIMS. Siga o documento para configurar a sua aplicação e utilize a plataforma BRIMS para verificação de identidade do utilizador.  

Para mais informações sobre a BRIMS da WhoIAM, consulte [a documentação](https://www.whoiam.ai/brims/) do produto

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Abra o inquilino Azure AD B2C e em Políticas selecione **Quadro de Experiência de Identidade.**

2. Selecione o **seu Signo De Assinatura de Inscrição**anteriormente criado.

3. Selecione **Executar o fluxo do utilizador** e selecione as definições:

   a. **Aplicação**: selecione a aplicação registada (a amostra é JWT)

   b. **URL de resposta**: selecione o **URL de redirecionamento**

   c. Selecione **Executar o fluxo do utilizador**.

4. Passe pelo fluxo de inscrição e crie uma conta

5. O serviço BRIMS será chamado durante o fluxo, após a criação do atributo do utilizador. Se o fluxo estiver incompleto, verifique se o utilizador não está guardado no diretório.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Começar com políticas personalizadas em Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
