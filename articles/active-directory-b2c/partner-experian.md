---
title: Tutorial para configurar Azure Ative Directy B2C com Experian
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação Azure AD B2C com a Experian para verificação e revisão de identificação com base em atributos do utilizador para prevenir fraudes.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 2058e5362a65b6cd5f3e5b7cb9c20ce32d020d30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96928703"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>Tutorial para configurar Experian com Azure Ative Directory B2C

Neste tutorial de amostra, fornecemos orientações sobre como integrar o Azure AD B2C com [a Experian.](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration) A Experian fornece uma variedade de soluções, que pode encontrar [aqui.](https://www.experian.com/)

Nesta amostra, é utilizada a plataforma integrada de risco de identidade digital e fraude da Experian **CrossCore.** CrossCore é um serviço de verificação de identificação que é usado para verificar a identificação do utilizador. Faz análises de risco com base em várias informações fornecidas pelo utilizador durante o fluxo de inscrição. O CrossCore é utilizado para determinar se o utilizador deve continuar a fazer login ou não. Os seguintes atributos podem ser usados na análise de risco CrossCore:

- E-mail
- Endereço IP
- Nome Próprio
- Segundo Nome
- Apelido
- Rua
- City
- Distrito
- Código Postal
- País/Região
- Número de Telefone

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- [Um inquilino Azure AD B2C](./tutorial-create-tenant.md) que está ligado à sua assinatura Azure.

## <a name="scenario-description"></a>Descrição do cenário

A integração experiana inclui os seguintes componentes:

- Azure AD B2C – O servidor de autorização, responsável pela verificação das credenciais do utilizador, também conhecido como fornecedor de identidade

- Experian – O serviço Experian recebe entradas fornecidas pelo utilizador e verifica a identidade do utilizador

- Custom Rest API – Esta API implementa a integração entre o Azure AD B2C e o serviço Experian.

O seguinte diagrama de arquitetura mostra a implementação.

![screenshot para experian-arquitetura-diagrama](media/partner-experian/experian-architecture-diagram.png)

|Passo | Description |
|:-----| :-----------|
| 1. | O utilizador chega a uma página de login. O utilizador seleciona a inscrição para criar uma nova conta e introduz informações na página. Azure AD B2C recolhe os atributos do utilizador.
| 2. | Azure AD B2C chama a API de camada média e transmite os atributos do utilizador.
| 3. | A API de camada média recolhe atributos do utilizador e transforma-os num formato que a API experiana poderia consumir. Depois, envia-o para a Experian.
| 4. | A Experian consome a informação e processa-a para validar a identificação do utilizador com base na análise de risco. Em seguida, devolve o resultado à API de camada média.
| 5. | A API de camada média processa a informação e envia de volta informações relevantes no formato JSON correto para Azure AD B2C.
| 6. | Azure AD B2C recebe informações da API de camada média. Se mostrar uma resposta de Falha, é apresentada uma mensagem de erro ao utilizador. Se mostrar uma resposta de Sucesso, o utilizador é autenticado e escrito no diretório.

## <a name="onboard-with-experian"></a>A bordo com a Experian

1. Para criar uma conta Experian, contacte [a Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)

2. Assim que uma conta for criada, receberá a informação de que necessita para a configuração da API. As seguintes secções descrevem o processo.

## <a name="configure-azure-ad-b2c-with-experian"></a>Configure Azure AD B2C com Experian

### <a name="part-1---deploy-the-api"></a>Parte 1 - Implantar a API

Implementar o [código API](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Experian/CrossCoreIntegrationApi/CrossCoreIntegrationApi.sln) fornecido para um serviço Azure. O código pode ser publicado a partir do Visual Studio, seguindo estas [instruções](/visualstudio/deployment/quickstart-deploy-to-azure).

>[!NOTE]
>Você precisará do URL do serviço implantado para configurar Azure AD com as configurações necessárias.

### <a name="part-2---deploy-the-client-certificate"></a>Parte 2 - Implementar o certificado de cliente

A chamada da Experian API está protegida por um certificado de cliente. Este certificado de cliente será fornecido pela Experian. Seguindo as instruções mencionadas neste [documento,](../app-service/environment/certificates.md#private-client-certificate)o certificado deve ser enviado para o serviço Azure App. A política da amostra utiliza estas etapas de chaves no processo:

- Carregar o certificado

- Desa parte da `WEBSITE_LOAD_ROOT_CERTIFICATES` chave com a impressão digital do certificado.

### <a name="part-3---configure-the-api"></a>Parte 3 - Configurar a API

As definições de aplicação podem ser [configuradas no serviço app em Azure.](../app-service/configure-common.md#configure-app-settings) Com este método, as definições podem ser configuradas de forma segura sem as verificar num repositório. Terá de fornecer as seguintes definições à API de Descanso:

| Definições da aplicação | Origem | Notas |
| :-------- | :------------| :-----------|
|CrossCoreConfig:TenantId | Configuração da conta experiana |     |
|CrossCoreConfig:OrgCode | Configuração da conta experiana |     |
|CrossCore:ApiEndpoint |Configuração da conta experiana|  |
|CrossCore:ClienteReference | Configuração da conta experiana | |
| CrossCore:ModelCode |Configuração da conta experiana|
| CrossCore:OrgCode | Configuração da conta experiana |
| CrossCore:SignatureKey  | Configuração da conta experiana |
| CrossCore:TenantId  | Configuração da conta experiana |
| CrossCore:CertificateThumbprint | Certificado experiano |
| BasicAuth:ApiUsername | Definir um nome de utilizador para a API | Usado na configuração ExtIde |
| BasicAuth:ApiPassword | Definir uma palavra-passe para a API | Usado na configuração ExtIde

### <a name="part-4---create-api-policy-keys"></a>Parte 4 - Criar chaves de política da API

Consulte este [documento](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) e crie duas teclas políticas – uma para o nome de utilizador da API e outra para a palavra-passe API que definiu acima para a autenticação básica HTTP.

>[!NOTE]
>Vai precisar das chaves para configurar as apólices mais tarde.

### <a name="part-5---replace-the-configuration-values"></a>Parte 5 - Substitua os valores de configuração

Nas [políticas personalizadas](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Experian/policy)fornecidas, encontre os seguintes espaços reservados e substitua-os pelos valores correspondentes da sua instância

|                      Marcador de posição                       |                                   Substituir por valor                                 |                   Exemplo                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | Seu inquilino nome curto                                                           | "yourtenant" de yourtenant.onmicrosoft.com |
| {your_trustframeworkbase_policy}                       | Nome Azure AD B2C da sua política TrustFrameworkBase                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | ID da aplicação IdentityExperienceFramework configurado no seu inquilino Azure AD B2C      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | ID da aplicação ProxyIdentityExperienceFramework configurado no seu inquilino Azure AD B2C | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | ID da aplicação da aplicação de armazenamento do seu inquilino                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | ID de objeto da aplicação de armazenamento do seu inquilino                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_api_username_key_name}                           | Nome da chave de nome de utilizador que criou [aqui](#part-4---create-api-policy-keys)             | B2C \_ 1A \_ RestApiUsername                     |
| {your_api_password_key_name}                           | Nome da chave-passe que criou [aqui](#part-4---create-api-policy-keys)             | B2C \_ 1A \_ RestApiPassword                     |
| {your_app_service_URL}                                 | URL do serviço de aplicações que criou                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Parte 6 - Configurar a política Azure AD B2C

Consulte este [documento](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) para obter instruções sobre como configurar o seu inquilino Azure AD B2C e configurar políticas.

>[!NOTE]
>Esta política de amostras baseia-se no [pacote de arranque de contas locais.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

>[!NOTE]
> Como uma boa prática, recomendamos que os clientes adicionem a notificação de consentimento na página de recolha de atributos. Notifique os utilizadores de que as informações serão enviadas para serviços de terceiros para verificação de identidade.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Abra o inquilino Azure AD B2C e em Políticas selecione **fluxos de utilizador**.

2. Selecione o seu **Fluxo de Utilizador** previamente criado.

3. Selecione **Executar o fluxo do utilizador** e selecione as definições:

   a. **Aplicação**: selecione a aplicação registada (a amostra é JWT)

   b. **URL de resposta**: selecione o **URL de redirecionamento**

   c. Selecione **Executar o fluxo do utilizador**.

4. Passe pelo fluxo de inscrição e crie uma conta

5. Log-out

6. Passar pelo fluxo de entrada  

7. O puzzle CrossCore aparecerá depois de entrares **em continuação**.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Começar com políticas personalizadas em Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
