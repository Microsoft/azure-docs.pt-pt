---
title: Manipular mensagens grandes-aplicativos lógicos do Azure | Microsoft Docs
description: Saiba como lidar com tamanhos de mensagens grandes com agrupamento em aplicativos lógicos do Azure
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
ms.openlocfilehash: ed086c4c36711f92ba654a64856b43a5fdaadf5f
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69989914"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Manipular mensagens grandes com agrupamento em aplicativos lógicos do Azure

Ao lidar com mensagens, os aplicativos lógicos limitam o conteúdo da mensagem a um tamanho máximo. Esse limite ajuda a reduzir a sobrecarga criada armazenando e processando mensagens grandes. Para lidar com mensagens maiores que esse limite, os aplicativos lógicos podem *dividir* uma mensagem grande em mensagens menores. Dessa forma, você ainda pode transferir arquivos grandes usando aplicativos lógicos sob condições específicas. Ao se comunicar com outros serviços por meio de conectores ou HTTP, os aplicativos lógicos podem consumir mensagens grandes, mas *apenas* em partes. Essa condição significa que os conectores também devem oferecer suporte a agrupamento ou a troca de mensagens HTTP subjacente entre aplicativos lógicos e esses serviços devem usar agrupamento.

Este artigo mostra como você pode configurar o agrupamento de ações que manipulam mensagens que são maiores que o limite. Os gatilhos de aplicativo lógico não dão suporte ao agrupamento devido à sobrecarga maior de troca de várias mensagens. 

## <a name="what-makes-messages-large"></a>O que torna as mensagens "grandes"?

As mensagens são "grandes" com base no serviço que manipula essas mensagens. O limite de tamanho exato em mensagens grandes difere entre aplicativos lógicos e conectores. Os aplicativos lógicos e os conectores não podem consumir diretamente mensagens grandes, que devem ser fragmentadas. Para o limite de tamanho de mensagem dos aplicativos lógicos, consulte [limites e configuração de aplicativos lógicos](../logic-apps/logic-apps-limits-and-config.md).
Para obter o limite de tamanho de mensagem de cada conector, consulte os [detalhes técnicos específicos do conector](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Manipulação de mensagens em partes para aplicativos lógicos

Os aplicativos lógicos não podem usar diretamente saídas de mensagens em partes que sejam maiores que o limite de tamanho da mensagem. Somente as ações que dão suporte a Agrupamento podem acessar o conteúdo da mensagem nessas saídas. Portanto, uma ação que manipula mensagens grandes deve atender *a* esses critérios:

* Dá suporte nativo ao agrupamento quando essa ação pertence a um conector. 
* Ter suporte de agrupamento habilitado na configuração de tempo de execução da ação. 

Caso contrário, você obterá um erro de tempo de execução quando tentar acessar a saída de conteúdo grande. Para habilitar o agrupamento, consulte [Configurar o suporte de agrupamento](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Manipulação de mensagens em partes para conectores

Serviços que se comunicam com aplicativos lógicos podem ter seus próprios limites de tamanho de mensagem. Esses limites geralmente são menores do que o limite dos aplicativos lógicos. Por exemplo, supondo que um conector ofereça suporte a agrupamento, um conector pode considerar uma mensagem de 30 MB como grande, enquanto os aplicativos lógicos não. Para obedecer ao limite deste conector, os aplicativos lógicos dividem qualquer mensagem maior que 30 MB em partes menores.

Para conectores que dão suporte a agrupamento, o protocolo subjacente é invisível para os usuários finais. No entanto, nem todos os conectores dão suporte a agrupamento, portanto, esses conectores geram erros de tempo de execução quando as mensagens de entrada excedem os limites de tamanho

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Configurar o agrupamento sobre HTTP

Em cenários HTTP genéricos, você pode dividir downloads de conteúdo grandes e carregamentos via HTTP, para que seu aplicativo lógico e um ponto de extremidade possam trocar mensagens grandes. No entanto, você deve dividir as mensagens da maneira esperada pelos aplicativos lógicos. 

Se um ponto de extremidade tiver habilitado o agrupamento para downloads ou carregamentos, as ações HTTP em seu aplicativo lógico farão partes de mensagens grandes automaticamente. Caso contrário, você deve configurar o suporte de agrupamento no ponto de extremidade. Se você não possuir ou controlar o ponto de extremidade ou conector, talvez não tenha a opção de configurar o agrupamento.

Além disso, se uma ação http ainda não habilitar o agrupamento, você também deverá configurar o agrupamento na propriedade da `runTimeConfiguration` ação. Você pode definir essa propriedade dentro da ação, seja diretamente no editor de modo de exibição de código, conforme descrito posteriormente, ou no designer de aplicativos lógicos, conforme descrito aqui:

1. No canto superior direito da ação http, escolha o botão de reticências ( **...** ) e, em seguida, escolha **configurações**.

   ![Na ação, abra o menu configurações](./media/logic-apps-handle-large-messages/http-settings.png)

2. Em **transferência de conteúdo**, defina **permitir agrupamento** como **ativado**.

   ![Ativar agrupamento](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Para continuar a configurar o agrupamento de downloads ou carregamentos, continue com as seções a seguir.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Baixar conteúdo em partes

Muitos pontos de extremidade enviam mensagens grandes em partes automaticamente quando baixados por meio de uma solicitação HTTP GET. Para baixar mensagens em partes de um ponto de extremidade sobre HTTP, o ponto de extremidade deve dar suporte a solicitações de conteúdo parcial ou *downloads em partes*. Quando seu aplicativo lógico envia uma solicitação HTTP GET para um ponto de extremidade para baixar conteúdo e o ponto de extremidade responde com um código de status "206", a resposta contém o conteúdo em partes. Os aplicativos lógicos não podem controlar se um ponto de extremidade dá suporte a solicitações parciais. No entanto, quando seu aplicativo lógico Obtém a primeira resposta "206", seu aplicativo lógico envia automaticamente várias solicitações para baixar todo o conteúdo.

Para verificar se um ponto de extremidade pode dar suporte a conteúdo parcial, envie uma solicitação HEAD. Essa solicitação ajuda a determinar se a resposta contém o `Accept-Ranges` cabeçalho. Dessa forma, se o ponto de extremidade oferecer suporte a downloads em bloco, mas não enviar conteúdo em partes, você poderá sugerir `Range` essa opção definindo o cabeçalho na sua solicitação HTTP Get. 

Estas etapas descrevem os aplicativos lógicos de processo detalhados usados para baixar conteúdo em partes de um ponto de extremidade para seu aplicativo lógico:

1. Seu aplicativo lógico envia uma solicitação HTTP GET para o ponto de extremidade.

   Opcionalmente, o cabeçalho da solicitação pode `Range` incluir um campo que descreve um intervalo de bytes para a solicitação de partes de conteúdo.

2. O ponto de extremidade responde com o código de status "206" e um corpo de mensagem HTTP.

    Os detalhes sobre o conteúdo nessa parte aparecem no cabeçalho da `Content-Range` resposta, incluindo informações que ajudam os aplicativos lógicos a determinar o início e o término da parte, além do tamanho total do conteúdo inteiro antes do agrupamento.

3. Seu aplicativo lógico envia automaticamente solicitações HTTP GET de acompanhamento.

    Seu aplicativo lógico envia solicitações GET-up até que todo o conteúdo seja recuperado.

Por exemplo, essa definição de ação mostra uma solicitação HTTP Get que define `Range` o cabeçalho. O cabeçalho *sugere* que o ponto de extremidade deve responder com conteúdo em partes:

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

A solicitação GET define o cabeçalho "Range" como "bytes = 0-1023", que é o intervalo de bytes. Se o ponto de extremidade der suporte a solicitações de conteúdo parcial, o ponto de extremidade responderá com uma parte de conteúdo do intervalo solicitado. Com base no ponto de extremidade, o formato exato do campo de cabeçalho "Range" pode ser diferente.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Carregar conteúdo em partes

Para carregar o conteúdo em partes de uma ação http, a ação deve ter o suporte de agrupamento habilitado por meio `runtimeConfiguration` da propriedade da ação. Essa configuração permite que a ação inicie o protocolo de agrupamento. Seu aplicativo lógico pode enviar uma mensagem de POSTAgem inicial ou PUT para o ponto de extremidade de destino. Depois que o ponto de extremidade responde com um tamanho de parte sugerido, seu aplicativo lógico segue o envio de solicitações de PATCH HTTP que contêm as partes de conteúdo.

Estas etapas descrevem os aplicativos lógicos de processo detalhados usados para carregar conteúdo em partes do seu aplicativo lógico para um ponto de extremidade:

1. Seu aplicativo lógico envia uma solicitação HTTP POST ou PUT inicial com um corpo de mensagem vazio. O cabeçalho da solicitação, inclui essas informações sobre o conteúdo que seu aplicativo lógico deseja carregar em partes:

   | Campo de cabeçalho de solicitação de aplicativos lógicos | Value | Type | Descrição |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-mode** | em bloco | String | Indica que o conteúdo é carregado em partes |
   | **x-ms-content-length** | <*content-length*> | Integer | O tamanho do conteúdo inteiro em bytes antes do agrupamento |
   ||||

2. O ponto de extremidade responde com o código de status de êxito "200" e essas informações opcionais:

   | Campo de cabeçalho de resposta do ponto de extremidade | Type | Necessário | Descrição |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | Integer | Não | O tamanho de parte sugerido em bytes |
   | **Location** | String | Sim | O local da URL para onde enviar as mensagens de PATCH HTTP |
   ||||

3. Seu aplicativo lógico cria e envia mensagens de PATCH HTTP de acompanhamento, cada uma com essas informações:

   * Uma parte de conteúdo com base no **tamanho x-MS-Chunk** ou em algum tamanho calculado internamente até que todo o conteúdo total de **x-MS-Content-Length** seja carregado sequencialmente

   * Estes detalhes de cabeçalho sobre a parte de conteúdo enviada em cada mensagem de PATCH:

     | Campo de cabeçalho de solicitação de aplicativos lógicos | Value | Type | Descrição |
     |---------------------------------|-------|------|-------------|
     | **Intervalo de conteúdo** | <*range*> | Cadeia | O intervalo de bytes para a parte de conteúdo atual, incluindo o valor inicial, o valor final e o tamanho total do conteúdo, por exemplo: "bytes = 0-1023/10100" |
     | **Content-Type** | <*content-type*> | Cadeia | O tipo de conteúdo em partes |
     | **Content-Length** | <*content-length*> | Cadeia | O comprimento do tamanho em bytes da parte atual |
     |||||

4. Após cada solicitação de PATCH, o ponto de extremidade confirma o recebimento de cada parte respondendo com o código de status "200" e os seguintes cabeçalhos de resposta:

   | Campo de cabeçalho de resposta do ponto de extremidade | Type | Necessário | Descrição |
   |--------------------------------|------|----------|-------------|
   | **Intervalo** | Cadeia | Sim | O intervalo de bytes para o conteúdo recebido pelo ponto de extremidade, por exemplo: "bytes = 0-1023" |   
   | **x-ms-chunk-size** | Integer | Não | O tamanho de parte sugerido em bytes |
   ||||

Por exemplo, essa definição de ação mostra uma solicitação HTTP POST para carregar conteúdo em partes para um ponto de extremidade. Na propriedade da ação `runTimeConfiguration` , a `contentTransfer` propriedade é definida `transferMode` como `chunked`:

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
