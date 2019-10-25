---
title: Aguardar e responder a eventos-aplicativos lógicos do Azure
description: Automatizar fluxos de trabalho que disparam, pausam e retomam com base em eventos em um ponto de extremidade de serviço usando aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 10/10/2019
tags: connectors
ms.openlocfilehash: ee86f53795b1b3e7bd61480a490d4e18c844d4c2
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804259"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Criar e executar fluxos de trabalho baseados em eventos automatizados usando WebHooks HTTP em aplicativos lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o conector de webhook http interno, você pode automatizar fluxos de trabalho que esperam e executam com base em eventos específicos que acontecem em um ponto de extremidade http ou HTTPS criando aplicativos lógicos. Por exemplo, você pode criar um aplicativo lógico que monitora um ponto de extremidade de serviço aguardando um evento específico antes de disparar o fluxo de trabalho e executar as ações especificadas, em vez de verificar regularmente ou *sondar* o ponto de extremidade.

Aqui estão alguns exemplos de fluxos de trabalho baseados em eventos:

* Aguarde até que um item chegue a partir de um [Hub de eventos do Azure](https://github.com/logicappsio/EventHubAPI) antes de disparar uma execução de aplicativo lógico.
* Aguarde uma aprovação antes de continuar um fluxo de trabalho.

## <a name="how-do-webhooks-work"></a>Como os WebHooks funcionam?

Um gatilho de webhook HTTP é baseado em evento, o que não depende da verificação ou sondagem regular de novos itens. Quando você salva um aplicativo lógico que começa com um gatilho de webhook ou quando você altera seu aplicativo lógico de desabilitado para habilitado, o gatilho de webhook *assina* um serviço ou ponto de extremidade específico registrando uma *URL de retorno de chamada* com esse serviço ou ponto de extremidade. Em seguida, o gatilho aguarda esse serviço ou ponto de extremidade para chamar a URL, que começa a executar o aplicativo lógico. Semelhante ao [gatilho de solicitação](connectors-native-reqres.md), o aplicativo lógico é acionado imediatamente quando o evento especificado ocorre. O gatilho *cancela a assinatura* do serviço ou do ponto de extremidade se você remover o gatilho e salvar seu aplicativo lógico, ou quando você alterar seu aplicativo lógico de habilitado para desabilitado.

Uma ação de webhook HTTP também é baseada em evento e *assina* um serviço ou ponto de extremidade específico registrando uma *URL de retorno de chamada* com esse serviço ou ponto de extremidade. A ação de webhook pausa o fluxo de trabalho do aplicativo lógico e aguarda até que o serviço ou ponto de extremidade chame a URL antes que o aplicativo lógico reinicie a execução. O aplicativo lógico de ação *cancela a assinatura* do serviço ou ponto de extremidade nestes casos:

* Quando a ação de webhook for concluída com êxito
* Se a execução do aplicativo lógico for cancelada enquanto aguarda uma resposta
* Antes de o aplicativo lógico atingir o tempo limite

Por exemplo, a ação [**Enviar email de aprovação**](connectors-create-api-office365-outlook.md) do conector do Outlook do Office 365 é um exemplo de ação de webhook que segue esse padrão. Você pode estender esse padrão para qualquer serviço usando a ação de webhook.

> [!NOTE]
> Os aplicativos lógicos imponham o protocolo TLS 1,2 ao receber a chamada de volta para o gatilho ou ação de webhook HTTP. Se você vir erros de handshake SSL, certifique-se de usar o TLS 1,2.

Para obter mais informações, consulte estes tópicos:

* [Parâmetros de gatilho de webhook HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [WebHooks e assinaturas](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Criar APIs personalizadas que dão suporte a um webhook](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* A URL de um ponto de extremidade ou API já implantado que dá suporte ao padrão de assinatura e cancelamento de assinante para [gatilhos de webhook em aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) ou [ações de webhook em aplicativos lógicos](../logic-apps/logic-apps-create-api-app.md#webhook-actions) , conforme apropriado

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

* O aplicativo lógico em que você deseja aguardar eventos específicos no ponto de extremidade de destino. Para começar com o gatilho de webhook HTTP, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar a ação de webhook HTTP, inicie seu aplicativo lógico com qualquer gatilho desejado. Este exemplo usa o gatilho HTTP como a primeira etapa.

## <a name="add-an-http-webhook-trigger"></a>Adicionar um gatilho de webhook HTTP

Esse gatilho interno registra uma URL de retorno de chamada com o serviço especificado e aguarda que esse serviço envie uma solicitação HTTP POST para essa URL. Quando esse evento acontece, o gatilho é acionado e executa imediatamente o aplicativo lógico.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico em branco no designer de aplicativo lógico.

1. No designer, na caixa de pesquisa, digite "http webhook" como filtro. Na lista de **gatilhos** , selecione o gatilho de **webhook http** .

   ![Selecionar gatilho de webhook HTTP](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Este exemplo renomeia o gatilho para "gatilho de webhook HTTP" para que a etapa tenha um nome mais descritivo. Além disso, o exemplo posteriormente adiciona uma ação de webhook HTTP e ambos os nomes devem ser exclusivos.

1. Forneça os valores para os [parâmetros de gatilho de webhook http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) que você deseja usar para as chamadas assinar e cancelar assinatura, por exemplo:

   ![Inserir parâmetros de gatilho de webhook HTTP](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. Para adicionar outros parâmetros disponíveis, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros desejados.

   Para obter mais informações sobre os tipos de autenticação disponíveis para webhook HTTP, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Continue criando o fluxo de trabalho do aplicativo lógico com ações que são executadas quando o gatilho é acionado.

1. Quando tiver terminado, lembre-se de salvar seu aplicativo lógico. Na barra de ferramentas do designer, selecione **salvar**.

   Salvar o aplicativo lógico chama o ponto de extremidade de assinatura e registra a URL de retorno de chamada para disparar esse aplicativo lógico.

1. Agora, sempre que o serviço de destino envia uma solicitação `HTTP POST` para a URL de retorno de chamada, o aplicativo lógico é acionado e inclui todos os dados que são transmitidos pela solicitação.

## <a name="add-an-http-webhook-action"></a>Adicionar uma ação de webhook HTTP

Essa ação interna registra uma URL de retorno de chamada com o serviço especificado, pausa o fluxo de trabalho do aplicativo lógico e aguarda que esse serviço envie uma solicitação HTTP POST para essa URL. Quando esse evento acontece, a ação retoma a execução do aplicativo lógico.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico no designer de aplicativo lógico.

   Este exemplo usa o gatilho de webhook HTTP como a primeira etapa.

1. Na etapa em que você deseja adicionar a ação de webhook HTTP, selecione **nova etapa**.

   Para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. No designer, na caixa de pesquisa, digite "http webhook" como filtro. Na lista **ações** , selecione a ação **webhook http** .

   ![Selecionar ação de webhook HTTP](./media/connectors-native-webhook/select-http-webhook-action.png)

   Este exemplo renomeia a ação como "ação de webhook HTTP" para que a etapa tenha um nome mais descritivo.

1. Forneça os valores para os parâmetros de ação de webhook HTTP, que são semelhantes aos [parâmetros de gatilho de webhook http](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger) que você deseja usar para as chamadas inscrever e cancelar assinatura, por exemplo:

   ![Inserir parâmetros de ação de webhook HTTP](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   Durante o tempo de execução, o aplicativo lógico chama o ponto de extremidade de assinatura ao executar esta ação. Em seguida, seu aplicativo lógico pausa o fluxo de trabalho e aguarda o serviço de destino enviar uma solicitação `HTTP POST` para a URL de retorno de chamada. Se a ação for concluída com êxito, a ação cancelará a assinatura do ponto de extremidade e seu aplicativo lógico retomará a execução do fluxo de trabalho.

1. Para adicionar outros parâmetros disponíveis, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros desejados.

   Para obter mais informações sobre os tipos de autenticação disponíveis para webhook HTTP, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Quando tiver terminado, lembre-se de salvar seu aplicativo lógico. Na barra de ferramentas do designer, selecione **salvar**.

## <a name="connector-reference"></a>Referência do conector

Para obter mais informações sobre parâmetros de ação e gatilho, que são semelhantes entre si, consulte [parâmetros de webhook http](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger).

### <a name="output-details"></a>Detalhes da saída

Aqui estão mais informações sobre as saídas de um gatilho ou ação de webhook HTTP, que retorna essas informações:

| Nome da propriedade | Tipo | Descrição |
|---------------|------|-------------|
| conector | objeto | Os cabeçalhos da solicitação |
| conteúdo | objeto | Objeto JSON | O objeto com o conteúdo do corpo da solicitação |
| código de status | int | O código de status da solicitação |
|||

| Código de estado | Descrição |
|-------------|-------------|
| 200 | OK |
| 202 | Aceitar |
| 400 | Solicitação inadequada |
| 401 | Não autorizado |
| 403 | Proibido |
| 404 | Não encontrado |
| 500 | Erro interno do servidor. Ocorreu um erro desconhecido. |
|||

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
