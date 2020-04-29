---
title: Aguarde e responda aos eventos
description: Automatizar fluxos de trabalho que desencadeiam, pausam e retomam com base em eventos num ponto final de serviço utilizando apps da Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/06/2020
tags: connectors
ms.openlocfilehash: 0a3fb9a8a72b384d2af4af38bdc382e541ddf535
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80656287"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Criar e executar fluxos de trabalho automatizados baseados em eventos utilizando webhooks HTTP em Aplicações lógicas azure

Com [as Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e o conector http Webhook incorporado, pode automatizar fluxos de trabalho que esperam e funcionam com base em eventos específicos que ocorrem num ponto final HTTP ou HTTPS através da construção de aplicações lógicas. Por exemplo, pode criar uma aplicação lógica que monitorize um ponto final de serviço esperando por um evento específico antes de desencadear o fluxo de trabalho e executar as ações especificadas, em vez de verificar ou *votar* regularmente esse ponto final.

Aqui estão alguns fluxos de trabalho baseados em eventos:

* Aguarde que um item chegue de um [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) antes de desencadear uma aplicação lógica.
* Aguarde uma aprovação antes de continuar um fluxo de trabalho.

## <a name="how-do-webhooks-work"></a>Como funcionam os webhooks?

Um gatilho http webhook é baseado em eventos, o que não depende de verificação ou sondagem regular para novos itens. Quando guarda uma aplicação lógica que começa com um gatilho de webhook, ou quando muda a sua aplicação lógica de desativada para ativada, o gatilho do webhook *subscreve* um serviço ou ponto final específico, registando um URL de *callback* com esse serviço ou ponto final. O gatilho aguarda então que esse serviço ou ponto final ligue para o URL, que começa a executar a aplicação lógica. Semelhante ao [gatilho do Pedido,](connectors-native-reqres.md)a aplicação lógica dispara imediatamente quando o evento especificado acontece. O gatilho *desliga-se* do serviço ou do ponto final se remover o gatilho e guardar a sua aplicação lógica, ou quando alterar a sua aplicação lógica de ativada para desativar.

Uma ação de webhook HTTP também é baseada em eventos e *subscreve* um serviço ou ponto final específico, registando um URL de *callback* com esse serviço ou ponto final. A ação do webhook interrompe o fluxo de trabalho da aplicação lógica e aguarda até que o serviço ou ponto final ligue para o URL antes que a aplicação lógica retome em funcionamento. A aplicação lógica de ação *desabre subscrições* do serviço ou ponto final nestes casos:

* Quando a ação do webhook terminar com sucesso
* Se a aplicação lógica for cancelada enquanto espera por uma resposta
* Antes que a aplicação lógica se estem

Por exemplo, a ação de email de [**envio**](connectors-create-api-office365-outlook.md) do conector Do Office 365 Outlook é um exemplo de ação webhook que segue este padrão. Pode estender este padrão a qualquer serviço utilizando a ação webhook.

> [!NOTE]
> Logic Apps aplica a Segurança da Camada de Transporte (TLS) 1.2 ao receber a chamada de volta para o gatilho ou ação do webhook HTTP. Se vir erros de aperto de mão TLS, certifique-se de que utiliza TLS 1.2. Para chamadas recebidas, aqui estão as suítes de cifra suportadas:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Para obter mais informações, veja estes tópicos:

* [PARÂMETROS do gatilho http Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhooks e subscrições](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Crie APIs personalizados que suportem um webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O URL para um ponto final ou API já implantado que suporta o padrão de subscrição e cancelamento de subscrição de webhook para [gatilhos webhook em aplicações lógicas](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) ou [ações de webhook em aplicações lógicas](../logic-apps/logic-apps-create-api-app.md#webhook-actions) conforme apropriado

* Conhecimento básico sobre [como criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

* A aplicação lógica onde você quer esperar por eventos específicos no ponto final do alvo. Para começar com o gatilho HTTP Webhook, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar a ação HTTP Webhook, inicie a sua aplicação lógica com qualquer gatilho que pretenda. Este exemplo utiliza o gatilho HTTP como primeiro passo.

## <a name="add-an-http-webhook-trigger"></a>Adicione um gatilho http Webhook

Este gatilho incorporado chama o ponto final de subscrição no serviço alvo e regista um URL de callback com o serviço alvo. A sua aplicação lógica aguarda então `HTTP POST` que o serviço alvo envie um pedido para o URL de callback. Quando este evento acontece, o gatilho dispara e passa quaisquer dados no pedido junto ao fluxo de trabalho.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a sua aplicação lógica em branco no Logic App Designer.

1. Na caixa de pesquisa do `http webhook` designer, introduza como filtro. A partir da lista **de Gatilhos,** selecione o gatilho **HTTP Webhook.**

   ![Selecione o gatilho http Webhook](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Este exemplo renomea `HTTP Webhook trigger` o gatilho para que o passo tenha um nome mais descritivo. Além disso, o exemplo adiciona mais tarde uma ação http Webhook, e ambos os nomes devem ser únicos.

1. Forneça os valores para os parâmetros do [gatilho http Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) que pretende utilizar para as chamadas de subscrição e cancelamento de subscrição.

   Neste exemplo, o gatilho inclui os métodos, URIs e organismos de mensagem a utilizar durante a realização das operações de subscrição e cancelamento de subscrição.

   ![Introduza os parâmetros do gatilho http Webhook](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Subscrição - Método** | Sim | O método a utilizar ao subscrever o ponto final do alvo |
   | **Inscreva-se - URI** | Sim | O URL a utilizar para subscrever o ponto final do alvo |
   | **Subscrever - Corpo** | Não | Qualquer organismo de mensagem a incluir no pedido de subscrição. Este exemplo inclui o URL de callback que identifica exclusivamente o `@listCallbackUrl()` assinante, que é a sua aplicação lógica, utilizando a expressão para recuperar o URL de callback da sua aplicação lógica. |
   | **Cancelar a subscrição - Método** | Não | O método a utilizar ao cancelar a subscrição do ponto final-alvo |
   | **Cancelar a subscrição - URI** | Não | O URL a utilizar para dessubscrever a partir do ponto final do alvo |
   | **Cancelar a subscrição - Corpo** | Não | Um organismo de mensagem opcional para incluir no pedido de cancelamento de subscrição <p><p>**Nota:** Esta propriedade não suporta `listCallbackUrl()` a utilização da função. No entanto, o gatilho inclui e `x-ms-client-tracking-id` `x-ms-workflow-operation-name`envia automaticamente os cabeçalhos, e , que o serviço-alvo pode usar para identificar exclusivamente o assinante. |
   ||||

1. Para adicionar outras propriedades do gatilho, abra a **lista de novos parâmetros Adicionar.**

   ![Adicione mais propriedades de gatilho](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Por exemplo, se precisar de utilizar a autenticação, pode adicionar as propriedades **Subscrever - Autenticação** e Cancelar A **autenticação** . Para obter mais informações sobre os tipos de autenticação disponíveis para http Webhook, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Continue a construir o fluxo de trabalho da sua aplicação lógica com ações que funcionam quando o gatilho dispara.

1. Quando terminar, pronto, lembre-se de guardar a sua aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

   Salvar a sua aplicação lógica chama o ponto final de subscrição no serviço alvo e regista o URL de callback. A sua aplicação lógica aguarda então `HTTP POST` que o serviço alvo envie um pedido para o URL de callback. Quando este evento acontece, o gatilho dispara e passa quaisquer dados no pedido junto ao fluxo de trabalho. Se esta operação terminar com sucesso, o gatilho desabre a partir do ponto final, e a sua aplicação lógica continua o fluxo de trabalho restante.

## <a name="add-an-http-webhook-action"></a>Adicione uma ação http Webhook

Esta ação incorporada chama o ponto final de subscrição no serviço alvo e regista um URL de callback com o serviço alvo. A sua aplicação lógica faz então uma `HTTP POST` pausa e aguarda que o serviço alvo envie um pedido para o URL de callback. Quando este evento acontece, a ação passa quaisquer dados no pedido junto ao fluxo de trabalho. Se a operação terminar com sucesso, a ação desabre a partir do ponto final, e a sua aplicação lógica continua a executar o fluxo de trabalho restante.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o gatilho HTTP Webhook como primeiro passo.

1. Sob o passo onde pretende adicionar a ação HTTP Webhook, selecione **Novo passo**.

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre os degraus. Selecione o**+** sinal de mais ( ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa do `http webhook` designer, introduza como filtro. Na lista **de Ações,** selecione a ação **HTTP Webhook.**

   ![Selecione ação http Webhook](./media/connectors-native-webhook/select-http-webhook-action.png)

   Este exemplo renomea a ação para "HTTP Webhook action" de modo a que o passo tenha um nome mais descritivo.

1. Forneça os valores para os parâmetros de ação HTTP Webhook, semelhantes aos parâmetros do [gatilho http Webhook,](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)que pretende utilizar para as chamadas de subscrição e cancelamento de subscrição.

   Neste exemplo, a ação inclui os métodos, URIs e organismos de mensagens a utilizar no momento da realização das operações de subscrição e cancelamento de subscrição.

   ![Introduza os parâmetros de ação http Webhook](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Subscrição - Método** | Sim | O método a utilizar ao subscrever o ponto final do alvo |
   | **Inscreva-se - URI** | Sim | O URL a utilizar para subscrever o ponto final do alvo |
   | **Subscrever - Corpo** | Não | Qualquer organismo de mensagem a incluir no pedido de subscrição. Este exemplo inclui o URL de callback que identifica exclusivamente o `@listCallbackUrl()` assinante, que é a sua aplicação lógica, utilizando a expressão para recuperar o URL de callback da sua aplicação lógica. |
   | **Cancelar a subscrição - Método** | Não | O método a utilizar ao cancelar a subscrição do ponto final-alvo |
   | **Cancelar a subscrição - URI** | Não | O URL a utilizar para dessubscrever a partir do ponto final do alvo |
   | **Cancelar a subscrição - Corpo** | Não | Um organismo de mensagem opcional para incluir no pedido de cancelamento de subscrição <p><p>**Nota:** Esta propriedade não suporta `listCallbackUrl()` a utilização da função. No entanto, a ação inclui e `x-ms-client-tracking-id` `x-ms-workflow-operation-name`envia automaticamente os cabeçalhos, e , que o serviço alvo pode usar para identificar exclusivamente o assinante. |
   ||||

1. Para adicionar outras propriedades de ação, abra a **lista adicionar novo parâmetro.**

   ![Adicione mais propriedades de ação](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Por exemplo, se precisar de utilizar a autenticação, pode adicionar as propriedades **Subscrever - Autenticação** e Cancelar A **autenticação** . Para obter mais informações sobre os tipos de autenticação disponíveis para http Webhook, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Quando terminar, lembre-se de guardar a sua aplicação lógica. Na barra de ferramentas de design, selecione **Guardar**.

   Agora, quando esta ação corre, a sua aplicação lógica chama o ponto final de subscrição no serviço alvo e regista o URL de callback. A aplicação lógica interrompe então o fluxo de trabalho `HTTP POST` e aguarda que o serviço alvo envie um pedido para o URL de callback. Quando este evento acontece, a ação passa quaisquer dados no pedido junto ao fluxo de trabalho. Se a operação terminar com sucesso, a ação desabre a partir do ponto final, e a sua aplicação lógica continua a executar o fluxo de trabalho restante.

## <a name="connector-reference"></a>Referência do conector

Para obter mais informações sobre os parâmetros de disparo e de ação, semelhantes uns aos outros, consulte os [parâmetros HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

### <a name="output-details"></a>Detalhes da saída

Aqui está mais informações sobre as saídas de um gatilho ou ação http Webhook, que devolve esta informação:

| Nome da propriedade | Tipo | Descrição |
|---------------|------|-------------|
| cabeçalhos | objeto | Os cabeçalhos do pedido |
| body | objeto | Objeto JSON | O objeto com o conteúdo do corpo a partir do pedido |
| código de estado | int | O código de estado do pedido |
|||

| Código de estado | Descrição |
|-------------|-------------|
| 200 | OK |
| 202 | Aceite |
| 400 | Mau pedido |
| 401 | Não autorizado |
| 403 | Proibido |
| 404 | Não encontrado |
| 500 | Erro interno do servidor. Erro desconhecido ocorreu. |
|||

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
