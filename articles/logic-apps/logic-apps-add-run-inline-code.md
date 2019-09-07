---
title: Adicionar e executar trechos de código-aplicativos lógicos do Azure
description: Adicionar e executar trechos de código com código embutido em aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: derek1ee, LADocs
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: 3b51215e0cf48df2d3cd9df85a3d4c5641a17215
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390807"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Adicionar e executar trechos de código usando código embutido em aplicativos lógicos do Azure

Quando você quiser executar um trecho de código dentro de seu aplicativo lógico, poderá adicionar a ação interna de **código embutido** como uma etapa no fluxo de trabalho do seu aplicativo lógico. Essa ação funciona melhor quando você deseja executar o código que se adapta a este cenário:

* É executado em JavaScript. Mais idiomas em breve.
* Termina a execução em cinco segundos ou menos.
* Manipula dados de até 50 MB de tamanho.
* Não requer trabalho com as [ações de **variáveis** ](../logic-apps/logic-apps-create-variables-store-values.md), que ainda não têm suporte.
* Usa o Node. js versão 8.11.1. Para obter mais informações, consulte [objetos internos padrão](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects). 

  > [!NOTE]
  > A `require()` função não é suportada pela ação de **código embutido** para executar o JavaScript.

Essa ação executa o trecho de código e retorna a saída desse trecho como um token chamado **Result**, que pode ser usado em ações subsequentes em seu aplicativo lógico. Para outros cenários em que você deseja criar uma função para seu código, tente [criar e chamar uma função do Azure](../logic-apps/logic-apps-azure-functions.md) em seu aplicativo lógico.

Neste artigo, o aplicativo lógico de exemplo dispara quando um novo email chega em uma conta do Outlook do Office 365. O trecho de código extrai e retorna os endereços de email que aparecem no corpo do email.

![Visão geral de exemplo](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O aplicativo lógico no qual você deseja adicionar seu trecho de código, incluindo um gatilho. Se você não tiver um aplicativo lógico, consulte [início rápido: Crie seu primeiro aplicativo](../logic-apps/quickstart-create-first-logic-app-workflow.md)lógico.

   O exemplo de aplicativo lógico neste tópico usa este gatilho do Outlook do Office 365: **Quando um novo email chegar**

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) vinculada ao seu aplicativo lógico

  > [!NOTE]
  > Certifique-se de usar uma conta de integração apropriada para seu caso ou cenário de uso. Por exemplo, as contas de integração de [camada gratuita](../logic-apps/logic-apps-pricing.md#integration-accounts) são destinadas apenas a cenários e cargas de trabalho exploratórios, não a cenários de produção, são limitadas de uso e taxa de transferência e não têm suporte de um SLA (contrato de nível de serviço). Outras camadas incorrem em custos, mas incluem suporte a SLA, oferecem mais taxa de transferência e têm limites maiores. Saiba mais sobre [camadas](../logic-apps/logic-apps-pricing.md#integration-accounts), [preços](https://azure.microsoft.com/pricing/details/logic-apps/)e [limites](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)da conta de integração.

## <a name="add-inline-code"></a>Adicionar código embutido

1. Se você ainda não fez isso, na [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer de aplicativo lógico.

1. No designer, adicione a ação de **código embutido** no local desejado no fluxo de trabalho do aplicativo lógico.

   * Para adicionar a ação no final do fluxo de trabalho, escolha **nova etapa**.

   * Para adicionar a ação entre as etapas existentes, mova o ponteiro do mouse sobre a seta que conecta essas etapas. Escolha o sinal de adição **+** () e selecione **Adicionar uma ação**.

   Este exemplo adiciona a ação de **código embutido** no gatilho do Outlook do Office 365.

   ![Adicionar nova etapa](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. Em **escolher uma ação**, na caixa de pesquisa, digite "código embutido" como filtro. Na lista ações, selecione esta ação: **Executar código JavaScript**

   ![Selecione "executar código JavaScript"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   A ação aparece no designer e contém algum código de exemplo padrão, incluindo uma instrução de retorno.

   ![Ação de código embutido com código de exemplo padrão](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Na caixa **código** , exclua o código de exemplo e insira o código que você deseja executar. Escreva o código que você colocaria dentro de um método, mas sem definir a assinatura do método. 

   Quando você digita uma palavra-chave reconhecida, a lista de preenchimento automático é exibida para que você possa selecionar uma das palavras-chave disponíveis, por exemplo:

   ![Lista de preenchimento automático de palavras-chave](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Este trecho de código de exemplo cria primeiro uma variável que armazena uma *expressão regular*, que especifica um padrão para corresponder ao texto de entrada. Em seguida, o código cria uma variável que armazena os dados do corpo do email do gatilho.

   ![Criar variáveis](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Para fazer com que os resultados do gatilho e das ações anteriores sejam mais fáceis de fazer referência, a lista de conteúdo dinâmico aparecerá enquanto o cursor estiver dentro da caixa de **código** . Para este exemplo, a lista mostra os resultados disponíveis do gatilho, incluindo o token de **corpo** , que você pode selecionar agora.

   Depois de selecionar o token de **corpo** , a ação de código embutido resolve o token para `workflowContext` um objeto que faz referência ao `Body` valor da Propriedade do email:

   ![Selecionar resultado](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   Na caixa **código** , seu trecho pode usar o objeto somente `workflowContext` leitura como entrada. Esse objeto tem subpropriedades que dão ao seu código acesso aos resultados do gatilho e ações anteriores em seu fluxo de trabalho.
   Para obter mais informações, consulte esta seção mais adiante neste tópico: [Gatilho de referência e resultados de ação em seu código](#workflowcontext).

   > [!NOTE]
   >
   > Se o trecho de código fizer referência a nomes de ação que usam o operador ponto (.), você deverá adicionar esses nomes de ação ao [parâmetro **Actions** ](#add-parameters). Essas referências também devem incluir os nomes de ação entre colchetes ([]) e aspas, por exemplo:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   A ação de código embutido não `return` requer uma instrução, mas os resultados `return` de uma instrução estão disponíveis para referência em ações posteriores por meio do token de **resultado** . 
   Por exemplo, o trecho de código retorna o resultado chamando a `match()` função, que localiza correspondências no corpo do email em relação à expressão regular. A ação **compor** usa o token de **resultado** para fazer referência aos resultados da ação de código embutida e cria um único resultado.

   ![Aplicação lógica concluída](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Quando tiver terminado, salve seu aplicativo lógico.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Gatilho de referência e resultados de ação em seu código

O `workflowContext` objeto tem essa estrutura, que inclui as `actions`subpropriedades `workflow` , `trigger`e:

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

Esta tabela contém mais informações sobre essas subpropriedades:

| Propriedade | Tipo | Descrição |
|----------|------|-------|
| `actions` | Coleção de objetos | Objetos de resultado de ações executadas antes do trecho de código ser executado. Cada objeto tem um par *chave-valor* em que a chave é o nome de uma ação e o valor é equivalente a chamar a [função Actions ()](../logic-apps/workflow-definition-language-functions-reference.md#actions) com `@actions('<action-name>')`. O nome da ação usa o mesmo nome de ação usado na definição de fluxo de trabalho subjacente, que substitui os espaços ("") no nome da ação por sublinhados (_). Este objeto fornece acesso aos valores de propriedade de ação da instância de fluxo de trabalho atual executada. |
| `trigger` | Objeto | Objeto de resultado do gatilho e equivalente a chamar a [função Trigger ()](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Este objeto fornece acesso para disparar valores de propriedade da execução da instância de fluxo de trabalho atual. |
| `workflow` | Objeto | O objeto de fluxo de trabalho e equivalente a chamar a [função de fluxo de trabalho ()](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Esse objeto fornece acesso aos valores de propriedade de fluxo de trabalho, como o nome do fluxo de trabalho, a ID de execução e assim por diante, da instância de fluxo de trabalho atual executada. |
|||

No exemplo deste tópico, o `workflowContext` objeto tem essas propriedades que seu código pode acessar:

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

Em alguns casos, talvez seja necessário exigir explicitamente que a ação de **código embutido** inclua resultados do gatilho ou de ações específicas às quais seu código faz referência como dependências adicionando os parâmetros de **ação** ou **gatilho** . Essa opção é útil para cenários em que os resultados referenciados não são encontrados em tempo de execução.

> [!TIP]
> Se você planeja reutilizar seu código, adicione referências a propriedades usando a caixa de **código** para que seu código inclua as referências de token resolvidas, em vez de adicionar o gatilho ou as ações como dependências explícitas.

Por exemplo, suponha que você tenha um código que referencie o resultado de **SelectedOption** da ação **Enviar email de aprovação** para o conector do Outlook do Office 365. No momento da criação, o mecanismo de aplicativos lógicos analisa seu código para determinar se você referenciou algum resultado de gatilho ou ação e inclui esses resultados automaticamente. Em tempo de execução, se você receber um erro informando que o gatilho ou o resultado da ação `workflowContext` referenciado não está disponível no objeto especificado, você poderá adicionar esse gatilho ou ação como uma dependência explícita. Neste exemplo, você adiciona o parâmetro **Actions** e especifica que a ação de **código embutido** inclui explicitamente o resultado da ação **Enviar email de aprovação** .

Para adicionar esses parâmetros, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros desejados:

   ![Adicionar parâmetros](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parâmetro | Descrição |
   |-----------|-------------|
   | **Ações** | Incluir resultados de ações anteriores. Consulte [incluir resultados da ação](#action-results). |
   | **Acionador** | Inclua os resultados do gatilho. Consulte [incluir resultados do gatilho](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Incluir resultados do gatilho

Se você selecionar **gatilhos**, será solicitado se deseja incluir os resultados do gatilho.

* Na lista de **gatilhos** , selecione **Sim**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Incluir resultados da ação

Se você selecionar **ações**, as ações que você deseja adicionar serão solicitadas. No entanto, antes de começar a adicionar ações, você precisa da versão do nome da ação que aparece na definição de fluxo de trabalho subjacente do aplicativo lógico.

* Esse recurso não dá suporte a variáveis, loops e índices de iteração.

* Os nomes na definição de fluxo de trabalho do aplicativo lógico usam um sublinhado (_), não um espaço.

* Para nomes de ação que usam o operador ponto (.), inclua esses operadores, por exemplo:

  `My.Action.Name`

1. Na barra de ferramentas do designer, escolha **exibição de código**e pesquise `actions` dentro do atributo para o nome da ação.

   Por exemplo, `Send_approval_email_` é o nome JSON para a ação **Enviar email de aprovação** .

   ![Localizar o nome da ação em JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Para retornar ao modo de exibição de designer, na barra de ferramentas de exibição de código, escolha **Designer**.

1. Para adicionar a primeira ação, na caixa **item de ações-1** , insira o nome JSON da ação.

   ![Inserir primeira ação](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Para adicionar outra ação, escolha **Adicionar novo item**.

## <a name="reference"></a>Referência

Para obter mais informações sobre a estrutura e a sintaxe da ação de **código JavaScript** na definição de fluxo de trabalho subjacente do aplicativo lógico usando a linguagem de definição de fluxo de trabalho, consulte a [seção de referência](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)da ação.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md)
