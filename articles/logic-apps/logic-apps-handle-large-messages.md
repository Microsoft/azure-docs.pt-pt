---
title: Processar mensagens grandes - Azure Logic Apps | Documentos da Microsoft
description: Saber como lidar com tamanhos de mensagem grande com a segmentação no Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: shae-hurst
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: ''
ms.tgt_pltfrm: ''
ms.topic: article
ms.date: 4/27/2018
ms.author: shhurst
ms.openlocfilehash: 5aa5ea2a39a0fb9f969e965fed14063522197cda
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303795"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Processar mensagens grandes com a segmentação no Azure Logic Apps

Quando o tratamento de mensagens, o Logic Apps limita conteúdo da mensagem para um tamanho máximo. Este limite ajuda a reduzir a sobrecarga criada ao armazenar e processar mensagens grandes. Para lidar com mensagens maiores do que este limite, o Logic Apps podem *segmentos* uma mensagem grande em mensagens menores. Dessa forma, pode ainda transferir ficheiros grandes utilizando o Logic Apps em condições específicas. Ao se comunicar com outros serviços através de conectores ou HTTP, o Logic Apps podem consumir mensagens grandes, mas *apenas* em blocos. Esta condição significa conectores também tem de suportar segmentação ou a troca de mensagens HTTP subjacente entre estes serviços e aplicações lógicas têm de utilizar o agrupamento.

Este artigo mostra como pode configurar a segmentação para manipulação de mensagens que são maiores do que o limite de ações. Acionadores da aplicação lógica não suportam a segmentação devido a maior sobrecarga de troca de várias mensagens. 

## <a name="what-makes-messages-large"></a>O que torna as mensagens "grandes"?

As mensagens são "grandes" com base no serviço de lidar com essas mensagens. O limite do tamanho exato mensagens grandes difere em Logic Apps e conectores. Logic Apps e conectores diretamente não é possível consumir mensagens grandes, que devem ser segmentadas. Para o limite de tamanho de mensagem do Logic Apps, consulte [Logic Apps limites e configuração](../logic-apps/logic-apps-limits-and-config.md).
Para o limite de tamanho de mensagem de cada conector, consulte a [detalhes técnicos de específico do conector](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Manipulação para o Logic Apps de mensagens em partes

O Logic Apps diretamente não é possível utilizar saídas de mensagens em partes que são maiores do que o limite de tamanho de mensagem. Apenas as ações que suportam a segmentação podem acessar o conteúdo da mensagem nessas saídas. Por isso, tem de corresponder a uma ação que trata de mensagens grandes *qualquer um dos* estes critérios:

* Suporta nativamente a segmentação quando essa ação pertence a um conector. 
* Tenha suporte habilitado na configuração de tempo de execução da ação de segmentação. 

Caso contrário, receberá um erro de tempo de execução quando tenta acessar o conteúdo de saída grande. Para ativar a segmentação, consulte [configurar o suporte de segmentação](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Manipulação de conectores de mensagens em partes

Os serviços que comunicam com o Logic Apps podem ter seus próprios limites de tamanho de mensagem. Estes limites são, muitas vezes, menores do que o limite de Logic Apps. Por exemplo, supondo que um conector suporta o agrupamento, um conector pode considerar uma mensagem de 30 MB como grande, enquanto o Logic Apps não faz. Para estar em conformidade com o limite deste conector, Logic Apps divide qualquer mensagem mais de 30 MB em segmentos mais pequenos.

Para os conectores que suportam a segmentação, o protocolo de criação de blocos subjacente é invisível para os utilizadores finais. No entanto, nem todos os conectores suportam segmentação, pelo que estes conectores geram erros de runtime, quando mensagens de entrada excederem os limites de tamanho dos conectores.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Configurar o agrupamento através de HTTP

Em cenários HTTP genéricos, pode dividir grandes transferências de conteúdos e carrega através de HTTP, para que a aplicação lógica e um ponto de extremidade podem trocar mensagens grandes. No entanto, deve colocar partes de mensagens da forma que espera de Logic Apps. 

Se um ponto final tiver ativado a segmentação para downloads ou carregamentos, as ações de HTTP na sua aplicação lógica automaticamente colocar partes de mensagens grandes. Caso contrário, tem de configurar suporte no ponto final de segmentação. Se não detém ou controla o ponto final ou o conector, não pode ter a opção para configurar a segmentação.

Além disso, se uma ação de HTTP já não habilitar a segmentação, tem também de configurar divisão na ação `runTimeConfiguration` propriedade. Pode definir esta propriedade dentro da ação, diretamente no editor de vista de código, conforme descrito mais tarde ou no estruturador de aplicações lógicas, conforme descrito aqui:

1. No canto superior direito da ação de HTTP, selecione o botão de reticências ( **...** ) e, em seguida, escolha **definições**.

   ![Em ação, abra o menu de definições](./media/logic-apps-handle-large-messages/http-settings.png)

2. Sob **transferência de conteúdo**, defina **permitir segmentação** para **no**.

   ![Ativar o agrupamento](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Para continuar a configuração de segmentação para downloads ou carregamentos, avance para as secções seguintes.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Transferir o conteúdo em partes

Muitos pontos de extremidade enviam automaticamente mensagens grandes em segmentos quando transferidos através de um pedido HTTP GET. Para transferir mensagens em partes de um ponto de extremidade através de HTTP, o ponto final tem de suportar pedidos de conteúdo parciais, ou *segmentado downloads*. Quando a aplicação lógica envia um pedido HTTP GET para um ponto final para baixar conteúdo e o ponto final responde com um código de estado "206", a resposta contém conteúdo em partes. O Logic Apps não é possível controlar se um ponto de extremidade suporta pedidos parciais. No entanto, quando a aplicação lógica recebe a resposta em primeiro lugar "206", a sua aplicação lógica envia automaticamente vários pedidos para transferir todo o conteúdo.

Para verificar se um ponto de extremidade pode suportar conteúdo parcial, envie um pedido HEAD. Este pedido ajuda-o a determinar se a resposta contém o `Accept-Ranges` cabeçalho. Dessa forma, se o ponto final oferece suporte a downloads em partes, mas não envia o conteúdo em partes, pode *sugerir* esta opção, definindo o `Range` cabeçalho no seu pedido de HTTP GET. 

Estes passos descrevem o processo detalhado que Logic Apps utiliza para o download de conteúdo em partes de um ponto final à sua aplicação lógica:

1. A aplicação lógica envia um pedido HTTP GET para o ponto final.

   O cabeçalho do pedido, opcionalmente, pode incluir um `Range` campo que descreve um intervalo de bytes que partes de conteúdo.

2. O ponto final responde com o código de estado "206" e um corpo de mensagem HTTP.

    Detalhes sobre os conteúdos dessa porção, são apresentados na resposta da `Content-Range` cabeçalho, incluindo informações que ajuda a Logic Apps determinar o início e de fim para o segmento, mais o tamanho total de todo o conteúdo antes de segmentação.

3. A aplicação lógica envia automaticamente pedidos HTTP GET acompanhamento.

    A aplicação lógica envia pedidos de seguimento GET até que todo o conteúdo é recuperado.

Por exemplo, esta definição de ação mostra um pedido HTTP GET que define o `Range` cabeçalho. O cabeçalho *sugere* que o ponto final deverá responder com segmentado conteúdo:

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

O pedido GET define o cabeçalho de "Range" "bytes = 0-1023", que é o intervalo de bytes. Se o ponto final suporta pedidos de conteúdo parcial, o ponto final responde com um segmento de conteúdo de intervalo pedido. Com base no ponto final, o formato exato para o campo de cabeçalho de "Range" pode ser diferente.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Carregar conteúdo em partes

Para carregar conteúdo em partes de uma ação de HTTP, a ação tem de ter ativado suporte segmentação através da ação `runtimeConfiguration` propriedade. Esta definição permite a ação para iniciar o protocolo de criação de blocos. A aplicação lógica, em seguida, pode enviar uma mensagem inicial de POST ou PUT para o ponto de extremidade de destino. Depois do ponto final responde com um tamanho de segmentos sugeridas, a aplicação lógica segue através do envio de pedidos de HTTP de PATCHES de mensagens em fila que contêm os segmentos de conteúdo.

Estes passos descrevem o processo detalhado que Logic Apps utiliza para carregar conteúdo em partes da sua aplicação lógica para um ponto final:

1. A aplicação lógica envia um pedido de HTTP POST ou PUT inicial com um corpo de mensagem vazio. O cabeçalho do pedido, inclui estas informações sobre o conteúdo que quer que a aplicação lógica para carregar em blocos:

   | Campo de cabeçalho do pedido de aplicações lógicas | Value | Type | Descrição |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-mode** | chunked | String | Indica que o conteúdo é carregado em blocos |
   | **x-ms-content-length** | <*content-length*> | Integer | O tamanho do conteúdo todo em bytes antes de segmentação |
   ||||

2. O ponto final responde com o código de estado de sucesso "200" e estas informações opcionais:

   | Campo de cabeçalho de resposta do ponto final | Type | Necessário | Descrição |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | Integer | Não | O tamanho do segmento sugerido em bytes |
   | **Location** | String | Não | A localização de URL para onde enviar as mensagens HTTP PATCH |
   ||||

3. A aplicação lógica cria e envia mensagens de HTTP PATCH seguimento - cada um com estas informações:

   * Um segmento de conteúdo com base na **x-ms--tamanho do segmento** ou tamanho internamente calculado até que todo o conteúdo totalizando **x-ms-content-length** sequencialmente é carregado

   * Estes detalhes de cabeçalho sobre o conteúdo segmentos enviados em cada mensagem de PATCH:

     | Campo de cabeçalho do pedido de aplicações lógicas | Value | Type | Descrição |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*range*> | String | O intervalo de bytes para o segmento atual do conteúdo, incluindo o valor inicial, terminando o valor e o tamanho total do conteúdo, por exemplo: "bytes = 0-1023/10100" |
     | **Content-Type** | <*content-type*> | String | O tipo de conteúdo em partes |
     | **Content-Length** | <*content-length*> | String | O comprimento do tamanho em bytes do segmento atual |
     |||||

4. Depois de cada pedido de PATCH, o ponto final confirma o recebimento para cada bloco de responder com o código de estado "200".

Por exemplo, esta definição de ação mostra um pedido de HTTP POST para carregar conteúdo em partes para um ponto de extremidade. Na ação de `runTimeConfiguration` propriedade, o `contentTransfer` conjuntos de propriedades `transferMode` para `chunked`:

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