---
title: Criar APIs web & APIs REST para aplicações lógicas azure
description: Crie APIs web & APIs REST para ligar para as suas APIs, serviços ou sistemas para integrações de sistemas em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: bb6c99ea12e5b53631d42a04b36b7bfef2337e42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270540"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Crie APIs personalizados que pode ligar de Aplicações Lógicas Azure

Embora as Aplicações Lógicas Azure ofereçam [centenas de conectores](../connectors/apis-list.md) que pode utilizar em fluxos de trabalho de aplicações lógicas, é possível que queira ligar para APIs, sistemas e serviços que não estão disponíveis como conectores. Pode criar as suas próprias APIs que fornecem ações e gatilhos para usar em aplicações lógicas. Aqui estão outras razões pelas quais você pode querer criar suas próprias APIs que você pode ligar de fluxos de trabalho de aplicações lógicas:

* Alargar os fluxos de trabalho de integração do sistema e integração de dados.
* Ajude os clientes a utilizar o seu serviço para gerir tarefas profissionais ou pessoais.
* Expanda o alcance, a descoberta e o uso para o seu serviço.

Basicamente, os conectores são APIs web que usam REST para interfaces pluggable, [formato de metadados Swagger](https://swagger.io/specification/) para documentação, e JSON como o seu formato de troca de dados. Como os conectores são APIs REST que comunicam através de pontos finais HTTP, você pode usar qualquer idioma, como .NET, Java, Python ou Node.js, para construir conectores. Também pode hospedar as suas APIs no [Azure App Service,](../app-service/overview.md)uma oferta de plataforma como serviço (PaaS) que fornece uma das melhores, mais fáceis e escaláveis formas de hospedagem da API. 

Para que as APIs personalizadas funcionem com aplicações lógicas, a sua API pode fornecer [*ações*](./logic-apps-overview.md#logic-app-concepts) que executam tarefas específicas em fluxos de trabalho de aplicações lógicas. A Sua API também pode funcionar como um [*gatilho*](./logic-apps-overview.md#logic-app-concepts) que inicia um fluxo de trabalho de aplicações lógicas quando novos dados ou um evento cumpre uma condição especificada. Este tópico descreve padrões comuns que pode seguir para ações de construção e gatilhos na sua API, com base no comportamento que pretende que a sua API forneça.

Você pode hospedar as suas APIs no [Azure App Service](../app-service/overview.md), uma oferta de plataforma-as-a-service (PaaS) que fornece hospedagem aPi altamente escalável e fácil.

> [!TIP] 
> Embora possa implementar as suas APIs como aplicações web, considere implementar as suas APIs como aplicações API, o que pode facilitar o seu trabalho quando constrói, hospeda e consome APIs na nuvem e nas instalações. Não tens de alterar nenhum código nas tuas APIs. Por exemplo, aprenda a construir aplicações API criadas com estas línguas: 
> 
> * [ASP.NET.](../app-service/app-service-web-get-started-dotnet.md) 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> Para amostras de aplicações DaPi construídas para aplicações lógicas, visite o repositório ou [blog](https://aka.ms/logicappsblog) [Azure Logic Apps GitHub.](https://github.com/logicappsio)

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Como é que as APIs personalizadas diferem dos conectores personalizados?

APIs personalizados e [conectores personalizados](../logic-apps/custom-connector-overview.md) são APIs web que usam REST para interfaces pluggable, [formato de metadados Swagger](https://swagger.io/specification/) para documentação, e JSON como o seu formato de troca de dados. E como estas APIs e conectores são APIs REST que comunicam através de pontos finais HTTP, você pode usar qualquer idioma, como .NET, Java, Python ou Node.js, para a construção de APIs e conectores personalizados.

ApIs personalizados permitem ligar para APIs que não são conectores, e fornecer pontos finais que você pode ligar com HTTP + Swagger, Azure API Management ou App Services. Os conectores personalizados funcionam como APIs personalizados, mas também têm estes atributos:

* Registado como recursos de Conector de Aplicações Lógicas em Azure.
* Apareça com ícones ao lado de conectores geridos pela Microsoft no Logic Apps Designer.
* Disponível apenas para os autores dos conectores e utilizadores de aplicações lógicas que possuam o mesmo inquilino do Azure Ative Directory e a subscrição do Azure na região onde as aplicações lógicas são implementadas.

Também pode nomear conectores registados para certificação da Microsoft. Este processo verifica que os conectores registados cumprem os critérios de utilização pública e disponibiliza esses conectores para os utilizadores em Power Automate e Microsoft Power Apps.

Para mais informações sobre conectores personalizados, consulte 

* [Descrição geral dos conectores personalizados](../logic-apps/custom-connector-overview.md)
* [Criar conectores personalizados a partir de APIs web](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Registe conectores personalizados em Aplicações Lógicas Azure](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Ferramentas úteis

Um API personalizado funciona melhor com aplicações lógicas quando a API também tem um [documento Swagger](https://swagger.io/specification/) que descreve as operações e parâmetros da API.
Muitas bibliotecas, como [swashbuckle,](https://github.com/domaindrivendev/Swashbuckle)podem gerar automaticamente o ficheiro Swagger para si. Para anotar o ficheiro Swagger para nomes de exibição, tipos de propriedade, e assim por diante, também pode usar [o TRex](https://github.com/nihaue/TRex) para que o seu ficheiro Swagger funcione bem com aplicações lógicas.

<a name="actions"></a>

## <a name="action-patterns"></a>Padrões de ação

Para que as aplicações lógicas realizem tarefas, a sua API personalizada deve fornecer [*ações.*](./logic-apps-overview.md#logic-app-concepts) Cada operação nos mapas da API para uma ação. Uma ação básica é um controlador que aceita pedidos http e devolve respostas HTTP. Assim, por exemplo, uma aplicação lógica envia um pedido HTTP para a sua aplicação web ou aplicação API. Em seguida, a sua aplicação devolve uma resposta HTTP, juntamente com conteúdos que a aplicação lógica pode processar.

Para uma ação padrão, pode escrever um método de pedido HTTP na sua API e descrever esse método num ficheiro Swagger. Em seguida, pode ligar diretamente para o seu API com uma [ação HTTP](../connectors/connectors-native-http.md) ou uma ação HTTP [+ Swagger.](../connectors/connectors-native-http-swagger.md) Por predefinição, as respostas devem ser devolvidas dentro do prazo de [pedido](./logic-apps-limits-and-config.md). 

![Padrão de ação padrão](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a>Para fazer uma aplicação lógica esperar enquanto a sua API termina tarefas de execução mais longa, a sua API pode seguir o padrão de [sondagens assíncronos](#async-pattern) ou o padrão de [webhook assíncrono](#webhook-actions) descrito neste tópico. Para uma analogia que o ajude a visualizar os diferentes comportamentos destes padrões, imagine o processo para encomendar um bolo personalizado de uma padaria. O padrão de sondagens espelha o comportamento em que se chama a padaria a cada 20 minutos para verificar se o bolo está pronto. O padrão do webhook espelha o comportamento em que a padaria lhe pede o seu número de telefone para que possam ligar-lhe quando o bolo estiver pronto.

Para obter amostras, visite o [repositório Logic Apps GitHub](https://github.com/logicappsio). Além disso, saiba mais sobre a medição de [utilização para ações.](logic-apps-pricing.md)

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Execute tarefas de longa duração com o padrão de ação de sondagens

Para que a sua API execute tarefas que possam ser mais longas do que o limite de tempo limite de [pedido,](./logic-apps-limits-and-config.md)pode utilizar o padrão de sondagens assíncronos. Este padrão tem o seu API funcionar em um fio separado, mas manter uma ligação ativa ao motor Logic Apps. Desta forma, a aplicação lógica não se estem ou continuam com o próximo passo no fluxo de trabalho antes de a API terminar de funcionar.

Aqui está o padrão geral:

1. Certifique-se de que o motor sabe que a sua API aceitou o pedido e começou a funcionar.
2. Quando o motor fizer pedidos subsequentes de estado de trabalho, informe o motor quando a sua API terminar a tarefa.
3. Devolva os dados relevantes ao motor para que o fluxo de trabalho da aplicação lógica possa continuar.

<a name="bakery-polling-action"></a>Agora aplique a analogia da padaria anterior ao padrão de sondagens, e imagine que você chama uma padaria e pede um bolo personalizado para entrega. O processo para fazer o bolo leva tempo, e você não quer esperar ao telefone enquanto a padaria trabalha no bolo. A padaria confirma o seu pedido e tem que ligar a cada 20 minutos para o estado do bolo. Depois de 20 minutos de passe, ligas para a padaria, mas eles dizem-te que o teu bolo ainda não acabou e que deves ligar daqui a 20 minutos. Este processo de ida e volta continua até ligar, e a padaria diz-lhe que o seu pedido está pronto e entrega o seu bolo. 

Vamos mapear este padrão de sondagens de volta. A padaria representa a sua API personalizada, enquanto você, o cliente do bolo, representa o motor Logic Apps. Quando o motor chama a Sua API com um pedido, a sua API confirma o pedido e responde com o intervalo de tempo em que o motor pode verificar o estado do trabalho. O motor continua a verificar o estado do trabalho até que a API responda que o trabalho está feito e devolve dados à sua aplicação lógica, que depois continua o fluxo de trabalho. 

![Padrão de ação de sondagens](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Aqui estão os passos específicos para a sua API seguir, descritos na perspetiva da API:

1. Quando o seu API receber um pedido de `202 ACCEPTED` HTTP para `location` iniciar o trabalho, devolva imediatamente uma resposta HTTP com o cabeçalho descrito mais tarde neste passo. Esta resposta permite ao motor Logic Apps saber que a sua API obteve o pedido, aceitou a carga útil do pedido (entrada de dados), e está agora a ser processada. 
   
   A `202 ACCEPTED` resposta deve incluir estes cabeçalhos:
   
   * *Obrigatório*: `location` Um cabeçalho que especifica o caminho absoluto para um URL onde o motor Logic Apps pode verificar o estado de trabalho da Sua API

   * *Opcional*: `retry-after` Um cabeçalho que especifica o número de segundos que o motor deve esperar antes de verificar o `location` URL para o estado do trabalho. 

     Por padrão, o motor verifica a cada 20 segundos. Para especificar um intervalo `retry-after` diferente, inclua o cabeçalho e o número de segundos até à próxima sondagem.

2. Após o tempo especificado passar, o `location` motor logic Apps pesquisa o URL para verificar o estado do trabalho. A Sua API deve efetuar estes controlos e devolver estas respostas:
   
   * Se o trabalho estiver feito, devolva uma resposta HTTP, `200 OK` juntamente com a carga útil da resposta (entrada para o próximo passo).

   * Se o trabalho ainda estiver `202 ACCEPTED` a ser processado, devolva outra resposta HTTP, mas com os mesmos cabeçalhos que a resposta original.

Quando a Sua API segue este padrão, não tem de fazer nada na definição lógica de fluxo de trabalho de aplicações para continuar a verificar o estado do trabalho. Quando o motor `202 ACCEPTED` obtém uma `location` resposta HTTP e um cabeçalho válido, o `location` motor respeita o padrão assíncrono e verifica o cabeçalho até que a sua API retorne uma resposta não-202.

> [!TIP]
> Por exemplo, um padrão assíncrono, reveja esta amostra de [resposta asíncrona do controlador no GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Execute tarefas de longo prazo com o padrão de ação webhook

Como alternativa, pode utilizar o padrão webhook para tarefas de longa duração e processamento assíncrono. Este padrão tem a pausa lógica da aplicação e espera por um "callback" da sua API para terminar o processamento antes de continuar o fluxo de trabalho. Este callback é um POST HTTP que envia uma mensagem a um URL quando um evento acontece. 

<a name="bakery-webhook-action"></a>Agora aplique a analogia da padaria anterior ao padrão webhook, e imagine que você chama uma padaria e peça um bolo personalizado para entrega. O processo para fazer o bolo leva tempo, e você não quer esperar ao telefone enquanto a padaria trabalha no bolo. A padaria confirma o seu pedido, mas desta vez, dê-lhes o seu número de telefone para que possam ligar-lhe quando o bolo estiver feito. Desta vez, a padaria diz-lhe quando o seu pedido está pronto e entrega o seu bolo.

Quando mapeamos este padrão de webhook de volta, a padaria representa a sua API personalizada, enquanto você, o cliente do bolo, representa o motor Logic Apps. O motor chama a sua API com um pedido e inclui um URL de "callback".
Quando o trabalho está feito, o seu API utiliza o URL para notificar o motor e devolver dados à sua aplicação lógica, que depois continua o fluxo de trabalho. 

Para este padrão, coloque dois pontos `subscribe` finais no seu controlador: e`unsubscribe`

*  `subscribe`ponto final: Quando a execução atinge a ação da Sua API `subscribe` no fluxo de trabalho, o motor Logic Apps chama o ponto final. Este passo faz com que a aplicação lógica crie um URL de callback que a sua API armazena e, em seguida, aguarde a chamada da sua API quando o trabalho estiver concluído. Em seguida, a Sua API liga com um POST HTTP para o URL e passa qualquer conteúdo e cabeçalhos devolvidos como entrada para a aplicação lógica.

* `unsubscribe`ponto final: Se a execução da aplicação lógica `unsubscribe` for cancelada, o motor Logic Apps chama o ponto final. A sua API pode então desregistar o URL de chamada e parar quaisquer processos necessários.

![Padrão de ação webhook](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Atualmente, o Logic App Designer não suporta descobrir pontos finais webhook através da Swagger. Assim, para este padrão, você tem que adicionar uma ação [ **Webhook** ](../connectors/connectors-native-webhook.md) e especificar o URL, cabeçalhos e corpo para o seu pedido. Consulte também [ações e gatilhos de Fluxo de Trabalho.](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action) Para passar o URL de backback, pode utilizar a `@listCallbackUrl()` função de fluxo de trabalho em qualquer um dos campos anteriores, se necessário.

> [!TIP]
> Para um padrão de webhook, reveja esta amostra de [gatilho webhook no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Padrões de gatilho

A Sua API personalizada pode funcionar como um [*gatilho*](./logic-apps-overview.md#logic-app-concepts) que inicia uma aplicação lógica quando novos dados ou um evento cumpre uma condição especificada. Este gatilho pode verificar regularmente, ou esperar e ouvir, para obter novos dados ou eventos no seu ponto final de serviço. Se novos dados ou evento satisfizerem a condição especificada, o gatilho dispara e inicia a aplicação lógica, que está a ouvir esse gatilho. Para iniciar aplicações lógicas desta forma, a sua API pode seguir o gatilho de [*sondagens*](#polling-triggers) ou o padrão do gatilho do [*webhook.*](#webhook-triggers) Estes padrões são semelhantes aos seus homólogos para [ações de sondagens](#async-pattern) e [ações de webhook.](#webhook-actions) Além disso, saiba mais sobre [a medição de utilização para os gatilhos](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Verifique regularmente novos dados ou eventos com o padrão de gatilho de sondagens

Um *gatilho de sondagens* funciona como a [ação de sondagens](#async-pattern) anteriormente descrita neste tema. O motor Logic Apps liga periodicamente e verifica o ponto final do gatilho para novos dados ou eventos. Se o motor encontrar novos dados ou um evento que satisfaça a sua condição especificada, o gatilho dispara. Em seguida, o motor cria uma instância lógica de aplicação que processa os dados como entrada. 

![Padrão de gatilho de sondagens](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Cada pedido de votação conta como uma execução de ação, mesmo quando não é criada nenhuma instância de aplicação lógica. Para evitar o processamento dos mesmos dados várias vezes, o gatilho deve limpar dados que já foram lidos e passados para a aplicação lógica.

Aqui estão passos específicos para um gatilho de sondagens, descrito na perspetiva da API:

| Encontrou novos dados ou evento?  | Resposta da API | 
| ------------------------- | ------------ |
| Localizado | Devolva `200 OK` um estado HTTP com a carga útil da resposta (entrada para o próximo passo). <br/>Esta resposta cria uma instância de aplicação lógica e inicia o fluxo de trabalho. | 
| Não encontrado | Devolva `202 ACCEPTED` um `location` estado HTTP `retry-after` com um cabeceamento e um cabeceamento. <br/>Para os gatilhos, o `location` `triggerState` cabeçalho também deve conter um parâmetro de consulta, que normalmente é um "timestamp". A Sua API pode usar este identificador para rastrear a última vez que a aplicação lógica foi desencadeada. | 
||| 

Por exemplo, para verificar periodicamente o seu serviço para novos ficheiros, pode construir um gatilho de sondagens que tenha estes comportamentos:

| O `triggerState`pedido inclui? | Resposta da API | 
| -------------------------------- | -------------| 
| Não | Devolva `202 ACCEPTED` um `location` estado `triggerState` HTTP mais um cabeçalho com o tempo atual e o `retry-after` intervalo para 15 segundos. | 
| Sim | Verifique se o seu `DateTime` serviço `triggerState`está a ser adicionado após o . | 
||| 

| Número de ficheiros encontrados | Resposta da API | 
| --------------------- | -------------| 
| Ficheiro único | Devolva `200 OK` um estado HTTP e `triggerState` a `DateTime` carga útil do `retry-after` conteúdo, atualize para o ficheiro devolvido e devolva o intervalo para 15 segundos. | 
| Vários ficheiros | Devolva um ficheiro de `200 OK` cada `triggerState`vez e `retry-after` um estado HTTP, atualize e detete o intervalo para 0 segundos. </br>Estes passos permitem ao motor saber que existem mais dados disponíveis e que `location` o motor deve solicitar imediatamente os dados do URL no cabeçalho. | 
| Sem ficheiros | Devolva `202 ACCEPTED` um estado HTTP, não mude e desloque `triggerState`o `retry-after` intervalo para 15 segundos. | 
||| 

> [!TIP]
> Por exemplo, no padrão do gatilho da sondagem, reveja esta amostra de controlador de gatilho de [sondagem no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Aguarde e ouça novos dados ou eventos com o padrão do gatilho do webhook

Um gatilho webhook é um *gatilho* que espera e ouve novos dados ou eventos no seu ponto final de serviço. Se novos dados ou um evento cumprirem a condição especificada, o gatilho dispara e cria uma instância lógica de aplicação, que então processa os dados como entrada.
Os gatilhos do Webhook agem muito como as ações de [webhook](#webhook-actions) anteriormente descritas neste tópico, e são configurados com `subscribe` e `unsubscribe` pontos finais. 

* `subscribe`ponto final: Quando adiciona e guarda um gatilho webhook na sua `subscribe` aplicação lógica, o motor Logic Apps chama o ponto final. Este passo faz com que a aplicação lógica crie um URL de callback que a sua API armazena. Quando há novos dados ou um evento que satisfaça a condição especificada, a sua API volta a ligar com um POST HTTP para o URL. A carga de conteúdo e os cabeçalhos passam como entrada para a aplicação lógica.

* `unsubscribe`ponto final: Se o gatilho do webhook ou toda `unsubscribe` a aplicação lógica for eliminada, o motor Logic Apps chama o ponto final. A sua API pode então desregistar o URL de chamada e parar quaisquer processos necessários.

![Padrão de gatilho webhook](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Atualmente, o Logic App Designer não suporta descobrir pontos finais webhook através da Swagger. Assim, para este padrão, você tem que adicionar um gatilho [ **Webhook** ](../connectors/connectors-native-webhook.md) e especificar o URL, cabeçalhos e corpo para o seu pedido. Consulte também o [gatilho HTTPWebhook](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Para passar o URL de backback, pode utilizar a `@listCallbackUrl()` função de fluxo de trabalho em qualquer um dos campos anteriores, se necessário.
>
> Para evitar o processamento dos mesmos dados várias vezes, o gatilho deve limpar dados que já foram lidos e passados para a aplicação lógica.

> [!TIP]
> Para um padrão de webhook, reveja esta amostra do controlador de [gatilho webhook no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>Melhorar a segurança das chamadas para as suas APIs de aplicações lógicas

Depois de criar as suas APIs personalizadas, configura a autenticação para as suas APIs para que possa chamá-las de forma segura a partir de aplicações lógicas. Saiba [como melhorar a segurança das chamadas para APIs personalizados de aplicações lógicas](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Desloque e ligue para as suas APIs

Depois de configurar a autenticação, instale a implementação para as suas APIs. Aprenda [a implementar e a chamar APIs personalizados de aplicações lógicas](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publicar APIs personalizados para o Azure

Para disponibilizar as suas APIs personalizadas para outros utilizadores de Aplicações Lógicas no Azure, deve adicionar segurança e registá-las como conectores de Aplicações Lógicas. Para obter mais informações, veja [Descrição geral dos conectores personalizados](../logic-apps/custom-connector-overview.md). 

Para disponibilizar as suas APIs personalizadas a todos os utilizadores em Aplicações Lógicas, Power Automate e Microsoft Power Apps, deve adicionar segurança, registar as suas APIs como conectores de Aplicações Lógicas e nomear os seus conectores para o [programa Certificado Microsoft Azure](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Obter suporte

* Para obter ajuda específica com [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com)APIs personalizados, contacte .

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ajudar a melhorar o Logic Apps, vote ou submeta ideais no [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Passos seguintes

* [Lidar com erros e exceções](../logic-apps/logic-apps-exception-handling.md)
* [Aplicativos de call, trigger ou nest logic com pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Medição de utilização para ações e gatilhos](../logic-apps/logic-apps-pricing.md)
