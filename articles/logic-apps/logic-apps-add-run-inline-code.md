---
title: Adicionar e executar fragmentos de código - Azure Logic Apps
description: Adicionar e executar fragmentos de código com o código embutido no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: derek1ee, LADocs
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: 0bfa98396ee3afb80b486a5a17959664dfbe603c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602111"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Adicionar e executar fragmentos de código com o código embutido no Azure Logic Apps

Quando quiser executar um trecho de código na sua aplicação lógica, pode adicionar o incorporado **código embutido** ação como um passo num fluxo de trabalho da sua aplicação lógica. Esta ação funciona melhor quando deseja executar o código que se adequa a este cenário:

* É executado em JavaScript. Mais idiomas estará disponível em breve.
* Estejam concluídas em cinco segundos ou menos.
* Processa dados de até 50 MB de tamanho.
* Utiliza a versão 8.11.1 de node. js. Para obter mais informações, consulte [objetos incorporados padrão](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects). 

  > [!NOTE]
  > A função de require() não é suportada pela **código embutido** ação para a execução de JavaScript.

Esta ação executa o trecho de código e devolve o resultado desse trecho de código como um token com o nome **resultado**, que pode ser usado em ações subsequentes na sua aplicação lógica. Para outros cenários em que pretende criar uma função para o seu código, tente [criar e chamar uma função do Azure](../logic-apps/logic-apps-azure-functions.md) na sua aplicação lógica.

Neste artigo, os acionadores da aplicação exemplo lógica quando um novo e-mail chega numa conta de Outlook do Office 365. O trecho de código extrai e devolve os endereços de e-mail que aparecem no corpo do e-mail.

![Descrição geral de exemplo](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* A aplicação de lógica onde pretende adicionar o seu trecho de código, incluindo um acionador. Se não tiver uma aplicação lógica, consulte [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   A aplicação de lógica de exemplo neste tópico utiliza este acionador do Outlook do Office 365: **Quando chega um novo e-mail**

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associado à sua aplicação lógica

## <a name="add-inline-code"></a>Adicionar código embutido

1. Se ainda não o fez, no [portal do Azure](https://portal.azure.com), abra a aplicação lógica no Estruturador da aplicação lógica.

1. No designer, adicione a **código embutido** ação na localização que pretende no fluxo de trabalho da sua aplicação lógica.

   * Para adicionar a ação no final do seu fluxo de trabalho, escolha **novo passo**.

   * Para adicionar a ação entre os passos existentes, mova o ponteiro do mouse sobre a seta que se liga esses passos. Selecione o sinal de adição ( **+** ) e selecione **adicionar uma ação**.

   Este exemplo adiciona a **código embutido** ação sob o acionador do Outlook do Office 365.

   ![Adicionar novo passo](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. Sob **escolher uma ação**, na caixa de pesquisa, introduza "código embutido" como o filtro. Na lista de ações, selecione a ação: **Executar o código de JavaScript**

   ![Selecione "Executar o código JavaScript"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   A ação é apresentada no designer e contém algum código de exemplo do padrão, incluindo uma instrução return.

   ![Ação de código embutido com o código de exemplo do padrão](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Na **código** caixa, eliminar o código de exemplo e introduza o código que pretende executar. Escreva código que colocaria num método, mas sem definir a assinatura do método. 

   Quando escreve uma palavra-chave reconhecida, é apresentada a lista de preenchimento automático para que possa selecionar de palavras-chave disponíveis, por exemplo:

   ![Lista de preenchimento automático da palavra-chave](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Este fragmento de código de exemplo cria primeiro uma variável que armazena um *expressão regular*, que especifica um padrão para corresponder ao texto de entrada. O código, em seguida, cria uma variável que armazena os dados de corpo de e-mail do acionador.

   ![Criar variáveis](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Para facilitar a referência dos resultados do acionador e ações anteriores, é apresentada a lista de conteúdo dinâmica, enquanto o cursor está dentro do **código** caixa. Neste exemplo, a lista mostra os resultados disponíveis do acionador, incluindo o **corpo** token, que agora, pode selecionar.

   Depois de selecionar o **corpo** token, a ação de código inline resolve o token a um `workflowContext` objeto que faz referência a mensagem de e-mail `Body` valor da propriedade:

   ![Selecione o resultado](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   Na **código** caixa, pode utilizar o seu trecho de só de leitura `workflowContext` de objeto como entrada. Este objeto tem subproperties conceder o acesso de código para os resultados a partir do acionador e ações anteriores no fluxo de trabalho.
   Para obter mais informações, consulte esta seção mais adiante neste tópico: [Resultados de Acionador e ação em seu código de referência](#workflowcontext).

   > [!NOTE]
   >
   > Se seu trecho de código faz referência a nomes de ação que usam o operador de ponto (.), tem de adicionar esses nomes de ação para o [ **ações** parâmetro](#add-parameters). Essas referências também tem de incluir os nomes de ação com Parênteses Retos ([]) e aspas, por exemplo:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   A ação de código inline não requer uma `return` instrução, mas os resultados de uma `return` instrução estão disponíveis para referência em ações subsequentes através a **resultado** token. 
   Por exemplo, o trecho de código devolve o resultado ao chamar o `match()` função, que localiza correspondências no corpo do e-mail em relação à expressão regular. O **Compose** ação utiliza o **resultado** token para referência os resultados a partir do inline ação de código e cria um único resultado.

   ![Aplicação lógica concluída](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Quando tiver terminado, guarde a aplicação lógica.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Resultados de Acionador e ação de referência no seu código

O `workflowContext` objeto tem esta estrutura, que inclui o `actions`, `trigger`, e `workflow` subproperties:

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Esta tabela contém mais informações sobre estes subproperties:

| Propriedade | Tipo | Descrição |
|----------|------|-------|
| `actions` | Coleção de objetos | Objetos de resultado de ações que executam antes de seu trecho de código é executado. Cada objeto tem um *chave-valor* par em que a chave é o nome de uma ação, e o valor é equivalente a chamar os [actions() função](../logic-apps/workflow-definition-language-functions-reference.md#actions) com `@actions('<action-name>')`. Nome da ação utiliza o mesmo nome de ação que é utilizado na definição de fluxo de trabalho subjacente, que substitui os espaços ("") no nome da ação com carateres de sublinhado (_). Esse objeto fornece acesso aos valores de propriedade de ação da instância do fluxo de trabalho atual, execute. |
| `trigger` | Object | Objeto de resultado do acionador e equivalente a chamar o [trigger() função](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Esse objeto fornece acesso para valores de propriedades do acionador da instância do fluxo de trabalho atual, execute. |
| `workflow` | Object | O objeto de fluxo de trabalho e o equivalente a chamar o [Workflow () função](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Esse objeto fornece acesso aos valores de propriedade de fluxo de trabalho, tais como o nome de fluxo de trabalho, ID de execução e assim por diante, da instância do fluxo de trabalho atual, execute. |
|||

No exemplo neste tópico, o `workflowContext` objeto tem essas propriedades que pode aceder a seu código:

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Adicionar parâmetros

Em alguns casos, poderá ter de exigir explicitamente que o **código embutido** ação inclui os resultados do acionador ou a ações específicas que faz referência a seu código como dependências ao adicionar o **acionador** ou **Ações** parâmetros. Esta opção é útil para cenários em que os resultados referenciados não forem encontrados no tempo de execução.

> [!TIP]
> Se planejar reutilizar seu código, adicione referências a propriedades utilizando o **código** caixa para que seu código inclui as referências de token resolvidas, em vez de adicionar o acionador ou ações como dependências explícitas.

Por exemplo, suponha que tenha o código que faz referência a **SelectedOption** resultam da **enviar e-mail de aprovação** ação para o conector do Outlook do Office 365. No tempo de criação, o motor do Logic Apps analisa o código para determinar se referenciado qualquer acionador ou ação resulta e inclui esses resultados automaticamente. Tempo de execução, deve receber um erro que o resultado de Acionador ou ação referenciado não está disponível no especificado `workflowContext` de objeto, pode adicionar esse acionador ou ação como uma dependência explícita. Neste exemplo, adicione a **ações** parâmetro e especificar que o **código embutido** ação incluir explicitamente o resultado do **enviar e-mail de aprovação** ação.

Para adicionar estes parâmetros, abra a **adicione o novo parâmetro** lista e selecione os parâmetros desejados:

   ![Adicionar parâmetros](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parâmetro | Descrição |
   |-----------|-------------|
   | **Ações** | Inclua resultados de ações anteriores. Ver [incluir resultados de ação](#action-results). |
   | **Acionador** | Inclua resultados a partir do acionador. Ver [resultados de Acionador de inclusão](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Incluir resultados de Acionador

Se selecionou **Acionadores**, será avisado se incluir o acionador de resultados.

* Partir do **acionador** lista, selecione **Sim**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Incluir resultados de ação

Se selecionou **ações**, lhe for pedido para as ações que pretende adicionar. No entanto, antes de começar a adicionar ações, terá da versão do nome da ação que é apresentado na definição de fluxo de trabalho da aplicação lógica subjacente.

* Esta capacidade não suporta variáveis, loops e índices de iteração.

* Nomes de definição de fluxo de trabalho da sua aplicação lógica utilizam um caráter de sublinhado (_), não é um espaço.

* Para nomes de ação que utilizam o operador de ponto (.), incluem os operadores, por exemplo:

  `My.Action.Name`

1. Na barra de ferramentas da estruturador, escolha **exibição de código**e pesquisar dentro da `actions` atributo para o nome da ação.

   Por exemplo, `Send_approval_email_` é o nome JSON para o **enviar e-mail de aprovação** ação.

   ![Localizar o nome de ação em JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Para regressar à exibição de design, na barra de ferramentas de exibição de código, escolha **Designer**.

1. Adicionar a primeira ação, o **ações Item - 1** , introduza o nome JSON da ação.

   ![Introduza a primeira ação](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Para adicionar outra ação, escolha **adicionar novo item**.

## <a name="reference"></a>Referência

Para obter mais informações sobre o **executar o código de JavaScript** estrutura da ação e a sintaxe na definição da sua aplicação lógica subjacente fluxo de trabalho usando a linguagem de definição de fluxo de trabalho, consulte esta ação [secção de referência ](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [conectores para o Azure Logic Apps](../connectors/apis-list.md)
