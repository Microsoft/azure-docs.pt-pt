---
title: Definições a montante no Serviço Azure SignalR
description: Introdução de configurações a montante e protocolo de mensagens a montante
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 7434e8796ddcd89968a0ffa0328a823d635f51c9
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988666"
---
# <a name="upstream-settings"></a>Definições de origem

A montante é uma funcionalidade que permite ao Serviço SignalR enviar mensagens e eventos de ligação a um conjunto de pontos finais no modo sem servidor. A montante pode ser usado para invocar o método do hub dos clientes em modo sem servidor e deixar que os pontos finais sejam notificados quando as ligações do cliente estiverem ligadas ou desligadas.

> [!NOTE]
> Só o modo sem servidor pode configurar as definições a montante.

## <a name="upstream-settings-details"></a>Detalhes de configurações a montante

As configurações a montante consistem numa lista de itens sensíveis à ordem. Cada item é composto por um `URL Template` , que especifica para onde as mensagens enviam, um conjunto e `Rules` `Authentication` configurações. Quando o evento especificado acontecer, o item `Rules` será verificado um por um em ordem e as mensagens serão enviadas para o URL upstream do primeiro item correspondente.

### <a name="url-template-settings"></a>Definições de modelo de URL

O URL pode ser parametrizado para suportar vários padrões. Existem três parâmetros predefinidos:

|Parâmetro predefinido|Description|
|---------|---------|
|{hub}| Hub é um conceito de SignalR. Um hub é uma unidade de isolamento, o âmbito de entrega de utilizadores e mensagens é limitado a um hub|
|{categoria}| A categoria pode ser um dos seguintes valores: <ul><li>**conexões**: Eventos de vida de ligação. Disparado quando uma ligação ao cliente está ligada ou desligada. Incluindo eventos *conectados* e *desligados*</li><li>**mensagens:** Disparado quando os clientes invocam o método do hub. Incluindo todos os outros eventos, exceto os da categoria *de ligações*</li></ul>|
|{evento}| Para a categoria *de mensagens,* o evento é o *alvo* na [mensagem de invocação](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) que os clientes enviam. Para a categoria *de ligações, apenas* é utilizado *ligado* e *desligado.*|

Estes parâmetros predefinidos podem ser utilizados no e os `URL Pattern` parâmetros serão substituídos por um valor especificado ao avaliar o URL upstream. Por exemplo, 
```
http://host.com/{hub}/api/{category}/{event}
```
Quando uma ligação do cliente no hub 'chat' estiver ligada, uma mensagem será enviada para URL:
```
http://host.com/chat/api/connections/connected
```
Quando um cliente no hub `chat` invoca o método do `broadcast` hub, uma mensagem será enviada para a URL:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rules-settings"></a>Definições de regras

Pode definir regras para *regras do hub,* *regras de categoria* e *regras de eventos* separadamente. A regra de correspondência suporta três formatos. Tome as *Regras* do Evento como exemplo:
- Utilize asterisco para combinar com quaisquer eventos.
- Vírgula do utilizador(,) para se juntar a vários eventos. Por exemplo, `connected, disconnected` corresponde a eventos *ligados* e *desligados*.
- Use o nome completo do evento para combinar com o evento. Por exemplo, `connected` corresponde a um evento *conectado.*

### <a name="authentication-settings"></a>Definições de autenticação

Pode configurar *autenticação* para cada item upstream configurar separadamente. Quando configurado *Autenticação,* será definido um sinal no cabeçalho de *autenticação* da mensagem a montante. Corrente, Serviço SignalR suporta o seguinte tipo de autenticação
- Nenhum
- ManagedIdentity

Ao selecionar *ManagedIdentity,* deve ativar a Identidade Gerida no Serviço SignalR com antecedência e especificar opcionalmente um *Recurso*. Veja [como utilizar identidades geridas para o Serviço Azure SignalR](howto-use-managed-identity.md) para mais detalhes.

## <a name="create-upstream-settings"></a>Criar configurações a montante

### <a name="create-upstream-settings-via-azure-portal"></a>Criar configurações a montante através do portal Azure

1. Vá ao Serviço Azure SignalR.
2. Clique nas *Definições* brandas e altere o *modo de serviço* para *Serverless*. As *Definições a montante* serão mostradas como abaixo:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Definições de origem":::

3. Preencha os urls no *Padrão de URL upstream,* em seguida, as configurações como *As Regras do Hub* mostrarão o valor predefinido.
4. Para definir configurações como *Regras do Hub*, *Regras do Evento,* *Regras de Categoria* e *Autenticação A montante,* clique no valor das *regras*do Hub . Uma página que permite editar as definições aparece como mostrado abaixo:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Definições de origem":::

5. Para configurar *a autenticação a montante,* certifique-se de que ativou a identidade gerida primeiro e, em seguida, selecione *Use Managed Identity* em *Autenticação A montante*. De acordo com as suas necessidades, pode escolher quaisquer opções em conformidade com *o ID de Recursos Auth.* Veja [como ativar a identidade gerida](howto-use-managed-identity.md) para mais detalhes.

### <a name="create-upstream-settings-via-arm-template"></a>Criar configurações a montante através do modelo ARM

Para criar definições a montante utilizando um [modelo de Gestor de Recursos,](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)defina a propriedade na `upstream` `properties` propriedade. Os seguintes snippets mostram como definir a `upstream` propriedade para criar e atualizar configurações a montante.

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="signalr-serverless-protocol"></a>Protocolo sem servidor SignalR

Serviço SignalR enviando mensagens para pontos finais que seguem os seguintes protocolos.

### <a name="method"></a>Método

POST

### <a name="request-header"></a>Cabeçalho do pedido

|Name |Description|
|---------|---------|
|X-ASRS-Connection-Id |O id de ligação para a ligação ao cliente|
|X-ASRS-Hub |O centro a que a ligação ao cliente pertence|
|Categoria X-ASRS |A categoria a que a mensagem pertence|
|X-ASRS-Evento |O evento a que a mensagem pertence|
|Assinatura X-ASRS |Um HMAC que usou para validação. Consulte [a Signature](#signature) para mais detalhes.|
|X-ASRS-User-Claims |Um grupo de reclamações da ligação ao cliente|
|X-ASRS-User-Id |A identidade do utilizador do cliente que envia a mensagem|
|Consulta X-ASRS-Cliente |A consulta do pedido quando os clientes se ligam ao serviço|
|Autenticação |Um símbolo opcional ao utilizar *a ManagedIdentity* |

### <a name="request-body"></a>Corpo do pedido

#### <a name="connected"></a>Ligada

Tipo de conteúdo: aplicação/json

#### <a name="disconnected"></a>Desligado

Tipo de conteúdo:`application/json`

|Name  |Tipo  |Description  |
|---------|---------|---------|
|Erro |string |A mensagem de erro da ligação fechou. Esvazie quando as ligações se fecham sem erro|

#### <a name="invocation-message"></a>Mensagem de invocação

Tipo de conteúdo: `application/json` ou`application/x-msgpack`

|Name  |Tipo  |Description  |
|---------|---------|---------|
|InvocationId |string | Uma corda opcional representa uma mensagem de invocação. Encontre detalhes em [Invocações.](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations)|
|Destino |string | O mesmo que o *evento* e o mesmo que o *alvo* na [mensagem de Invocação.](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) |
|Argumentos |Matriz de objeto |Um Array que contém argumentos para aplicar ao método referido no Target. |

### <a name="signature"></a>Assinatura

O serviço calculará o código SHA256 para o `X-ASRS-Connection-Id` valor utilizando a chave de acesso primário e a chave de acesso secundário como chave de acesso secundário como `HMAC` chave, e irá defini-lo no `X-ASRS-Signature` cabeçalho ao esclarçar pedidos HTTP para o Upstream:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Passos seguintes

- [Como utilizar identidades geridas para o Serviço Azure SignalR](howto-use-managed-identity.md)
- [Desenvolvimento das Funções do Azure e configuração com o Azure SignalR Service](signalr-concept-serverless-development-config.md)
