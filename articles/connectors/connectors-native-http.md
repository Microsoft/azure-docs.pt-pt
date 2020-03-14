---
title: Pontos finais do serviço de chamada utilizando HTTP ou HTTPS
description: Envie pedidos de saída http ou HTTPS para serviço de pontos finais de Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 8aefe851708c0b8d8780d03e4364e034e783bf4a
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297215"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Pontos finais do serviço de chamada sobre HTTP ou HTTPS de Aplicações Lógicas Azure

Com [as Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e o gatilho ou ação HTTP incorporado, pode criar tarefas e fluxos de trabalho automatizados que enviam pedidos para pontos finais de serviço em HTTP ou HTTPS. Por exemplo, pode monitorizar o ponto final do serviço para o seu website, verificando esse ponto final numa programação específica. Quando o evento especificado acontece nesse ponto final, como o seu site a descer, o evento despoleta o fluxo de trabalho da sua aplicação lógica e executa as ações nesse fluxo de trabalho. Se quiser receber e responder às chamadas HTTPS de entrada, utilize o gatilho de pedido incorporado [ou a ação resposta](../connectors/connectors-native-reqres.md).

> [!NOTE]
> Baseado na capacidade do ponto final do alvo, o conector HTTP suporta as versões Transport Layer Security (TLS) 1.0, 1.1 e 1.2. Logic Apps negoceia com o ponto final sobre a utilização da versão suportada mais alta possível. Assim, por exemplo, se o ponto final suportar 1.2, o conector utiliza 1.2 primeiro. Caso contrário, o conector utiliza a versão suportada mais alta.

Para verificar ou *fazer uma sondagem* sobre um calendário recorrente, [adicione o gatilho HTTP](#http-trigger) como o primeiro passo no seu fluxo de trabalho. Cada vez que o gatilho verifica o ponto final, o gatilho chama ou envia um *pedido* para o ponto final. A resposta do ponto final determina se o fluxo de trabalho da sua aplicação lógica funciona. O gatilho transmite qualquer conteúdo da resposta do ponto final às ações na sua aplicação lógica.

Para chamar um ponto final de qualquer outro lugar do seu fluxo de trabalho, [adicione a ação HTTP](#http-action). A resposta do ponto final determina como as restantes ações do seu fluxo de trabalho funcionam.

> [!IMPORTANT]
> Se um gatilho ou ação HTTP incluir estes cabeçalhos, as Aplicações Lógicas removem estes cabeçalhos da mensagem de pedido gerada sem mostrar qualquer aviso ou erro:
>
> * `Accept-*`
> * `Allow`
> * `Content-*` com estas exceções: `Content-Disposition`, `Content-Encoding`e `Content-Type`
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Embora as Aplicações Lógicas não o impeçam de salvar aplicações lógicas que usam um gatilho ou ação HTTP com estes cabeçalhos, as Aplicações Lógicas ignoram estes cabeçalhos.

Este artigo mostra como adicionar um gatilho ou ação HTTP ao fluxo de trabalho da sua aplicação lógica.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O URL para o ponto final do alvo que você quer chamar

* Conhecimento básico sobre [como criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Do Azure?](../logic-apps/logic-apps-overview.md)

* A aplicação lógica de onde pretende chamar o ponto final do alvo. Para começar com o gatilho HTTP, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar a ação HTTP, inicie a sua aplicação lógica com qualquer gatilho que pretenda. Este exemplo utiliza o gatilho HTTP como primeiro passo.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Adicione um gatilho HTTP

Este gatilho incorporado faz uma chamada http para o URL especificado para um ponto final e devolve uma resposta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a sua aplicação lógica em branco no Logic App Designer.

1. Sob a caixa de pesquisa do designer, selecione **Incorporado**. Na caixa de pesquisa, introduza `http` como filtro. A partir da lista **de Gatilhos,** selecione o gatilho **HTTP.**

   ![Selecione gatilho HTTP](./media/connectors-native-http/select-http-trigger.png)

   Este exemplo renomeia o gatilho para "HTTP trigger" de modo a que o passo tenha um nome mais descritivo. Além disso, o exemplo adiciona mais tarde uma ação HTTP, e ambos os nomes devem ser únicos.

1. Forneça os valores para os [parâmetros](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) do gatilho HTTP que pretende incluir na chamada para o ponto final do alvo. Configurar a recorrência para saber quantas vezes pretende que o gatilho verifique o ponto final do alvo.

   ![Introduza os parâmetros do gatilho HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Se selecionar um tipo de autenticação diferente do **Nenhum,** as definições de autenticação diferem com base na sua seleção. Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP, consulte estes tópicos:

   * [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autenticar o acesso a recursos com identidades geridas](../logic-apps/create-managed-service-identity.md)

1. Para adicionar outros parâmetros disponíveis, abra a **lista de novos parâmetros E** selecione os parâmetros que deseja.

1. Continue a construir o fluxo de trabalho da sua aplicação lógica com ações que funcionam quando o gatilho dispara.

1. Quando terminar, lembre-se de guardar a sua aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Adicione uma ação HTTP

Esta ação incorporada faz uma chamada http para o URL especificado para um ponto final e devolve uma resposta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o gatilho HTTP como primeiro passo.

1. Sob o passo onde pretende adicionar a ação HTTP, selecione **Novo passo**.

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre os degraus. Selecione o sinal plus **(+** ) que aparece e, em seguida, **selecione Adicionar uma ação**.

1. Em **'Escolha uma ação**', selecione **Incorporado**' . Na caixa de pesquisa, introduza `http` como filtro. Na lista **de Ações,** selecione a ação **HTTP.**

   ![Selecione ação HTTP](./media/connectors-native-http/select-http-action.png)

   Este exemplo renomea a ação para "HTTP action" de modo a que o passo tenha um nome mais descritivo.

1. Forneça os valores para os [parâmetros](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) de ação HTTP que pretende incluir na chamada para o ponto final do alvo.

   ![Insira os parâmetros de ação HTTP](./media/connectors-native-http/http-action-parameters.png)

   Se selecionar um tipo de autenticação diferente do **Nenhum,** as definições de autenticação diferem com base na sua seleção. Para obter mais informações sobre os tipos de autenticação disponíveis para HTTP, consulte estes tópicos:

   * [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autenticar o acesso a recursos com identidades geridas](../logic-apps/create-managed-service-identity.md)

1. Para adicionar outros parâmetros disponíveis, abra a **lista de novos parâmetros E** selecione os parâmetros que deseja.

1. Quando terminar, lembre-se de guardar a sua aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

## <a name="content-with-multipartform-data-type"></a>Conteúdo com tipo de dados multipart/formulário

Para lidar com o conteúdo que tem `multipart/form-data` tipo em pedidos HTTP, pode adicionar um objeto JSON que inclui a `$content-type` e `$multipart` atribui ao corpo do pedido HTTP utilizando este formato.

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

Por exemplo, suponha que tenha uma aplicação lógica que envia um pedido HTTP POST para um ficheiro Excel para um website usando a API desse site, que suporta o tipo `multipart/form-data`. Eis como esta ação pode parecer:

![Dados de formulários multipartes](./media/connectors-native-http/http-action-multipart.png)

Aqui está o mesmo exemplo que mostra a definição JSON da ação HTTP na definição subjacente de fluxo de trabalho:

```json
{
   "HTTP_action": {
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

## <a name="connector-reference"></a>Referência do conector

Para obter mais informações sobre os parâmetros de disparo e ação, consulte estas secções:

* [Parâmetros de gatilho HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parâmetros de ação HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Detalhes da saída

Aqui está mais informações sobre as saídas de um gatilho ou ação HTTP, que devolve esta informação:

| Nome da propriedade | Tipo | Descrição |
|---------------|------|-------------|
| cabeçalhos | object | Os cabeçalhos do pedido |
| corpo | object | Objeto JSON | O objeto com o conteúdo do corpo a partir do pedido |
| código de estado | int | O código de estado do pedido |
|||

| Código de estado | Descrição |
|-------------|-------------|
| 200 | OK |
| 202 | Aceite |
| 400 | Pedido incorreto |
| 401 | Não Autorizada |
| 403 | Proibido |
| 404 | Não foi encontrado |
| 500 | Erro interno do servidor. Erro desconhecido ocorreu. |
|||

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
