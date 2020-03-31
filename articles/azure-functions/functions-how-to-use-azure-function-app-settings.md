---
title: Configurar as definições da aplicação de funções em Azure
description: Saiba como configurar as definições da aplicação de funções Azure.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276949"
---
# <a name="manage-your-function-app"></a>Gerir a sua aplicação de funções 

Nas Funções Azure, uma aplicação de função fornece o contexto de execução para as suas funções individuais. Os comportamentos da aplicação de função aplicam-se a todas as funções hospedadas por uma determinada aplicação de função. Todas as funções numa aplicação de função devem ser do mesmo [idioma.](supported-languages.md) 

Funções individuais numa aplicação de função são implantadas em conjunto e são dimensionadas em conjunto. Todas as funções na mesma função de app partilham recursos, por exemplo, à medida que a aplicação de função escala. 

As cordas de ligação, as variáveis ambientais e outras definições de aplicação são definidas separadamente para cada aplicação de função. Quaisquer dados que devem ser partilhados entre aplicações de função devem ser armazenados externamente numa loja persificada.

Este artigo descreve como configurar e gerir as suas aplicações de função. 

> [!TIP]  
> Muitas opções de configuração também podem ser geridas utilizando o [Azure CLI]. 

## <a name="get-started-in-the-azure-portal"></a>Começar a utilizar o portal do Azure

Para começar, vá ao [portal Azure] e inscreva-se na sua conta Azure. Na barra de pesquisa na parte superior do portal, escreva o nome da sua aplicação de função e selecione-a na lista. Depois de selecionar a sua aplicação de funções, consulte a seguinte página:

![Visão geral da aplicação de funções no portal Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Pode navegar para tudo o que precisa para gerir a sua aplicação de funções a partir da página de visão geral, em particular as **[definições](#settings)** de Aplicação e **[funcionalidades da Plataforma](#platform-features)**.

## <a name="application-settings"></a><a name="settings"></a>Definições da aplicação

O separador Definições de **Aplicação** mantém as definições utilizadas pela sua aplicação de função. Estas definições são armazenadas encriptadas e deve selecionar **valores do Show** para ver os valores no portal. Também pode aceder às definições de aplicações utilizando o Azure CLI.

### <a name="portal"></a>Portal

Para adicionar uma definição no portal, selecione **nova definição** de aplicação e adicione o novo par de valor-chave.

![Função de configurações de aplicativos no portal Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>CLI do Azure

O [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) comando devolve as definições de aplicação existentes, como no seguinte exemplo:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

O [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) comando adiciona ou atualiza uma definição de aplicação. O exemplo seguinte cria uma `CUSTOM_FUNCTION_APP_SETTING` definição com `12345`uma chave nomeada e um valor de:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Utilizar as definições de aplicação

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Quando desenvolve uma aplicação de função localmente, deve manter cópias locais destes valores no ficheiro do projeto local.settings.json. Para saber mais, consulte o [ficheiro de definições locais](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>Funcionalidades da plataforma

![A plataforma de funcionalidades de funções funcionalidades do separador.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

As aplicações de função são executadas e são mantidas, pela plataforma Azure App Service. Como tal, as aplicações de funções têm acesso à maioria das funcionalidades da plataforma de hospedagem web principal do Azure. O separador **de funcionalidades** da Plataforma é onde acede às muitas funcionalidades da plataforma App Service que pode utilizar nas suas aplicações de função. 

> [!NOTE]
> Nem todas as funcionalidades do Serviço de Aplicações estão disponíveis quando uma aplicação de função funciona no plano de hospedagem do Consumo.

O resto deste artigo centra-se nas seguintes funcionalidades do Serviço de Aplicações no portal Azure que são úteis para funções:

+ [Editor de Serviço de Aplicações](#editor)
+ [Consola](#console)
+ [Ferramentas avançadas (Kudu)](#kudu)
+ [Opções de implementação](#deployment)
+ [CORS](#cors)
+ [Autenticação](#auth)

Para obter mais informações sobre como trabalhar com as definições do Serviço de Aplicações, consulte as definições do serviço de [aplicações Do Configure Azure](../app-service/configure-common.md).

### <a name="app-service-editor"></a><a name="editor"></a>Editor de Serviço de Aplicações

![O editor do Serviço de Aplicações](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

O editor do Serviço de Aplicações é um editor avançado no portal que pode usar para modificar ficheiros de configuração JSON e ficheiros de código. A escolha desta opção lança um separado separado do navegador com um editor básico. Isto permite-lhe integrar-se com o código de repositório Git, executar e depurar e modificar as definições da aplicação de função. Este editor proporciona um ambiente de desenvolvimento melhorado para as suas funções em comparação com o editor de funções incorporado.  

Recomendamos que considere desenvolver as suas funções no seu computador local. Quando desenvolve localmente e publica para o Azure, os seus ficheiros de projeto são apenas lidos no portal. Para saber mais, consulte [Código e teste funções azure localmente](functions-develop-local.md).

### <a name="console"></a><a name="console"></a>Consola

![Consola de aplicativo de função](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

A consola in-portal é uma ferramenta de desenvolvimento ideal quando prefere interagir com a sua aplicação de função a partir da linha de comando. Comandos comuns incluem diretório e criação de ficheiros e navegação, bem como executar ficheiros de lote e scripts. 

Ao desenvolver-se localmente, recomendamos a utilização das [Ferramentas Core funções Azure](functions-run-local.md) e do [Azure CLI].

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Ferramentas avançadas (Kudu)

![Configure Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

As ferramentas avançadas para o App Service (também conhecida como Kudu) fornecem acesso a funcionalidades administrativas avançadas da sua aplicação de funções. A partir de Kudu, gere informações do sistema, configurações de aplicações, variáveis ambientais, extensões do site, cabeçalhos HTTP e variáveis do servidor. Você também pode lançar **Kudu** navegando para o ponto final SCM para a sua app de funções, como`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Centro de Implementação

Quando utiliza uma solução de controlo de fonte para desenvolver e manter o código das suas funções, o Centro de Implementação permite-lhe construir e implantar a partir do controlo de origem. O seu projeto é construído e implantado para o Azure quando fizer atualizações. Para mais informações, consulte tecnologias de [implantação em Funções Azure.](functions-deployment-technologies.md)

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Partilha de recursos de várias origens

Para evitar a execução de código malicioso no cliente, os navegadores modernos bloqueiam pedidos de aplicações web para recursos executados em um domínio separado. [A partilha de recursos de origem cruzada (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) permite que um `Access-Control-Allow-Origin` cabeçalho declare quais as origens permitidas para chamar pontos finais na sua aplicação de função.

#### <a name="portal"></a>Portal

Quando configura a lista de **origens permitidas** para a sua aplicação de função, o `Access-Control-Allow-Origin` cabeçalho é adicionado automaticamente a todas as respostas de pontos finais HTTP na sua aplicação de função. 

![Configure lista cors da aplicação de função](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Quando o`*`wildcard () é usado, todos os outros domínios são ignorados. 

Utilize [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) o comando para adicionar um domínio à lista de origens permitidas. O exemplo que se segue acrescenta o domínio contoso.com:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Utilize [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) o comando para listar as origens permitidas.

### <a name="authentication"></a><a name="auth"></a>Autenticação

![Configure a autenticação para uma aplicação de função](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Quando as funções usam um gatilho HTTP, pode exigir que as chamadas sejam autenticadas primeiro. O App Service suporta a autenticação do Diretório Ativo azure e o início de sessão com fornecedores sociais, como facebook, Microsoft e Twitter. Para mais informações sobre a configuração de fornecedores específicos de autenticação, consulte a visão geral da autenticação do Serviço de [Aplicações Azure](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Passos seguintes

+ [Configure Definições de serviço de aplicações Azure](../app-service/configure-common.md)
+ [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Portal Azure]: https://portal.azure.com
