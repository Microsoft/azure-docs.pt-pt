---
title: Definições a montante no Serviço Azure SignalR
description: Obtenha uma introdução de configurações a montante e protocolos de mensagens a montante.
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 33df4410b9dd82fd0b1c732eb03ab5e0e77e9869
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763120"
---
# <a name="upstream-settings"></a>Definições de origem

A montante é uma funcionalidade de pré-visualização que permite ao Serviço Azure SignalR enviar mensagens e eventos de ligação a um conjunto de pontos finais no modo sem servidor. Pode utilizar a montante para invocar um método de hub dos clientes em modo sem servidor e deixar que os pontos finais sejam notificados quando as ligações do cliente estiverem ligadas ou desligadas.

> [!NOTE]
> Só o modo sem servidor pode configurar as definições a montante.

## <a name="details-of-upstream-settings"></a>Detalhes das configurações a montante

As configurações a montante consistem numa lista de itens sensíveis à ordem. Cada item é composto por:

* Um modelo de URL, que especifica para onde as mensagens são enviadas.
* Um conjunto de regras.
* Configurações de autenticação. 

Quando o evento especificado acontece, as regras de um item são verificadas uma a uma em ordem. As mensagens serão enviadas para o URL a montante do primeiro item correspondente.

### <a name="url-template-settings"></a>Definições de modelo de URL

Pode parametrizar o URL para suportar vários padrões. Existem três parâmetros predefinidos:

|Parâmetro predefinido|Description|
|---------|---------|
|{hub}| Um hub é um conceito de Serviço Azure SignalR. Um centro é uma unidade de isolamento. O âmbito dos utilizadores e a entrega de mensagens estão limitados a um hub.|
|{categoria}| Uma categoria pode ser um dos seguintes valores: <ul><li>**conexões**: Eventos de vida de ligação. É disparado quando uma ligação com o cliente está ligada ou desligada. Inclui eventos conectados e desligados.</li><li>**mensagens:** Disparado quando os clientes invocam um método de hub. Inclui todos os outros eventos, exceto os da categoria **de ligações.**</li></ul>|
|{evento}| Para a categoria **de mensagens,** um evento é o alvo numa [mensagem de invocação](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) que os clientes enviam. Para a categoria **de ligações, apenas** são *ligadas* e *desligadas.*|

Estes parâmetros predefinidos podem ser usados no padrão URL. Os parâmetros serão substituídos por um valor especificado quando estiver a avaliar o URL a montante. Por exemplo: 
```
http://host.com/{hub}/api/{category}/{event}
```
Quando uma ligação do cliente no hub "chat" estiver ligada, uma mensagem será enviada para este URL:
```
http://host.com/chat/api/connections/connected
```
Quando um cliente no centro de "chat" invoca o método do `broadcast` hub, uma mensagem será enviada para este URL:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="key-vault-secret-reference-in-url-template-settings"></a>Referência secreta do cofre chave nas definições do modelo de URL

O URL de a montante não é encriptação em repouso. Se tiver alguma informação sensível, é sugerido que use o Cofre chave para salvá-los onde o controlo de acesso tem melhor seguro. Basicamente, pode ativar a identidade gerida do Serviço Azure SignalR e, em seguida, conceder permissão de leitura numa instância do Cofre de Chaves e usar referência key Vault em vez de texto simples em Padrão DE URL Upstream.

1. Adicione uma identidade atribuída ao sistema ou identidade atribuída ao utilizador. Ver [Como adicionar identidade gerida no Portal Azure](./howto-use-managed-identity.md#add-a-system-assigned-identity)

2. Grant Secret leia permissão para a identidade gerida nas políticas de acesso no Cofre de Chaves. Consulte [atribuir uma política de acesso ao Cofre de Chaves utilizando o portal Azure](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal)

3. Substitua o seu texto sensível pela sintaxe `{@Microsoft.KeyVault(SecretUri=<secret-identity>)}` no Padrão URL A montante.

> [!NOTE]
> O conteúdo secreto só relê quando altera as definições de Upstream ou altera a identidade gerida. Certifique-se de que concedeu permissão secreta de leitura à identidade gerida antes de usar a referência secreta do Cofre chave.

### <a name="rule-settings"></a>Definições de regras

Pode definir regras para *regras de hub,* *regras de categoria* e *regras de eventos* separadamente. A regra de correspondência suporta três formatos. Tome as regras do evento como um exemplo:
- Use um asterisco para combinar com quaisquer eventos.
- Use uma vírgula (,) para se juntar a vários eventos. Por exemplo, `connected, disconnected` corresponde aos eventos ligados e desligados.
- Use o nome completo do evento para combinar com o evento. Por exemplo, `connected` corresponde ao evento conectado.

> [!NOTE]
> Se estiver a utilizar funções Azure e [o gatilho SignalR,](../azure-functions/functions-bindings-signalr-service-trigger.md)o gatilho SignalR exporá um único ponto final no seguinte `<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>` formato:
> Pode configurar as **definições do modelo de URL** para este url e manter as **definições de Regras** padrão. Consulte a [integração do Serviço SignalR](../azure-functions/functions-bindings-signalr-service-trigger.md#signalr-service-integration) para obter mais detalhes sobre como encontrar `<Function_App_URL>` e `<API_KEY>` .

### <a name="authentication-settings"></a>Definições de autenticação

Pode configurar a autenticação para cada item de regulação a montante separadamente. Ao configurar a autenticação, é definido um token no `Authentication` cabeçalho da mensagem a montante. Atualmente, o Serviço Azure SignalR suporta os seguintes tipos de autenticação:
- `None`
- `ManagedIdentity`

Ao `ManagedIdentity` selecionar, deve ativar uma identidade gerida no Serviço Azure SignalR com antecedência e especificar opcionalmente um recurso. Consulte [as identidades geridas para o Serviço Azure SignalR](howto-use-managed-identity.md) para obter mais detalhes.

## <a name="create-upstream-settings-via-the-azure-portal"></a>Criar configurações a montante através do portal Azure

1. Vá ao Serviço Azure SignalR.
2. Selecione **Definições** e altere **o modo de serviço** para **serverless**. As definições a montante aparecerão:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Definições de origem":::

3. Adicione URLs sob **o padrão de URL a montante.** Em seguida, as definições como **as Regras do Hub** mostrarão o valor predefinido.
4. Para definir as definições para **as regras do hub**, **regras de eventos,** **regras de categoria** e **autenticação a montante,** selecione o valor das **regras** do Hub . Aparece uma página que lhe permite editar definições:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Detalhes de definição a montante":::

5. Para configurar **a Autenticação A montante,** certifique-se de que ativou uma identidade gerida primeiro. Em seguida, selecione **Use Managed Identity**. De acordo com as suas necessidades, pode escolher quaisquer opções em conformidade com **o ID de Recursos Auth.** Consulte [as identidades geridas para o Serviço Azure SignalR](howto-use-managed-identity.md) para obter mais detalhes.

## <a name="create-upstream-settings-via-resource-manager-template"></a>Criar definições a montante através do modelo de Gestor de Recursos

Para criar configurações a montante utilizando um [modelo de Gestor de Recursos Azure,](../azure-resource-manager/templates/overview.md)defina a propriedade na `upstream` `properties` propriedade. O seguinte corte mostra como definir a `upstream` propriedade para criar e atualizar configurações a montante.

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

## <a name="serverless-protocols"></a>Protocolos sem servidor

O Serviço Azure SignalR envia mensagens para pontos finais que seguem os seguintes protocolos. Pode utilizar [a ligação do gatilho signalR service](../azure-functions/functions-bindings-signalr-service-trigger.md) com a App de Função, que trata destes protocolos para si.

### <a name="method"></a>Método

POST

### <a name="request-header"></a>Cabeçalho do pedido

|Nome |Descrição|
|---------|---------|
|X-ASRS-Connection-Id |A identificação da ligação para a ligação ao cliente.|
|X-ASRS-Hub |O centro a que pertence a ligação com o cliente.|
|Categoria X-ASRS |A categoria a que a mensagem pertence.|
|X-ASRS-Evento |O evento a que a mensagem pertence.|
|Assinatura X-ASRS |Um código de autenticação de mensagem baseado em haxixe (HMAC) que é usado para validação. Consulte [a Signature](#signature) para mais detalhes.|
|X-ASRS-User-Claims |Um grupo de reclamações da ligação com o cliente.|
|X-ASRS-User-Id |A identidade do utilizador do cliente que envia a mensagem.|
|Consulta X-ASRS-Cliente |A consulta do pedido quando os clientes se ligam ao serviço.|
|Autenticação |Um símbolo opcional quando se está a `ManagedIdentity` usar. |

### <a name="request-body"></a>Corpo do pedido

#### <a name="connected"></a>Ligada

Tipo de conteúdo: aplicação/json

#### <a name="disconnected"></a>Desligado

Tipo de conteúdo: `application/json`

|Nome  |Tipo  |Description  |
|---------|---------|---------|
|Erro |string |A mensagem de erro de uma ligação fechada. Esvazie quando as ligações fecharem sem erro.|

#### <a name="invocation-message"></a>Mensagem de invocação

Tipo de conteúdo: `application/json` ou `application/x-msgpack`

|Nome  |Tipo  |Description  |
|---------|---------|---------|
|InvocationId |string | Uma corda opcional que representa uma mensagem de invocação. Encontre detalhes em [Invocações.](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations)|
|Destino |string | O mesmo que o evento e o mesmo que o alvo numa [mensagem de invocação.](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) |
|Argumentos |Matriz de objeto |Uma matriz que contém argumentos para aplicar ao método referido em `Target` . |

### <a name="signature"></a>Assinatura

O serviço calculará o código SHA256 para o `X-ASRS-Connection-Id` valor utilizando a chave de acesso primária e a chave de acesso secundária como `HMAC` chave. O serviço irá defini-lo no `X-ASRS-Signature` cabeçalho ao es fazer pedidos HTTP a montante:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Passos seguintes

- [Identidades geridas para o Serviço Azure SignalR](howto-use-managed-identity.md)
- [Desenvolvimento das Funções do Azure e configuração com o Azure SignalR Service](signalr-concept-serverless-development-config.md)
- [Manuseie as mensagens do Serviço SignalR (ligação do gatilho)](../azure-functions/functions-bindings-signalr-service-trigger.md)
- [Amostra de ligação do gatilho do sinalr](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)
