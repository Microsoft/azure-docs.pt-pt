---
title: Definir configurações do aplicativo de funções no Azure
description: Saiba como definir as configurações do aplicativo de funções do Azure.
author: ggailey777
manager: gwallace
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 7ad7f6156bbd8ea86e3e71bda4b23dac9722a0ef
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170746"
---
# <a name="manage-your-function-app"></a>Gerenciar seu aplicativo de funções 

No Azure Functions, um aplicativo de funções fornece o contexto de execução para suas funções individuais. Os comportamentos do aplicativo de funções se aplicam a todas as funções hospedadas por um determinado aplicativo de funções. Todas as funções em um aplicativo de funções devem ser do mesmo [idioma](supported-languages.md). 

As funções individuais em um aplicativo de funções são implantadas juntas e dimensionadas juntas. Todas as funções no mesmo aplicativo de funções compartilham recursos, por instância, à medida que o aplicativo de funções é dimensionado. 

Cadeias de conexão, variáveis de ambiente e outras configurações de aplicativo são definidas separadamente para cada aplicativo de funções. Todos os dados que devem ser compartilhados entre os aplicativos de funções devem ser armazenados externamente em um repositório persistente.

Este artigo descreve como configurar e gerenciar seus aplicativos de funções. 

> [!TIP]  
> Muitas opções de configuração também podem ser gerenciadas usando o [CLI do Azure]. 

## <a name="get-started-in-the-azure-portal"></a>Introdução ao portal do Azure

Para começar, acesse a [Azure portal] e entre em sua conta do Azure. Na barra de pesquisa na parte superior do portal, escreva o nome da sua aplicação de função e selecione-a na lista. Depois de selecionar seu aplicativo de funções, você verá a seguinte página:

![Visão geral do aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Você pode navegar até tudo o que precisa para gerenciar seu aplicativo de funções na página Visão geral, em particular as **[configurações do aplicativo](#settings)** e os **[recursos da plataforma](#platform-features)** .

## <a name="settings"></a>Configurações do aplicativo

A guia **configurações do aplicativo** mantém as configurações que são usadas pelo seu aplicativo de funções. Essas configurações são armazenadas criptografadas e você deve selecionar **Mostrar valores** para ver os valores no Portal. Você também pode acessar as configurações do aplicativo usando o CLI do Azure.

### <a name="portal"></a>Portal

Para adicionar uma configuração no portal, selecione **nova configuração de aplicativo** e adicione o novo par chave-valor.

![Configurações do aplicativo de funções no portal do Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>CLI do Azure

O [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) comando retorna as configurações de aplicativo existentes, como no exemplo a seguir:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

O [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) comando adiciona ou atualiza uma configuração de aplicativo. O exemplo a seguir cria uma configuração com uma chave `CUSTOM_FUNCTION_APP_SETTING` chamada e um valor `12345`de:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Usar configurações do aplicativo

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Ao desenvolver um aplicativo de funções localmente, você deve manter cópias locais desses valores no arquivo de projeto local. Settings. JSON. Para obter mais informações, consulte [Local Settings File](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>Funcionalidades de plataforma

![Guia recursos da plataforma do aplicativo de funções.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Os aplicativos de funções são executados no e são mantidos pela plataforma de serviço Azure App. Assim, seus aplicativos de funções têm acesso à maioria dos recursos da plataforma principal de hospedagem na Web do Azure. A guia **recursos da plataforma** é onde você acessa os vários recursos da plataforma do serviço de aplicativo que você pode usar em seus aplicativos de funções. 

> [!NOTE]
> Nem todos os recursos do serviço de aplicativo estão disponíveis quando um aplicativo de funções é executado no plano de Hospedagem de consumo.

O restante deste artigo se concentra nos seguintes recursos do serviço de aplicativo no portal do Azure que são úteis para as funções do:

+ [Editor do serviço de aplicativo](#editor)
+ [Console](#console)
+ [Ferramentas avançadas (kudu)](#kudu)
+ [Opções de implantação](#deployment)
+ [CORS](#cors)
+ [Autenticação](#auth)

Para obter mais informações sobre como trabalhar com as configurações do serviço de aplicativo, consulte [definir configurações de serviço Azure app](../app-service/configure-common.md).

### <a name="editor"></a>Editor do Serviço de Aplicativo

![O editor do serviço de aplicativo](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

O editor do serviço de aplicativo é um editor avançado no portal que você pode usar para modificar arquivos de configuração JSON e arquivos de código da mesma forma. A escolha dessa opção inicia uma guia separada do navegador com um editor básico. Isso permite que você integre com o repositório git, execute e depure o código e modifique as configurações do aplicativo de funções. Esse editor fornece um ambiente de desenvolvimento aprimorado para suas funções comparadas com o editor de funções interno.  

Recomendamos que você considere o desenvolvimento de suas funções no computador local. Quando você desenvolve localmente e publica no Azure, seus arquivos de projeto são somente leitura no Portal. Para saber mais, confira [código e teste Azure Functions localmente](functions-develop-local.md).

### <a name="console"></a>MMC

![Console do aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

O console no portal é uma ferramenta de desenvolvedor ideal quando você prefere interagir com seu aplicativo de funções na linha de comando. Os comandos comuns incluem criação e navegação de diretório e arquivo, bem como a execução de scripts e arquivos em lotes. 

Ao desenvolver localmente, é recomendável usar o [Azure Functions Core Tools](functions-run-local.md) e o [CLI do Azure].

### <a name="kudu"></a>Ferramentas avançadas (kudu)

![Configurar o kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

As ferramentas avançadas para o serviço de aplicativo (também conhecido como kudu) fornecem acesso a recursos administrativos avançados do seu aplicativo de funções. Em kudu, você gerencia informações do sistema, configurações do aplicativo, variáveis de ambiente, extensões de site, cabeçalhos HTTP e variáveis de servidor. Você também pode iniciar o **kudu** navegando até o ponto de extremidade do SCM para seu aplicativo de funções, como`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment"></a>Centro de implantação

Quando você usa uma solução de controle do código-fonte para desenvolver e manter seu código de funções, a central de implantação permite criar e implantar a partir do controle do código-fonte. Seu projeto é compilado e implantado no Azure quando você faz atualizações. Para obter mais informações, consulte [tecnologias de implantação no Azure Functions](functions-deployment-technologies.md).

### <a name="cors"></a>Compartilhamento de recursos entre origens

Para evitar a execução de código mal-intencionado no cliente, os navegadores modernos bloqueiam solicitações de aplicativos Web para recursos em execução em um domínio separado. O [CORS (compartilhamento de recursos entre origens)](https://developer.mozilla.org/docs/Web/HTTP/CORS) permite `Access-Control-Allow-Origin` que um cabeçalho declare quais origens têm permissão para chamar pontos de extremidade em seu aplicativo de funções.

#### <a name="portal"></a>Portal

Quando você configura a lista de **origens permitidas** para seu aplicativo de `Access-Control-Allow-Origin` funções, o cabeçalho é automaticamente adicionado a todas as respostas de pontos de extremidade http em seu aplicativo de funções. 

![Configurar lista CORS do aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Quando o curinga (`*`) é usado, todos os outros domínios são ignorados. 

Use o [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) comando para adicionar um domínio à lista de origens permitidas. O exemplo a seguir adiciona o domínio contoso.com:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Use o [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) comando para listar as origens permitidas atualmente.

### <a name="auth"></a>Authentication

![Configurar a autenticação para um aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Quando as funções usam um gatilho HTTP, você pode exigir que as chamadas sejam autenticadas primeiro. O serviço de aplicativo dá suporte à autenticação Azure Active Directory e a entrar com provedores sociais, como Facebook, Microsoft e Twitter. Para obter detalhes sobre como configurar provedores de autenticação específicos, consulte [Azure app visão geral da autenticação do serviço](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Passos seguintes

+ [Definir configurações de serviço de Azure App](../app-service/configure-common.md)
+ [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)

[CLI do Azure]: /cli/azure/
[Azure portal]: https://portal.azure.com
