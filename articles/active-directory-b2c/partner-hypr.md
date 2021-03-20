---
title: Tutorial para configurar Azure Ative Directy B2C com HYPR
titleSuffix: Azure AD B2C
description: Tutorial para configurar Azure Ative Directy B2C com Hypr para autenticação forte sem palavras-passe
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b80b1a4b3f9bcde6cf01b0e0e59425c6783bd5d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953768"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>Tutorial para configurar HYPR com Azure Ative Directory B2C

Neste tutorial de amostra, fornecemos orientações sobre como configurar a Azure AD B2C com [HYPR](https://get.hypr.com). Com o Azure AD B2C como fornecedor de identidade, pode integrar o HYPR com qualquer uma das suas aplicações de clientes para fornecer uma autenticação verdadeiramente sem palavras-passe aos seus utilizadores. HYPR substitui palavras-passe por encriptações de chaves públicas eliminando fraude, phishing e reutilização credencial.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- Um [inquilino Azure AD B2C.](./tutorial-create-tenant.md) O inquilino está ligado à sua assinatura Azure.

- Um inquilino de nuvem HYPR, obtenha uma [conta de teste](https://get.hypr.com/free-trial)gratuita.

- Um dispositivo móvel de utilizador registado utilizando as APIs de repouso HYPR ou o Gestor de Dispositivos HYPR no seu inquilino HYPR. Por exemplo, pode utilizar o [HYPR Java SDK](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk) para realizar esta tarefa.

## <a name="scenario-description"></a>Descrição do cenário

A integração HYRP inclui os seguintes componentes:

- Azure AD B2C – O servidor de autorização, responsável pela verificação das credenciais do utilizador, também conhecido como fornecedor de identidade

- Aplicações web e móveis - As suas aplicações móveis ou web que escolhe proteger com HYPR e Azure AD B2C. A HYPR disponibiliza um SDK móvel robusto, também uma aplicação móvel que pode utilizar nas plataformas iOS e Android para fazer uma verdadeira autenticação sem palavras-passe.

- A aplicação móvel HYPR - A aplicação móvel HYPR pode ser usada para executar esta amostra se preferir não usar os SDKs móveis nas suas próprias aplicações móveis.

- APIs de repouso HYPR - Pode utilizar as APIs HYPR para fazer o registo e a autenticação do dispositivo de utilizador. Estas APIs podem ser encontradas [aqui.](https://apidocs.hypr.com)

O seguinte diagrama de arquitetura mostra a implementação.

![Screenshot para o diagrama hypr-arquitetura](media/partner-hypr/hypr-architecture-diagram.png)

|Passo | Description |
|:-----| :-----------|
| 1. | O utilizador chega a uma página de login. Os utilizadores selecionam o pré-in/inscrição e introduzem o nome de utilizador na página.
| 2. | A aplicação envia os atributos do utilizador ao Azure AD B2C para verificação de identificação.
| 3. | O Azure AD B2C recolhe os atributos do utilizador e envia os atributos ao HYPR para autenticar o utilizador através da aplicação móvel HYPR.
| 4. | A HYPR envia uma notificação push para o dispositivo móvel do utilizador registado para uma autenticação certificada Fast Identity Online (FIDO). Pode ser uma impressão digital do utilizador, pino biométrico ou descentralizado.  
| 5. | Após o utilizador reconhecer a notificação push, o utilizador é concedido ou negado acesso à aplicação do cliente com base nos resultados de verificação.

## <a name="configure-the-azure-ad-b2c-policy"></a>Configure a política Azure AD B2C

1. Aceda à [política Azure AD B2C HYPR](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy) na pasta Política.

2. Siga este [documento](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) para baixar o [pacote de arranque LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Configure a política para o inquilino Azure AD B2C.

>[!NOTE]
>Atualize as políticas fornecidas para se relacionar com o seu inquilino específico.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Abra o inquilino Azure AD B2C e em Políticas selecione **Quadro de Experiência de Identidade.**

2. Selecione o **seu Signo De Assinatura de Inscrição** anteriormente criado.

3. Selecione **Executar o fluxo do utilizador** e selecione as definições:

   a. **Aplicação**: selecione a aplicação registada (a amostra é JWT)

   b. **URL de resposta**: selecione o **URL de redirecionamento**

   c. Selecione **Executar o fluxo do utilizador**.

4. Passe pelo fluxo de inscrição e crie uma conta

5. O HYPR será chamado durante o fluxo, após a criação do atributo do utilizador. Se o fluxo estiver incompleto, verifique se o utilizador não está guardado no diretório.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Começar com políticas personalizadas em Azure AD B2C](./custom-policy-get-started.md?tabs=applications)