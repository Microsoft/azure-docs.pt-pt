---
title: Criar web APIs e as APIs REST para o Azure Logic Apps | Documentos da Microsoft
description: Criar web APIs e as APIs REST para chamar as APIs, serviços ou sistemas de integrações de sistema no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.date: 05/26/2017
ms.openlocfilehash: 620ede672d71338abeff5198fd5f94e92dc193d0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895860"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Criar APIs personalizadas, pode chamar a partir do Azure Logic Apps

Embora o Azure Logic Apps oferece [mais de 100 conectores incorporados](../connectors/apis-list.md) que pode utilizar em fluxos de trabalho de aplicação de lógica, pode querer chamar APIs, sistemas e serviços que não estão disponíveis como conectores. Pode criar suas próprias APIs que fornecem ações e acionadores para utilizar no logic apps. Seguem-se outros motivos por que pode desejar criar suas próprias APIs que pode chamar de fluxos de trabalho de aplicação de lógica:

* Expanda o seu atual sistema fluxos de integração de dados e de integração.
* Ajude os clientes a utilizar o seu serviço para gerir as tarefas pessoais ou profissionais.
* Expanda o alcance, a capacidade de deteção e a utilização para o seu serviço.

Basicamente, os conectores são web APIs que utilizar REST para interfaces de conectáveis [formato de metadados do Swagger](https://swagger.io/specification/) para documentação e JSON como seu formato de troca de dados. Como os conectores são APIs REST que comunicam através de pontos finais HTTP, pode utilizar qualquer linguagem, como .NET, Java ou node. js, para a criação de conectores. Também pode alojar as suas APIs num [App Service do Azure](../app-service/overview.md), uma plataforma-como-um-serviço (PaaS que fornece uma das formas melhores, mais fácil e mais escaláveis para alojar a API da oferta). 

Para obter APIs personalizadas trabalhar com o logic apps, pode fornecer a sua API [ *ações* ](./logic-apps-overview.md#logic-app-concepts) que realizam tarefas específicas em fluxos de trabalho de aplicação de lógica. A API pode também agir como um [ *acionador* ](./logic-apps-overview.md#logic-app-concepts) que inicia um fluxo de trabalho de aplicação lógica quando novos dados ou um evento cumpre uma condição especificada. Este tópico descreve os padrões comuns que pode seguir para a criação de ações e acionadores na sua API, com base no comportamento que pretende que a API para fornecer.

Pode alojar as suas APIs num [App Service do Azure](../app-service/overview.md), uma plataforma-como-um-serviço (PaaS que fornece fácil, altamente dimensionável e que aloja a API da oferta).

> [!TIP] 
> Embora possa implementar as suas APIs como aplicações web, considere implementar as suas APIs como aplicações de API, que podem tornar seu trabalho mais fácil quando cria, host e consumir APIs na cloud e no local. Não precisa alterar qualquer código em suas APIs – basta implementar o seu código numa aplicação API. Por exemplo, saiba como criar aplicações de API criadas com esses idiomas: 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> Para exemplos de aplicações de API criados para o logic apps, visite o [repositório do GitHub do Azure Logic Apps](https://github.com/logicappsio) ou [blog](https://aka.ms/logicappsblog).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Como a APIs personalizadas diferem dos conectores personalizados?

APIs personalizadas e [conectores personalizados](../logic-apps/custom-connector-overview.md) são APIs que utilizar REST para interfaces conectáveis, web [formato de metadados do Swagger](https://swagger.io/specification/) para documentação e JSON como seu formato de troca de dados. E como essas APIs e conectores são APIs REST que comunicam através de pontos finais HTTP, pode utilizar qualquer linguagem, como .NET, Java ou node. js, para a criação de conectores e APIs personalizadas.

APIs personalizadas permitem chamar as APIs que não são conectores e fornecer pontos de extremidade que pode chamar com HTTP + Swagger, gestão de API do Azure ou serviços de aplicações. Conectores personalizados funcionam como APIs personalizadas, mas também tem estes atributos:

* Registado como recursos de conector do Logic Apps no Azure.
* Aparecem com ícones juntamente com conectores geridos pela Microsoft no Designer de aplicações lógicas.
* Disponível apenas para dos conectores autores e os utilizadores da aplicação lógica que têm o mesmo inquilino do Azure Active Directory e a subscrição do Azure na região onde as aplicações lógicas estão implementadas.

Também pode indicar registados conectores para certificação da Microsoft. Este processo verifica se o conectores registados cumprem os critérios para utilização pública e disponibiliza esses conectores para os utilizadores no Microsoft Flow e Microsoft PowerApps.

Para obter mais informações sobre conectores personalizados, consulte 

* [Descrição geral dos conectores personalizados](../logic-apps/custom-connector-overview.md)
* [Criar conectores personalizados a partir de Web APIs](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Registar conectores personalizados no Azure Logic Apps](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Ferramentas úteis

Uma API personalizada funciona melhor com o logic apps quando a API também tem um [documento Swagger](https://swagger.io/specification/) que descreve a API de operações e os parâmetros.
Muitas bibliotecas, como [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle), pode gerar automaticamente o ficheiro Swagger para. Anotar o ficheiro de Swagger para os nomes a apresentar, tipos de propriedade e assim por diante, também pode utilizar [TRex](https://github.com/nihaue/TRex) para que o ficheiro Swagger funciona bem com o logic apps.

<a name="actions"></a>

## <a name="action-patterns"></a>Padrões de ação

No logic apps executar tarefas, a API personalizada deve fornecer [ *ações*](./logic-apps-overview.md#logic-app-concepts). Cada operação na sua API é mapeado para uma ação. Uma ação básica é um controlador que aceite pedidos de HTTP e devolve as respostas HTTP. Por exemplo, uma aplicação lógica envia um pedido HTTP para a aplicação web ou a aplicação API. A aplicação, em seguida, devolve uma resposta HTTP, juntamente com o conteúdo que a aplicação lógica pode processar.

Para uma ação padrão, pode escrever um método de pedido HTTP na sua API e descrever esse método num arquivo de Swagger. Em seguida, pode chamar a API diretamente com um [ação de HTTP](../connectors/connectors-native-http.md) ou uma [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) ação. Por predefinição, tem de voltar respostas dentro de [limite de tempo limite do pedido](./logic-apps-limits-and-config.md). 

![Padrão de ação padrão](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Para tornar uma aplicação de lógica aguardar enquanto as tarefas de execução mais tempo de conclusão da sua API, a sua API pode seguir a [padrão de consulta assíncrona](#async-pattern) ou o [padrão assíncrono webhook](#webhook-actions) descritas neste tópico. Para uma analogia que ajuda a visualizar diferentes comportamentos desses padrões, imagine o processo para pedir um bolo personalizado a partir de um bakery. O padrão de sondagem Espelha o comportamento quando chama o bakery cada 20 minutos para verificar se o bolo está pronto. O padrão de webhook Espelha o comportamento em que o bakery solicita o número de telefone para que eles podem ligar-lhe quando o bolo estiver pronto.

Para exemplos, visite o [repositório do GitHub de aplicações lógicas](https://github.com/logicappsio). Além disso, saiba mais sobre [medição de utilização para ações](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Executar tarefas de execução longa com o padrão de ação de consulta

Para ter a sua API realizar tarefas que poderiam ser executada mais do que o [limite de tempo limite do pedido](./logic-apps-limits-and-config.md), pode usar o padrão de consulta assíncrona. Este padrão possui seu executar API trabalhar num thread separado, mas manter uma conexão ativa com o motor do Logic Apps. Dessa forma, a aplicação lógica não não tempo limite, ou continuar com a próxima etapa no fluxo de trabalho antes de sua API concluir o trabalho.

Este é o padrão geral:

1. Certifique-se de que o mecanismo sabe que a sua API aceita o pedido e começou a trabalhar.
2. Ao fazer pedidos subsequentes para o estado da tarefa, o mecanismo de permitir que o mecanismo de saber quando a tarefa é concluída a sua API.
3. Devolve os dados relevantes para o mecanismo para que possa continuar o fluxo de trabalho de aplicação lógica.

<a name="bakery-polling-action"></a> Agora, aplicar a analogia bakery anterior para o padrão de consulta e imagine que chamar um bakery e a ordem de um bolo personalizado para entrega. O processo para fazer o bolo leva tempo e não quiser esperar no telefone, enquanto o bakery funciona no muito simples. O bakery confirma que a sua encomenda e tem que chamar a cada 20 minutos para obter o estado do bolo. Depois de passar a 20 minutos, chama o bakery, mas lhe dizem que o melhor não foi feito e que deve chamar em outro 20 minutos. Este processo back lado para o outro continua até que chamar e o bakery indica que o seu pedido está pronto e fornece o melhor. 

Então, vamos mapear este padrão de consulta de volta. O bakery representa a API personalizada, embora, o cliente muito simples, representa o motor do Logic Apps. Quando o mecanismo chama a API com um pedido, a API confirma a solicitação e responde com o intervalo de tempo, quando o mecanismo pode verificar o estado da tarefa. O mecanismo continuará a verificar o estado da tarefa até que a sua API responde a que a tarefa está concluída e devolve dados à sua aplicação lógica, que, em seguida, continua o fluxo de trabalho. 

![Padrão de ação de consulta](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Eis os passos específicos para a sua API a seguir, descrito da perspectiva da API:

1. Quando a API recebe um pedido HTTP para iniciar o trabalho, imediatamente retorna um HTTP `202 ACCEPTED` resposta com o `location` cabeçalho descrito mais adiante neste passo. Esta resposta permite que o motor do Logic Apps sabe que a sua API recebeu o pedido, aceite o payload de pedido (dados de entrada) e agora está a processar. 
   
   O `202 ACCEPTED` resposta deve incluir esses cabeçalhos:
   
   * *Necessário*: A `location` cabeçalho que especifica o caminho absoluto para um URL em que o motor do Logic Apps pode verificar o estado da tarefa da sua API

   * *Opcional*: R `retry-after` cabeçalho que especifica o número de segundos que o mecanismo deve aguardar antes de a verificar o `location` URL para o estado da tarefa. 

     Por predefinição, o mecanismo verifica cada 20 segundos. Para especificar um intervalo diferente, inclua o `retry-after` cabeçalho e o número de segundos até que a consulta seguinte.

2. Depois de passa do tempo especificado, o Logic Apps do motor de inquéritos o `location` URL para verificar o estado da tarefa. A API deve executar essas verificações e retornar essas respostas:
   
   * Se a tarefa está concluída, devolver um HTTP `200 OK` resposta, juntamente com o payload de resposta (entrada para a próxima etapa).

   * Se a tarefa ainda está a processar, devolver HTTP outro `202 ACCEPTED` resposta, mas com os mesmos cabeçalhos como a resposta original.

Quando a sua API segue este padrão, não tem de fazer nada na definição de fluxo de trabalho da aplicação lógica para continuar a verificar o estado da tarefa. Quando o motor recebe um HTTP `202 ACCEPTED` resposta e válido `location` cabeçalho, o mecanismo respeita o padrão assíncrono e verifica o `location` cabeçalho até que a sua API devolve uma resposta não 202.

> [!TIP]
> Para um padrão assíncrono de exemplo, veja esta [exemplo de resposta do controlador assíncrono no GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Executar tarefas de execução longa com o padrão de ação do webhook

Como alternativa, pode usar o padrão de webhook para tarefas de longa execução e processamento assíncrono. Este padrão possui a aplicação lógica, colocar em pausa e espere até que um "retorno de chamada" da sua API para concluir o processamento antes de continuar o fluxo de trabalho. Esse retorno de chamada é um HTTP POST que envia uma mensagem para um URL quando ocorre um evento. 

<a name="bakery-webhook-action"></a> Agora aplique a analogia bakery anterior para o padrão de webhook e imagine que chamar um bakery e a ordem de um bolo personalizado para entrega. O processo para fazer o bolo leva tempo e não quiser esperar no telefone, enquanto o bakery funciona no muito simples. O bakery confirma o seu pedido, mas desta vez, que lhes fornece o número de telefone, de modo que podem ligar-lhe quando o bolo é feito. Desta vez, o bakery indica quando o seu pedido está pronto e fornece o melhor.

Quando vamos mapear novamente este padrão de webhook, o bakery representa a API personalizada, embora, o cliente muito simples, representa o motor do Logic Apps. O mecanismo chama a API com um pedido e inclui um URL de "retorno de chamada".
Quando a tarefa está concluída, a sua API utiliza o URL para o notificar o mecanismo e devolve os dados à sua aplicação lógica, que, em seguida, continua o fluxo de trabalho. 

Para esse padrão, configurar dois pontos de extremidade no seu controlador: `subscribe` e `unsubscribe`

*  `subscribe` Ponto final: Quando a execução atinge a ação da sua API no fluxo de trabalho, o Logic Apps do motor de chamadas a `subscribe` ponto final. Este passo faz com que a aplicação lógica para criar um URL de retorno de chamada que armazena a sua API e, em seguida, aguarde o retorno de chamada de API quando o trabalho está concluído. A API, em seguida, chama de volta com um HTTP POST para o URL e passa a qualquer conteúdo devolvido e cabeçalhos como entrada para a aplicação lógica.

* `unsubscribe` Ponto final: Se a execução da aplicação lógica é cancelada, o Logic Apps do motor de chamadas a `unsubscribe` ponto final. A API pode, em seguida, anular o registo o URL de retorno de chamada e parar todos os processos conforme necessário.

![Padrão de ação do Webhook](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Atualmente, o Estruturador da aplicação lógica não suporta a deteção de webhook de pontos finais por meio do Swagger. Portanto, para que este padrão, deve adicionar um [ **Webhook** ação](../connectors/connectors-native-webhook.md) e especifique o URL, cabeçalhos e o corpo para o seu pedido. Consulte também [ações de fluxo de trabalho e acionadores](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Para introduzir o URL de retorno de chamada, pode usar o `@listCallbackUrl()` função de fluxo de trabalho em qualquer um dos campos anteriores conforme necessário.

> [!TIP]
> Para um padrão de webhook de exemplo, veja esta [exemplo de Acionador de webhook no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Padrões de Acionador

A API personalizada pode atuar como um [ *acionador* ](./logic-apps-overview.md#logic-app-concepts) que inicia uma aplicação lógica quando novos dados ou um evento cumpre uma condição especificada. Este acionador pode verificar regularmente, ou aguardar e escutar, novos dados ou eventos no seu ponto final de serviço. Se novos dados ou um evento atenda à condição especificada, o acionador é acionado e inicia a aplicação de lógica, que está à escuta para esse acionador. Para iniciar as aplicações lógicas desta forma, a sua API pode seguir a [ *acionador de sondagem* ](#polling-triggers) ou o [ *acionador de webhook* ](#webhook-triggers) padrão. Esses padrões são semelhantes às suas contrapartes para [ações de consulta](#async-pattern) e [ações de webhook](#webhook-actions). Além disso, saiba mais sobre [medição de utilização para acionadores](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Verifique a existência de novos dados ou eventos regularmente com o padrão de Acionador de consulta

R *acionador de consulta* atua mais como o [ação de sondagem](#async-pattern) descrito anteriormente neste tópico. O motor do Logic Apps periodicamente chama e verifica o ponto de final de Acionador para novos dados ou eventos. Se o mecanismo encontrar novos dados ou um evento que satisfaça a condição especificada, o acionador é acionado. Em seguida, o motor cria uma instância da aplicação lógica que processa os dados como entrada. 

![Padrão de Acionador de consulta](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Cada solicitação de consulta é contabilizado como uma execução da ação, mesmo quando nenhuma instância da aplicação lógica é criada. Para impedir que os mesmos dados a processar várias vezes, o acionador deve limpar os dados que já foi lidos e transmitidos para a aplicação lógica.

Aqui estão algumas etapas específicas para um acionador de consulta, descrito da perspectiva da API:

| Foram encontrados novos dados ou eventos?  | Resposta da API | 
| ------------------------- | ------------ |
| Localizado | Devolver um HTTP `200 OK` Estado com o payload de resposta (entrada para o passo seguinte). <br/>Esta resposta cria uma instância da aplicação lógica e inicia o fluxo de trabalho. | 
| Não encontrado | Devolver um HTTP `202 ACCEPTED` Estado com um `location` cabeçalho e uma `retry-after` cabeçalho. <br/>Para acionadores, o `location` cabeçalho deve conter também uma `triggerState` parâmetro de consulta, que é normalmente um "timestamp". A API pode utilizar este identificador para controlar a última vez que a aplicação lógica foi acionada. | 
||| 

Por exemplo, para verificar periodicamente o seu serviço para novos ficheiros, talvez queira criar um acionador de consulta com esses comportamentos:

| Pedido inclui `triggerState`? | Resposta da API | 
| -------------------------------- | -------------| 
| Não | Devolver um HTTP `202 ACCEPTED` estado e ainda recebe um `location` cabeçalho com `triggerState` definido como a hora atual e o `retry-after` intervalo para 15 segundos. | 
| Sim | Verifique o seu serviço para os ficheiros adicionados depois do `DateTime` para `triggerState`. | 
||| 

| Número de ficheiros foi encontrado | Resposta da API | 
| --------------------- | -------------| 
| Ficheiro único | Devolver um HTTP `200 OK` atualizar o estado e o payload de conteúdo `triggerState` para o `DateTime` para o ficheiro retornado e o conjunto `retry-after` intervalo para 15 segundos. | 
| Vários ficheiros | Voltar um arquivo num período de tempo e um HTTP `200 OK` Estado, a atualização `triggerState`e defina o `retry-after` intervalo de 0 segundos. </br>Estes passos permitem que o mecanismo de saber que estão disponíveis mais dados e que o motor imediatamente deve solicitar os dados de URL no `location` cabeçalho. | 
| Nenhum ficheiro | Devolver um HTTP `202 ACCEPTED` Estado, não altere `triggerState`e defina o `retry-after` intervalo para 15 segundos. | 
||| 

> [!TIP]
> Para obter um exemplo de consulta padrão do acionador, veja esta [exemplo de controlador de Acionador de consulta no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Aguarde e escuta de novos dados ou eventos com o padrão de Acionador de webhook

Um acionador de webhook é um *acionador push* que deve aguardar e está à escuta de novos dados ou eventos no seu ponto final de serviço. Se novos dados ou um evento atenda à condição especificada, o acionador é acionado e cria uma instância da aplicação lógica, que, em seguida, processa os dados como entrada.
Acionadores de Webhook agem muito como a [ações de webhook](#webhook-actions) anteriormente descritas neste tópico e são configurados com `subscribe` e `unsubscribe` pontos de extremidade. 

* `subscribe` Ponto final: Quando adicionar e guardar um acionador de webhook na sua aplicação lógica, o Logic Apps do motor de chamadas a `subscribe` ponto final. Este passo faz com que a aplicação lógica criar um URL de retorno de chamada que armazena a sua API. Quando existe novos dados ou um evento que atenda à condição especificada, a API chama-se novamente com um HTTP POST para o URL. O payload de conteúdo e cabeçalhos de passam como entrada para a aplicação lógica.

* `unsubscribe` Ponto final: Se o acionador de webhook ou uma aplicação lógica todo for eliminada, o Logic Apps do motor de chamadas a `unsubscribe` ponto final. A API pode, em seguida, anular o registo o URL de retorno de chamada e parar todos os processos conforme necessário.

![Padrão de Acionador de Webhook](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Atualmente, o Estruturador da aplicação lógica não suporta a deteção de webhook de pontos finais por meio do Swagger. Portanto, para que este padrão, deve adicionar um [ **Webhook** acionador](../connectors/connectors-native-webhook.md) e especifique o URL, cabeçalhos e o corpo para o seu pedido. Consulte também [HTTPWebhook acionador](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Para introduzir o URL de retorno de chamada, pode usar o `@listCallbackUrl()` função de fluxo de trabalho em qualquer um dos campos anteriores conforme necessário.
>
> Para impedir que os mesmos dados a processar várias vezes, o acionador deve limpar os dados que já foi lidos e transmitidos para a aplicação lógica.

> [!TIP]
> Para um padrão de webhook de exemplo, veja esta [exemplo de controlador de Acionador de webhook no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="secure-calls-to-your-apis-from-logic-apps"></a>Proteger chamadas a APIs de aplicações lógicas

Depois de criar as suas APIs personalizados, configure a autenticação para as suas APIs para que possam chamá-los de forma segura de aplicações lógicas. Saiba mais [como proteger chamadas a APIs personalizadas de aplicações lógicas](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Implementar e chamar as suas APIs

Depois de configurar a autenticação, configure a implementação para as suas APIs. Saiba mais [como implementar e chamar APIs personalizadas de aplicações lógicas](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publique APIs personalizadas no Azure

Para disponibilizar as suas APIs personalizadas para outros utilizadores do Logic Apps no Azure, tem de adicionar a segurança e registá-los como conectores de aplicação lógica. Para obter mais informações, veja [Descrição geral dos conectores personalizados](../logic-apps/custom-connector-overview.md). 

Para disponibilizar as suas APIs personalizadas para todos os utilizadores no Logic Apps, Microsoft Flow e Microsoft PowerApps, tem de adicionar segurança, registe as suas APIs como conectores de aplicação lógica e nomeie os seus conectores para o [programa Microsoft Azure Certified](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Obter suporte

* Para obter ajuda específica com APIs personalizadas, contacte [ customapishelp@microsoft.com ](mailto:customapishelp@microsoft.com).

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ajudar a melhorar o Logic Apps, vote ou submeta ideais no [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Passos Seguintes

* [Lidar com erros e exceções](../logic-apps/logic-apps-exception-handling.md)
* [Chamar, acionar, ou aninhar aplicações lógicas com pontos de extremidade HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Medição de utilização para ações e acionadores](../logic-apps/logic-apps-pricing.md)
