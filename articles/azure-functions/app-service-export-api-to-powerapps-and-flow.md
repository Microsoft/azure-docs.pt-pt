---
title: Exportar uma API alojado no Azure para o PowerApps e Microsoft Flow | Documentos da Microsoft
description: Descrição geral de como expor uma API alojada no serviço de aplicações para o PowerApps e Microsoft Flow
services: app-service
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: app-service
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: glenga
ms.reviewer: sunayv
ms.openlocfilehash: 9f4bbf91b09abeb917fd9f49482881e33bf788ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60499634"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportar uma API alojado no Azure para o PowerApps e Microsoft Flow

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) é um serviço para criar e utilizar aplicações empresariais personalizadas que se ligam aos seus dados e a trabalham entre plataformas. [Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/) torna mais fácil automatizar fluxos de trabalho e processos empresariais entre as aplicações favoritas e serviços. O PowerApps e Microsoft Flow são fornecidos com uma variedade de conectores incorporados para origens de dados, como o Office 365, Dynamics 365, Salesforce e muito mais. Em alguns casos, os criadores de aplicações e fluxo também querem ligar a origens de dados e APIs criadas pela sua organização.

Da mesma forma, os desenvolvedores que desejam expõem suas APIs mais amplamente dentro de uma organização podem disponibilizar suas APIs aos criadores de aplicações e fluxo. Este tópico mostra-lhe como exportar uma API criada com [as funções do Azure](../azure-functions/functions-overview.md) ou [App Service do Azure](../app-service/overview.md). A API exportada se torna um *conector personalizado*, que é usado no PowerApps e Microsoft Flow como um conector incorporado.

## <a name="create-and-export-an-api-definition"></a>Criar e exportar uma definição de API
Antes de exportar uma API, tem de descrever a API com uma definição de OpenAPI (anteriormente conhecida como uma [Swagger](https://swagger.io/) ficheiro). Esta definição contém informações sobre as operações que estão disponíveis numa API e a forma como os dados de pedido e resposta para a API devem ser estruturados. PowerApps e Microsoft Flow, podem criar conectores personalizados para qualquer definição de OpenAPI 2.0. Serviço de aplicações do Azure e as funções do Azure têm suporte incorporado para criar, alojar e gerir as definições de OpenAPI. Para obter mais informações, consulte [alojar uma API RESTful com CORS no App Service do Azure](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Também pode criar conectores personalizados no PowerApps e Microsoft Flow da interface do Usuário, sem utilizar uma definição de OpenAPI. Para obter mais informações, consulte [registar e utilizar um conector personalizado (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) e [registar e utilizar um conector personalizado (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/).

Para exportar a definição de API, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com), navegue para as suas funções do Azure ou de outra aplicação de serviço de aplicações.

    Se utilizar as funções do Azure, selecione a sua aplicação de função, escolha **funcionalidades de plataforma**e, em seguida **definição da API**.

    ![Definição de API de funções do Azure](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Se utilizar o serviço de aplicações do Azure, selecione **definição de API** na lista de definições.

    ![Definição de serviço de aplicações API](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. O **exportar para PowerApps + Microsoft Flow** botão deve estar disponível (se não, primeiro tem de criar uma definição de OpenAPI). Clique neste botão para iniciar o processo de exportação.

    ![Exportar para PowerApps + Microsoft Flow botão](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Selecione o **modo de exportação**:

    **Express** lhe permite criar o conector personalizado a partir do portal do Azure. Ele requer que tem sessão iniciada no PowerApps ou Microsoft Flow e que tem permissão para criar conectores no ambiente de destino. Esta é a abordagem recomendada se esses dois requisitos podem ser atendidos. Se utilizar este modo, siga os [utilização da exportação express](#express) instruções abaixo.

    **Manual** permite que exporte a definição de API, que irá, em seguida, importar a utilizar os portais do PowerApps ou Microsoft Flow. Esta é a abordagem recomendada se o utilizador do Azure e o utilizador com permissão para criar conectores são pessoas diferentes, ou se o conector tem de ser criado noutro inquilino do Azure. Se utilizar este modo, siga os [utilização da exportação manual](#manual) instruções abaixo.

    ![Modo de exportação](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> O conector personalizado utiliza um *cópia* da definição de API, para que o PowerApps e Microsoft Flow não imediatamente saberá se fizer alterações ao aplicativo e sua definição de API. Se fizer alterações, repita os passos de exportação para a nova versão.

<a name="express"></a>
## <a name="use-express-export"></a>Utilização da exportação express

Para concluir a exportação no **Express** modo, siga estes passos:

1. Certifique-se de que tem sessão iniciada inquilino do PowerApps ou Microsoft Flow para o qual pretende exportar. 

2. Utilize as definições especificadas na tabela.

    |Definição|Descrição|
    |--------|------------|
    |**Ambiente**|Selecione o ambiente que o conector personalizado deve ser guardado. Para obter mais informações, consulte [descrição geral de ambientes](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Nome da API personalizada**|Introduza um nome, o PowerApps e Microsoft Flow construtores verá na lista de conector.|
    |**Preparar configuração de segurança**|Se for necessário, forneça os detalhes de configuração de segurança necessários para conceder aos utilizadores acesso à sua API. Este exemplo mostra uma chave de API. Para obter mais informações, consulte [especifique o tipo de autenticação](#auth) abaixo.|
 
    ![Express exportação para o PowerApps e Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Clique em **OK**. O conector personalizado é agora criado e adicionado para o ambiente especificado.

Para exemplos de utilização **Express** modo com as funções do Azure, veja [chamar uma função a partir do PowerApps](functions-powerapps-scenario.md) e [chamar uma função a partir do Microsoft Flow](functions-flow-scenario.md).

<a name="manual"></a>
## <a name="use-manual-export"></a>Utilize a exportação manual

Para concluir a exportação no **Manual** modo, siga estes passos:

1. Clique em **transferir** e guarde o ficheiro, ou clique no botão Copiar e guardar o URL. Usará o arquivo de download ou o URL durante a importação.
 
    ![Exportação manual para o PowerApps e Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Se a definição de API inclui quaisquer definições de segurança, estes são realçadas no passo 2 de #. Durante a importação, o PowerApps e Microsoft Flow, Deteta estes e pede-lhe informações de segurança. Recolha as credenciais relacionadas com a cada definição para utilização na secção seguinte. Para obter mais informações, consulte [especifique o tipo de autenticação](#auth) abaixo.

    ![Segurança para exportação manual](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Este exemplo mostra a definição de segurança de chaves de API que foi incluída na definição OpenAPI.

Agora que exportou a definição de API, importe-o para criar um conector personalizado no PowerApps e Microsoft Flow. Conectores personalizados são partilhados entre os dois serviços, por isso só terá de importar a definição de uma vez.

Para importar a definição de API para PowerApps e Microsoft Flow, siga estes passos:

1. Aceda a [powerapps.com](https://web.powerapps.com) ou a [flow.microsoft.com](https://flow.microsoft.com).

2. No canto superior direito, clique no ícone de engrenagem, em seguida, clique em **conectores personalizados**.

   ![Ícone de engrenagem no serviço](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Clique em **criar conector personalizado**, em seguida, clique em **importar uma definição de OpenAPI**.

   ![Criar conector personalizado](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Introduza um nome para o conector personalizado, navegue para a definição de OpenAPI que exportou e clique em **continuar**.

   ![Carregar a definição de OpenAPI](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Sobre o **gerais** separador, reveja as informações que vem da definição de OpenAPI.

5. Sobre o **segurança** separador, se lhe for pedido para fornecer detalhes de autenticação, introduza os valores adequados para o tipo de autenticação. Clique em **Continue** (Continuar).

    ![Guia de segurança](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Este exemplo mostra os campos necessários para autenticação de chave de API. Os campos são diferentes consoante o tipo de autenticação.

6. Sobre o **definições** separador, todas as operações definidas no seu ficheiro OpenAPI são preenchidos automaticamente. Se todas as operações necessárias forem definidas, pode ir para o passo seguinte. Caso contrário, pode adicionar e modificar operações aqui.

    ![Separador de definições](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Neste exemplo tem uma operação, com o nome `CalculateCosts`. Os metadados, como **Descrição**, tudo se do ficheiro OpenAPI.

7. Clique em **criar conector** na parte superior da página.

Pode agora ligar para o conector personalizado no PowerApps e Microsoft Flow. Para obter mais informações sobre a criação de conectores em portais do PowerApps e Microsoft Flow, veja [registar o conector personalizado (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) e [registar o conector personalizado (Microsoft Flow)](https://flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Especificar o tipo de autenticação

PowerApps e Microsoft Flow suportam uma coleção de fornecedores de identidade que fornecer autenticação para os conectores personalizados. Se a sua API requer autenticação, certifique-se de que é capturado como uma _a definição de segurança_ no documento OpenAPI, semelhante ao seguinte exemplo:

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
Durante a exportação, forneça os valores de configuração que permitem que o PowerApps e Microsoft Flow autenticar os utilizadores.

Esta secção abrange os tipos de autenticação que são suportados no **Express** modo: Chave de API, Azure Active Directory e genérico OAuth 2.0. PowerApps e Microsoft Flow também oferecem suporte a autenticação básica e o OAuth 2.0 para serviços específicos, como o Dropbox, Facebook e SalesForce.

### <a name="api-key"></a>Chave de API
Quando utilizar uma chave de API, os utilizadores do seu conector for pedidos para fornecer a chave de quando criam uma ligação. Especifique um nome de chave de API para os ajudar a compreender qual a chave é necessária. No exemplo anterior, utilizamos o nome `API Key (contact meganb@contoso.com)` para que as pessoas saibam onde pode obter informações sobre a chave de API. Para as funções do Azure, a chave é normalmente uma das chaves de anfitrião, que abrangem várias funções dentro da aplicação de função.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Ao utilizar o Azure AD, tem dois registos de aplicação do Azure AD: uma para a API propriamente dita e outra para o conector personalizado:

- Para configurar o registo para a API, utilize o [aplicação serviço de autenticação/autorização](../app-service/configure-authentication-provider-aad.md) funcionalidade.

- Para configurar o registo para o conector, siga os passos em [adicionar uma aplicação do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). O registo deve ter acesso delegado à sua API e um URL de resposta de `https://msmanaged-na.consent.azure-apim.net/redirect`. 

Para obter mais informações, consulte os exemplos de registo do Azure AD para [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) e [Microsoft Flow](https://flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/). Estes exemplos utilizam o Azure Resource Manager, como a API; Substitua a sua API se seguir os passos.

Os seguintes valores de configuração são necessários:
- **ID de cliente** -o ID de cliente do seu registo do Azure AD do conector
- **Segredo do cliente** -o segredo do cliente do seu registo do Azure AD do conector
- **URL de início de sessão** -o URL de base para o Azure AD. No Azure, isto é normalmente `https://login.windows.net`.
- **ID do inquilino** -o ID do inquilino a utilizar para o início de sessão. Isso deve ser "common" ou o ID do inquilino no qual o conector é criado.
- **URL de recurso** -o URL de recurso de registo do Azure AD para a sua API

> [!IMPORTANT]
> Se outra pessoa irá importar a definição de API para o PowerApps e Microsoft Flow como parte do fluxo manual, terá de fornecê-los com o ID de cliente e segredo do cliente do *registo do conetor*, bem como o URL de recurso da sua API. Certifique-se de que estes segredos são geridos de forma segura. **Não partilhe as credenciais de segurança da API propriamente dita.**

### <a name="generic-oauth-20"></a>OAuth 2.0 genérico
Quando utilizar genérico OAuth 2.0, pode integrar com qualquer fornecedor de OAuth 2.0. Isto permite-lhe trabalhar com provedores personalizados que não são suportados nativamente.

Os seguintes valores de configuração são necessários:
- **ID de cliente** -o ID de cliente OAuth 2.0
- **Segredo do cliente** -o segredo do cliente OAuth 2.0
- **URL de autorização** -o URL de autorização de OAuth 2.0
- **URL do token** -o URL de token de OAuth 2.0
- **Atualizar URL** -o URL de atualização de OAuth 2.0


