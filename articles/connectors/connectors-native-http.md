---
title: Ligue para os pontos finais do serviço utilizando HTTP ou HTTPS
description: Enviar pedidos http ou HTTPS de saída para atender pontos finais a partir de Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
tags: connectors
ms.openlocfilehash: 33075173385a6e36829199c5bda854c78a4424fc
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84325121"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Call service endpoints over HTTP ou HTTPS from Azure Logic Apps

Com [as Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o gatilho ou ação HTTP incorporados, pode criar tarefas automatizadas e fluxos de trabalho que enviam pedidos para pontos finais de serviço em HTTP ou HTTPS. Por exemplo, pode monitorizar o ponto final de serviço do seu website verificando esse ponto final num horário específico. Quando o evento especificado acontece nesse ponto final, como o seu site a descer, o evento desencadeia o fluxo de trabalho da sua aplicação lógica e executa as ações nesse fluxo de trabalho. Se pretender receber e responder às chamadas HTTPS de entrada, utilize a ação [de detonador ou resposta incorporada](../connectors/connectors-native-reqres.md).

* Para verificar ou *sondar* um ponto final num horário recorrente, [adicione o gatilho HTTP](#http-trigger) como o primeiro passo no seu fluxo de trabalho. Cada vez que o gatilho verifica o ponto final, o gatilho chama ou envia um *pedido* para o ponto final. A resposta do ponto final determina se o fluxo de trabalho da sua aplicação lógica funciona. O gatilho transmite qualquer conteúdo da resposta do ponto final às ações da sua aplicação lógica.

* Para chamar um ponto final de qualquer outro lugar do seu fluxo de trabalho, [adicione a ação HTTP](#http-action). A resposta do ponto final determina como as restantes ações do seu fluxo de trabalho funcionam.

Este artigo mostra como adicionar um gatilho HTTP ou ação ao fluxo de trabalho da sua aplicação lógica.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O URL para o ponto final alvo que você quer chamar

* Conhecimento básico sobre [como criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* A aplicação lógica de onde pretende chamar o ponto final do alvo. Para começar com o gatilho HTTP, [crie uma aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Para utilizar a ação HTTP, inicie a sua aplicação lógica com o gatilho que pretende. Este exemplo utiliza o gatilho HTTP como primeiro passo.

<a name="tls-support"></a>

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Com base na capacidade do ponto final do ponto final, as chamadas de saída suportam a Segurança da Camada de Transporte (TLS), que anteriormente era Secure Sockets Layer (SSL), versões 1.0, 1.1 e 1.2. A Logic Apps negoceia com o ponto final utilizando a versão mais suportada possível.

Por exemplo, se o ponto final suportar 1.2, o conector HTTP utiliza primeiro o 1.2. Caso contrário, o conector utiliza a próxima versão suportada mais alta.

<a name="self-signed"></a>

## <a name="self-signed-certificates"></a>Certificados auto-assinados

* Para aplicações lógicas no ambiente global e multi-inquilino Azure, o conector HTTP não permite certificados TLS/SSL auto-assinados. Se a sua aplicação lógica fizer uma chamada HTTP para um servidor e apresentar um certificado auto-assinado TLS/SSL, a chamada HTTP falha com um `TrustFailure` erro.

* Para aplicações lógicas num [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)o conector HTTP permite certificados auto-assinados para apertos de mão TLS/SSL. No entanto, primeiro deve [ativar o suporte de certificado auto-assinado](../logic-apps/create-integration-service-environment-rest-api.md#request-body) para um ISE existente ou novo ISE, utilizando a API de Aplicações Lógicas e instalar o certificado público no `TrustedRoot` local.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="omitted-http-headers"></a>Cabeçalhos HTTP omitidos

Se um gatilho ou ação HTTP incluir estes cabeçalhos, as Aplicações Lógicas removem estes cabeçalhos da mensagem de pedido gerada sem mostrar qualquer aviso ou erro:

* `Accept-*`
* `Allow`
* `Content-*`com estas exceções: `Content-Disposition` `Content-Encoding` , e`Content-Type`
* `Cookie`
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

Embora as Aplicações Lógicas não o impeçam de guardar aplicações lógicas que usam um gatilho HTTP ou ação com estes cabeçalhos, as Aplicações Lógicas ignoram estes cabeçalhos.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Adicione um gatilho HTTP

Este gatilho incorporado faz uma chamada HTTP para o URL especificado para um ponto final e devolve uma resposta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a sua aplicação lógica em branco no Logic App Designer.

1. Sob a caixa de pesquisa do designer, selecione **Built-in**. Na caixa de pesquisa, introduza `http` como filtro. Na lista **de gatilhos,** selecione o gatilho **HTTP.**

   ![Selecione o gatilho HTTP](./media/connectors-native-http/select-http-trigger.png)

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

## <a name="connector-reference"></a>Referência do conector

Para obter mais informações sobre os parâmetros de desencadeamento e ação, consulte estas secções:

* [Parâmetros de disparo HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parâmetros de ação HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Detalhes da saída

Aqui está mais informações sobre as saídas de um gatilho http ou ação, que devolve esta informação:

| Nome da propriedade | Tipo | Description |
|---------------|------|-------------|
| cabeçalhos | objeto | Os cabeçalhos do pedido |
| body | objeto | Objeto JSON | O objeto com o conteúdo do corpo do pedido |
| código de estado | int | O código de estado do pedido |
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

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
