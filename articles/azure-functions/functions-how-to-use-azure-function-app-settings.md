---
title: Configurar definições da aplicação de função do Azure | Documentos da Microsoft
description: Saiba como configurar as definições de aplicação de função do Azure.
services: ''
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 188c17b4e8ef84f3907b63fd62bf110ee94b4d7f
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511246"
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Como gerir uma aplicação de funções no portal do Azure 

Nas funções do Azure, uma aplicação de funções fornece o contexto de execução para as suas funções individuais. Comportamentos da aplicação de função aplicam-se a todas as funções alojadas por uma aplicação de função especificada. Este tópico descreve como configurar e gerir as suas aplicações de função no portal do Azure.

Para começar, vá para o [portal do Azure](https://portal.azure.com) e inicie sessão na sua conta do Azure. Na barra de pesquisa na parte superior do portal, escreva o nome da sua aplicação de função e selecione-a na lista. Depois de selecionar a sua aplicação function app, verá a página seguinte:

![Descrição geral da aplicação de função no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Pode navegar para todo o necessário para gerir a sua aplicação de função em particular na página Descrição geral, o **[definições da aplicação](#settings)** e **[defuncionalidadesdeplataforma](#platform-features)**.

## <a name="settings"></a>Definições da aplicação

O **as definições da aplicação** separador mantém as definições que são utilizadas pela sua aplicação de função.

![Definições da aplicação de funções no portal do Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

Estas definições são armazenadas de forma encriptada e tem de selecionar **Mostrar valores** para ver os valores no portal.

Para adicionar uma definição, selecione **nova definição de aplicação** e adicione o novo par de chave-valor.

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Ao desenvolver uma aplicação de funções localmente, estes valores são mantidos no arquivo de projeto Settings.

## <a name="platform-features"></a>Funcionalidades de plataforma

![Guia de funcionalidades de plataforma de aplicação de função.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Aplicações de funções executados em e são mantidas pela plataforma do App Service do Azure. Como tal, as suas aplicações de função tem acesso à maioria dos recursos da plataforma de alojamento de web do núcleo do Azure. O **funcionalidades de plataforma** guia é onde acessar as várias funcionalidades da plataforma do serviço de aplicações que pode utilizar nas suas aplicações de função. 

> [!NOTE]
> Nem todas as funcionalidades do serviço de aplicações estão disponíveis quando uma aplicação de função é executada no consumo plano de alojamento.

O resto deste tópico concentra-se as seguintes funcionalidades de serviço de aplicações no portal do Azure, que são úteis para as funções:

+ [Editor do serviço de aplicações](#editor)
+ [Console](#console)
+ [Ferramentas Avançadas (Kudu)](#kudu)
+ [Opções de implementação](#deployment)
+ [CORS](#cors)
+ [Autenticação](#auth)
+ [Definição de API](#swagger)

Para obter mais informações sobre como trabalhar com definições de serviço de aplicações, consulte [configurar definições de serviço de aplicações do Azure](../app-service/web-sites-configure.md).

### <a name="editor"></a>Editor do serviço de aplicações

| | |
|-|-|
| ![Aplicação de função de editor do serviço de aplicações.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | O editor do serviço de aplicações é um editor no portal avançado que pode utilizar para modificar ficheiros de configuração JSON e arquivos de código semelhantes. Escolher esta opção inicia um separador do browser separado com um editor básico. Isto permite-lhe integrar com o repositório de Git, executar e depurar o código e modificar as definições de aplicação de função. Este editor fornece um ambiente de desenvolvimento avançado para as suas funções em comparação com o painel de aplicação de função predefinido.    |

![O editor do serviço de aplicações](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="console"></a>Console

| | |
|-|-|
| ![Consola de aplicação de função no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | A consola de dentro do portal é uma ferramenta de desenvolvimento ideal quando preferir interagir com a sua aplicação de função na linha de comando. Comandos comuns incluem o diretório e criação de ficheiros e navegação, bem como executar scripts e arquivos em lote. |

![Consola de aplicação de função](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Ferramentas Avançadas (Kudu)

| | |
|-|-|
| ![Aplicação de funções Kudu no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | As ferramentas avançadas para o serviço de aplicações (também conhecido como Kudu) fornecem acesso a funcionalidades administrativas avançadas da sua aplicação de função. Do Kudu, a gerenciar informações do sistema, as definições da aplicação, as variáveis de ambiente, as extensões de site, cabeçalhos HTTP e variáveis de servidor. Também pode iniciar **Kudu** ao navegar para o ponto de final do SCM para a sua aplicação de função, como `https://<myfunctionapp>.scm.azurewebsites.net/` |

![Configurar o Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Opções de implementação

| | |
|-|-|
| ![Opções de implementação de aplicação de função no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | As funções permitem desenvolver o código de função no seu computador local. Em seguida, pode carregar seu projeto de aplicação de função local para o Azure. Para além da tradicional de carregamento FTP, as funções permite-lhe implementar a sua aplicação de função usando soluções de integração contínua populares, como o GitHub, do Azure DevOps, Dropbox, Bitbucket e outros. Para obter mais informações, consulte [implementação contínua para funções do Azure](functions-continuous-deployment.md). Para carregar manualmente através de FTP ou local Git, terá também [configurar as credenciais de implementação](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Aplicação de funções CORS no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Para impedir a execução de código mal-intencionado nos seus serviços, o serviço de aplicações bloqueia chamadas às suas aplicações de função de fontes externas. As funções suportam os recursos de várias origens (CORS) para permitem-lhe definir uma "lista aprovada" de origens permitidas a partir do qual as suas funções podem aceitar pedidos remotos de partilha.  |

![Configurar a aplicação de funções CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Autenticação

| | |
|-|-|
| ![Autenticação de aplicação de função no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Quando as funções utilizam um acionador HTTP, pode exigir a chamadas para ser autenticada pela primeira vez. Serviço de aplicações suporta a autenticação do Azure Active Directory e inicie sessão com fornecedores de redes sociais, como o Facebook, Microsoft e Twitter. Para obter detalhes sobre como configurar fornecedores de autenticação específicos, consulte [descrição geral da autenticação do serviço de aplicações do Azure](../app-service/overview-authentication-authorization.md). |

![Configurar a autenticação para uma aplicação de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Definição de API

| | |
|-|-|
| ![A definição no portal do Azure de swagger da API de aplicação de função](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | As funções suportam Swagger para permitir aos clientes consumir mais facilmente suas funções acionada por HTTP. Para obter mais informações sobre como criar definições de API com Swagger, visite [alojar uma API RESTful com CORS no App Service do Azure](../app-service/app-service-web-tutorial-rest-api.md). Também pode utilizar os Proxies de funções para definir uma única superfície de API para várias funções. Para obter mais informações, consulte [trabalhar com os Proxies de funções do Azure](functions-proxies.md). |

![Configurar API da aplicação de função](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Passos Seguintes

+ [Configurar as definições do serviço de aplicações do Azure](../app-service/web-sites-configure.md)
+ [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)



