---
title: Lidar com mensagens grandes usando o chunking
description: Aprenda a lidar com grandes tamanhos de mensagens utilizando o chunking em tarefas automatizadas e fluxos de trabalho que cria com apps Azure Logic
services: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 12/18/2020
ms.openlocfilehash: de4af34182fc1a95968e95d322a6ec35101a3dc9
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695871"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Lidar com mensagens grandes com chunking em Azure Logic Apps

Ao manusear mensagens, as Aplicações Lógicas limitam o conteúdo da mensagem a um tamanho máximo. Este limite ajuda a reduzir as sobrecargas criadas armazenando e processando mensagens grandes. Para lidar com mensagens maiores do que este limite, as Aplicações Lógicas podem *emsebrir* uma grande mensagem em mensagens menores. Desta forma, ainda pode transferir ficheiros grandes usando As Aplicações Lógicas em condições específicas. Ao comunicar com outros serviços através de conectores ou HTTP, as Aplicações Lógicas podem consumir mensagens grandes, mas *apenas* em pedaços. Esta condição significa que os conectores também devem suportar o chunking, ou a troca de mensagens HTTP subjacente entre as Aplicações Lógicas e estes serviços deve utilizar o chunking.

Este artigo mostra como pode configurar o chunking para ações que manuseiam mensagens que são maiores do que o limite. Os gatilhos da Logic App não suportam o chunking devido ao aumento da sobrecarga de troca de várias mensagens. 

## <a name="what-makes-messages-large"></a>O que torna as mensagens "grandes"?

As mensagens são "grandes" com base no tratamento do serviço que manuseiam essas mensagens. O limite de tamanho exato em mensagens grandes difere entre apps e conectores logicais. Tanto as Aplicações Lógicas como os conectores não podem consumir diretamente mensagens grandes, que devem ser em pedaços. Para o limite de tamanho de mensagem de Apps lógicas, consulte [os limites e configuração](../logic-apps/logic-apps-limits-and-config.md)de Apps Lógicas .
Para obter o limite de tamanho de cada conector, consulte os [detalhes técnicos específicos do conector](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Manipulação de mensagens em pedaços para apps lógicas

As Aplicações Lógicas não podem utilizar diretamente saídas de mensagens em pedaços que sejam maiores do que o limite do tamanho da mensagem. Apenas as ações que suportam o chunking podem aceder ao conteúdo da mensagem nestas saídas. Assim, uma ação que lide com mensagens grandes deve satisfazer *estes* critérios:

* Suporte nativo em pedaços quando essa ação pertence a um conector. 
* Ter suporte de chunking ativado na configuração de tempo de execução dessa ação. 

Caso contrário, obtém-se um erro de tempo de execução quando tenta aceder à saída de grandes conteúdos. Para ativar o chunking, consulte [Configurar suporte para a parte de um pedaço](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Manuseamento de mensagens em pedaços para conectores

Os serviços que comunicam com as Aplicações Lógicas podem ter os seus próprios limites de tamanho de mensagem. Estes limites são muitas vezes menores do que o limite de Aplicações Lógicas. Por exemplo, assumindo que um conector suporta o chunking, um conector pode considerar uma mensagem de 30-MB tão grande, enquanto as Aplicações Lógicas não. Para cumprir o limite deste conector, as Aplicações Lógicas dividem qualquer mensagem maior do que 30 MB em pedaços menores.

Para os conectores que suportam a chunking, o protocolo subjacente é invisível para os utilizadores finais. No entanto, nem todos os conectores suportam a chunking, pelo que estes conectores geram erros de tempo de funcionamento quando as mensagens recebidas excedem os limites de tamanho dos conectores.


Para ações que suportam e estão habilitados para o chunking, você não pode usar corpos, variáveis e expressões de gatilho, tais como `@triggerBody()?['Content']` porque usar qualquer uma destas entradas impede que a operação de chunking aconteça. Em vez disso, utilize a ação [ **Compor**](../logic-apps/logic-apps-perform-data-operations.md#compose-action). Especificamente, você deve criar um `body` campo usando a ação **Compose** para armazenar a saída de dados do corpo do gatilho, variável, expressão, e assim por diante, por exemplo:

```json
"Compose": {
    "inputs": {
        "body": "@variables('myVar1')"
    },
    "runAfter": {
        "Until": [
            "Succeeded"
        ]
    },
    "type": "Compose"
},
```
Em seguida, para fazer referência aos dados, na ação de emaamento, `@body('Compose')` utilize.

```json
"Create_file": {
    "inputs": {
        "body": "@body('Compose')",
        "headers": {
            "ReadFileMetadataFromServer": true
        },
        "host": {
            "connection": {
                "name": "@parameters('$connections')['sftpwithssh_1']['connectionId']"
            }
        },
        "method": "post",
        "path": "/datasets/default/files",
        "queries": {
            "folderPath": "/c:/test1/test1sub",
            "name": "tt.txt",
            "queryParametersSingleEncoded": true
        }
    },
    "runAfter": {
        "Compose": [
            "Succeeded"
        ]
    },
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "Chunked"
        }
    },
    "type": "ApiConnection"
},
```

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Configurar em pedaços sobre HTTP

Em cenários GENÉRICOS HTTP, pode dividir grandes transferências de conteúdos e uploads em HTTP, para que a sua aplicação lógica e um ponto final possam trocar grandes mensagens. No entanto, deve ser desem pedaços de mensagens da forma que as Logic Apps esperam. 

Se um ponto final tiver ativado o chunking para downloads ou uploads, as ações HTTP na sua aplicação lógica automaticamente emcascam mensagens grandes. Caso contrário, deve configurar suporte de estames no ponto final. Se não possuir ou controlar o ponto final ou o conector, poderá não ter a opção de configurar o chunking.

Além disso, se uma ação HTTP já não permitir a chunking, também deve configurar o chunking na propriedade da `runTimeConfiguration` ação. Pode definir esta propriedade dentro da ação, quer diretamente no editor de visualização de código, como descrito posteriormente, quer no Logic Apps Designer, conforme descrito aqui:

1. No canto superior direito da ação HTTP, escolha o botão elipse **(... )** e, em seguida, escolha **Definições**.

   ![Na ação, abra o menu de definições](./media/logic-apps-handle-large-messages/http-settings.png)

2. Em **Transferência de Conteúdo**, definir Deixe em **cima** de **.**

   ![Ligue os pedaços](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Para continuar a configurar o chunking para downloads ou uploads, continue com as seguintes secções.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Descarregue conteúdo em pedaços

Muitos pontos finais enviam automaticamente mensagens grandes em pedaços quando descarregados através de um pedido HTTP GET. Para descarregar mensagens em pedaços de um ponto final sobre HTTP, o ponto final deve suportar pedidos parciais de conteúdo, ou *transferências em pedaços*. Quando a sua aplicação lógica envia um pedido HTTP GET para um ponto final para o descarregamento de conteúdos, e o ponto final responde com um código de estado "206", a resposta contém conteúdo em pedaços. As Aplicações Lógicas não conseguem controlar se um ponto final suporta pedidos parciais. No entanto, quando a sua aplicação lógica obtém a primeira resposta "206", a sua aplicação lógica envia automaticamente vários pedidos para descarregar todos os conteúdos.

Para verificar se um ponto final pode suportar o conteúdo parcial, envie um pedido DE CABEÇA. Este pedido ajuda-o a determinar se a resposta contém o `Accept-Ranges` cabeçalho. Desta forma, se o ponto final suportar downloads em pedaços mas não enviar conteúdo em pedaços, pode *sugerir* esta opção definindo o `Range` cabeçalho no seu pedido HTTP GET. 

Estes passos descrevem o processo detalhado que as Apps Lógicas usam para descarregar conteúdo em pedaços de um ponto final para a sua aplicação lógica:

1. A sua aplicação lógica envia um pedido HTTP GET para o ponto final.

   O cabeçalho de pedido pode opcionalmente incluir um `Range` campo que descreve uma gama byte para solicitar pedaços de conteúdo.

2. O ponto final responde com o código de estado "206" e um organismo de mensagem HTTP.

    Os detalhes sobre o conteúdo deste pedaço aparecem no cabeçalho da `Content-Range` resposta, incluindo informações que ajudam as Aplicações Lógicas a determinar o início e o fim para o pedaço, além do tamanho total de todo o conteúdo antes de bater.

3. A sua aplicação lógica envia automaticamente pedidos de acompanhamento HTTP GET.

    A sua aplicação lógica envia pedidos get de seguimento até que todo o conteúdo seja recuperado.

Por exemplo, esta definição de ação mostra um pedido HTTP GET que define o `Range` cabeçalho. O cabeçalho *sugere* que o ponto final deve responder com conteúdo em pedaços:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

O pedido GET define o cabeçalho "Range" para "bytes=0-1023", que é o intervalo de bytes. Se o ponto final suportar pedidos de conteúdo parcial, o ponto final responde com um pedaço de conteúdo da gama solicitada. Com base no ponto final, o formato exato para o campo de cabeçalho "Range" pode diferir.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Carregar conteúdo em pedaços

Para carregar conteúdo em pedaços a partir de uma ação HTTP, a ação deve ter permitido o suporte de chunking através da propriedade da `runtimeConfiguration` ação. Esta definição permite que a ação inicie o protocolo de chunking. A sua aplicação lógica pode então enviar uma mensagem POST ou PUT inicial para o ponto final do alvo. Depois de o ponto final responder com um tamanho de pedaço sugerido, a sua aplicação lógica segue-se enviando pedidos HTTP PATCH que contêm os pedaços de conteúdo.

Estes passos descrevem o processo detalhado que as Apps Lógicas usam para carregar conteúdo em pedaços da sua aplicação lógica para um ponto final:

1. A sua aplicação lógica envia um pedido inicial HTTP POST ou PUT com um corpo de mensagem vazio. O cabeçalho do pedido, inclui esta informação sobre o conteúdo que a sua aplicação lógica quer carregar em pedaços:

   | Aplicativos lógicos solicitam campo de cabeçalho | Valor | Tipo | Description |
   |---------------------------------|-------|------|-------------|
   | **x-ms-modo de transferência** | em pedaços | String | Indica que o conteúdo é carregado em pedaços |
   | **x-ms-content-comprimento** | <*comprimento do conteúdo*> | Número inteiro | Todo o tamanho do conteúdo em bytes antes de bater |
   ||||

2. O ponto final responde com código de estado de sucesso "200" e esta informação opcional:

   | Campo de cabeçalho de resposta de ponto final | Tipo | Necessário | Descrição |
   |--------------------------------|------|----------|-------------|
   | **x-ms-tamanho-chunk** | Número inteiro | No | O tamanho do pedaço sugerido em bytes |
   | **Localização** | String | Yes | A localização do URL para onde enviar as mensagens HTTP PATCH |
   ||||

3. A sua aplicação lógica cria e envia mensagens HTTP PATCH de seguimento - cada uma com esta informação:

   * Um pedaço de conteúdo baseado em **tamanho x-ms-chunk** ou algum tamanho calculado internamente até que todo o conteúdo total de **x-ms-content-length** é sequencialmente carregado

   * Estes detalhes do cabeçalho sobre o pedaço de conteúdo enviado em cada mensagem PATCH:

     | Aplicativos lógicos solicitam campo de cabeçalho | Valor | Tipo | Description |
     |---------------------------------|-------|------|-------------|
     | **Gama de conteúdos** | <*gama*> | String | A gama byte para o pedaço de conteúdo atual, incluindo o valor inicial, o valor final, e o tamanho total do conteúdo, por exemplo: "bytes=0-1023/10100" |
     | **Tipo de conteúdo** | <*tipo de conteúdo*> | String | O tipo de conteúdo em pedaços |
     | **Comprimento do conteúdo** | <*comprimento do conteúdo*> | String | O comprimento do tamanho em bytes do pedaço atual |
     |||||

4. Após cada pedido patch, o ponto final confirma o recibo de cada pedaço respondendo com o código de estado "200" e os seguintes cabeçalhos de resposta:

   | Campo de cabeçalho de resposta de ponto final | Tipo | Necessário | Descrição |
   |--------------------------------|------|----------|-------------|
   | **Intervalo** | String | Yes | A gama byte para conteúdos que foi recebido pelo ponto final, por exemplo: "bytes=0-1023" |   
   | **x-ms-tamanho-chunk** | Número inteiro | No | O tamanho do pedaço sugerido em bytes |
   ||||

Por exemplo, esta definição de ação mostra um pedido HTTP POST para o upload de conteúdo em pedaços para um ponto final. Na propriedade da `runTimeConfiguration` ação, o `contentTransfer` imóvel define `transferMode` `chunked` para:

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```
