---
title: Exportação de uma API hospedada no Azure para PowerApps e Microsoft Flow
description: Visão geral de como expor uma API hospedada no App Service ao PowerApps e microsoft Flow
ms.topic: conceptual
ms.date: 04/28/2020
ms.reviewer: sunayv
ms.openlocfilehash: 8ded1c5fba902adeaeb883894452c00c4ae1d617
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115830"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportação de uma API hospedada no Azure para PowerApps e Microsoft Flow

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) é um serviço para construir e usar aplicações de negócios personalizadas que se conectam aos seus dados e trabalham em todas as plataformas. O [Microsoft Flow](/learn/modules/get-started-with-flow/index) facilita a automatização de fluxos de trabalho e processos de negócio entre as suas aplicações e serviços favoritos. Tanto o PowerApps como o Microsoft Flow vêm com uma variedade de conectores incorporados a fontes de dados como o Office 365, Dynamics 365, Salesforce e muito mais. Em alguns casos, os construtores de aplicações e fluxos também querem ligar-se a fontes de dados e APIs construídos pela sua organização.

Da mesma forma, os desenvolvedores que queiram expor as suas APIs de forma mais ampla dentro de uma organização podem disponibilizar as suas APIs para os desenvolvedores de aplicações e fluxos. Este artigo mostra-lhe como exportar uma API construída com [Funções Azure](../azure-functions/functions-overview.md) ou [Serviço de Aplicações Azure.](../app-service/overview.md) A API exportada torna-se um *conector personalizado*, que é usado em PowerApps e Microsoft Flow tal como um conector incorporado.

> [!IMPORTANT]
> A funcionalidade de definição API mostrada neste artigo só é suportada para a versão 1.x das aplicações de execução de [funções Do Azure](functions-versions.md#creating-1x-apps) e aplicações de Serviços de Aplicações. A versão 2.x de Funções integra-se com a API Management para criar e manter definições OpenAPI. Para saber mais, consulte [Criar uma definição OpenAPI para uma função com a Gestão API Azure.](functions-openapi-definition.md) 

## <a name="create-and-export-an-api-definition"></a>Criar e exportar uma definição de API
Antes de exportar uma API, deve descrever a API utilizando uma definição OpenAPI (anteriormente conhecida como ficheiro [Swagger).](https://swagger.io/) Esta definição contém informações sobre as operações que estão disponíveis numa API e a forma como os dados de pedido e resposta para a API devem ser estruturados. PowerApps e Microsoft Flow podem criar conectores personalizados para qualquer definição OpenAPI 2.0. As Funções Azure e o Azure App Service têm suporte integrado para a criação, hospedagem e gestão das definições OpenAPI. Para mais informações, consulte [Host a RESTful API com CORS in Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Também pode construir conectores personalizados nos PowerApps e Microsoft Flow UI, sem utilizar uma definição OpenAPI. Para mais informações, consulte [O Registo e utilize um conector personalizado (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) e [registe-se e utilize um conector personalizado (Microsoft Flow)](/power-automate/developer/register-custom-api).

Para exportar a definição de API, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)navegue para a sua app de funções ou para uma aplicação do Serviço de Aplicações.

    A partir do menu esquerdo, em **API,** selecione **a definição DePi**.

    :::image type="content" source="media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png" alt-text="Definição de API funções azure":::

2. O botão **Export to PowerApps + Microsoft Flow** deve estar disponível (se não, tem primeiro de criar uma definição OpenAPI). Selecione este botão para iniciar o processo de exportação.

    ![Exportação para PowerApps + botão Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Selecione o **modo de exportação:**

    **O Expresso** permite criar o conector personalizado a partir do portal Azure. Requer que esteja inscrito no PowerApps ou no Microsoft Flow e tenha permissão para criar conectores no ambiente-alvo. Esta abordagem é recomendada se estes dois requisitos puderem ser cumpridos. Se utilizar este modo, siga as instruções de [exportação expressas use](#express) abaixo.

    **O manual** permite-lhe exportar a definição DePi, que depois importa utilizando os portais PowerApps ou Microsoft Flow. Esta abordagem é recomendada se o utilizador Azure e o utilizador com permissão para criar conectores forem pessoas diferentes, ou se o conector precisar de ser criado noutro inquilino do Azure. Se utilizar este modo, siga as instruções [de exportação manual use](#manual) abaixo.

    ![Modo de exportação](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> O conector personalizado utiliza uma *cópia* da definição API, pelo que o PowerApps e o Microsoft Flow não saberão imediatamente se efetua alterações na aplicação e na sua definição de API. Se fizer alterações, repita os passos de exportação para a nova versão.

<a name="express"></a>
## <a name="use-express-export"></a>Use exportação expressa

Para completar a exportação em modo **Expresso,** siga estes passos:

1. Certifique-se de que está inscrito no PowerApps ou no Microsoft Flow inquilino para o qual pretende exportar. 

2. Utilize as definições conforme especificado na tabela.

    |Definição|Descrição|
    |--------|------------|
    |**Ambiente**|Selecione o ambiente para o que o conector personalizado deve ser guardado. Para obter mais informações, veja [Descrição geral de ambientes](https://powerapps.microsoft.com/tutorials/environments-overview/).|
    |**Nome API personalizado**|Introduza um nome, que os construtores powerApps e Microsoft Flow verão na sua lista de conector.|
    |**Preparar configuração de segurança**|Se necessário, forneça os detalhes de configuração de segurança necessários para dar aos utilizadores acesso à sua API. Este exemplo mostra uma chave API. Para mais informações, consulte [O tipo de autenticação Especifique](#auth) abaixo.|
 
    ![Exportação expressa para PowerApps e Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Clique em **OK**. O conector personalizado foi agora construído e adicionado ao ambiente que especificou.

<a name="manual"></a>
## <a name="use-manual-export"></a>Utilizar a exportação manual

Para completar a exportação em modo **Manual,** siga estes passos:

1. Clique em **Baixar** e guardar o ficheiro, ou clique no botão de cópia e guarde o URL. Utilizará o ficheiro de descarregamento ou o URL durante a importação.
 
    ![Exportação manual para PowerApps e Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Se a sua definição de API incluir definições de segurança, estas definições são chamadas em fase #2. Durante a importação, powerApps e Microsoft Flow detetam estas definições e solicitações para informações de segurança. Reúna as credenciais relacionadas com cada definição para utilização na secção seguinte. Para mais informações, consulte [O tipo de autenticação Especifique](#auth) abaixo.

    ![Segurança para exportação manual](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Este exemplo mostra a definição de segurança chave DaPI que foi incluída na definição OpenAPI.

Agora que exportou a definição de API, importa-a para criar um conector personalizado em PowerApps e Microsoft Flow. Os conectores personalizados são partilhados entre os dois serviços, pelo que só precisa importar a definição uma vez.

Para importar a definição de API em PowerApps e Microsoft Flow, siga estes passos:

1. Aceda a [powerapps.com](https://web.powerapps.com) ou a [flow.microsoft.com](https://flow.microsoft.com).

2. No canto superior direito, clique no ícone da engrenagem e, em seguida, clique em **conectores personalizados**.

   ![Ícone de engrenagem no serviço](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Clique **em Criar conector personalizado**e, em seguida, clique em importar uma **definição OpenAPI**.

   ![Criar conector personalizado](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Introduza um nome para o conector personalizado, depois navegue para a definição OpenAPI que exportou e clique em **Continuar**.

   ![Carregar definição OpenAPI](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. No separador **Geral,** reveja a informação que provém da definição OpenAPI.

5. No separador **'Segurança',** se for solicitado que forneça detalhes de autenticação, introduza os valores adequados para o tipo de autenticação. Clique em **Continue** (Continuar).

    ![Separador de segurança](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    Este exemplo mostra os campos necessários para a autenticação da chave API. Os campos diferem consoante o tipo de autenticação.

6. No separador **Definições,** todas as operações definidas no seu ficheiro OpenAPI são auto-povoadas. Se todas as suas operações necessárias forem definidas, pode passar ao próximo passo. Caso contrário, pode adicionar e modificar as operações aqui.

    ![Separador definições](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Este exemplo tem uma operação, chamada `CalculateCosts` . Os metadados, como **descrição,** todos vêm do ficheiro OpenAPI.

7. Clique em **Criar conector** na parte superior da página.

Agora pode ligar-se ao conector personalizado no PowerApps e no Microsoft Flow. Para obter mais informações sobre a criação de conectores nos portais PowerApps e Microsoft Flow, consulte [O registo do seu conector personalizado (PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) e [registe o seu conector personalizado (Microsoft Flow)](/power-automate/get-started-flow-dev#create-a-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Especificar o tipo de autenticação

PowerApps e Microsoft Flow suportam uma coleção de fornecedores de identidade que fornecem autenticação para conectores personalizados. Se a sua API necessitar de autenticação, certifique-se de que é capturada como uma _definição_ de segurança no seu documento OpenAPI, como o seguinte exemplo:

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
Durante a exportação, fornece valores de configuração que permitem ao PowerApps e ao Microsoft Flow autenticar os utilizadores.

Esta secção abrange os tipos de autenticação suportados no modo **Express:** Chave API, Diretório Ativo Azure e Genérico OAuth 2.0. PowerApps e Microsoft Flow também suportam a Autenticação Básica, e OAuth 2.0 para serviços específicos como Dropbox, Facebook e SalesForce.

### <a name="api-key"></a>Chave API
Ao utilizar uma tecla API, os utilizadores do conector são solicitados a fornecer a chave quando criam uma ligação. Especifica um nome-chave da API para os ajudar a perceber qual a chave necessária. No exemplo anterior, usamos o nome `API Key (contact meganb@contoso.com)` para que as pessoas saibam onde obter informações sobre a chave API. Para as Funções Azure, a chave é tipicamente uma das chaves hospedeiras, cobrindo várias funções dentro da aplicação de funções.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
Ao utilizar o Azure AD, necessita de dois registos de aplicação Azure AD: um para a própria API e um para o conector personalizado:

- Para configurar o registo da API, utilize a função de [autenticação/autorização do serviço de aplicações.](../app-service/configure-authentication-provider-aad.md)

- Para configurar o registo do conector, siga os passos na adição de [uma aplicação Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). O registo deve ter dedelegado o acesso à sua API e um URL de resposta de `https://msmanaged-na.consent.azure-apim.net/redirect` . 

Para mais informações, consulte os exemplos de registo da AD Azure para [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) e [Microsoft Flow](https://docs.microsoft.com/connectors/custom-connectors/azure-active-directory-authentication). Estes exemplos utilizam o Gestor de Recursos Azure como API; substituir a sua API se seguir os passos.

São necessários os seguintes valores de configuração:
- **ID do cliente** - a identificação do cliente do registo do seu conector Azure AD
- **Segredo** do cliente - o segredo do cliente do registo da AD do seu conector Azure
- URL de **login** - o URL base para Azure AD. Em Azure, `https://login.windows.net` tipicamente.
- **Id** do inquilino - a identificação do inquilino para ser usado para o login. Esta identificação deve ser "comum" ou a identificação do inquilino em que o conector é criado.
- **URL de recursos** - o URL de recursos do registo da AD Azure para a sua API

> [!IMPORTANT]
> Se outra pessoa importar a definição de API para PowerApps e Microsoft Flow como parte do fluxo manual, deve fornecer-lhes o ID do cliente e o segredo do cliente do registo do *conector,* bem como o URL de recursos da sua API. Certifique-se de que estes segredos sejam geridos de forma segura. **Não partilhe as credenciais de segurança da própria API.**

### <a name="generic-oauth-20"></a>OAuth 2.0 genérico
Ao utilizar o OAuth 2.0 genérico, pode integrar-se com qualquer fornecedor OAuth 2.0. Fazê-lo permite-lhe trabalhar com fornecedores personalizados que não são apoiados de forma nativa.

São necessários os seguintes valores de configuração:
- **ID do cliente** - o ID do cliente OAuth 2.0
- **Segredo** do cliente - o segredo do cliente OAuth 2.0
- **URL de autorização** - o URL de autorização OAuth 2.0
- **TOKen URL** - o URL de ficha OAuth 2.0
- **REFRESH URL** - o URL de atualização OAuth 2.0


