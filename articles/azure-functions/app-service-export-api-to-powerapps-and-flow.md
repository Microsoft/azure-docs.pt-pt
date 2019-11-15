---
title: Exportando uma API hospedada no Azure para o PowerApps e o Microsoft Flow | Microsoft Docs
description: Visão geral de como expor uma API hospedada no serviço de aplicativo para o PowerApps e Microsoft Flow
services: app-service
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: app-service
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: glenga
ms.reviewer: sunayv
ms.openlocfilehash: b12d1fec9b7852835d3d5b5346d64868d2ee8c46
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082855"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportando uma API hospedada no Azure para o PowerApps e Microsoft Flow

O [PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) é um serviço para criar e usar aplicativos de negócios personalizados que se conectam aos seus dados e trabalham entre plataformas. [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) facilita a automatização de fluxos de trabalho e processos de negócios entre seus aplicativos e serviços favoritos. O PowerApps e o Microsoft Flow vêm com uma variedade de conectores internos para fontes de dados como o Office 365, o Dynamics 365, o Salesforce e muito mais. Em alguns casos, os construtores de aplicativo e de fluxo também querem se conectar a fontes de dados e APIs criadas por sua organização.

Da mesma forma, os desenvolvedores que desejam expor suas APIs mais amplamente dentro de uma organização podem disponibilizar suas APIs para os construtores de aplicativo e de fluxo. Este tópico mostra como exportar uma API criada com [Azure Functions](../azure-functions/functions-overview.md) ou [serviço Azure app](../app-service/overview.md). A API exportada torna-se um *conector personalizado*, que é usado no PowerApps e Microsoft Flow assim como um conector interno.

> [!IMPORTANT]
> A funcionalidade de definição de API mostrada neste artigo só tem suporte para [a versão 1. x dos aplicativos de tempo de execução de Azure Functions](functions-versions.md#creating-1x-apps) e serviços de aplicativos. A versão 2. x das funções integra-se ao gerenciamento de API para criar e manter definições de OpenAPI. Para saber mais, confira [criar uma definição de openapi para uma função com o gerenciamento de API do Azure](functions-openapi-definition.md). 

## <a name="create-and-export-an-api-definition"></a>Criar e exportar uma definição de API
Antes de exportar uma API, você deve descrever a API usando uma definição de OpenAPI (anteriormente conhecida como um arquivo do [Swagger](https://swagger.io/) ). Esta definição contém informações sobre as operações que estão disponíveis numa API e a forma como os dados de pedido e resposta para a API devem ser estruturados. O PowerApps e o Microsoft Flow podem criar conectores personalizados para qualquer definição de OpenAPI 2,0. Azure Functions e Azure App serviço têm suporte interno para criar, hospedar e gerenciar definições de OpenAPI. Para obter mais informações, consulte [hospedar uma API RESTful com CORS no serviço Azure app](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Você também pode criar conectores personalizados no PowerApps e Microsoft Flow interface do usuário, sem usar uma definição de OpenAPI. Para obter mais informações, consulte [registrar e usar um conector personalizado (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) e [registrar e usar um conector personalizado (Microsoft Flow)](/power-automate/developer/register-custom-api).

Para exportar a definição de API, siga estas etapas:

1. Na [portal do Azure](https://portal.azure.com), navegue até o Azure Functions ou outro aplicativo do serviço de aplicativo.

    Se estiver usando Azure Functions, selecione seu aplicativo de funções, escolha **recursos de plataforma**e, em seguida, **definição de API**.

    ![Definição de API Azure Functions](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Se estiver usando o serviço Azure App, selecione **definição de API** na lista configurações.

    ![Definição de API do serviço de aplicativo](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. O botão **exportar para PowerApps + Microsoft Flow** deve estar disponível (caso contrário, você deve primeiro criar uma definição de openapi). Clique neste botão para iniciar o processo de exportação.

    ![Botão Exportar para PowerApps + Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Selecione o **modo de exportação**:

    O **Express** permite que você crie o conector personalizado de dentro do portal do Azure. Ele requer que você esteja conectado ao PowerApps ou Microsoft Flow e tenha permissão para criar conectores no ambiente de destino. Essa é a abordagem recomendada se esses dois requisitos puderem ser atendidos. Se estiver usando esse modo, siga as instruções [use Express Export](#express) abaixo.

    **Manual** permite exportar a definição de API, que você então importa usando os portais do PowerApps ou Microsoft Flow. Essa é a abordagem recomendada se o usuário do Azure e o usuário com permissão para criar conectores forem pessoas diferentes ou se o conector precisar ser criado em outro locatário do Azure. Se usar esse modo, siga as instruções de [exportação manual](#manual) a seguir.

    ![Modo de exportação](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> O conector personalizado usa uma *cópia* da definição de API, portanto, o PowerApps e o Microsoft Flow não saberão imediatamente se você fizer alterações no aplicativo e sua definição de API. Se você fizer alterações, repita as etapas de exportação para a nova versão.

<a name="express"></a>
## <a name="use-express-export"></a>Usar exportação expressa

Para concluir a exportação no modo **expresso** , siga estas etapas:

1. Verifique se você está conectado ao PowerApps ou Microsoft Flow locatário para o qual deseja exportar. 

2. Use as configurações conforme especificado na tabela.

    |Definição|Descrição|
    |--------|------------|
    |**Ambiente**|Selecione o ambiente no qual o conector personalizado deve ser salvo. Para obter mais informações, consulte [visão geral de ambientes](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Nome da API personalizada**|Insira um nome, que o PowerApps e os construtores de Microsoft Flow verão na lista de conectores.|
    |**Preparar configuração de segurança**|Se necessário, forneça os detalhes de configuração de segurança necessários para conceder aos usuários acesso à sua API. Este exemplo mostra uma chave de API. Para obter mais informações, consulte [especificar o tipo de autenticação](#auth) abaixo.|
 
    ![Exportação expressa para o PowerApps e Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Clique em **OK**. O conector personalizado agora é criado e adicionado ao ambiente especificado.

<a name="manual"></a>
## <a name="use-manual-export"></a>Usar exportação manual

Para concluir a exportação no modo **manual** , siga estas etapas:

1. Clique em **baixar** e salve o arquivo ou clique no botão Copiar e salve a URL. Você usará o arquivo de download ou a URL durante a importação.
 
    ![Exportação manual para PowerApps e Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Se sua definição de API incluir quaisquer definições de segurança, elas serão chamadas na etapa #2. Durante a importação, o PowerApps e o Microsoft Flow detecta esses e solicita informações de segurança. Reúna as credenciais relacionadas a cada definição para uso na próxima seção. Para obter mais informações, consulte [especificar o tipo de autenticação](#auth) abaixo.

    ![Segurança para exportação manual](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Este exemplo mostra a definição de segurança da chave de API que foi incluída na definição de OpenAPI.

Agora que você exportou a definição de API, importe-a para criar um conector personalizado no PowerApps e Microsoft Flow. Os conectores personalizados são compartilhados entre os dois serviços, portanto, você só precisa importar a definição uma vez.

Para importar a definição de API para o PowerApps e Microsoft Flow, siga estas etapas:

1. Aceda a [powerapps.com](https://web.powerapps.com) ou a [flow.microsoft.com](https://flow.microsoft.com).

2. No canto superior direito, clique no ícone de engrenagem e, em seguida, clique em **conectores personalizados**.

   ![Ícone de engrenagem no serviço](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Clique em **criar conector personalizado**e em **importar uma definição de openapi**.

   ![Criar conector personalizado](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Insira um nome para o conector personalizado e, em seguida, navegue até a definição de OpenAPI que você exportou e clique em **continuar**.

   ![Carregar definição de OpenAPI](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Na guia **geral** , examine as informações provenientes da definição de openapi.

5. Na guia **segurança** , se for solicitado que você forneça detalhes de autenticação, insira os valores apropriados para o tipo de autenticação. Clique em **Continue** (Continuar).

    ![Guia Segurança](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Este exemplo mostra os campos obrigatórios para autenticação de chave de API. Os campos diferem dependendo do tipo de autenticação.

6. Na guia **definições** , todas as operações definidas no arquivo openapi são preenchidas automaticamente. Se todas as suas operações necessárias forem definidas, você poderá ir para a próxima etapa. Caso contrário, você pode adicionar e modificar as operações aqui.

    ![Guia definições](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Este exemplo tem uma operação, chamada `CalculateCosts`. Os metadados, como **Descrição**, são provenientes do arquivo openapi.

7. Clique em **criar conector** na parte superior da página.

Agora você pode se conectar ao conector personalizado no PowerApps e Microsoft Flow. Para obter mais informações sobre como criar conectores no PowerApps e Microsoft Flow portais, consulte [registrar seu conector personalizado (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) e [registrar seu conector personalizado (Microsoft Flow)](/power-automate/get-started-flow-dev#create-a-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Especificar o tipo de autenticação

O PowerApps e o Microsoft Flow dão suporte a uma coleção de provedores de identidade que fornecem autenticação para conectores personalizados. Se sua API exigir autenticação, certifique-se de que ela seja capturada como uma _definição de segurança_ em seu documento openapi, como no exemplo a seguir:

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
Durante a exportação, você fornece valores de configuração que permitem ao PowerApps e Microsoft Flow autenticar usuários.

Esta seção aborda os tipos de autenticação que têm suporte no modo **expresso** : chave de API, Azure Active Directory e OAuth 2,0 genérico. O PowerApps e o Microsoft Flow também dão suporte à autenticação básica e ao OAuth 2,0 para serviços específicos, como dropbox, Facebook e SalesForce.

### <a name="api-key"></a>Chave de API
Ao usar uma chave de API, os usuários do seu conector são solicitados a fornecer a chave ao criarem uma conexão. Especifique um nome de chave de API para ajudá-los a entender qual chave é necessária. No exemplo anterior, usamos o nome `API Key (contact meganb@contoso.com)` para que as pessoas saibam onde obter informações sobre a chave de API. Por Azure Functions, a chave é normalmente uma das chaves de host, cobrindo várias funções dentro do aplicativo de funções.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Ao usar o Azure AD, você precisa de dois registros de aplicativo do Azure AD: um para a API em si e um para o conector personalizado:

- Para configurar o registro para a API, use o recurso de [autenticação/autorização do serviço de aplicativo](../app-service/configure-authentication-provider-aad.md) .

- Para configurar o registro para o conector, siga as etapas em [adicionando um aplicativo do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). O registro deve ter acesso delegado à sua API e a uma URL de resposta de `https://msmanaged-na.consent.azure-apim.net/redirect`. 

Para obter mais informações, consulte os exemplos de registro do Azure AD para [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) e [Microsoft Flow](https://docs.microsoft.com/connectors/custom-connectors/azure-active-directory-authentication). Esses exemplos usam Azure Resource Manager como a API; Substitua sua API se você seguir as etapas.

Os seguintes valores de configuração são necessários:
- **ID do cliente** -a ID do cliente do seu registro do Azure AD do conector
- **Segredo do cliente** -o segredo do cliente do registro do seu conector do Azure AD
- **URL de logon** -a URL base para o Azure AD. No Azure, isso geralmente é `https://login.windows.net`.
- **ID do locatário** -a ID do locatário a ser usado para o logon. Isso deve ser "comum" ou a ID do locatário no qual o conector é criado.
- **URL de recurso** -a URL de recurso do registro do Azure ad para sua API

> [!IMPORTANT]
> Se outra pessoa importar a definição de API para o PowerApps e Microsoft Flow como parte do fluxo manual, você deverá fornecê-las com a ID do cliente e o segredo do cliente do *registro do conector*, bem como a URL do recurso da sua API. Certifique-se de que esses segredos sejam gerenciados com segurança. **Não compartilhe as credenciais de segurança da API em si.**

### <a name="generic-oauth-20"></a>OAuth 2,0 genérico
Ao usar o OAuth 2,0 genérico, você pode integrar com qualquer provedor OAuth 2,0. Isso permite que você trabalhe com provedores personalizados que não têm suporte nativo.

Os seguintes valores de configuração são necessários:
- **ID do cliente** -a ID do cliente OAuth 2,0
- **Segredo do cliente** -o segredo do cliente OAuth 2,0
- **URL de autorização** -a URL de autorização do OAuth 2,0
- **URL do token** -a URL do token 2,0 do OAuth
- **URL de atualização** -a URL de atualização do OAuth 2,0


