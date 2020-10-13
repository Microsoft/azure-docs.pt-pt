---
title: Criar APIs web & REST APIs para apps lógicas Azure
description: Crie APIs web & REST APIs para ligar para as suas APIs, serviços ou sistemas para integrações de sistemas em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/26/2017
ms.openlocfilehash: 3fe98160cc10eb3607b8309a9a263d63380dcfb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89073221"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Crie APIs personalizados que pode chamar a partir de Azure Logic Apps

Embora o Azure Logic Apps ofereça [centenas de conectores](../connectors/apis-list.md) que pode utilizar em fluxos de trabalho de aplicações lógicas, é possível que queira chamar APIs, sistemas e serviços que não estão disponíveis como conectores. Pode criar as suas próprias APIs que fornecem ações e gatilhos para usar em aplicações lógicas. Aqui estão outras razões pelas quais você pode querer criar as suas próprias APIs que você pode chamar a partir de fluxos de trabalho de aplicações lógicas:

* Alargar os fluxos de trabalho de integração do sistema e integração de dados.
* Ajude os clientes a utilizar o seu serviço para gerir tarefas profissionais ou pessoais.
* Expanda o alcance, a descoberta e o uso para o seu serviço.

Basicamente, os conectores são APIs web que usam REST para interfaces pluggable, [formato de metadados Swagger](https://swagger.io/specification/) para documentação, e JSON como o seu formato de troca de dados. Como os conectores são APIs REST que comunicam através de pontos finais HTTP, pode utilizar qualquer idioma, como .NET, Java, Python ou Node.js, para a construção de conectores. Também pode hospedar as suas APIs no [Azure App Service](../app-service/overview.md), uma oferta de plataforma como serviço (PaaS) que fornece uma das melhores, mais fáceis e escaláveis maneiras de hospedagem da API. 

Para que as APIs personalizadas funcionem com aplicações lógicas, a sua API pode fornecer [*ações*](./logic-apps-overview.md#logic-app-concepts) que executam tarefas específicas em fluxos de trabalho de aplicações lógicas. A sua API também pode funcionar como um [*gatilho*](./logic-apps-overview.md#logic-app-concepts) que inicia um fluxo de trabalho de aplicações lógicas quando novos dados ou um evento cumpre uma condição especificada. Este tópico descreve padrões comuns que pode seguir para ações de construção e gatilhos na sua API, com base no comportamento que pretende que a sua API forneça.

Você pode hospedar as suas APIs no [Azure App Service](../app-service/overview.md), uma oferta de plataforma como um serviço (PaaS) que fornece hospedagem API altamente escalável e fácil.

> [!TIP] 
> Embora possa implementar as suas APIs como aplicações web, considere implementar as suas APIs como aplicações API, o que pode facilitar o seu trabalho ao construir, hospedar e consumir APIs na nuvem e nas instalações. Não tens de alterar nenhum código nas tuas APIs, apenas implementa o teu código numa aplicação da API. Por exemplo, aprenda a construir aplicações API criadas com estas línguas: 
> 
> * [ASP.NET.](../app-service/quickstart-dotnetcore.md) 
> * [Java](../app-service/quickstart-java.md)
> * [Node.js](../app-service/quickstart-nodejs.md)
> * [PHP](../app-service/quickstart-php.md)
> * [Python](../app-service/quickstart-python.md)
> * [Ruby](../app-service/quickstart-ruby.md)
>
> Para amostras de aplicativos API construídos para aplicações lógicas, visite o [repositório Azure Logic Apps GitHub](https://github.com/logicappsio).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Como é que as APIs personalizadas diferem dos conectores personalizados?

ApIs personalizados e [conectores personalizados](../logic-apps/custom-connector-overview.md) são APIs web que usam REST para interfaces pluggable, [formato de metadados Swagger](https://swagger.io/specification/) para documentação, e JSON como o seu formato de troca de dados. E como estas APIs e conectores são APIs REST que comunicam através de pontos finais HTTP, pode utilizar qualquer idioma, como .NET, Java, Python ou Node.js, para construir APIs e conectores personalizados.

As APIs personalizadas permitem-lhe ligar para APIs que não são conectores e fornecer pontos finais que pode ligar com HTTP + Swagger, Azure API Management ou App Services. Os conectores personalizados funcionam como APIs personalizados, mas também têm estes atributos:

* Registado como Logic Apps Connector recursos em Azure.
* Apareça com ícones ao lado de conectores geridos pela Microsoft no Logic Apps Designer.
* Disponível apenas para os autores dos conectores e utilizadores de aplicações lógicas que têm o mesmo inquilino do Azure Ative Directory e a subscrição do Azure na região onde as aplicações lógicas são implementadas.

Também pode nomear conectores registados para a certificação microsoft. Este processo verifica se os conectores registados cumprem os critérios de uso público e disponibiliza esses conectores para os utilizadores em Power Automamate e Microsoft Power Apps.

Para obter mais informações sobre conectores personalizados, consulte 

* [Visão geral dos conectores personalizados](../logic-apps/custom-connector-overview.md)
* [Criar conectores personalizados a partir de APIs web](/connectors/custom-connectors/create-web-api-connector)
* [Registar conectores personalizados em Azure Logic Apps](/connectors/custom-connectors/)

## <a name="helpful-tools"></a>Ferramentas úteis

Uma API personalizada funciona melhor com aplicações lógicas quando a API também tem um [documento Swagger](https://swagger.io/specification/) que descreve as operações e parâmetros da API.
Muitas bibliotecas, como [o Swashbuckle,](https://github.com/domaindrivendev/Swashbuckle)podem gerar automaticamente o ficheiro Swagger para si. Para anotar o ficheiro Swagger para exibir nomes, tipos de propriedade, etc., também pode utilizar [o TRex](https://github.com/nihaue/TRex) para que o seu ficheiro Swagger funcione bem com aplicações lógicas.

<a name="actions"></a>

## <a name="action-patterns"></a>Padrões de ação

Para que as aplicações lógicas executem tarefas, a sua API personalizada deve fornecer [*ações.*](./logic-apps-overview.md#logic-app-concepts) Cada operação no seu mapa da API para uma ação. Uma ação básica é um controlador que aceita pedidos HTTP e devolve respostas HTTP. Assim, por exemplo, uma aplicação lógica envia um pedido HTTP para a sua aplicação web ou app API. A sua aplicação devolve então uma resposta HTTP, juntamente com o conteúdo que a aplicação lógica pode processar.

Para uma ação padrão, pode escrever um método de pedido HTTP na sua API e descrever esse método num ficheiro Swagger. Em seguida, pode ligar diretamente para a sua API com uma [ação HTTP](../connectors/connectors-native-http.md) ou HTTP [+ Swagger.](../connectors/connectors-native-http-swagger.md) Por predefinição, as respostas devem ser devolvidas dentro do [prazo limite de tempo do pedido](./logic-apps-limits-and-config.md). 

![Padrão de ação padrão](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Para fazer uma aplicação lógica esperar enquanto a API termina tarefas mais longas, a sua API pode seguir o [padrão de sondagem assíncrono](#async-pattern) ou o [padrão assíncrono de webhook](#webhook-actions) descrito neste tópico. Para uma analogia que o ajuda a visualizar os diferentes comportamentos destes padrões, imagine o processo para encomendar um bolo personalizado de uma padaria. O padrão de sondagens espelha o comportamento em que se liga para a padaria a cada 20 minutos para verificar se o bolo está pronto. O padrão webhook espelha o comportamento em que a padaria pede o seu número de telefone para que eles possam ligar-lhe quando o bolo estiver pronto.

Para amostras, visite o [repositório De Aplicações Lógicas GitHub.](https://github.com/logicappsio) Além disso, saiba mais sobre [a medição de utilização para ações.](logic-apps-pricing.md)

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Execute tarefas de longa duração com o padrão de ação de votação

Para que a sua API execute tarefas que possam ser mais longas do que o [limite de tempo de pedido,](./logic-apps-limits-and-config.md)pode utilizar o padrão de sondagem assíncrono. Este padrão tem a sua API a funcionar num fio separado, mas mantenha uma ligação ativa com o motor De Aplicações Lógicas. Desta forma, a aplicação lógica não demora ou continua com o próximo passo no fluxo de trabalho antes que a sua API termine de funcionar.

Aqui está o padrão geral:

1. Certifique-se de que o motor sabe que a sua API aceitou o pedido e começou a funcionar.
2. Quando o motor fizer pedidos subsequentes para o estado do trabalho, informe o motor quando a sua API terminar a tarefa.
3. Devolva os dados relevantes ao motor para que o fluxo de trabalho da aplicação lógica possa continuar.

<a name="bakery-polling-action"></a> Agora aplique a analogia da padaria anterior ao padrão de sondagens, e imagine que você chama uma padaria e peça um bolo personalizado para entrega. O processo para fazer o bolo leva tempo, e você não quer esperar ao telefone enquanto a padaria trabalha no bolo. A padaria confirma o seu pedido e tem que ligar a cada 20 minutos para o estado do bolo. Depois de 20 minutos de passagem, ligue para a padaria, mas eles dizem-lhe que o seu bolo não está feito e que deve ligar daqui a 20 minutos. Este processo de ida e volta continua até ligar, e a padaria diz-lhe que o seu pedido está pronto e entrega o seu bolo. 

Vamos mapear este padrão de sondagens de volta. A padaria representa a sua API personalizada, enquanto você, o cliente do bolo, representa o motor Logic Apps. Quando o motor liga para a sua API com um pedido, a sua API confirma o pedido e responde com o intervalo de tempo quando o motor pode verificar o estado do trabalho. O motor continua a verificar o estado do trabalho até que a API responda que o trabalho está feito e devolve dados à sua aplicação lógica, que depois continua o fluxo de trabalho. 

![Padrão de ação de sondagem](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Aqui estão os passos específicos para a sua API seguir, descritos na perspetiva da API:

1. Quando a sua API receber um pedido HTTP para iniciar o trabalho, devolva imediatamente uma resposta HTTP `202 ACCEPTED` com o `location` cabeçalho descrito mais tarde neste passo. Esta resposta permite ao motor da Logic Apps saber que a sua API recebeu o pedido, aceitou a carga útil do pedido (entrada de dados) e está agora a ser processada. 
   
   A `202 ACCEPTED` resposta deve incluir estes cabeçalhos:
   
   * *Obrigatório*: Um `location` cabeçalho que especifica o caminho absoluto para um URL onde o motor de Aplicações Lógicas pode verificar o estado de trabalho da sua API

   * *Opcional*: Um `retry-after` cabeçalho que especifica o número de segundos que o motor deve esperar antes de verificar o `location` URL para o estado do trabalho. 

     Por defeito, o motor verifica a cada 20 segundos. Para especificar um intervalo diferente, inclua o `retry-after` cabeçalho e o número de segundos até à próxima sondagem.

2. Após o tempo especificado passar, o motor da Logic Apps sonda o URL para verificar o `location` estado do trabalho. A sua API deve efetuar estas verificações e devolver estas respostas:
   
   * Se o trabalho estiver feito, devolva uma `200 OK` resposta HTTP, juntamente com a carga útil de resposta (entrada para o passo seguinte).

   * Se o trabalho ainda estiver a ser processado, devolva outra `202 ACCEPTED` resposta HTTP, mas com os mesmos cabeçalhos que a resposta original.

Quando a sua API segue este padrão, não tem de fazer nada na definição de fluxo de trabalho de aplicações lógicas para continuar a verificar o estado do trabalho. Quando o motor obtém uma resposta HTTP `202 ACCEPTED` e um `location` cabeçalho válido, o motor respeita o padrão assíncrono e verifica o `location` cabeçalho até que a API retorne uma resposta não-202.

> [!TIP]
> Por exemplo, padrão assíncronos, reveja esta [amostra de resposta do controlador assíncronos no GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Execute tarefas de longa duração com o padrão de ação webhook

Como alternativa, pode utilizar o padrão webhook para tarefas de longa duração e processamento assíncronos. Este padrão tem a aplicação lógica para parar e esperar que uma "chamada" da sua API termine o processamento antes de continuar o fluxo de trabalho. Esta chamada é um HTTP POST que envia uma mensagem para um URL quando um evento acontece. 

<a name="bakery-webhook-action"></a> Agora aplique a analogia da padaria anterior ao padrão webhook, e imagine que você chama uma padaria e peça um bolo personalizado para entrega. O processo para fazer o bolo leva tempo, e você não quer esperar ao telefone enquanto a padaria trabalha no bolo. A padaria confirma o seu pedido, mas desta vez, dê-lhes o seu número de telefone para que possam ligar-lhe quando o bolo estiver pronto. Desta vez, a padaria diz-lhe quando o seu pedido está pronto e entrega o seu bolo.

Quando mapeamos este padrão webhook de volta, a padaria representa a sua API personalizada, enquanto você, o cliente do bolo, representa o motor De Aplicações Lógicas. O motor liga para a sua API com um pedido e inclui um URL de "callback".
Quando o trabalho está feito, a sua API utiliza o URL para notificar o motor e devolver dados à sua aplicação lógica, que depois continua o fluxo de trabalho. 

Para este padrão, crie dois pontos finais no seu controlador: `subscribe` e `unsubscribe`

*  `subscribe` ponto final: Quando a execução atinge a ação da sua API no fluxo de trabalho, o motor De Aplicações Lógicas chama o `subscribe` ponto final. Este passo faz com que a aplicação lógica crie um URL de retorno que a sua API armazena e, em seguida, aguarde a chamada da sua API quando o trabalho estiver concluído. A sua API volta a ligar com um HTTP POST para o URL e passa qualquer conteúdo e cabeçalhos devolvidos como entrada para a aplicação lógica.

* `unsubscribe` ponto final: Se a aplicação lógica for cancelada, o motor De Aplicações Lógicas chama o `unsubscribe` ponto final. A sua API pode então desagregá-lo no URL de retorno e parar quaisquer processos conforme necessário.

![Padrão de ação webhook](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

Atualmente, o Logic App Designer não suporta a descoberta de pontos finais webhook através da Swagger. Assim, para este padrão, você tem que adicionar uma [ação **Webhook** ](../connectors/connectors-native-webhook.md) e especificar o URL, cabeçalhos e corpo para o seu pedido. Consulte também [ações e gatilhos de fluxo de trabalho.](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action) Para um padrão de webhook, reveja esta [amostra de gatilho webhook no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

Aqui estão outras dicas e notas:

* Para passar no URL de retorno, pode utilizar a `@listCallbackUrl()` função de fluxo de trabalho em qualquer um dos campos anteriores, se necessário.

* Se possuir tanto a aplicação lógica como o serviço subscrito, não precisa de ligar para o `unsubscribe` ponto final depois de o URL de retorno de chamadas ser chamado. Caso contrário, o tempo de funcionamento das Apps Lógicas necessita de chamar o `unsubscribe` ponto final para sinalizar que não são esperadas mais chamadas e permitir a limpeza de recursos no lado do servidor.

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Padrões de gatilho

A sua API personalizada pode funcionar como um [*gatilho*](./logic-apps-overview.md#logic-app-concepts) que inicia uma aplicação lógica quando novos dados ou um evento cumpre uma condição especificada. Este gatilho pode verificar regularmente, ou esperar e ouvir, para novos dados ou eventos no seu ponto final de serviço. Se novos dados ou um evento satisfaçam a condição especificada, o gatilho dispara e inicia a aplicação lógica, que está a ouvir o gatilho. Para iniciar aplicações lógicas desta forma, a sua API pode seguir o [*gatilho das sondagens*](#polling-triggers) ou o padrão [*de gatilho webhook.*](#webhook-triggers) Estes padrões são semelhantes aos seus homólogos para [ações de sondagens](#async-pattern) e [ações webhook](#webhook-actions). Além disso, saiba mais sobre [a medição de utilização para gatilhos](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Verifique regularmente novos dados ou eventos com o padrão de disparo de sondagens

Um *gatilho das sondagens* age muito como a [ação eleitoral](#async-pattern) anteriormente descrita neste tema. O motor De Aplicações Lógicas liga periodicamente e verifica o ponto final do gatilho para novos dados ou eventos. Se o motor encontrar novos dados ou um evento que satisfaça a sua condição especificada, o gatilho dispara. Em seguida, o motor cria uma instância de aplicação lógica que processa os dados como entrada. 

![Padrão de disparo de sondagens](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Cada pedido de sondagem conta como uma execução de ação, mesmo quando não é criada nenhuma instância de aplicação lógica. Para evitar o processamento dos mesmos dados várias vezes, o seu gatilho deve limpar dados que já foram lidos e transmitidos para a aplicação lógica.

Aqui estão os passos específicos para um gatilho de sondagens, descritos na perspetiva da API:

| Encontrou novos dados ou evento?  | Resposta da API | 
| ------------------------- | ------------ |
| Localizado | Devolva um estado HTTP `200 OK` com a carga útil de resposta (entrada para o próximo passo). <br/>Esta resposta cria uma instância lógica de aplicação e inicia o fluxo de trabalho. | 
| Não encontrado | Devolva um estado HTTP `202 ACCEPTED` com um `location` cabeçalho e um `retry-after` cabeçalho. <br/>Para os gatilhos, o `location` cabeçalho também deve conter um `triggerState` parâmetro de consulta, que normalmente é uma "horada". A sua API pode usar este identificador para rastrear a última vez que a aplicação lógica foi ativada. | 
||| 

Por exemplo, para verificar periodicamente o seu serviço para novos ficheiros, poderá construir um gatilho de sondagens que tenha estes comportamentos:

| O pedido `triggerState` inclui? | Resposta da API | 
| -------------------------------- | -------------| 
| Não | Volte a colocar um estado HTTP `202 ACCEPTED` mais um `location` cabeçalho com a hora atual e `triggerState` o intervalo a `retry-after` 15 segundos. | 
| Sim | Verifique se o seu serviço tem ficheiros adicionados após o `DateTime` `triggerState` seguinte. | 
||| 

| Número de ficheiros encontrados | Resposta da API | 
| --------------------- | -------------| 
| Arquivo único | Devolva um estado HTTP `200 OK` e a carga útil do conteúdo, atualize para o ficheiro devolvido e `triggerState` `DateTime` desempatize `retry-after` o intervalo para 15 segundos. | 
| Vários ficheiros | Devolva um ficheiro de cada vez e um `200 OK` estado HTTP, atualize, `triggerState` e desave o `retry-after` intervalo para 0 segundos. </br>Estes passos permitem ao motor saber que há mais dados disponíveis e que o motor deve solicitar imediatamente os dados do URL no `location` cabeçalho. | 
| Sem ficheiros | Devolva um `202 ACCEPTED` estado HTTP, não altere `triggerState` , e desate o `retry-after` intervalo para 15 segundos. | 
||| 

> [!TIP]
> Por exemplo, o padrão de disparo de sondagens, reveja esta [amostra do controlador de gatilho da sondagem no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Aguarde e ouça novos dados ou eventos com o padrão de gatilho webhook

Um gatilho webhook é um *gatilho* que espera e ouve novos dados ou eventos no seu ponto final de serviço. Se novos dados ou um evento satisfaçam a condição especificada, o gatilho dispara e cria uma instância lógica da aplicação, que então processa os dados como entrada.
Os gatilhos webhook agem muito como as [ações webhook](#webhook-actions) anteriormente descritas neste tópico, e são configurado com `subscribe` e `unsubscribe` pontos finais. 

* `subscribe` ponto final: Quando adiciona e guarda um gatilho webhook na sua aplicação lógica, o motor Logic Apps chama o `subscribe` ponto final. Este passo faz com que a aplicação lógica crie um URL de retorno que a sua API armazena. Quando há novos dados ou um evento que satisfaça a condição especificada, a sua API volta a ligar com um HTTP POST para o URL. A carga útil do conteúdo e os cabeçalhos passam como entrada para a aplicação lógica.

* `unsubscribe` ponto final: Se o gatilho webhook ou a aplicação lógica inteira for eliminada, o motor De Aplicações Lógicas chama o `unsubscribe` ponto final. A sua API pode então desagregá-lo no URL de retorno e parar quaisquer processos conforme necessário.

![Padrão do gatilho webhook](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

Atualmente, o Logic App Designer não suporta a descoberta de pontos finais webhook através da Swagger. Assim, para este padrão, você tem que adicionar um gatilho [ **Webhook** ](../connectors/connectors-native-webhook.md) e especificar o URL, cabeçalhos e corpo para o seu pedido. Consulte também [o gatilho HTTPWebhook](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Para um padrão de webhook, reveja esta [amostra do controlador do gatilho webhook no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

Aqui estão outras dicas e notas:

* Para passar no URL de retorno, pode utilizar a `@listCallbackUrl()` função de fluxo de trabalho em qualquer um dos campos anteriores, se necessário.

* Para evitar o processamento dos mesmos dados várias vezes, o seu gatilho deve limpar dados que já foram lidos e transmitidos para a aplicação lógica.

* Se possuir tanto a aplicação lógica como o serviço subscrito, não precisa de ligar para o `unsubscribe` ponto final depois de o URL de retorno de chamadas ser chamado. Caso contrário, o tempo de funcionamento das Apps Lógicas necessita de chamar o `unsubscribe` ponto final para sinalizar que não são esperadas mais chamadas e permitir a limpeza de recursos no lado do servidor.

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>Melhorar a segurança das chamadas para as suas APIs a partir de aplicações lógicas

Depois de criar as suas APIs personalizadas, configurar a autenticação para as suas APIs para que possa chamá-las de forma segura a partir de aplicações lógicas. Saiba [como melhorar a segurança das chamadas para APIs personalizadas a partir de aplicações lógicas.](../logic-apps/logic-apps-custom-api-authentication.md)

## <a name="deploy-and-call-your-apis"></a>Implemente e ligue para os seus APIs

Depois de configurar a autenticação, configurar a implementação para as suas APIs. Saiba [como implementar e chamar APIs personalizados a partir de aplicações lógicas.](../logic-apps/logic-apps-custom-api-host-deploy-call.md)

## <a name="publish-custom-apis-to-azure"></a>Publicar APIs personalizados para Azure

Para disponibilizar os seus APIs personalizados para outros utilizadores de Aplicações Lógicas em Azure, tem de adicionar segurança e registá-los como conectores Logic App. Para obter mais informações, veja [Descrição geral dos conectores personalizados](../logic-apps/custom-connector-overview.md). 

Para disponibilizar os seus APIs personalizados a todos os utilizadores em Aplicações Lógicas, Power Automamate e Microsoft Power Apps, tem de adicionar segurança, registar as suas APIs como conectores Logic App e nomear os seus conectores para o [programa Microsoft Azure Certified.](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/) 

## <a name="get-support"></a>Obter suporte

* Para obter ajuda específica com APIs personalizados, [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com) contacte.

* Para dúvidas, visite a página de perguntas do [Microsoft Q&Uma página de perguntas para aplicações Azure Logic](/answers/topics/azure-logic-apps.html).

* Para ajudar a melhorar o Logic Apps, vote ou submeta ideais no [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Passos seguintes

* [Lidar com erros e exceções](../logic-apps/logic-apps-exception-handling.md)
* [Apps lógicas de chamada, gatilho ou ninho com pontos finais HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Medição de utilização para ações e gatilhos](../logic-apps/logic-apps-pricing.md)
