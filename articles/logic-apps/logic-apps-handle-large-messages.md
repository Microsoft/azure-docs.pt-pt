---
title: Manuseie mensagens grandes usando o chunking
description: Aprenda a lidar com grandes tamanhos de mensagens usando chunking em tarefas automatizadas e fluxos de trabalho que cria com Apps Lógicas Azure
services: logic-apps
ms.suite: integration
author: DavidCBerry13
ms.author: daberry
ms.topic: article
ms.date: 12/03/2019
ms.openlocfilehash: 54828dded5196c86946d99a9cd8cec7a42533661
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117568"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Manuseie mensagens grandes com chunking em Aplicativos Azure Logic

Ao manusear mensagens, as Aplicações Lógicas limitam o conteúdo da mensagem a um tamanho máximo. Este limite ajuda a reduzir as despesas gerais criadas através do armazenamento e processamento de grandes mensagens. Para lidar com mensagens maiores do que este limite, as Aplicações Lógicas podem *enviar* uma grande mensagem para mensagens menores. Desta forma, ainda pode transferir ficheiros grandes utilizando Aplicações Lógicas em condições específicas. Ao comunicar com outros serviços através de conectores ou HTTP, as Aplicações Lógicas podem consumir grandes mensagens, mas *apenas* em pedaços. Esta condição significa que os conectores também devem suportar a chunking, ou a troca de mensagens http subjacente entre as Aplicações Lógicas e estes serviços deve usar chunking.

Este artigo mostra como pode configurar a redação de ações de manipulação de mensagens que são maiores do que o limite. Os gatilhos da Logic App não suportam a chunking devido ao aumento da sobrecarga de troca de várias mensagens. 

## <a name="what-makes-messages-large"></a>O que torna as mensagens "grandes"?

As mensagens são "grandes" com base no serviço que manuseia essas mensagens. O limite de tamanho exato em mensagens grandes difere entre apps lógicas e conectores. Tanto as Aplicações Lógicas como os conectores não podem consumir diretamente mensagens grandes, que devem ser repartedas. Para o limite de tamanho de mensagem de Apps Lógicas, consulte [os limites e configuração das Aplicações Lógicas.](../logic-apps/logic-apps-limits-and-config.md)
Para o limite de tamanho da mensagem de cada conector, consulte os [detalhes técnicos específicos do conector](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Manipulação de mensagens chunked para Apps Lógicas

As Aplicações Lógicas não podem utilizar diretamente saídas de mensagens em pedaços que são maiores do que o limite de tamanho da mensagem. Só as ações que suportam a chunking podem aceder ao conteúdo da mensagem nestas saídas. Assim, uma ação que trate mensagens de grande dimensão deve cumprir *estes* critérios:

* Apoio nativo quando essa ação pertence a um conector. 
* Tenha suporte de chunking ativado na configuração de tempo de execução dessa ação. 

Caso contrário, obtém um erro de tempo de execução quando tenta aceder a uma grande saída de conteúdo. Para ativar o chunking, consulte O suporte de [chunking configurar](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Manuseamento de mensagens em pedaços para conectores

Os serviços que comunicam com apps lógicas podem ter os seus próprios limites de tamanho de mensagem. Estes limites são muitas vezes menores do que o limite de Aplicações Lógicas. Por exemplo, assumindo que um conector suporta o chunking, um conector pode considerar uma mensagem de 30 MB como grande, enquanto as Aplicações Lógicas não. Para cumprir o limite deste conector, as Aplicações Lógicas dividem qualquer mensagem superior a 30 MB em pedaços menores.

Para conectores que suportam a chunking, o protocolo de chunking subjacente é invisível para os utilizadores finais. No entanto, nem todos os conectores suportam a chunking, pelo que estes conectores geram erros de tempo de execução quando as mensagens de entrada excedem os limites de tamanho dos conectores.

> [!NOTE]
> Para ações que usam chunking, você não pode passar o corpo do gatilho ou usar expressões como `@triggerBody()?['Content']` nessas ações. Em vez disso, para conteúdo de ficheiros de texto ou JSON, pode tentar utilizar a ação [ **Compor** ](../logic-apps/logic-apps-perform-data-operations.md#compose-action) ou [criar uma variável](../logic-apps/logic-apps-create-variables-store-values.md) para lidar com esse conteúdo. Se o corpo do gatilho contiver outros tipos de conteúdo, tais como ficheiros de mídia, terá de executar outras etapas para lidar com esse conteúdo.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Configurar chunking sobre HTTP

Em cenários genéricos de HTTP, pode dividir grandes transferências de conteúdos e uploads em HTTP, para que a sua aplicação lógica e um ponto final possam trocar grandes mensagens. No entanto, é preciso colar mensagens da forma que as Aplicações Lógicas esperam. 

Se um ponto final tiver ativado a chunking para downloads ou uploads, as ações DO HTTP na sua aplicação lógica automaticamente embalaram grandes mensagens. Caso contrário, tem de configurar suporte de chunking no ponto final. Se não possuir ou controlar o ponto final ou o conector, pode não ter a opção de configurar a chunking.

Além disso, se uma ação HTTP já não permitir a chunking, você também deve configurar pedaços na propriedade da `runTimeConfiguration` ação. Pode definir esta propriedade dentro da ação, quer diretamente no editor de visualização de código, como descrito mais tarde, ou no Logic Apps Designer como descrito aqui:

1. No canto superior direito da ação HTTP, escolha o botão de elipse **(...**), e, em seguida, escolha **Definições**.

   ![Na ação, abra o menu de definições](./media/logic-apps-handle-large-messages/http-settings.png)

2. Em transferência **de conteúdo,** **coloque deixe a chunking** **on**. .

   ![Ligue o pedaço](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Para continuar a configurar as fatias para downloads ou uploads, continue com as seguintes secções.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Descarregue o conteúdo em pedaços

Muitos pontos finais enviam automaticamente grandes mensagens em pedaços quando descarregados através de um pedido HTTP GET. Para descarregar mensagens chunked de um ponto final sobre HTTP, o ponto final deve suportar pedidos de conteúdo parcial, ou *downloads em pedaços*. Quando a sua aplicação lógica envia um pedido HTTP GET para um ponto final para descarregar conteúdo, e o ponto final responde com um código de estado "206", a resposta contém conteúdo em pedaços. As Aplicações Lógicas não conseguem controlar se um ponto final suporta pedidos parciais. No entanto, quando a sua aplicação lógica obtém a primeira resposta "206", a sua aplicação lógica envia automaticamente vários pedidos para descarregar todos os conteúdos.

Para verificar se um ponto final pode suportar conteúdo parcial, envie um pedido HEAD. Este pedido ajuda-o a determinar se a resposta contém o `Accept-Ranges` cabeçalho. Desta forma, se o ponto final suportar downloads em pedaços mas não enviar conteúdo em pedaços, pode *sugerir* esta opção definindo o `Range` cabeçalho no seu pedido HTTP GET. 

Estes passos descrevem o processo detalhado que as Apps Lógicas usam para descarregar conteúdo em pedaços de um ponto final para a sua aplicação lógica:

1. A sua aplicação lógica envia um pedido HTTP GET para o ponto final.

   O cabeçalho de pedido pode incluir opcionalmente um `Range` campo que descreve uma gama de bytes para solicitar pedaços de conteúdo.

2. O ponto final responde com o código de estado "206" e um corpo de mensagem HTTP.

    Os detalhes sobre o conteúdo neste pedaço aparecem no cabeçalho da `Content-Range` resposta, incluindo informações que ajudam as Aplicações Lógicas a determinar o início e o fim para o pedaço, além do tamanho total de todo o conteúdo antes de partir.

3. A sua aplicação lógica envia automaticamente pedidos de acompanhamento HTTP GET.

    A sua aplicação lógica envia pedidos get de seguimento até que todo o conteúdo seja recuperado.

Por exemplo, esta definição de ação mostra um pedido HTTP GET que define o `Range` cabeçalho. O cabeçalho *sugere* que o ponto final deve responder com conteúdo retaliado:

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

O pedido GET define o cabeçalho "Range" para "bytes=0-1023", que é o intervalo de bytes. Se o ponto final apoiar pedidos de conteúdo parcial, o ponto final responde com um pedaço de conteúdo da gama solicitada. Com base no ponto final, o formato exato para o campo de cabeçalho "Range" pode diferir.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Carregar conteúdo em pedaços

Para fazer o upload de conteúdo de uma ação HTTP, a ação deve ter permitido o suporte de chunking através da propriedade da `runtimeConfiguration` ação. Esta definição permite que a ação inicie o protocolo de chunking. A sua aplicação lógica pode então enviar uma mensagem post ou PUT inicial para o ponto final do alvo. Após o ponto final responder com um tamanho de pedaço sugerido, a sua aplicação lógica segue enviando pedidos HTTP PATCH que contêm os pedaços de conteúdo.

Estes passos descrevem o processo detalhado que as Apps Lógicas usam para carregar conteúdo em pedaços da sua aplicação lógica para um ponto final:

1. A sua aplicação lógica envia um pedido inicial http post ou PUT com um corpo de mensagem vazio. O cabeçalho de pedido, inclui esta informação sobre o conteúdo que a sua aplicação lógica quer carregar em pedaços:

   | Aplicativos lógicos solicitam campo de cabeçalho | Valor | Tipo | Descrição |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-mode** | pedaços | String | Indica que o conteúdo é carregado em pedaços |
   | **x-ms-conteúdo-comprimento** | <*comprimento do conteúdo*> | Número inteiro | Todo o tamanho do conteúdo em bytes antes de chunking |
   ||||

2. O ponto final responde com o código de estado de sucesso "200" e esta informação opcional:

   | Campo de cabeçalho de resposta endpoint | Tipo | Necessário | Descrição |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | Número inteiro | Não | O tamanho sugerido em bytes |
   | **Localização** | String | Sim | A localização do URL onde enviar as mensagens HTTP PATCH |
   ||||

3. A sua aplicação lógica cria e envia mensagens http patch de seguimento - cada uma com esta informação:

   * Um pedaço de conteúdo baseado em **tamanho x-ms-chunk** ou algum tamanho calculado internamente até que todo o conteúdo total de **x-ms-conteúdo-comprimento** seja sequencialmente carregado

   * Estes detalhes do cabeçalho sobre o pedaço de conteúdo enviado em cada mensagem PATCH:

     | Aplicativos lógicos solicitam campo de cabeçalho | Valor | Tipo | Descrição |
     |---------------------------------|-------|------|-------------|
     | **Gama de Conteúdos** | <*alcance*> | String | A gama de bytes para a parte atual do conteúdo, incluindo o valor inicial, o valor final e o tamanho total do conteúdo, por exemplo: "bytes=0-1023/10100" |
     | **Tipo de conteúdo** | <*tipo de conteúdo*> | String | O tipo de conteúdo em pedaços |
     | **Comprimento do conteúdo** | <*comprimento do conteúdo*> | String | O comprimento do tamanho em bytes do pedaço atual |
     |||||

4. Após cada pedido de PATCH, o ponto final confirma o recibo de cada pedaço respondendo com o código de estado "200" e os seguintes cabeçalhos de resposta:

   | Campo de cabeçalho de resposta endpoint | Tipo | Necessário | Descrição |
   |--------------------------------|------|----------|-------------|
   | **Alcance** | String | Sim | A gama de bytes para conteúdos que tenha sido recebido pelo ponto final, por exemplo: "bytes=0-1023" |   
   | **x-ms-chunk-size** | Número inteiro | Não | O tamanho sugerido em bytes |
   ||||

Por exemplo, esta definição de ação mostra um pedido HTTP POST para carregar conteúdo em pedaços para um ponto final. No imóvel da `runTimeConfiguration` ação, o `contentTransfer` imóvel `transferMode` define-se `chunked` para:

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
