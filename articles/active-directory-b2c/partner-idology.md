---
title: Integração de IDologia com Azure Ative Directory B2C
titleSuffix: Azure AD B2C
description: Saiba como integrar uma aplicação de pagamento online de amostra em Azure AD B2C com IDology. A IDology é um fornecedor de verificação de identidade e de revisão com múltiplas soluções.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: df0fcaf7987e30f9c2599346aaef5fcabb4f04e9
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256655"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>Tutorial para configurar iDologia com Diretório Ativo Azure B2C 

Neste tutorial de amostra, fornecemos orientações sobre como integrar o Azure AD B2C com [IDology.](https://www.idology.com/solutions/) A IDology é um fornecedor de verificação de identidade e de revisão com múltiplas soluções. Nesta amostra, cobriremos a solução ExpectID pela IDology.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* [Um inquilino Azure AD B2C](tutorial-create-tenant.md) que está ligado à sua assinatura Azure.

## <a name="scenario-description"></a>Descrição do cenário

A integração idologia inclui os seguintes componentes:

- Azure AD B2C – O servidor de autorização responsável pela verificação das credenciais do utilizador. Também é conhecido como o fornecedor de identidade.
- IDology – O serviço IDology requer a entrada fornecida pelo utilizador e verifica a identidade do utilizador.
- Custom Rest API – Esta API implementa a integração entre a Azure AD e o serviço IDology.

O seguinte diagrama de arquitetura mostra a implementação.

![Diagrama de arquitetura de IDologia](media/partner-idology/idology-architecture-diagram.png)

| Passo | Descrição |
|------|------|
|1     | Um utilizador chega à página de início de s.000. |
|2     | O utilizador seleciona a opção de inscrição para criar uma nova conta e introduzir informações na página. Azure AD B2C recolhe os atributos do utilizador. |
|3     | Azure AD B2C chama a API de camada média e transmite os atributos do utilizador. |
|4     | A API de camada média recolhe atributos do utilizador e transforma-os num formato que a IDOlogy API pode consumir. Depois envia a informação para a IDology. |
|5     | IDology consome a informação e processa-a, e depois devolve o resultado à API de camada média. |
|6     | A API de camada média processa a informação e envia a informação relevante de volta para Azure AD B2C. |
|7     | Azure AD B2C recebe informações da API de camada média. Se mostrar uma resposta **de Falha,** é apresentada uma mensagem de erro ao utilizador. Se mostrar uma resposta **de Sucesso,** o utilizador é autenticado e escrito no diretório. |
|      |      |

> [!NOTE]
> O Azure AD B2C também pode pedir ao cliente para realizar a autenticação de step-up, mas este cenário está fora do âmbito deste tutorial.

## <a name="onboard-with-idology"></a>A bordo com IDology

1. IDology fornece uma variedade de soluções, que você pode encontrar [aqui.](https://www.idology.com/solutions/) Para esta amostra, usamos ExpectID.

2. Para criar uma conta de IDology, contacte [a IDology.](https://www.idology.com/request-a-demo/microsoft-integration-signup/)

3. Assim que uma conta for criada, receberá a informação de que necessita para a configuração da API. As seguintes secções descrevem o processo.

## <a name="integrate-with-azure-ad-b2c"></a>Integrar com o Azure AD B2C

### <a name="part-1---deploy-the-api"></a>Parte 1 - Implantar a API

Implementar o [código API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/Api) fornecido para um serviço Azure. O código pode ser publicado a partir do Visual Studio, seguindo estas [instruções](/visualstudio/deployment/quickstart-deploy-to-azure).

Você precisará do URL do serviço implantado para configurar Azure AD com as configurações necessárias.

### <a name="part-2---configure-the-api"></a>Parte 2 - Configurar a API 

As definições de aplicação podem ser [configuradas no Serviço de Aplicações em Azure.](../app-service/configure-common.md#configure-app-settings) Com este método, as definições podem ser configuradas de forma segura sem as verificar num repositório. Terá de fornecer as seguintes definições à API de Descanso:

| Definições da aplicação | Origem | Notas |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | Configuração da conta de IDology |     |
|IdologySettings:ApiPassword | Configuração da conta de IDology |     |
|WebApiSettings:ApiUsername |Definir um nome de utilizador para a API| Usado na configuração ExtIde |
|WebApiSettings:ApiPassword | Definir uma palavra-passe para a API | Usado na configuração ExtIde

### <a name="part-3---create-api-policy-keys"></a>Parte 3 - Criar chaves de política da API

Siga este [documento](secure-rest-api.md#add-rest-api-username-and-password-policy-keys) para criar duas chaves de política: uma para o nome de utilizador da API e outra para a palavra-passe API que definiu acima.

A política da amostra utiliza estes nomes-chave:

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Parte 4 - Configurar a Política Azure AD B2C

1. Siga este [documento](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) para descarregar o [pacote de iniciação LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) e configurar a política para o inquilino Azure AD B2C. Siga as instruções até completar a secção **'Testar a Política Personalizada'.**

2. Descarregue as duas políticas de amostra [aqui.](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy)

3. Atualizar as duas políticas de amostragem:

   1. Abrir ambas as políticas:

      1. Na `Idology-ExpectId-API` secção, atualize o `ServiceUrl` item dos metadados com a localização da API implantada acima.

      1. `yourtenant`Substitua-o pelo nome do seu inquilino Azure AD B2C.
      Por exemplo, se o nome do seu inquilino Azure AD B2C  `contosotenant` for, substitua todas as instâncias de  `yourtenant.onmicrosoft.com`   `contosotenant.onmicrosoft.com` .

   1. Abra o ficheiro TrustFrameworkExtensions.xml:

      1. Encontre o elemento  `<TechnicalProfile Id="login-NonInteractive">` . Substitua ambas as instâncias  `IdentityExperienceFrameworkAppId`   com o ID de aplicação da aplicação IdentityExperienceFramework que criou anteriormente.

      1. Substitua ambas as instâncias  `ProxyIdentityExperienceFrameworkAppId`   com o ID de aplicação da aplicação ProxyIdentityExperienceFramework que criou anteriormente.

4. Substitua o SignInorSignUp.xml e TrustFrameworkExtensions.xml previamente enviado para Azure AD B2C no passo 1 com as duas políticas de amostra atualizadas.

> [!NOTE]
> Como uma boa prática, recomendamos que os clientes adicionem a notificação de consentimento na página de recolha de atributos. Notifique os utilizadores de que as informações serão enviadas para serviços de verificação de identidade de terceiros.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Abra o inquilino Azure AD B2C e, em **Políticas,** selecione **fluxos de utilizador**.

2. Selecione o seu **Fluxo de Utilizador** previamente criado.

3. Selecione **Executar o fluxo do utilizador** e selecione as definições:

   1. **Aplicação** - selecione a aplicação registada (a amostra é JWT).

   1. **URL de resposta** - selecione o **URL de redirecionamento**.

   1. Selecione **Executar o fluxo do utilizador**.

4. Passe pelo fluxo de inscrição e crie uma conta.

5. Termine a sessão.

6. Ando pelo fluxo de entrada.

7. O puzzle de IDology aparecerá depois de **entrar.**

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](custom-policy-overview.md)

- [Começar com políticas personalizadas em Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
