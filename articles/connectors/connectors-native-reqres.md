---
title: Receber e responder a chamadas HTTPS
description: Manipular solicitações e eventos HTTPS em tempo real usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 10/11/2019
tags: connectors
ms.openlocfilehash: 822a6d1cd812ead8e677a66a9b1e47ebdbcf8aea
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030139"
---
# <a name="receive-and-respond-to-incoming-https-calls-by-using-azure-logic-apps"></a>Receber e responder a chamadas HTTPS de entrada usando aplicativos lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e a ação de resposta ou gatilho de solicitação interna, você pode criar tarefas automatizadas e fluxos de trabalho que recebem e respondem a solicitações HTTPS de entrada. Por exemplo, você pode ter seu aplicativo lógico:

* Receber e responder a uma solicitação HTTPS para dados em um banco de dado local.
* Disparar um fluxo de trabalho quando ocorrer um evento de webhook externo.
* Receber e responder a uma chamada HTTPS de outro aplicativo lógico.

> [!NOTE]
> O gatilho de solicitação dá suporte *apenas* ao protocolo TLS 1,2 para chamadas de entrada. As chamadas de saída continuam a dar suporte a TLS 1,0, 1,1 e 1,2. Se você vir erros de handshake SSL, certifique-se de usar o TLS 1,2. Para chamadas de entrada, aqui estão os conjuntos de codificação com suporte:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você não tiver uma assinatura, poderá se [inscrever para uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [aplicativos lógicos](../logic-apps/logic-apps-overview.md). Se você for novo em aplicativos lógicos, saiba [como criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Adicionar gatilho de solicitação

Esse gatilho interno cria um ponto de extremidade HTTPS manualmente que pode receber *somente* solicitações HTTPS de entrada. Quando esse evento acontece, o gatilho é acionado e executa o aplicativo lógico. Para obter mais informações sobre a definição de JSON subjacente do gatilho e como chamar esse gatilho, consulte o [tipo de gatilho de solicitação](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) e [chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade http em aplicativos lógicos do Azure](../logic-apps/logic-apps-http-endpoint.md).

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Criar uma aplicação lógica em branco.

1. Depois que o designer de aplicativo lógico for aberto, na caixa de pesquisa, digite "solicitação HTTP" como filtro. Na lista de gatilhos, selecione o gatilho **quando uma solicitação HTTP é recebida** , que é a primeira etapa no fluxo de trabalho do aplicativo lógico.

   ![Selecionar gatilho de solicitação](./media/connectors-native-reqres/select-request-trigger.png)

   O gatilho de solicitação mostra essas propriedades:

   ![Gatilho de solicitação](./media/connectors-native-reqres/request-trigger.png)

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **URL HTTP POST** | None | Sim | A URL do ponto de extremidade que é gerada depois que você salva o aplicativo lógico e é usada para chamar seu aplicativo lógico |
   | **Esquema JSON do corpo da solicitação** | `schema` | Não | O esquema JSON que descreve as propriedades e os valores no corpo da solicitação de entrada |
   |||||

1. Na caixa **esquema JSON do corpo da solicitação** , opcionalmente, insira um esquema JSON que descreve o corpo na solicitação de entrada, por exemplo:

   ![Esquema JSON de exemplo](./media/connectors-native-reqres/provide-json-schema.png)

   O designer usa esse esquema para gerar tokens para as propriedades na solicitação. Dessa forma, seu aplicativo lógico pode analisar, consumir e passar dados da solicitação por meio do gatilho para seu fluxo de trabalho.

   Aqui está o esquema de exemplo:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Quando você insere um esquema JSON, o designer mostra um lembrete para incluir o cabeçalho de `Content-Type` em sua solicitação e definir esse valor de cabeçalho como `application/json`. Para obter mais informações, consulte [manipular tipos de conteúdo](../logic-apps/logic-apps-content-type.md).

   ![Lembrete para incluir o cabeçalho "Content-Type"](./media/connectors-native-reqres/include-content-type.png)

   Veja a aparência desse cabeçalho no formato JSON:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Para gerar um esquema JSON baseado na carga esperada (dados), você pode usar uma ferramenta como [JSONSchema.net](https://jsonschema.net)ou pode seguir estas etapas:

   1. No gatilho de solicitação, selecione **usar conteúdo de exemplo para gerar o esquema**.

      ![Gerar esquema com base na carga](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Insira a carga de exemplo e selecione **concluído**.

      ![Gerar esquema com base na carga](./media/connectors-native-reqres/enter-payload.png)

      Aqui está o conteúdo de exemplo:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": { 
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Para especificar propriedades adicionais, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros que você deseja adicionar.

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **Método** | `method` | Não | O método que a solicitação de entrada deve usar para chamar o aplicativo lógico |
   | **Caminho relativo** | `relativePath` | Não | O caminho relativo para o parâmetro que a URL do ponto de extremidade do aplicativo lógico pode aceitar |
   |||||

   Este exemplo adiciona a propriedade do **método** :

   ![Adicionar parâmetro de método](./media/connectors-native-reqres/add-parameters.png)

   A propriedade **Method** aparece no gatilho para que você possa selecionar um método na lista.

   ![Selecionar método](./media/connectors-native-reqres/select-method.png)

1. Agora, adicione outra ação como a próxima etapa no fluxo de trabalho. No gatilho, selecione **próxima etapa** para que você possa encontrar a ação que deseja adicionar.

   Por exemplo, você pode responder à solicitação [adicionando uma ação de resposta](#add-response), que pode ser usada para retornar uma resposta personalizada e é descrita mais adiante neste tópico.

   Seu aplicativo lógico mantém a solicitação de entrada aberta somente por um minuto. Supondo que o fluxo de trabalho do aplicativo lógico inclua uma ação de resposta, se o aplicativo lógico não retornar uma resposta após esse tempo passar, seu aplicativo lógico retornará um `504 GATEWAY TIMEOUT` ao chamador. Caso contrário, se seu aplicativo lógico não incluir uma ação de resposta, seu aplicativo lógico retornará imediatamente uma resposta de `202 ACCEPTED` para o chamador.

1. Quando tiver terminado, salve seu aplicativo lógico. Na barra de ferramentas do designer, selecione **salvar**. 

   Esta etapa gera a URL a ser usada para enviar a solicitação que dispara o aplicativo lógico. Para copiar essa URL, selecione o ícone de cópia ao lado da URL.

   ![URL para usar o disparo do seu aplicativo lógico](./media/connectors-native-reqres/generated-url.png)

1. Para disparar seu aplicativo lógico, envie um HTTP POST para a URL gerada. Por exemplo, você pode usar uma ferramenta como o [postmaster](https://www.getpostman.com/).

### <a name="trigger-outputs"></a>Saídas de gatilho

Veja mais informações sobre as saídas do gatilho de solicitação:

| Nome da propriedade JSON | Data type | Descrição |
|--------------------|-----------|-------------|
| `headers` | Objeto | Um objeto JSON que descreve os cabeçalhos da solicitação |
| `body` | Objeto | Um objeto JSON que descreve o conteúdo do corpo da solicitação |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Adicionar uma ação de resposta

Você pode usar a ação de resposta para responder com uma carga (dados) a uma solicitação HTTPS de entrada, mas somente em um aplicativo lógico que é disparado por uma solicitação HTTPS. Você pode adicionar a ação de resposta em qualquer ponto do fluxo de trabalho. Para obter mais informações sobre a definição de JSON subjacente para esse gatilho, consulte o [tipo de ação de resposta](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

Seu aplicativo lógico mantém a solicitação de entrada aberta somente por um minuto. Supondo que o fluxo de trabalho do aplicativo lógico inclua uma ação de resposta, se o aplicativo lógico não retornar uma resposta após esse tempo passar, seu aplicativo lógico retornará um `504 GATEWAY TIMEOUT` ao chamador. Caso contrário, se seu aplicativo lógico não incluir uma ação de resposta, seu aplicativo lógico retornará imediatamente uma resposta de `202 ACCEPTED` para o chamador.

1. No designer do aplicativo lógico, na etapa em que você deseja adicionar uma ação de resposta, selecione **nova etapa**.

   Por exemplo, usando o gatilho de solicitação anterior:

   ![Adicionar nova etapa](./media/connectors-native-reqres/add-response.png)

   Para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre essas etapas. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Em **escolher uma ação**, na caixa de pesquisa, insira "resposta" como filtro e selecione a ação de **resposta** .

   ![Selecionar a ação de resposta](./media/connectors-native-reqres/select-response-action.png)

   O gatilho de solicitação é recolhido neste exemplo para simplificar.

1. Adicione quaisquer valores necessários para a mensagem de resposta. 

   Em alguns campos, clicar dentro de suas caixas abre a lista de conteúdo dinâmico. Em seguida, você pode selecionar tokens que representam as saídas disponíveis de etapas anteriores no fluxo de trabalho. As propriedades do esquema especificado no exemplo anterior agora aparecem na lista de conteúdo dinâmico.

   Por exemplo, para a caixa **cabeçalhos** , inclua `Content-Type` como o nome da chave e defina o valor da chave como `application/json` conforme mencionado anteriormente neste tópico. Para a caixa **corpo** , você pode selecionar a saída do corpo do gatilho na lista de conteúdo dinâmico.

   ![Detalhes da ação de resposta](./media/connectors-native-reqres/response-details.png)

   Para exibir os cabeçalhos no formato JSON, selecione **alternar para exibição de texto**.

   ![Cabeçalhos – alternar para a exibição de texto](./media/connectors-native-reqres/switch-to-text-view.png)

   Aqui estão mais informações sobre as propriedades que podem ser definidas na ação de resposta. 

   | Nome da propriedade | Nome da propriedade JSON | Obrigatório | Descrição |
   |---------------|--------------------|----------|-------------|
   | **Código de Estado** | `statusCode` | Sim | O código de status a ser retornado na resposta |
   | **Headers** (Cabeçalhos) | `headers` | Não | Um objeto JSON que descreve um ou mais cabeçalhos a serem incluídos na resposta |
   | **Corpo** | `body` | Não | O corpo da resposta |
   |||||

1. Para especificar propriedades adicionais, como um esquema JSON para o corpo da resposta, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros que você deseja adicionar.

1. Quando tiver terminado, salve seu aplicativo lógico. Na barra de ferramentas do designer, selecione **salvar**. 

## <a name="next-steps"></a>Passos seguintes

* [Conectores para aplicativos lógicos](../connectors/apis-list.md)
