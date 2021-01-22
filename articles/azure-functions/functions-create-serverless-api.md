---
title: Personalize um ponto final HTTP em Funções Azure
description: Saiba como personalizar um ponto final do gatilho HTTP em Funções Azure
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 7375a46245fbe523ddf0512bb5a55371adff64e9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683747"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Personalize um ponto final HTTP em Funções Azure

Neste artigo, você aprende como as Funções Azure permite-lhe construir APIs altamente escaláveis. As Funções Azure vêm com uma coleção de gatilhos e encadernações HTTP incorporadas, que facilitam a autoria de um ponto final em várias línguas, incluindo Node.js, C#, e muito mais. Neste artigo, você irá personalizar um gatilho HTTP para lidar com ações específicas no seu design API. Você também se preparará para o crescimento da sua API, integrando-a com Proxies de Funções Azure e criando APIs simulados. Estas tarefas são realizadas em cima do ambiente de computação sem servidor de Funções, por isso não tem que se preocupar com a escala de recursos - você pode apenas focar-se na sua lógica API.

## <a name="prerequisites"></a>Pré-requisitos 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

A função resultante será utilizada para o resto deste artigo.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta do Azure.

## <a name="customize-your-http-function"></a>Personalizar a sua função HTTP

Por predefinição, a função de gatilho HTTP está configurada para aceitar qualquer método HTTP. Também pode utilizar o URL padrão, `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` . Nesta secção, modifica a função para responder apenas aos pedidos GET com `/api/hello` . 

1. Navegue até à sua função no portal do Azure. Selecione **Integração** no menu esquerdo e, em seguida, selecione **HTTP (req)** no **Gatilho**.

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="Personalizar uma função HTTP":::

1. Utilize as definições do gatilho HTTP conforme especificado na tabela seguinte.

    | Campo | Valor da amostra | Description |
    |---|---|---|
    | Modelo de rota | /hello | Determina qual a rota utilizada para invocar esta função |
    | Nível de autorização | Anónimo | Opcional: torna a sua função acessível sem uma chave de API |
    | Métodos HTTP selecionados | GET | Permite apenas a utilização dos métodos HTTP selecionados para invocar esta função |

    Não incluiu o `/api` prefixo do caminho base no modelo de rota, porque é tratado por uma configuração global.

1. Selecione **Guardar**.

Para obter mais informações sobre a personalização das funções HTTP, consulte [as ligações HTTP funções Azure .](./functions-bindings-http-webhook.md)

### <a name="test-your-api"></a>Testar a API

Em seguida, teste a sua função para ver como funciona com a nova superfície da API:
1. Na página de função, selecione **Código + Teste** a partir do menu esquerdo.

1. Selecione Obter URL de **função** a partir do menu superior e copiar o URL. Confirme que agora usa o `/api/hello` caminho.
 
1. Copie o URL para um novo separador do browser ou para o seu cliente REST preferido. 

   Os navegadores usam GET por padrão.
 
1. Adicione parâmetros à cadeia de consulta no seu URL. 

   Por exemplo, `/api/hello/?name=John`.
 
1. Pressione a Entrada para confirmar que está a funcionar. Devias ver a resposta,*"Olá John."*

1. Também pode tentar ligar para o ponto final com outro método HTTP para confirmar que a função não é executada. Para tal, utilize um cliente REST, como cURL, Carteiro ou Violinista.

## <a name="proxies-overview"></a>Descrição geral dos Proxies

Na próxima secção, irás emergir a tua API através de um representante. Os Proxies de Funções do Azure permitem reencaminhar pedidos para outros recursos. Define um ponto final HTTP tal como com o gatilho HTTP. No entanto, em vez de escrever código para executar quando esse ponto final é chamado, você fornece um URL para uma implementação remota. Ao fazê-lo, permite-lhe compor várias fontes de API numa única superfície API, o que é fácil para os clientes consumirem, o que é útil se pretender construir a sua API como microserviços.

Um proxy pode apontar para qualquer recurso HTTP, como, por exemplo:
- Funções do Azure 
- Aplicações API no [Serviço de Aplicações Azure](../app-service/overview.md)
- Contentores do docker no [Serviço de Aplicações no Linux](../app-service/overview.md#app-service-on-linux)
- Qualquer outra API alojada

Para saber mais sobre proxies, veja [Trabalhar com os Proxies de Funções do Azure].

## <a name="create-your-first-proxy"></a>Criar o seu primeiro proxy

Nesta secção, você cria um novo proxy, que serve como um frontend para a sua API geral. 

### <a name="setting-up-the-frontend-environment"></a>Configurar o ambiente de front-end

Repita os passos para [Criar uma aplicação de funções](./functions-get-started.md) para criar uma nova aplicação de funções na qual vai criar o proxy. O URL desta nova aplicação serve como frontend para a nossa API, e a aplicação de função que estava a editar anteriormente serve como backend.

1. Navegue para a nova aplicação de funções de front-end no portal.
1. Selecione **Funcionalidades de Plataforma** e escolha **Definições da Aplicação**.
1. Desloque-se para as **definições de Aplicação**, onde os pares de chaves/valor são armazenados e crie uma nova definição com a chave `HELLO_HOST` . Defina o respetivo valor como o sistema anfitrião da sua aplicação de funções de back-end, como, por exemplo `<YourBackendApp>.azurewebsites.net`. Este valor faz parte do URL que copiou anteriormente ao testar a sua função HTTP. Vai fazer referência a esta definição na configuração mais à frente.

    > [!NOTE] 
    > As definições da aplicação são recomendadas para a configuração do sistema anfitrião para evitar uma dependência de ambiente hard-coded para o proxy. Com as definições da aplicação, pode mover a configuração do proxy entre ambientes e as definições da aplicação específicas do ambiente serão aplicadas.

1. Selecione **Guardar**.

### <a name="creating-a-proxy-on-the-frontend"></a>Criar um proxy no front-end

1. Volte para a sua aplicação de função frontal no portal.

1. No menu da esquerda, selecione **Proxies** e, em seguida, **selecione Adicionar**. 

1. Na página **New Proxy,** utilize as definições na tabela seguinte e, em seguida, selecione **Criar**.

    | Campo | Valor da amostra | Descrição |
    |---|---|---|
    | Nome | HelloProxy | Um nome amigável utilizado apenas para gestão |
    | Modelo de rota | /api/remotehello | Determina qual a rota utilizada para invocar este proxy |
    | URL de back-end | https://%HELLO_HOST%/api/hello | Especifica o ponto final para o qual o pedido deve ser transmitido por proxy |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="Criar um proxy":::

    As funções Azure Não fornecem o `/api` prefixo do caminho base, que deve ser incluído no modelo de rota. A `%HELLO_HOST%` sintaxe refere-se à definição de aplicação que criou anteriormente. O URL resolvido apontará para a sua função original.

1. Experimente o seu novo representante copiando o URL de procuração e testando-o no navegador ou com o seu cliente HTTP favorito:
    - Para uma utilização de funções anónimas:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"` .
    - Para uma função com autorização:   `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"` .

## <a name="create-a-mock-api"></a>Criar uma API fictícia

Em seguida, você usará um representante para criar uma API falsa para a sua solução. Este proxy permite que o desenvolvimento do cliente progrida, sem precisar do backend totalmente implementado. Mais tarde, pode criar uma nova app de função, que suporta esta lógica e redireciona o seu representante para a presente.

Para criar esta API falsa, vamos criar um novo proxy, desta vez usando o [Editor de Serviço de Aplicações.](https://github.com/projectkudu/kudu/wiki/App-Service-Editor) Para começar, navegue para a aplicação de funções no portal. Selecione **funcionalidades da Plataforma**, e em **Ferramentas de Desenvolvimento** encontre Editor de Serviço de **Aplicações.** O Editor de Serviço de Aplicações abre num novo separador.

Selecione `proxies.json` no painel de navegação esquerdo. Este ficheiro armazena a configuração para todos os seus proxies. Se utilizar um dos métodos de [implantação de Funções,](./functions-continuous-deployment.md)mantenha este ficheiro no controlo de origem. Para saber mais sobre este ficheiro, veja [Configuração avançada de proxies](./functions-proxies.md#advanced-configuration).

Se acompanhou até agora, o seu proxies.jsdeve parecer o seguinte:

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

Em seguida, vai adicionar a sua API falsa. Substitua o seu proxies.jsno ficheiro pelo seguinte código:

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

Este código adiciona um novo representante, `GetUserByName` sem a `backendUri` propriedade. Em vez de chamar outro recurso, modifica a resposta predefinida dos Proxies com uma substituição de resposta. As substituições de pedido e resposta também podem ser utilizadas em conjunto com um URL de back-end. Esta técnica é particularmente útil quando procura um sistema antigo, onde você pode precisar modificar cabeçalhos, parâmetros de consulta, e assim por diante. Para saber mais sobre substituições de pedido e resposta, veja [Modificar pedidos e respostas nos Proxies](./functions-proxies.md).

Teste a sua API fictícia, chamando o ponto final `<YourProxyApp>.azurewebsites.net/api/users/{username}` com um browser ou com o seu cliente REST favorito. Não se esqueça de substituir _{username}_ por um valor de cadeia que represente um nome de utilizador.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a construir e personalizar uma API em Funções Azure. Também aprendeu a reunir várias APIs, incluindo as fictícias, como uma superfície de API unificada. Pode utilizar estas técnicas para criar APIs com qualquer nível de complexidade, tudo isto enquanto as executa no modelo de computação sem servidor fornecido pelas Funções do Azure.

As referências seguintes podem ser úteis à medida que desenvolve ainda mais a sua API:

- [Enlaces HTTP das Funções do Azure](./functions-bindings-http-webhook.md)
- [Trabalhar com Proxies de Funções do Azure]
- [Documentar uma API de Funções do Azure (pré-visualização)](./functions-openapi-definition.md)


[Create your first function]: ./functions-get-started.md
[Trabalhar com Proxies de Funções do Azure]: ./functions-proxies.md
