---
title: Políticas de integração da Azure API Management Dapr | Microsoft Docs
description: Conheça as políticas de gestão da AZure API para interagir com extensões de microserviços Dapr.
author: vladvino
ms.author: vlvinogr
ms.date: 02/18/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 051bf4398555f318f613c66d58ec65be1d30e215
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646814"
---
# <a name="api-management-dapr-integration-policies"></a>Políticas de integração da API Management Dapr

Este tópico fornece uma referência para as políticas de gestão da API de integração da Dapr. Dapr é um tempo de execução portátil para a construção de aplicações apátridas e apátridas baseadas em microserviços com qualquer linguagem ou enquadramento. Codifica os padrões comuns de microserviços, como a descoberta de serviço e a invocação com lógica de retírição de construção, publicar e subscrever com semântica de entrega pelo menos uma vez, ou recursos de ligação pluggable para facilitar a composição usando serviços externos. Vá a [dapr.io](https://dapr.io) para obter informações detalhadas e instruções sobre como começar com Dapr. Para obter informações sobre políticas de adição e configuração, consulte [Políticas em Gestão de API.](api-management-howto-policies.md)

> [!CAUTION]
> As políticas referenciadas neste tópico estão em Visualização Pública e estão sujeitas a [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> As políticas referenciadas neste tópico funcionam apenas na [versão auto-acolhida da porta de entrada de Gestão da API](self-hosted-gateway-overview.md) com o suporte da Dapr ativado.

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>Ativar o suporte da Dapr na porta de entrada auto-hospedada

Para ligar o suporte da Dapr no gateway auto-hospedado adicione as [anotações da Dapr](https://github.com/dapr/docs/blob/master/README.md) abaixo ao [modelo de implementação de Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md) substituindo "nome de aplicação" por um nome desejado. A passagem completa da configuração e utilização da Gestão da API com a Dapr está disponível [aqui.](https://aka.ms/apim/dapr/walkthru)
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>Políticas de integração do Distributed Application Runtime (Dapr)

-  [Enviar pedido a um serviço](api-management-dapr-policies.md#invoke): Utiliza o tempo de execução da Dapr para localizar e comunicar de forma fiável com um microserviço Dapr. Para saber mais sobre a invocação de serviço em Dapr, consulte a descrição neste ficheiro [README.](https://github.com/dapr/docs/blob/master/README.md#service-invocation)
-  [Enviar mensagem para Pub/Sub topic](api-management-dapr-policies.md#pubsub): Usa o tempo de execução da Dapr para publicar uma mensagem para um tópico de publicação/subscrição. Para saber mais sobre publicar/subscrever mensagens em Dapr, consulte a descrição neste ficheiro [README.](https://github.com/dapr/docs/blob/master/README.md)
-  [Ligação da saída do gatilho](api-management-dapr-policies.md#bind): Utiliza o tempo de execução da Dapr para invocar um sistema externo através da ligação de saída. Para saber mais sobre encadernações em Dapr, consulte a descrição deste ficheiro [README.](https://github.com/dapr/docs/blob/master/README.md)

## <a name="send-request-to-a-service"></a><a name="invoke"></a> Enviar pedido para um serviço

Esta política define o URL-alvo para o pedido atual `http://localhost:3500/v1.0/invoke/{app-id}[.{ns-name}]/method/{method-name}` de substituição dos parâmetros do modelo por valores especificados na declaração de política.

A apólice pressupõe que o Dapr funciona num contentor de sidecar na mesma cápsula que o portal. Ao receber o pedido, o tempo de execução da Dapr realiza a descoberta do serviço e a invocação real, incluindo possível tradução de protocolo entre HTTP e gRPC, retries, rastreio distribuído e manipulação de erros.

### <a name="policy-statement"></a>Declaração política

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" dapr-namespace="ns-name" />
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

O exemplo a seguir demonstra a invocação do método denominado "back" no microserviço chamado "eco". A `set-backend-service` política define o URL de destino para `http://localhost:3500/v1.0/invoke/echo.echo-app/method/back` . A `forward-request` apólice envia o pedido para o tempo de execução da Dapr, que o entrega ao microserviço.

A `forward-request` política é mostrada aqui para ser clara. A política é tipicamente "herdada" do âmbito global através da `base` palavra-chave.

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" dapr-namespace="echo-app" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementos

| Elemento             | Descrição  | Obrigatório |
|---------------------|--------------|----------|
| set-backend-serviço | Elemento de raiz | Sim      |

### <a name="attributes"></a>Atributos

| Atributo        | Descrição                     | Obrigatório | Predefinição |
|------------------|---------------------------------|----------|---------|
| backend-id       | Deve ser definido para "dapr"           | Sim      | N/D     |
| dapr-app-id      | Nome do microserviço alvo. Usado para formar o parâmetro [appId](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) em Dapr.| Sim | N/D |
| dapr-método      | Nome do método ou url para invocar no microserviço-alvo. Mapeia o parâmetro [do nome-método](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) em Dapr.| Sim | N/D |
| espaço dapr-nome   | Nome do espaço-nome onde reside o microserviço-alvo. Usado para formar o parâmetro [appId](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) em Dapr.| Não | N/D |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

- **Secções políticas:** entrada
- **Âmbitos de política:** todos os âmbitos

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> Enviar mensagem para pub/sub tópico

Esta política instrui a porta de entrada da API Management para enviar uma mensagem para um tópico Dapr Publish/Subscribe. A política realiza-o fazendo um pedido HTTP POST para `http://localhost:3500/v1.0/publish/{{pubsub-name}}/{{topic}}` substituir os parâmetros do modelo e adicionar conteúdo especificado na declaração de política.

A apólice pressupõe que o tempo de funcionamento do Dapr está a funcionar num contentor lateral na mesma cápsula que o portal. O runtime dapr implementa a semântica Pub/Sub.

### <a name="policy-statement"></a>Declaração política

```xml
<publish-to-dapr pubsub-name="pubsub-name" topic="topic-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

O exemplo a seguir demonstra o envio do corpo do pedido atual para o [tópico](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters) "novo" do [componente](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters)"encomendas" Pub/Sub . A resposta recebida do tempo de execução da Dapr é armazenada na entrada "dapr-response" da coleção Variables no objeto [de contexto.](api-management-policy-expressions.md#ContextVariables)

Se o tempo de execução da Dapr não conseguir localizar o tópico alvo, por exemplo, e responder com um erro, a secção "on-error" é ativada. A resposta recebida do tempo de execução da Dapr é devolvida ao autor da chamada verbatim. Caso contrário, a resposta por defeito `200 OK` é devolvida.

A secção "backend" está vazia e o pedido não é reencaminhado para o backend.

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
           pubsub-name="orders"
               topic="new"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementos

| Elemento             | Descrição  | Obrigatório |
|---------------------|--------------|----------|
| publicar-a-dapr     | Elemento de raiz | Sim      |

### <a name="attributes"></a>Atributos

| Atributo        | Descrição                     | Obrigatório | Predefinição |
|------------------|---------------------------------|----------|---------|
| pubsub-nome      | O nome do componente pubSub alvo. Mapas para o parâmetro [pubsubname](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) em Dapr. Se não estiver presente, o valor do atributo __tópico__ deve ser na forma de `pubsub-name/topic-name` .    | Não       | Nenhum    |
| tópico            | O nome do tópico. Mapas para o parâmetro [tópico](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) em Dapr.               | Sim      | N/D     |
| ignorar erro     | Se for definido para `true` instruir a política para não desencadear a secção ["on-error"](api-management-error-handling-policies.md) ao receber erro do tempo de execução da Dapr | Não | `false` |
| resposta-nome variável | Nome da entrada de recolha de [variáveis](api-management-policy-expressions.md#ContextVariables) para utilizar para armazenar resposta a partir do tempo de execução da Dapr | Não | Nenhum |
| tempo limite | Tempo (em segundos) para esperar que o tempo de execução da Dapr responda. Pode variar de 1 a 240 segundos. | Não | 5 |
| modelo | Motor templário para usar para transformar o conteúdo da mensagem. "Líquido" é o único valor suportado. | Não | Nenhum |
| tipo de conteúdo | Tipo de conteúdo da mensagem. "aplicação/json" é o único valor suportado. | Não | Nenhum |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

- **Secções políticas:** entrada, saída, erro
- **Âmbitos de política:** todos os âmbitos

## <a name="trigger-output-binding"></a><a name="bind"></a> Ligação da saída do gatilho

Esta política instrui a porta de entrada da API Management para desencadear uma [ligação](https://github.com/dapr/docs/blob/master/README.md)da Dapr de saída. A política realiza-o fazendo um pedido HTTP POST para `http://localhost:3500/v1.0/bindings/{{bind-name}}` substituir o parâmetro do modelo e adicionar conteúdo especificado na declaração de política.

A apólice pressupõe que o tempo de funcionamento do Dapr está a funcionar num contentor lateral na mesma cápsula que o portal. O tempo de execução da Dapr é responsável por invocar o recurso externo representado pela ligação.

### <a name="policy-statement"></a>Declaração política

```xml
<invoke-dapr-binding name="bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <metadata>
        <item key="item-name"><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>Exemplos

#### <a name="example"></a>Exemplo

O exemplo a seguir demonstra o desencadear de uma ligação de saída denominada "sistemas externos" com o nome de operação "criar", metadados constituídos por dois itens chave/valor denominados "fonte" e "cliente-ip", e o organismo proveniente do pedido original. A resposta recebida do tempo de execução da Dapr é capturada na entrada "bind-response" da coleção De Variáveis no objeto de [contexto.](api-management-policy-expressions.md#ContextVariables)

Se o tempo de execução da Dapr falhar por alguma razão e responder com um erro, a secção "on-error" é ativada e a resposta recebida do tempo de execução da Dapr é devolvida ao verbatim do chamador. Caso contrário, a resposta por defeito `200 OK` é devolvida.

A secção "backend" está vazia e o pedido não é reencaminhado para o backend.

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementos

| Elemento             | Descrição  | Obrigatório |
|---------------------|--------------|----------|
| invocar-dapr-binding | Elemento de raiz | Sim      |
| do IdP            | Ligação de metadados específicos sob a forma de pares chave/valor. Mapeia para a propriedade [de metadados](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) em Dapr. | Não |
| dados            | Conteúdo da mensagem. Mapas para a propriedade de [dados](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) em Dapr. | Não |


### <a name="attributes"></a>Atributos

| Atributo        | Descrição                     | Obrigatório | Predefinição |
|------------------|---------------------------------|----------|---------|
| name            | Nome de ligação ao alvo. Deve coincidir com o nome das encadernações [definidas](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#bindings-structure) em Dapr.           | Sim      | N/D     |
| operation       | Nome de operação-alvo (específico de ligação). Mapas para a propriedade [da operação](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) em Dapr. | Não | Nenhum |
| ignorar erro     | Se for definido para `true` instruir a política para não desencadear a secção ["on-error"](api-management-error-handling-policies.md) ao receber erro do tempo de execução da Dapr | Não | `false` |
| resposta-nome variável | Nome da entrada de recolha de [variáveis](api-management-policy-expressions.md#ContextVariables) para utilizar para armazenar resposta a partir do tempo de execução da Dapr | Não | Nenhum |
| tempo limite | Tempo (em segundos) para esperar que o tempo de execução da Dapr responda. Pode variar de 1 a 240 segundos. | Não | 5 |
| modelo | Motor templário para usar para transformar o conteúdo da mensagem. "Líquido" é o único valor suportado. | Não | Nenhum |
| tipo de conteúdo | Tipo de conteúdo da mensagem. "aplicação/json" é o único valor suportado. | Não | Nenhum |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

- **Secções políticas:** entrada, saída, erro
- **Âmbitos de política:** todos os âmbitos
