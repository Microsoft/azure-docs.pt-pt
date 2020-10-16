---
title: Esperar e responder aos acontecimentos
description: Automatizar fluxos de trabalho que desencadeiam, pausam e retomam com base em eventos num ponto final de serviço utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: 7c6f3c4e3e4a2a29fe6a02c03043e3dfb81a2010
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89227904"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Criar e executar fluxos de trabalho automatizados baseados em eventos utilizando webhooks HTTP em Azure Logic Apps

Com [as Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o conector HTTP Webhook incorporado, pode criar tarefas automatizadas e fluxos de trabalho que subscrevam um ponto final de serviço, aguardar eventos específicos e correr com base nesses eventos, em vez de verificar ou *sondar* regularmente esse ponto final.

Aqui estão alguns fluxos de trabalho baseados em webhook:

* Aguarde que um item chegue de um [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) antes de desencadear uma aplicação lógica.
* Aguarde uma aprovação antes de continuar um fluxo de trabalho.

Este artigo mostra como usar o gatilho Webhook e a ação Webhook para que a sua aplicação lógica possa receber e responder a eventos num ponto final de serviço.

## <a name="how-do-webhooks-work"></a>Como funcionam os webhooks?

Um gatilho webhook é baseado em eventos, o que não depende de verificar ou sondar regularmente para novos itens. Quando guarda uma aplicação lógica que começa com um gatilho webhook, ou quando muda a sua aplicação lógica de *desativada* para ativada, o gatilho webhook subscreve o ponto final de serviço especificado, registando um *URL de retorno* com esse ponto final. Em seguida, o gatilho aguarda que o ponto final do serviço ligue para o URL, que começa a executar a aplicação lógica. Semelhante ao [gatilho Request](connectors-native-reqres.md), a aplicação lógica dispara imediatamente quando o evento especificado acontece. O webhook desencadeia *subscrições* a partir do ponto final do serviço se remover o gatilho e guardar a sua aplicação lógica, ou quando alterar a sua aplicação lógica de ativação para desativar.

Uma ação webhook também é baseada em *eventos* e subscreve o ponto final de serviço especificado, registando um *URL de retorno* de chamada com esse ponto final. A ação webhook interrompe o fluxo de trabalho da aplicação lógica e aguarda até que o ponto final de serviço ligue para o URL antes que a aplicação lógica retome o funcionamento. A ação webhook *não é subscrita* do ponto final de serviço nestes casos:

* Quando a ação webhook terminar com sucesso
* Se a aplicação lógica for cancelada enquanto se aguarda uma resposta
* Antes que a aplicação lógica se estória

Por exemplo, o conector do Office 365 Outlook [**Enviar**](connectors-create-api-office365-outlook.md) por email é um exemplo de ação webhook que segue este padrão. Pode estender este padrão a qualquer serviço utilizando a ação webhook.

Para obter mais informações, veja estes tópicos:

* [Webhooks e subscrições](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Crie APIs personalizados que suportem um webhook](../logic-apps/logic-apps-create-api-app.md)

Para obter informações sobre encriptação, segurança e autorização para chamadas de entrada para a sua aplicação lógica, como [a Transport Layer Security (TLS),](https://en.wikipedia.org/wiki/Transport_Layer_Security)anteriormente conhecida como Secure Sockets Layer (SSL), ou [Azure Ative Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml), consulte [acesso seguro e dados - Acesso a chamadas de entrada para gatilhos baseados em pedidos](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O URL para um ponto final ou API já implementado que suporta o webhook subscrever e cancelar o padrão de subscrição para [webhook triggers em apps lógicas](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) ou [ações webhook em apps lógicas](../logic-apps/logic-apps-create-api-app.md#webhook-actions) conforme apropriado

* Conhecimento básico sobre [como criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* A aplicação lógica onde pretende esperar por eventos específicos no ponto final do alvo. Para começar com o gatilho HTTP Webhook, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar a ação HTTP Webhook, inicie a sua aplicação lógica com o gatilho que quiser. Este exemplo utiliza o gatilho HTTP como primeiro passo.

## <a name="add-an-http-webhook-trigger"></a>Adicione um gatilho HTTP Webhook

Este gatilho incorporado chama o ponto final de subscrição no serviço alvo e regista um URL de retorno com o serviço alvo. A sua aplicação lógica aguarda então que o serviço alvo envie um `HTTP POST` pedido para o URL de retorno. Quando este evento acontece, o gatilho dispara e transmite quaisquer dados no pedido junto ao fluxo de trabalho.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a sua aplicação lógica em branco no Logic App Designer.

1. Na caixa de pesquisa do designer, introduza `http webhook` como filtro. Na lista **De gatilhos,** selecione o gatilho **HTTP Webhook.**

   ![Selecione o gatilho HTTP Webhook](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Este exemplo renomea o gatilho para `HTTP Webhook trigger` que o passo tenha um nome mais descritivo. Além disso, o exemplo adiciona mais tarde uma ação HTTP Webhook, e ambos os nomes devem ser únicos.

1. Forneça os valores para os parâmetros de [gatilho HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) que pretende utilizar para as chamadas de subscrição e de subscrição.

   Neste exemplo, o gatilho inclui os métodos, URIs e órgãos de mensagens a utilizar na realização das operações de subscrição e de subscrição.

   ![Introduza os parâmetros do gatilho HTTP Webhook](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Assinatura - Método** | Sim | O método a utilizar ao subscrever o ponto final do alvo |
   | **Inscreva-se - URI** | Sim | O URL a utilizar para subscrever o ponto final do alvo |
   | **Subscrever - Corpo** | Não | Qualquer órgão de mensagem a incluir no pedido de subscrição. Este exemplo inclui o URL de retorno que identifica exclusivamente o assinante, que é a sua aplicação lógica, utilizando a expressão para recuperar o `@listCallbackUrl()` URL de callback da sua aplicação lógica. |
   | **Cancelar a subscrição - Método** | Não | O método a utilizar ao cancelar a subscrição do ponto final alvo |
   | **Cancelar a subscrição - URI** | Não | O URL a utilizar para não subscrever a partir do ponto final alvo |
   | **Cancelar a subscrição - Corpo** | Não | Um corpo de mensagem opcional para incluir no pedido de não subscrição <p><p>**Nota:** Esta propriedade não suporta a utilização da `listCallbackUrl()` função. No entanto, o gatilho inclui e envia automaticamente os cabeçalhos, `x-ms-client-tracking-id` e , que o serviço alvo pode usar para identificar `x-ms-workflow-operation-name` exclusivamente o assinante. |
   ||||

1. Para adicionar outras propriedades do gatilho, abra a nova lista **de parâmetros** Adicionar.

   ![Adicione mais propriedades de gatilho](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Por exemplo, se necessitar de autenticação, pode adicionar as propriedades **de Assinatura - Autenticação** e **Inscrição - Autenticação.** Para obter mais informações sobre os tipos de autenticação disponíveis para http Webhook, consulte [adicionar autenticação a chamadas de saída.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

1. Continue a construir o fluxo de trabalho da sua aplicação lógica com ações que funcionam quando o gatilho dispara.

1. Quando terminar, lembre-se de guardar a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

   Guardar a sua aplicação lógica chama o ponto final de subscrição no serviço alvo e regista o URL de retorno. A sua aplicação lógica aguarda então que o serviço alvo envie um `HTTP POST` pedido para o URL de retorno. Quando este evento acontece, o gatilho dispara e transmite quaisquer dados no pedido junto ao fluxo de trabalho. Se esta operação estiver concluída com sucesso, o gatilho não se subscreve a partir do ponto final, e a sua aplicação lógica continua o fluxo de trabalho restante.

## <a name="add-an-http-webhook-action"></a>Adicione uma ação HTTP Webhook

Esta ação incorporada chama o ponto final de subscrição no serviço alvo e regista um URL de retorno com o serviço alvo. A sua aplicação lógica faz então uma pausa e aguarda que o serviço de destino envie um `HTTP POST` pedido para o URL de retorno. Quando este evento acontece, a ação transmite quaisquer dados no pedido junto ao fluxo de trabalho. Se a operação terminar com sucesso, a ação não se subscreve a partir do ponto final, e a sua aplicação lógica continua a executar o fluxo de trabalho restante.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra a sua aplicação lógica no Logic App Designer.

   Este exemplo utiliza o gatilho HTTP Webhook como primeiro passo.

1. Sob o passo em que pretende adicionar a ação HTTP Webhook, selecione **Novo passo**.

   Para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre os degraus. Selecione o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Na caixa de pesquisa do designer, introduza `http webhook` como filtro. Na lista **de Ações,** selecione a ação **HTTP Webhook.**

   ![Selecione HTTP Webhook ação](./media/connectors-native-webhook/select-http-webhook-action.png)

   Este exemplo renomea a ação para "HTTP Webhook action" para que o passo tenha um nome mais descritivo.

1. Forneça os valores dos parâmetros de ação HTTP Webhook, que são semelhantes aos [parâmetros de disparo http Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger), que pretende utilizar para as chamadas de subscrição e de subscrição.

   Neste exemplo, a ação inclui os métodos, URIs e organismos de mensagens a utilizar na realização das operações de subscrição e de subscrição.

   ![Introduza os parâmetros de ação HTTP Webhook](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Assinatura - Método** | Sim | O método a utilizar ao subscrever o ponto final do alvo |
   | **Inscreva-se - URI** | Sim | O URL a utilizar para subscrever o ponto final do alvo |
   | **Subscrever - Corpo** | Não | Qualquer órgão de mensagem a incluir no pedido de subscrição. Este exemplo inclui o URL de retorno que identifica exclusivamente o assinante, que é a sua aplicação lógica, utilizando a expressão para recuperar o `@listCallbackUrl()` URL de callback da sua aplicação lógica. |
   | **Cancelar a subscrição - Método** | Não | O método a utilizar ao cancelar a subscrição do ponto final alvo |
   | **Cancelar a subscrição - URI** | Não | O URL a utilizar para não subscrever a partir do ponto final alvo |
   | **Cancelar a subscrição - Corpo** | Não | Um corpo de mensagem opcional para incluir no pedido de não subscrição <p><p>**Nota:** Esta propriedade não suporta a utilização da `listCallbackUrl()` função. No entanto, a ação inclui e envia automaticamente os cabeçalhos, `x-ms-client-tracking-id` e , que o serviço alvo pode usar para identificar `x-ms-workflow-operation-name` exclusivamente o assinante. |
   ||||

1. Para adicionar outras propriedades de ação, abra a nova lista **de parâmetros Adicionar.**

   ![Adicionar mais propriedades de ação](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Por exemplo, se necessitar de autenticação, pode adicionar as propriedades **de Assinatura - Autenticação** e **Inscrição - Autenticação.** Para obter mais informações sobre os tipos de autenticação disponíveis para http Webhook, consulte [adicionar autenticação a chamadas de saída.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

1. Quando terminar, lembre-se de guardar a sua aplicação lógica. Na barra de ferramentas do designer, **selecione Save**.

   Agora, quando esta ação é executado, a sua aplicação lógica chama o ponto final de subscrição no serviço alvo e regista o URL de retorno de chamadas. A aplicação lógica faz então uma pausa no fluxo de trabalho e aguarda que o serviço-alvo envie um `HTTP POST` pedido para o URL de retorno. Quando este evento acontece, a ação transmite quaisquer dados no pedido junto ao fluxo de trabalho. Se a operação terminar com sucesso, a ação não se subscreve a partir do ponto final, e a sua aplicação lógica continua a executar o fluxo de trabalho restante.

## <a name="trigger-and-action-outputs"></a>Saídas de gatilho e ação

Aqui está mais informações sobre as saídas de um gatilho ou ação http Webhook, que devolve esta informação:

| Nome da propriedade | Tipo | Descrição |
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

## <a name="connector-reference"></a>Referência do conector

Para obter mais informações sobre os parâmetros de desencadeamento e ação, que são semelhantes entre si, consulte [os parâmetros HTTP Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

## <a name="next-steps"></a>Passos seguintes

* [Acesso seguro e dados - Acesso a chamadas de entrada para gatilhos baseados em pedidos](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Conectores para as Logic Apps](../connectors/apis-list.md)
