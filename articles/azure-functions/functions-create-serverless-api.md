---
title: Personalizar um ponto de extremidade HTTP no Azure Functions
description: Saiba como personalizar um ponto de extremidade de gatilho HTTP no Azure Functions
author: mattchenderson
ms.topic: conceptual
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 61b930eec1385b8c4054f9c202547a82e61e55e7
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769273"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Personalizar um ponto de extremidade HTTP no Azure Functions

Neste artigo, você aprende como Azure Functions permite que você crie APIs altamente escalonáveis. O Azure Functions vem com uma coleção de gatilhos e associações HTTP internos, que facilitam o autor de um ponto de extremidade em uma variedade de linguagens, incluindo node. C#js, e muito mais. Neste artigo, você personalizará um gatilho HTTP para manipular ações específicas em seu design de API. Também vai preparar-se para aumentar a sua API ao integrá-la com os Proxies de Funções do Azure e ao configurar APIs fictícias. Tudo isto é feito sobre o ambiente de computação sem servidor das Funções, para que não tenha de se preocupar com o dimensionamento de recursos – pode concentrar-se apenas na lógica da API.

## <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

A função resultante será usada para o restante deste artigo.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra o portal do Azure. Para tal, inicie sessão em [https://portal.azure.com](https://portal.azure.com) com a sua conta do Azure.

## <a name="customize-your-http-function"></a>Personalizar a sua função HTTP

Por predefinição, a sua função acionada por HTTP está configurada para aceitar qualquer método HTTP. Também existe um URL predefinido do formulário `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Se acompanhou o início rápido, `<funcname>` provavelmente é semelhante a "HttpTriggerJS1". Nesta secção, vai modificar a função para responder apenas a pedidos GET para a rota `/api/hello`. 

1. Navegue até à sua função no portal do Azure. Selecione **Integrar** no painel de navegação à esquerda.

    ![Personalizar uma função HTTP](./media/functions-create-serverless-api/customizing-http.png)

1. Use as configurações de gatilho HTTP conforme especificado na tabela.

    | Campo | Valor da amostra | Descrição |
    |---|---|---|
    | Métodos HTTP permitidos | Métodos selecionados | Determina que métodos HTTP podem ser utilizados para invocar esta função |
    | Métodos HTTP selecionados | GET | Permite apenas a utilização dos métodos HTTP selecionados para invocar esta função |
    | Modelo de rota | /hello | Determina qual a rota utilizada para invocar esta função |
    | Nível de Autorização | Anónimo | Opcional: torna a sua função acessível sem uma chave de API |

    > [!NOTE] 
    > Note que não incluiu o prefixo do caminho de base `/api` no modelo de rota, uma vez que é processado por uma definição global.

1. Clique em **Guardar**.

Pode saber mais sobre a personalização de funções HTTP em [Enlaces HTTP de Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook).

### <a name="test-your-api"></a>Testar a API

Em seguida, teste a função para vê-la funcionar com a nova superfície de API.
1. Navegue de volta para a página de desenvolvimento ao clicar no nome da função na navegação à esquerda.
1. Clique em **Obter URL de função** e copie o URL. Deverá ver que utiliza agora a rota `/api/hello`.
1. Copie o URL para um novo separador do browser ou para o seu cliente REST preferido. Os browsers utilizam GET por predefinição.
1. Adicione parâmetros à cadeia de consulta no URL, por exemplo `/api/hello/?name=John`
1. Prima "Enter" para confirmar que está a funcionar. Deverá ver a resposta "*Hello John*"
1. Também pode tentar chamar o ponto final com outro método HTTP para confirmar que a função não é executada. Para isso, terá de utilizar um cliente REST, como o cURL, Postman ou Fiddler.

## <a name="proxies-overview"></a>Descrição geral dos Proxies

Na secção seguinte, vai revelar a sua API através de um proxy. Os Proxies de Funções do Azure permitem reencaminhar pedidos para outros recursos. Defina um ponto final HTTP, tal como acontece com o acionador HTTP, mas em vez de escrever código para executar quando esse ponto final for chamado, forneça um URL para uma implementação remota. Isto permite-lhe compor várias origens de API numa única superfície de API, que é mais fácil para os clientes utilizarem. Este aspeto é particularmente útil se quiser criar a sua API como microsserviços.

Um proxy pode apontar para qualquer recurso HTTP, como, por exemplo:
- Funções do Azure 
- Aplicações API no [Serviço de Aplicações Azure](https://docs.microsoft.com/azure/app-service/overview)
- Contentores do docker no [Serviço de Aplicações no Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)
- Qualquer outra API alojada

Para saber mais sobre proxies, veja [Trabalhar com Proxies de Funções do Azure].

## <a name="create-your-first-proxy"></a>Criar o seu primeiro proxy

Nesta secção, vai criar um novo proxy que serve como um front-end para a sua API geral. 

### <a name="setting-up-the-frontend-environment"></a>Configurar o ambiente de front-end

Repita os passos para [Criar uma aplicação de funções](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) para criar uma nova aplicação de funções na qual vai criar o proxy. O URL desta nova aplicação irá servir como o front-end para a API e a aplicação de funções que editou anteriormente irá servir como um back-end.

1. Navegue para a nova aplicação de funções de front-end no portal.
1. Selecione **Funcionalidades de Plataforma** e escolha **Definições da Aplicação**.
1. Desloque o ecrã para baixo até **Definições da aplicação** onde os pares chave/valor são armazenados e crie uma nova definição com a chave "HELLO_HOST". Defina o respetivo valor como o sistema anfitrião da sua aplicação de funções de back-end, como, por exemplo `<YourBackendApp>.azurewebsites.net`. Isto é parte do URL que copiou anteriormente quando testou a sua função HTTP. Vai fazer referência a esta definição na configuração mais à frente.

    > [!NOTE] 
    > As definições da aplicação são recomendadas para a configuração do sistema anfitrião para evitar uma dependência de ambiente hard-coded para o proxy. Com as definições da aplicação, pode mover a configuração do proxy entre ambientes e as definições da aplicação específicas do ambiente serão aplicadas.

1. Clique em **Guardar**.

### <a name="creating-a-proxy-on-the-frontend"></a>Criar um proxy no front-end

1. Navegue de volta para a aplicação de funções de front-end no portal.
1. No painel de navegação à esquerda, clique no sinal de adição "+" junto a "Proxies".
    ![Criar um proxy](./media/functions-create-serverless-api/creating-proxy.png)
1. Utilize as definições de proxy conforme especificado na tabela. 

    | Campo | Valor da amostra | Descrição |
    |---|---|---|
    | Nome | HelloProxy | Um nome amigável utilizado apenas para gestão |
    | Modelo de rota | /api/remotehello | Determina qual a rota utilizada para invocar este proxy |
    | URL de back-end | https://%HELLO_HOST%/api/hello | Especifica o ponto final para o qual o pedido deve ser transmitido por proxy |
    
1. Tenha em atenção que os Proxies não fornecem o prefixo do caminho de base `/api`; este tem de ser incluído no modelo de rota.
1. A sintaxe `%HELLO_HOST%` vai fazer referência à definição da aplicação que criou anteriormente. O URL resolvido apontará para a sua função original.
1. Clique em **Criar**.
1. Pode experimentar o novo proxy ao copiar o URL do Proxy e testá-lo no browser ou com o seu cliente HTTP favorito.
    1. Para uma função anónima, utilize:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"`
    1. Para uma função com autorização, utilize:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>Criar uma API fictícia

Em seguida, vai utilizar um proxy para criar uma API fictícia para a sua solução. Isto permite um avanço no desenvolvimento do cliente, sem ser necessário que o back-end esteja totalmente implementado. Numa fase posterior do desenvolvimento, pode criar uma nova aplicação de funções que suporte esta lógica e redirecionar o proxy para a mesma.

Para criar esta API fictícia, vamos criar um novo proxy, desta vez com o [Editor do Serviço de Aplicações](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Para começar, navegue para a aplicação de funções no portal. Selecione **Funcionalidades de plataforma** e, em **Ferramentas de desenvolvimento**, localize **Editor do Serviço de Aplicações**. Ao clicar aqui, o Editor do Serviço de Aplicações abrirá num novo separador.

Selecione `proxies.json` no painel de navegação esquerdo. Este é o ficheiro que armazena a configuração de todos os proxies. Se utilizar um dos [métodos de implementação de Funções](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment), este é o ficheiro que vai manter no controlo do código fonte. Para saber mais sobre este ficheiro, veja [Configuração avançada de proxies](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration).

Se acompanhou até aqui o início rápido, o ficheiro proxies.json deve ter um aspeto semelhante ao seguinte:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

Em seguida, vai adicionar a API fictícia. Substitua o ficheiro proxies.json pelo seguinte:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Esta ação adiciona um novo proxy, "GetUserByName", sem a propriedade backendUri. Em vez de chamar outro recurso, modifica a resposta predefinida dos Proxies com uma substituição de resposta. As substituições de pedido e resposta também podem ser utilizadas em conjunto com um URL de back-end. Isso é particularmente útil ao fazer o proxy para um sistema herdado, onde talvez seja necessário modificar cabeçalhos, parâmetros de consulta, etc. Para saber mais sobre substituições de solicitação e resposta, consulte [modificando solicitações e respostas em proxies](https://docs.microsoft.com/azure/azure-functions/functions-proxies).

Teste a sua API fictícia, chamando o ponto final `<YourProxyApp>.azurewebsites.net/api/users/{username}` com um browser ou com o seu cliente REST favorito. Não se esqueça de substituir _{username}_ por um valor de cadeia que represente um nome de utilizador.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu como criar e personalizar uma API no Azure Functions. Também aprendeu a reunir várias APIs, incluindo as fictícias, como uma superfície de API unificada. Pode utilizar estas técnicas para criar APIs com qualquer nível de complexidade, tudo isto enquanto as executa no modelo de computação sem servidor fornecido pelas Funções do Azure.

As referências seguintes podem ser úteis à medida que desenvolve ainda mais a sua API:

- [Enlaces HTTP das Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Trabalhar com Proxies de Funções do Azure]
- [Documentar uma API de Funções do Azure (pré-visualização)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Trabalhar com Proxies de Funções do Azure]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
