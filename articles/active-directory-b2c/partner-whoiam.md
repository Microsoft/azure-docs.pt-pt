---
title: Tutorial para configurar Azure Ative Directy B2C com WhoIAM
titleSuffix: Azure AD B2C
description: Neste tutorial, aprenda a integrar a autenticação Azure AD B2C com o WhoIAM para verificação do utilizador.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: d1b2c7513562e951e1098cf327780387ddf6a495
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953547"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Tutorial para configurar WhoIAM com Azure Ative Directory B2C

Neste tutorial de amostra, fornecemos orientações sobre como configurar o Sistema de Gestão de Identidade da Marca [WhoIAM](https://www.whoiam.ai/brims/) (BRIMS) no seu ambiente e integrá-lo com o Ative Directory B2C (Azure AD B2C).

BRIMS é um conjunto de aplicações e serviços que são implantados no seu ambiente. Fornece verificação de voz, SMS e e-mail da sua base de utilizadores. A BRIMS trabalha em conjunto com a sua solução de gestão de identidade e acesso existente e é agnóstica de plataforma.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- [Um inquilino Azure AD B2C](./tutorial-create-tenant.md) que está ligado à sua assinatura Azure.

- Uma [conta de julgamento](https://www.whoiam.ai/contact-us/)da WhoIAM.

## <a name="scenario-description"></a>Descrição do cenário

A integração do WhoIAM inclui os seguintes componentes:

- Um inquilino Azure AD B2C. É o servidor de autorização que verifica as credenciais do utilizador com base em políticas personalizadas definidas nele. Também é conhecido como o fornecedor de identidade.

- Um portal de administração para gestão de clientes e suas configurações.

- Um serviço API que expõe várias funcionalidades através de pontos finais.  

- Azure Cosmos DB, que funciona como o back end tanto para o portal de administração BRIMS como para o serviço API.

O seguinte diagrama de arquitetura mostra a implementação.

![Diagrama da arquitetura da integração Azure AD B2C com a WhoIAM.](media/partner-whoiam/whoiam-architecture-diagram.png)

|Passo | Description |
|:-----| :-----------|
| 1. | O utilizador chega a uma página para iniciar o pedido de inscrição ou inscrição para uma aplicação que utiliza o Azure AD B2C como seu fornecedor de identidade.
| 2. | Como parte da autenticação, o utilizador solicita para verificar a propriedade do seu e-mail ou telefone ou usar a sua voz como fator de verificação biométrica.  
| 3. | Azure AD B2C faz uma chamada para o serviço API BRIMS e passa no endereço de e-mail do utilizador, número de telefone e gravação de voz.
| 4. | A BRIMS utiliza configurações predefinidas, tais como e-mails totalmente personalizáveis e modelos DE SMS para interagir com o utilizador no respetivo idioma de uma forma consistente com o estilo da aplicação.
| 5. | Após a verificação de identidade de um utilizador estar concluída, a BRIMS devolve um símbolo ao Azure AD B2C para indicar o resultado da verificação. O Azure AD B2C concede então ao utilizador acesso à app ou falha a sua tentativa de autenticação.  

## <a name="sign-up-with-whoiam"></a>Inscreva-se na WhoIAM

1. Contacte [a WhoIAM](https://www.whoiam.ai/contact-us/) e crie uma conta BRIMS.

2. Utilize as diretrizes de inscrição disponibilizadas e configuure os seguintes serviços Azure:

    - [Cofre de chaves Azure](https://azure.microsoft.com/services/key-vault/): Utilizado para armazenamento seguro de senhas, como palavras-passe de serviço de correio.

    - [Azure App Service](https://azure.microsoft.com/services/app-service/): Usado para acolher os serviços de porta de administração da BRIMS.

    - [Diretório Ativo Azure](https://azure.microsoft.com/services/active-directory/): Usado para autenticar utilizadores administrativos para o portal de administração.

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): Usado para armazenar e recuperar configurações.

    - [Insights de Aplicação](../azure-monitor/app/app-insights-overview.md) (opcional): Usado para iniciar sessão tanto na API como no portal de administração.

3. Implemente a API BRIMS e o portal de administração BRIMS no seu ambiente Azure.

4. As amostras de política personalizada Azure AD B2C estão disponíveis na sua documentação de inscrição BRIMS. Siga a documentação para configurar a sua aplicação e utilize a plataforma BRIMS para verificação de identidade do utilizador.  

Para obter mais informações sobre a BRIMS da WhoIAM, consulte a documentação do [produto.](https://www.whoiam.ai/brims/)

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Abra o inquilino Azure AD B2C. No âmbito **de Políticas**, selecione Identity **Experience Framework**.

2. Selecione o **seu Signo De Assinatura de Inscrição** anteriormente criado.

3. Selecione **Executar o fluxo do utilizador** e, em seguida:

   a. Para **Aplicação**, selecione a aplicação registada (a amostra é JWT).

   b. Para **URL de resposta,** selecione o **URL de redirecionamento**.

   c. Selecione **Executar o fluxo do utilizador**.

4. Passe pelo fluxo de inscrição e crie uma conta.

5. O serviço BRIMS será chamado durante o fluxo, após a criação do atributo do utilizador. Se o fluxo estiver incompleto, verifique se o utilizador não está guardado no diretório.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Começar com políticas personalizadas em Azure AD B2C](./custom-policy-get-started.md?tabs=applications)