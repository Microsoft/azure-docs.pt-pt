---
title: Ligue para os pontos finais do serviço utilizando HTTP ou HTTPS
description: Enviar pedidos http ou HTTPS de saída para atender pontos finais a partir de Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/14/2020
tags: connectors
ms.openlocfilehash: f005bdfa5643ea187fb2973cac065563c4cc2ee6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91292460"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Chamar pontos finais de serviço através de HTTP ou HTTPS a partir do Azure Logic Apps

Com [as Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o gatilho ou ação HTTP incorporados, pode criar tarefas e fluxos de trabalho automatizados que podem enviar pedidos de saída para pontos finais em outros serviços e sistemas através de HTTP ou HTTPS. Para receber e responder às chamadas HTTPS de entrada, utilize a ação de [detonador e resposta do pedido](../connectors/connectors-native-reqres.md)incorporado .

Por exemplo, pode monitorizar um ponto final de serviço para o seu website, verificando esse ponto final num horário específico. Quando o evento especificado acontece nesse ponto final, como o seu site a descer, o evento desencadeia o fluxo de trabalho da sua aplicação lógica e executa as ações nesse fluxo de trabalho.

* Para verificar ou *sondar* um ponto final num horário recorrente, [adicione o gatilho HTTP](#http-trigger) como o primeiro passo no seu fluxo de trabalho. Cada vez que o gatilho verifica o ponto final, o gatilho chama ou envia um *pedido* para o ponto final. A resposta do ponto final determina se o fluxo de trabalho da sua aplicação lógica funciona. O gatilho transmite qualquer conteúdo da resposta do ponto final às ações da sua aplicação lógica.

* Para chamar um ponto final de qualquer outro lugar do seu fluxo de trabalho, [adicione a ação HTTP](#http-action). A resposta do ponto final determina como as restantes ações do seu fluxo de trabalho funcionam.

Este artigo mostra como utilizar o gatilho HTTP e a ação HTTP para que a sua aplicação lógica possa enviar chamadas de saída para outros serviços e sistemas.

Para obter informações sobre encriptação, segurança e autorização para chamadas de saída da sua aplicação lógica, como [a Transport Layer Security (TLS),](https://en.wikipedia.org/wiki/Transport_Layer_Security)anteriormente conhecida como Secure Sockets Layer (SSL), certificados auto-assinados ou [Autenticação Aberta do Diretório Ativo Azure (Azure AD OAuth)](../active-directory/develop/index.yml), consulte [acesso seguro e dados - Acesso a chamadas de saída para outros serviços e sistemas](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O URL para o ponto final alvo que você quer chamar

* Conhecimento básico sobre [como criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* A aplicação lógica de onde pretende chamar o ponto final do alvo. Para começar com o gatilho HTTP, [crie uma aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para utilizar a ação HTTP, inicie a sua aplicação lógica com o gatilho que pretende. Este exemplo utiliza o gatilho HTTP como primeiro passo.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Adicione um gatilho HTTP

Este gatilho incorporado faz uma chamada HTTP para o URL especificado para um ponto final e devolve uma resposta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a sua aplicação lógica em branco no Logic App Designer.

1. Sob a caixa de pesquisa do designer, selecione **Built-in**. Na caixa de pesquisa, introduza `http` como filtro. Na lista **de gatilhos,** selecione o gatilho **HTTP.**

   ![Selecione Acionador HTTP](./media/connectors-native-http/select-http-trigger.png)

   Este exemplo renomea o gatilho para "HTTP trigger" para que o passo tenha um nome mais descritivo. Além disso, o exemplo adiciona mais tarde uma ação HTTP, e ambos os nomes devem ser únicos.

1. Forneça os valores para os [parâmetros do gatilho HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) que pretende incluir na chamada para o ponto final do alvo. Encete a recorrência para a frequência com que pretende que o gatilho verifique o ponto final do alvo.

   ![Introduza os parâmetros do gatilho HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Se selecionar um tipo de autenticação diferente de **Nenhum,** as definições de autenticação diferem em função da sua seleção. Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP, consulte estes tópicos:

   * [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autenticar o acesso a recursos com identidades geridas](../logic-apps/create-managed-service-identity.md)

1. Para adicionar outros parâmetros disponíveis, abra a nova lista **de parâmetros add** e selecione os parâmetros que deseja.

1. Continue a construir o fluxo de trabalho da sua aplicação lógica com ações que funcionam quando o gatilho dispara.

1. Quando terminar, lembre-se de guardar a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Adicione uma ação HTTP

Esta ação incorporada faz uma chamada HTTP para o URL especificado para um ponto final e devolve uma resposta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o gatilho HTTP como primeiro passo.

1. Sob o passo em que pretende adicionar a ação HTTP, selecione **Novo passo**.

   Para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre os degraus. Selecione o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Em **Escolha uma ação**, selecione **Built-in**. Na caixa de pesquisa, introduza `http` como filtro. Na lista **de Ações,** selecione a ação **HTTP.**

   ![Selecione ação HTTP](./media/connectors-native-http/select-http-action.png)

   Este exemplo renomea a ação para "AÇÃO HTTP" para que o passo tenha um nome mais descritivo.

1. Forneça os valores para os [parâmetros de ação HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) que pretende incluir na chamada para o ponto final do destino.

   ![Introduza parâmetros de ação HTTP](./media/connectors-native-http/http-action-parameters.png)

   Se selecionar um tipo de autenticação diferente de **Nenhum,** as definições de autenticação diferem em função da sua seleção. Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP, consulte estes tópicos:

   * [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autenticar o acesso a recursos com identidades geridas](../logic-apps/create-managed-service-identity.md)

1. Para adicionar outros parâmetros disponíveis, abra a nova lista **de parâmetros add** e selecione os parâmetros que deseja.

1. Quando terminar, lembre-se de guardar a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

## <a name="trigger-and-action-outputs"></a>Saídas de gatilho e ação

Aqui está mais informações sobre as saídas de um gatilho http ou ação, que devolve esta informação:

| Propriedade | Tipo | Descrição |
|----------|------|-------------|
| `headers` | Objeto JSON | Os cabeçalhos do pedido |
| `body` | Objeto JSON | O objeto com o conteúdo do corpo do pedido |
| `status code` | Número inteiro | O código de estado do pedido |
|||

| Código de estado | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Aceite |
| 400 | Mau pedido |
| 401 | Não autorizado |
| 403 | Proibido |
| 404 | Não encontrado |
| 500 | Erro interno do servidor. Ocorreu um erro desconhecido. |
|||

## <a name="content-with-multipartform-data-type"></a>Conteúdo com tipo multiparte/dados de formulário

Para lidar com conteúdo que tenha `multipart/form-data` tipo em pedidos HTTP, pode adicionar um objeto JSON que inclua o `$content-type` e atribui ao corpo do pedido HTTP utilizando este `$multipart` formato.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Por exemplo, suponha que tem uma aplicação lógica que envia um pedido HTTP POST para um ficheiro Excel para um website utilizando a API desse site, que suporta o `multipart/form-data` tipo. Eis o que esta ação pode parecer:

![Dados de formulários multipartes](./media/connectors-native-http/http-action-multipart.png)

Aqui está o mesmo exemplo que mostra a definição JSON da ação HTTP na definição de fluxo de trabalho subjacente:

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="content-with-applicationx-www-form-urlencoded-type"></a>Conteúdo com aplicação/x-www-form-urlencoded type

Para fornecer dados codificados por formulários no organismo para um pedido HTTP, tem de especificar que os dados têm o `application/x-www-form-urlencoded` tipo de conteúdo. No gatilho ou ação HTTP, adicione o `content-type` cabeçalho. Desa estale o valor do cabeçalho para `application/x-www-form-urlencoded` .

Por exemplo, suponha que tem uma aplicação lógica que envia um pedido HTTP POST para um site, que suporta o `application/x-www-form-urlencoded` tipo. Eis o que esta ação pode parecer:

![Screenshot que mostra um pedido HTTP com o cabeçalho 'tipo conteúdo' definido para 'aplicação/x-www-form-urlencoded'](./media/connectors-native-http/http-action-urlencoded.png)

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>Comportamento assíncronos de pedido-resposta

Por padrão, todas as ações baseadas em HTTP em Azure Logic Apps seguem o [padrão de funcionamento assíncrona.](/azure/architecture/patterns/async-request-reply) Este padrão especifica que após uma chamada de ação HTTP ou enviar um pedido para um ponto final, serviço, sistema ou API, o recetor devolve imediatamente uma resposta ["202 ACCEPTED".](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) Este código confirma que o recetor aceitou o pedido mas ainda não terminou o processamento. A resposta pode incluir um `location` cabeçalho que especifica o URL e um ID de atualização que o chamador pode usar para pesquisar ou verificar o estado do pedido assíncronos até que o recetor pare de processar e devolva uma resposta de sucesso ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) ou outra resposta não-202. No entanto, o chamador não tem que esperar pelo pedido para terminar o processamento e pode continuar a executar a próxima ação. Para mais informações, consulte [a integração de microserviços Asynchronous que impõe autonomia de microserviços.](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging)

* No Logic App Designer, a ação HTTP, mas não desencadeada, tem uma definição **de Padrão Assíncronos,** que é ativada por padrão por padrão. Esta definição especifica que o chamador não espera que o processamento termine e pode passar para a próxima ação, mas continua a verificar o estado até que o processamento pare. Se desativado, esta definição especifica que o chamador aguarda que o processamento termine antes de passar para a ação seguinte.

  Para encontrar esta definição, siga estes passos:

  1. Na barra de títulos http action, selecione o botão elipses **(...**) que abre as definições da ação.

  1. Encontre a **definição de padrão assíncronos.**

     ![Definição de "Padrão Assíncronos"](./media/connectors-native-http/asynchronous-pattern-setting.png)

* A definição de Notação de Objetos JavaScript (JSON) subjacente da ação HTTP segue implicitamente o padrão de funcionamento assíncrona.

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>Desativar operações assíncronos

Por vezes, pode querer o comportamento assíncrodo da ação HTTP em cenários específicos, por exemplo, quando pretende:

* [Evite intervalos de tempo HTTP para tarefas de longa duração](#avoid-http-timeouts)
* [Desativar cabeçalhos de localização de verificação](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>Desligue **a definição de padrão assíncronos**

1. No Logic App Designer, na barra de títulos http action, selecione o botão elipses **(...**) que abre as definições da ação.

1. Encontre a **definição de padrão assíncronos,** ligue a definição para **desligar** se ativada e selecione **Feito**.

   ![Desative a definição "Padrão Assíncronos"](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Desativar o padrão assíncronos na definição JSON da ação

Na definição JSON subjacente da ação HTTP, adicione a opção de [ `"DisableAsyncPattern"` operação](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options) à definição da ação de modo a que a ação siga o padrão de funcionamento sincronizado. Para obter mais informações, consulte também [executar ações num padrão de funcionamento sincronizado.](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern)

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>Evite intervalos de tempo HTTP para tarefas de longa duração

Os pedidos HTTP têm um [limite de tempo.](../logic-apps/logic-apps-limits-and-config.md#http-limits) Se tiver uma ação HTTP de longa duração que se esgota devido a este limite, tem estas opções:

* [Desative o padrão de funcionamento assíncronos da ação HTTP](#disable-asynchronous-operations) para que a ação não faça uma sondagem contínua ou verifique o estado do pedido. Em vez disso, a ação aguarda que o recetor responda com o estado e os resultados após o pedido terminar o processamento.

* Substitua a ação HTTP pela ação [HTTP Webhook,](../connectors/connectors-native-webhook.md)que aguarda que o recetor responda com o estado e os resultados após o processamento do pedido.

<a name="disable-location-header-check"></a>

## <a name="disable-checking-location-headers"></a>Desativar cabeçalhos de localização de verificação

Alguns pontos finais, serviços, sistemas ou APIs devolvem uma resposta "202 ACCEPTED" que não tem `location` cabeçalho. Para evitar que uma ação HTTP verifique continuamente o estado do pedido quando o `location` cabeçalho não existe, pode ter estas opções:

* [Desative o padrão de funcionamento assíncronos da ação HTTP](#disable-asynchronous-operations) para que a ação não faça uma sondagem contínua ou verifique o estado do pedido. Em vez disso, a ação aguarda que o recetor responda com o estado e os resultados após o pedido terminar o processamento.

* Substitua a ação HTTP pela ação [HTTP Webhook,](../connectors/connectors-native-webhook.md)que aguarda que o recetor responda com o estado e os resultados após o processamento do pedido.

## <a name="known-issues"></a>Problemas conhecidos

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>Cabeçalhos HTTP omitidos

Se um gatilho ou ação HTTP incluir estes cabeçalhos, as Aplicações Lógicas removem estes cabeçalhos da mensagem de pedido gerada sem mostrar qualquer aviso ou erro:

* `Accept-*` cabeçalhos, exceto para `Accept-version`
* `Allow`
* `Content-*` com estas exceções: `Content-Disposition` `Content-Encoding` , e `Content-Type`
* `Cookie`
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

Embora as Aplicações Lógicas não o impeçam de guardar aplicações lógicas que usam um gatilho HTTP ou ação com estes cabeçalhos, as Aplicações Lógicas ignoram estes cabeçalhos.

## <a name="connector-reference"></a>Referência do conector

Para obter mais informações sobre os parâmetros de desencadeamento e ação, consulte estas secções:

* [Parâmetros de disparo HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parâmetros de ação HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

## <a name="next-steps"></a>Passos seguintes

* [Acesso seguro e dados - Acesso a chamadas de saída para outros serviços e sistemas](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)
* [Conectores para as Logic Apps](../connectors/apis-list.md)
