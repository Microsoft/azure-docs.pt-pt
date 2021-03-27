---
title: Tutorial para configurar Azure Ative Directory B2C com Microsoft Dynamics 365 Fraud Protection
titleSuffix: Azure AD B2C
description: Tutorial para configurar O Azure Ative Directory B2C com a Microsoft Dynamics 365 Fraud Protection para identificar conta arriscada e fraudulenta
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 24fe28eafc1d1da90b6a7b7f9d41b7e645e62855
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625777"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Tutorial: Configurar a Proteção contra fraudes Microsoft Dynamics 365 com O Diretório Ativo B2C

Neste tutorial de amostra, fornecemos orientações sobre como integrar o [Microsoft Dynamics 365 Fraud Protection](https://docs.microsoft.com/dynamics365/fraud-protection/overview) (DFP) com o Azure Ative Directory (AD) B2C.

O Microsoft DFP fornece aos clientes a capacidade de avaliar se o risco de tentativas de criar novas contas e tentativas de login no ecossistema do cliente é fraudulento. A avaliação do Microsoft DFP pode ser usada pelo cliente para bloquear ou desafiar tentativas suspeitas de criar novas contas falsas ou de comprometer as contas existentes. A proteção da conta inclui a recolha de impressões digitais de dispositivos em inteligência artificial, APIs para avaliação de risco em tempo real, experiência de regras e listas para otimizar a estratégia de risco conforme as necessidades do cliente, e um cartão de pontuação para monitorizar a eficácia e tendências de proteção da fraude no ecossistema do cliente.

Nesta amostra, vamos integrar as funcionalidades de proteção de conta do Microsoft DFP com um fluxo de utilizador AD B2C Azure. O serviço irá recolher uma impressão digital externa de todas as tentativas de inscrição ou inscrição e vigilância de qualquer comportamento suspeito passado ou presente. O Azure AD B2C invoca um ponto final de decisão da Microsoft DFP, que devolve um resultado baseado em todo o comportamento passado e presente do utilizador identificado, e também as regras personalizadas especificadas no serviço Microsoft DFP. Azure AD B2C toma uma decisão de aprovação com base neste resultado e passa o mesmo para o Microsoft DFP.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma subscrição do Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- Um [inquilino Azure AD B2C.](./tutorial-create-tenant.md) O inquilino está ligado à sua assinatura Azure.

- Obtenha uma [subscrição](https://dynamics.microsoft.com/pricing/#Sales)do Microsoft DFP . Também pode configurar uma [versão do cliente experimental.](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin)

## <a name="scenario-description"></a>Descrição do cenário

A integração do Microsoft DFP inclui os seguintes componentes:

- **Azure AD B2C inquilino**: Autentica o utilizador e atua como cliente da Microsoft DFP. Acolhe um script de recolha de impressões digitais recolhendo dados de identificação e diagnóstico de cada utilizador que executa uma política-alvo. Mais tarde bloqueia ou desafia tentativas de inscrição ou inscrição se o Microsoft DFP os achar suspeitos.

- **Serviço de aplicações personalizados**: Uma aplicação web que serve dois propósitos.

  - Serve páginas HTML para serem usadas como UI do Identity Experience Framework. Responsável pela incorporação do script de impressão digital Microsoft Dynamics 365.

  - Um controlador API com pontos finais RESTful que liga o Microsoft DFP ao Azure AD B2C. O tratamento de dados, estrutura e adere aos requisitos de segurança de ambos.

- **Serviço de impressão digital Do Microsoft DFP**: Script incorporado dinamicamente, que regista telemetria do dispositivo e detalhes do utilizador autoafirmados para criar uma impressão digital exclusivamente identificável para o utilizador ser usado mais tarde no processo de tomada de decisão.

- **Pontos finais da Microsoft DFP API**: Fornece o resultado da decisão e aceita um estatuto final que reflita a operação realizada pela aplicação do cliente. O Azure AD B2C não comunica diretamente com os pontos finais devido aos diferentes requisitos de segurança e carga útil da API, em vez disso utiliza o serviço de aplicações como intermediário.

O seguinte diagrama de arquitetura mostra a implementação.

![Imagem mostra diagrama de arquitetura de proteção à fraude microsoft dynamics365](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Passo | Description |
|:-----| :-----------|
| 1. | O utilizador chega a uma página de login. Os utilizadores selecionam o sesurá-se para criar uma nova conta e introduzir informações na página. Azure AD B2C recolhe atributos do utilizador.
| 2. | Azure AD B2C chama a API de camada média e transmite os atributos do utilizador.
| 3. | A API de camada média recolhe atributos do utilizador e transforma-os num formato que a Microsoft DFP API poderia consumir. Em seguida, enviá-lo para a Microsoft DFP API.
| 4. | Depois de a API do Microsoft DFP consumir a informação e processá-la, devolve o resultado à API de camada média.
| 5. | A API de camada média processa a informação e envia informações relevantes para a Azure AD B2C.
| 6. | Azure AD B2C recebe informações da API da camada média. Se mostrar uma resposta de Falha, é apresentada uma mensagem de erro ao utilizador. Se mostrar uma resposta de Sucesso, o utilizador é autenticado e escrito no diretório.

## <a name="set-up-the-solution"></a>Configurar a solução

1. [Crie uma aplicação do Facebook](./identity-provider-facebook.md#create-a-facebook-application) configurada para permitir que a federação aD B2C azure.
2. [Adicione o segredo do Facebook](./custom-policy-get-started.md#create-the-facebook-key) que criou como chave de política de Identidade Experiência.

## <a name="configure-your-application-under-microsoft-dfp"></a>Configure a sua aplicação no âmbito do Microsoft DFP

[Confiem o seu inquilino AZure AD](/dynamics365/fraud-protection/integrate-real-time-api) para usar o Microsoft DFP.

## <a name="deploy-to-the-web-application"></a>Implementar para a aplicação web

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Implementar a impressão digital do serviço Microsoft DFP

[A recolha de impressões digitais do dispositivo Microsoft DFP](/dynamics365/fraud-protection/device-fingerprinting) é um requisito para a proteção da conta do Microsoft DFP.

>[!NOTE]
>Além das páginas Azure AD B2C UI, o cliente também pode implementar o serviço de impressão digital dentro do código da aplicação para perfis de dispositivos mais abrangentes. O serviço de impressão digital no código de aplicações não está incluído nesta amostra.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>Implementar o código API AD B2C AZure

Implementar o [código API fornecido](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) para um serviço Azure. O código pode ser [publicado a partir do Visual Studio](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

Configurar CORS, adicionar **Origem Permitida**`https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>Mais tarde, necessitará do URL do serviço implantado para configurar a Azure AD com as definições necessárias.

Consulte [a documentação do serviço app](../app-service/app-service-web-tutorial-rest-api.md) para saber mais.

### <a name="add-context-dependent-configuration-settings"></a>Adicionar definições de configuração dependentes de contexto

Configure as definições de aplicação no [serviço app em Azure](../app-service/configure-common.md#configure-app-settings). Isto permite configurar de forma segura as definições sem as colocar num repositório. A API de Repouso necessita das seguintes definições fornecidas:

| Definições da aplicação | Origem | Notas |
| :-------- | :------------| :-----------|
|FraudeProtectionSettings:InstanceId | Configuração do Microsoft DFP |     |
|FraudeProtectionSettings:DeviceFingerprintingCustomerId | O seu dispositivo Microsoft que recolha a impressão digital do cliente identificação |     |
| FraudeProtectionSettings:ApiBaseUrl |  O seu URL base do Portal Do Microsoft DFP   | Remover 'int' para chamar a API de produção em vez|
|  TokenProviderConfig: Recurso  | O seu URL base - https://api.dfp.dynamics-int.com     | Remover 'int' para chamar a API de produção em vez|
|   TokenProviderConfig:ClientId       |O seu mercador de proteção contra fraudes Azure AD app iD      |       |
| TokenProviderConfig:Autoridade | https://login.microsoftonline.com/<directory_ID> | Sua autoridade de inquilino da Azure AD |
| TokenProviderConfig:CertificateThumbprint* | A impressão digital do certificado para usar para autenticar contra a sua aplicação de cliente AD Azure |
| TokenProviderConfig:ClientSecret* | O segredo para o seu aplicativo de cliente Azure AD mercante | Recomendado para usar um gestor de segredos |

*Desafine apenas 1 dos 2 parâmetros marcados dependendo se autenticar com um certificado ou um segredo, como uma palavra-passe.

## <a name="azure-ad-b2c-configuration"></a>Configuração Azure AD B2C

### <a name="replace-the-configuration-values"></a>Substitua os valores de configuração

Nas [políticas personalizadas](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)fornecidas, encontre os seguintes espaços reservados e substitua-os pelos valores correspondentes da sua instância.

| Marcador de posição | Substituir | Notas |
| :-------- | :------------| :-----------|
|{your_tenant_name} | Seu inquilino nome curto |  "yourtenant" de yourtenant.onmicrosoft.com   |
|{your_tenantId} | ID do inquilino do seu inquilino Azure AD B2C |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   ID da aplicação IdentityExperienceFramework configurado no seu inquilino Azure AD B2C    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  ID da aplicação ProxyIdentityExperienceFramework configurado no seu inquilino Azure AD B2C      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  ID da aplicação da aplicação de armazenamento do seu inquilino   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | ID de objeto da aplicação de armazenamento do seu inquilino    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   Chave de instrumentação da sua instância de insights de aplicação*  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | Ponto final no seu serviço de aplicações a partir do local onde os seus ficheiros de UI são servidos    | https://yourapp.azurewebsites.net/B2CUI/GetUIPage   |
|   {your_app_service_url}  | URL do seu serviço de aplicações    |  https://yourapp.azurewebsites.net  |
|   {o seu facebook-app-id}  |  ID da app do facebook que configuraste para federação com Azure AD B2C   | 000000000000000   |
|  {o seu facebook-app-secret}   |  Nome da chave de política que guardou o segredo da aplicação do Facebook   | B2C_1A_FacebookAppSecret   |

*Insights de aplicativos podem estar em um inquilino diferente. Este passo é opcional. Remova os respetivos Filtros Técnicos e OrechestrationSteps, se não necessário.

### <a name="call-microsoft-dfp-label-api"></a>Ligue para a API da etiqueta do Microsoft DFP

Os clientes precisam de implementar a [etiqueta API.](/dynamics365/fraud-protection/integrate-ap-api) Consulte [a Microsoft DFP API](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0) para saber mais.

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

O valor do userID tem de ser o mesmo que o do valor correspondente de configuração AD B2C (ObjectID).

>[!NOTE]
>Adicione a notificação de consentimento à página de recolha de atributos. Notifique que a telemetria e a identidade dos utilizadores serão registadas para efeitos de proteção da conta.

## <a name="configure-the-azure-ad-b2c-policy"></a>Configure a política Azure AD B2C

1. Aceda à [política Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) na pasta Políticas.

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

5. O serviço Microsoft DFP será chamado durante o fluxo, após a criação do atributo do utilizador. Se o fluxo estiver incompleto, verifique se o utilizador não está guardado no diretório.

>[!NOTE]
>Atualizar as regras diretamente no Portal Do Microsoft DFP se utilizar [o motor de regra do Microsoft DFP](/dynamics365/fraud-protection/rules).

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Amostras de Microsoft DFP](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Começar com políticas personalizadas em Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
