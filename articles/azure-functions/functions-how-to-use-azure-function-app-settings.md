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
ms.openlocfilehash: 90b5009b3b260a0f4245459467ae248f800ad818
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094540"
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Como gerir uma aplicação de funções no portal do Azure 

Nas funções do Azure, uma aplicação de funções fornece o contexto de execução para as suas funções individuais. Comportamentos da aplicação de função aplicam-se a todas as funções alojadas por uma aplicação de função especificada. Este tópico descreve como configurar e gerir as suas aplicações de função no portal do Azure.

Para começar, vá para o [portal do Azure](http://portal.azure.com) e inicie sessão na sua conta do Azure. Na barra de pesquisa na parte superior do portal, escreva o nome da sua aplicação de função e selecione-a na lista. Depois de selecionar a sua aplicação function app, verá a página seguinte:

![Descrição geral da aplicação de função no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="favorite"></a>Funções favoritas no portal 

Por vezes, pode ser difícil encontrar os recursos no [portal do Azure]. Para tornar mais fácil encontrar as aplicações de função que criou, adicione aplicações Function App aos seus favoritos no portal. 

1. Inicie sessão no [Portal do Azure].

2. Clique na seta no canto inferior esquerdo para expandir todos os serviços, escreva `Functions` no campo **Filtro** e clique na estrela junto a **Aplicações Function App**.  
 
    ![Criar uma aplicação de função no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/functions-favorite-function-apps.png)

    É adicionado o ícone Functions ao menu no lado esquerdo do portal.

3. Feche o menu e desloque-se para a parte inferior para ver o ícone Functions. Clique neste ícone para ver uma lista de todas as suas aplicações Function App. Clique na sua aplicação Function App para trabalhar com funções nesta aplicação. 
 
    ![Aplicações de funções nos Favoritos](./media/functions-how-to-use-azure-function-app-settings/functions-function-apps-hub.png)
 
[Portal do Azure]: https://portal.azure.com/

## <a name="manage-app-service-settings"></a>Separador de definições de aplicação de função

![Função descrição geral da aplicação no portal do Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

O **definições** separador é onde pode atualizar a versão de runtime das funções utilizada pela sua aplicação de função. Também é onde pode gerir as chaves de anfitrião utilizadas para restringir o acesso HTTP para todas as funções alojadas pela aplicação de funções.

As funções suportam alojamento de consumo e planos de alojamento de serviço de aplicações. Para obter mais informações, consulte [escolher o plano de serviço correto para as funções do Azure](functions-scale.md). Para melhor previsibilidade no plano de consumo, o funções permite limitar a utilização da plataforma definindo uma quota de utilização diária, em segundos de gigabytes. Assim que a quota de utilização diária é atingida, a function app é parada. Uma aplicação de função parada como resultado de atingir o quota de gastos pode ser ativada novamente no mesmo contexto como estabelecer o diário de quota de gastos. Consulte a [funções do Azure, página de preços](http://azure.microsoft.com/pricing/details/functions/) para obter detalhes sobre a faturação.   

## <a name="platform-features-tab"></a>Guia de funcionalidades de plataforma

![Guia de funcionalidades de plataforma de aplicação de função.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Aplicações de funções executados em e são mantidas pela plataforma do App Service do Azure. Como tal, as suas aplicações de função tem acesso à maioria dos recursos da plataforma de alojamento de web do núcleo do Azure. O **funcionalidades de plataforma** guia é onde acessar as várias funcionalidades da plataforma do serviço de aplicações que pode utilizar nas suas aplicações de função. 

> [!NOTE]
> Nem todas as funcionalidades do serviço de aplicações estão disponíveis quando uma aplicação de função é executada no consumo plano de alojamento.

O resto deste tópico concentra-se as seguintes funcionalidades de serviço de aplicações no portal do Azure, que são úteis para as funções:

+ [Editor do serviço de aplicações](#editor)
+ [Definições da aplicação](#settings) 
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

### <a name="settings"></a>Definições da aplicação

| | |
|-|-|
| ![Definições de aplicação da aplicação de funções.](./media/functions-how-to-use-azure-function-app-settings/function-app-application-settings.png) | O serviço de aplicações **as definições da aplicação** painel é onde pode configurar e gerir versões de estrutura, depuração remota, as definições da aplicação e as cadeias de ligação. Quando integrar a sua aplicação de função com outros serviços de terceiros e do Azure, pode modificar essas definições aqui. Para eliminar uma definição, desloque-se para a direita e selecione o **X** ícone na extremidade direita da linha (não mostrada na imagem seguinte).

![Configurar definições da aplicação](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="console"></a>Consola do

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
| ![Opções de implementação de aplicação de função no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | As funções permitem desenvolver o código de função no seu computador local. Em seguida, pode carregar seu projeto de aplicação de função local para o Azure. Para além da tradicional de carregamento FTP, as funções permite-lhe implementar a sua aplicação de função usando soluções de integração contínua populares, como o GitHub, VSTS, Dropbox, Bitbucket e outros. Para obter mais informações, consulte [implementação contínua para funções do Azure](functions-continuous-deployment.md). Para carregar manualmente através de FTP ou local Git, terá também [configurar as credenciais de implementação](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Aplicação de funções CORS no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Para impedir a execução de código mal-intencionado nos seus serviços, o serviço de aplicações bloqueia chamadas às suas aplicações de função de fontes externas. As funções suportam os recursos de várias origens (CORS) para permitem-lhe definir uma "lista branca" de origens permitidas a partir do qual as suas funções podem aceitar pedidos remotos de partilha.  |

![Configurar a aplicação de funções CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Autenticação

| | |
|-|-|
| ![Autenticação de aplicação de função no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Quando as funções utilizam um acionador HTTP, pode exigir a chamadas para ser autenticada pela primeira vez. Serviço de aplicações suporta a autenticação do Azure Active Directory e inicie sessão com fornecedores de redes sociais, como o Facebook, Microsoft e Twitter. Para obter detalhes sobre como configurar fornecedores de autenticação específicos, consulte [descrição geral da autenticação do serviço de aplicações do Azure](../app-service/app-service-authentication-overview.md). |

![Configurar a autenticação para uma aplicação de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Definição de API

| | |
|-|-|
| ![A definição no portal do Azure de swagger da API de aplicação de função](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | As funções suportam Swagger para permitir aos clientes consumir mais facilmente suas funções acionada por HTTP. Para obter mais informações sobre como criar definições de API com Swagger, visite [alojar uma API RESTful com CORS no App Service do Azure](../app-service/app-service-web-tutorial-rest-api.md). Também pode utilizar os Proxies de funções para definir uma única superfície de API para várias funções. Para obter mais informações, consulte [trabalhar com os Proxies de funções do Azure](functions-proxies.md). |

![Configurar API da aplicação de função](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Passos Seguintes

+ [Configurar as definições do serviço de aplicações do Azure](../app-service/web-sites-configure.md)
+ [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)



