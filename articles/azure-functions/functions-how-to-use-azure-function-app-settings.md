---
title: Definir configurações de Aplicativo de funções do Azure | Microsoft Docs
description: Saiba como definir as configurações do aplicativo de funções do Azure.
author: ggailey777
manager: gwallace
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 27e56c3bcaa08fee25c362cdfed64c61b92873cb
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982647"
---
# <a name="how-to-manage-a-function-app"></a>Como gerenciar um aplicativo de funções

No Azure Functions, um aplicativo de funções fornece o contexto de execução para suas funções individuais. Os comportamentos do aplicativo de funções se aplicam a todas as funções hospedadas por um determinado aplicativo de funções. Este tópico descreve como configurar e gerenciar seus aplicativos de funções no portal do Azure.

Para começar, acesse a [portal do Azure](https://portal.azure.com) e entre em sua conta do Azure. Na barra de pesquisa na parte superior do portal, escreva o nome da sua aplicação de função e selecione-a na lista. Depois de selecionar seu aplicativo de funções, você verá a seguinte página:

![Visão geral do aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Você pode navegar até tudo o que precisa para gerenciar seu aplicativo de funções na página Visão geral, em particular as **[configurações do aplicativo](#settings)** e os **[recursos da plataforma](#platform-features)** .

## <a name="settings"></a>Configurações do aplicativo

A guia **configurações do aplicativo** mantém as configurações que são usadas pelo seu aplicativo de funções.

![Configurações do aplicativo de funções no portal do Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

Essas configurações são armazenadas criptografadas e você deve selecionar **Mostrar valores** para ver os valores no Portal.

Para adicionar uma configuração, selecione **nova configuração de aplicativo** e adicione o novo par chave-valor.

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Quando você desenvolve um aplicativo de funções localmente, esses valores são mantidos no arquivo de projeto local. Settings. JSON.

## <a name="platform-features"></a>Funcionalidades de plataforma

![Guia recursos da plataforma do aplicativo de funções.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Os aplicativos de funções são executados no e são mantidos pela plataforma de serviço Azure App. Assim, seus aplicativos de funções têm acesso à maioria dos recursos da plataforma principal de hospedagem na Web do Azure. A guia **recursos da plataforma** é onde você acessa os vários recursos da plataforma do serviço de aplicativo que você pode usar em seus aplicativos de funções. 

> [!NOTE]
> Nem todos os recursos do serviço de aplicativo estão disponíveis quando um aplicativo de funções é executado no plano de Hospedagem de consumo.

O restante deste tópico se concentra nos seguintes recursos do serviço de aplicativo no portal do Azure que são úteis para as funções do:

+ [Editor do serviço de aplicativo](#editor)
+ [Console](#console)
+ [Ferramentas avançadas (kudu)](#kudu)
+ [Opções de implantação](#deployment)
+ [CORS](#cors)
+ [Autenticação](#auth)
+ [Definição de API](#swagger)

Para obter mais informações sobre como trabalhar com as configurações do serviço de aplicativo, consulte [definir configurações de serviço Azure app](../app-service/configure-common.md).

### <a name="editor"></a>Editor do Serviço de Aplicativo

| | |
|-|-|
| ![Editor do serviço de aplicativo do aplicativo de funções.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | O editor do serviço de aplicativo é um editor avançado no portal que você pode usar para modificar arquivos de configuração JSON e arquivos de código da mesma forma. A escolha dessa opção inicia uma guia separada do navegador com um editor básico. Isso permite que você integre com o repositório git, execute e depure o código e modifique as configurações do aplicativo de funções. Esse editor fornece um ambiente de desenvolvimento aprimorado para suas funções comparadas com a folha do aplicativo de funções padrão.    |

![O editor do serviço de aplicativo](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="console"></a>MMC

| | |
|-|-|
| ![Console do aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | O console no portal é uma ferramenta de desenvolvedor ideal quando você prefere interagir com seu aplicativo de funções na linha de comando. Os comandos comuns incluem criação e navegação de diretório e arquivo, bem como a execução de scripts e arquivos em lotes. |

![Console do aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Ferramentas avançadas (kudu)

| | |
|-|-|
| ![Kudu de aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | As ferramentas avançadas para o serviço de aplicativo (também conhecido como kudu) fornecem acesso a recursos administrativos avançados do seu aplicativo de funções. Em kudu, você gerencia informações do sistema, configurações do aplicativo, variáveis de ambiente, extensões de site, cabeçalhos HTTP e variáveis de servidor. Você também pode iniciar o **kudu** navegando até o ponto de extremidade do SCM para seu aplicativo de funções, como`https://<myfunctionapp>.scm.azurewebsites.net/` |

![Configurar o kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Opções de implantação

| | |
|-|-|
| ![Opções de implantação de aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | O Functions permite que você desenvolva seu código de função em seu computador local. Em seguida, você pode carregar seu projeto de aplicativo de função local no Azure. Além do carregamento de FTP tradicional, o Functions permite que você implante seu aplicativo de funções usando soluções de integração contínua populares, como GitHub, Azure DevOps, Dropbox, bitbucket e outros. Para obter mais informações, consulte [implantação contínua para Azure Functions](functions-continuous-deployment.md). Para carregar manualmente usando FTP ou git local, você também deve [configurar suas credenciais de implantação](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![CORS do aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Para evitar a execução de código mal-intencionado em seus serviços, o serviço de aplicativo bloqueia chamadas para seus aplicativos de funções de fontes externas. As funções suportam os recursos de várias origens (CORS) para permitem-lhe definir uma "lista aprovada" de origens permitidas a partir do qual as suas funções podem aceitar pedidos remotos de partilha.  |

![Configurar CORS do Aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Authentication

| | |
|-|-|
| ![Autenticação do aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Quando as funções usam um gatilho HTTP, você pode exigir que as chamadas sejam autenticadas primeiro. O serviço de aplicativo dá suporte à autenticação Azure Active Directory e a entrar com provedores sociais, como Facebook, Microsoft e Twitter. Para obter detalhes sobre como configurar provedores de autenticação específicos, consulte [Azure app visão geral da autenticação do serviço](../app-service/overview-authentication-authorization.md). |

![Configurar a autenticação para um aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Definição de API

| | |
|-|-|
| ![Definição de Swagger de API de aplicativo de função no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | O Functions oferece suporte ao Swagger para permitir que os clientes consumam com mais facilidade suas funções disparadas por HTTP. Para obter mais informações sobre como criar definições de API com o Swagger, visite [hospedar uma API RESTful com CORS no serviço Azure app](../app-service/app-service-web-tutorial-rest-api.md). Você também pode usar os proxies do Functions para definir uma única superfície de API para várias funções. Para obter mais informações, consulte [trabalhando com proxies do Azure Functions](functions-proxies.md). |

![Configurar a API do Aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Passos Seguintes

+ [Definir configurações de serviço de Azure App](../app-service/configure-common.md)
+ [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)



