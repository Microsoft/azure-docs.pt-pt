---
title: Criar APIs Web & APIs REST para aplicativos lógicos do Azure
description: Criar APIs Web & APIs REST para chamar suas APIs, serviços ou sistemas para integrações do sistema em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: e4200d09a02da1fd95f9bf5051b7f9d5fca5aa98
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793220"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Criar APIs personalizadas que você pode chamar de aplicativos lógicos do Azure

Embora os aplicativos lógicos do Azure ofereçam [centenas de conectores](../connectors/apis-list.md) que você pode usar em fluxos de trabalho de aplicativo lógico, talvez você queira chamar APIs, sistemas e serviços que não estão disponíveis como conectores. Você pode criar suas próprias APIs que fornecem ações e gatilhos a serem usados em aplicativos lógicos. Aqui estão outras razões pelas quais você pode querer criar suas próprias APIs que podem ser chamadas de fluxos de trabalho do aplicativo lógico:

* Estenda os fluxos de trabalho atuais de integração de dados e de integrações do sistema.
* Ajude os clientes a usar seu serviço para gerenciar tarefas profissionais ou pessoais.
* Expanda o alcance, a descoberta e o uso do seu serviço.

Basicamente, os conectores são APIs Web que usam REST para interfaces conectáveis, o [formato de metadados do Swagger](https://swagger.io/specification/) para documentação e JSON como seu formato de troca de dados. Como os conectores são APIs REST que se comunicam por meio de pontos de extremidade HTTP, você pode usar qualquer linguagem, como .NET, Java, Python ou node. js, para criar conectores. Você também pode hospedar suas APIs no [serviço de Azure app](../app-service/overview.md), uma oferta de PaaS (plataforma como serviço) que fornece uma das maneiras melhores, mais fáceis e mais escalonáveis para hospedagem de API. 

Para que as APIs personalizadas funcionem com os aplicativos lógicos, sua API pode fornecer [*ações*](./logic-apps-overview.md#logic-app-concepts) que executam tarefas específicas em fluxos de trabalho de aplicativo lógico. Sua API também pode atuar como um [*gatilho*](./logic-apps-overview.md#logic-app-concepts) que inicia um fluxo de trabalho de aplicativo lógico quando novos dados ou um evento atendem a uma condição especificada. Este tópico descreve os padrões comuns que você pode seguir para criar ações e gatilhos em sua API, com base no comportamento que você deseja que sua API forneça.

Você pode hospedar suas APIs no [serviço de Azure app](../app-service/overview.md), uma oferta de PaaS (plataforma como serviço) que fornece Hospedagem de API altamente escalonável e fácil.

> [!TIP] 
> Embora você possa implantar suas APIs como aplicativos Web, considere implantar suas APIs como aplicativos de API, o que pode facilitar o trabalho quando você criar, hospedar e consumir APIs na nuvem e no local. Você não precisa alterar nenhum código em suas APIs – basta implantar seu código em um aplicativo de API. Por exemplo, saiba como criar aplicativos de API criados com estes idiomas: 
> 
> * [ASP.net](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> Para exemplos de aplicativos de API criados para aplicativos lógicos, visite o [blog](https://aka.ms/logicappsblog)ou o [repositório GitHub de aplicativos lógicos do Azure](https://github.com/logicappsio) .

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Como as APIs personalizadas diferem dos conectores personalizados?

APIs personalizadas e [conectores personalizados](../logic-apps/custom-connector-overview.md) são APIs Web que usam REST para interfaces conectáveis, [formato de metadados do Swagger](https://swagger.io/specification/) para documentação e JSON como seu formato de troca de dados. E como essas APIs e conectores são APIs REST que se comunicam por meio de pontos de extremidade HTTP, você pode usar qualquer linguagem, como .NET, Java, Python ou node. js, para criar APIs e conectores personalizados.

As APIs personalizadas permitem chamar APIs que não são conectores e fornecer pontos de extremidade que você pode chamar com HTTP + Swagger, gerenciamento de API do Azure ou serviços de aplicativos. Os conectores personalizados funcionam como APIs personalizadas, mas também têm estes atributos:

* Registrado como recursos do conector de aplicativos lógicos no Azure.
* Aparecem com ícones ao lado de conectores gerenciados pela Microsoft no designer de aplicativos lógicos.
* Disponível somente para os autores dos conectores e os usuários do aplicativo lógico que têm o mesmo locatário Azure Active Directory e a assinatura do Azure na região em que os aplicativos lógicos são implantados.

Você também pode indicar conectores registrados para certificação da Microsoft. Esse processo verifica se os conectores registrados atendem aos critérios de uso público e disponibilizam esses conectores para os usuários na energia automatizada e no Microsoft Power apps.

Para obter mais informações sobre conectores personalizados, consulte 

* [Descrição geral dos conectores personalizados](../logic-apps/custom-connector-overview.md)
* [Criar conectores personalizados de APIs Web](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Registrar conectores personalizados em aplicativos lógicos do Azure](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Ferramentas úteis

Uma API personalizada funciona melhor com aplicativos lógicos quando a API também tem um [documento do Swagger](https://swagger.io/specification/) que descreve as operações e os parâmetros da API.
Muitas bibliotecas, como [swashbuckle](https://github.com/domaindrivendev/Swashbuckle), podem gerar automaticamente o arquivo do Swagger para você. Para anotar o arquivo do Swagger para nomes de exibição, tipos de propriedade e assim por diante, você também pode usar [Trex](https://github.com/nihaue/TRex) para que o arquivo do Swagger funcione bem com os aplicativos lógicos.

<a name="actions"></a>

## <a name="action-patterns"></a>Padrões de ação

Para que os aplicativos lógicos executem tarefas, sua API personalizada deve fornecer [*ações*](./logic-apps-overview.md#logic-app-concepts). Cada operação em sua API é mapeada para uma ação. Uma ação básica é um controlador que aceita solicitações HTTP e retorna respostas HTTP. Por exemplo, um aplicativo lógico envia uma solicitação HTTP para seu aplicativo Web ou aplicativo de API. Em seguida, seu aplicativo retorna uma resposta HTTP, juntamente com o conteúdo que o aplicativo lógico pode processar.

Para uma ação padrão, você pode escrever um método de solicitação HTTP em sua API e descrever esse método em um arquivo do Swagger. Em seguida, você pode chamar sua API diretamente com uma [ação http](../connectors/connectors-native-http.md) ou uma ação [http + Swagger](../connectors/connectors-native-http-swagger.md) . Por padrão, as respostas devem ser retornadas dentro do tempo limite da [solicitação](./logic-apps-limits-and-config.md). 

![Padrão de ação padrão](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a>Para fazer com que um aplicativo lógico aguarde enquanto sua API conclui tarefas de execução mais longa, sua API pode seguir o [padrão de sondagem assíncrona](#async-pattern) ou o [padrão de webhook assíncrono](#webhook-actions) descrito neste tópico. Para uma analogia que ajuda a visualizar esses comportamentos diferentes de padrões, imagine o processo de ordenar um bolo personalizado de uma padaria. O padrão de sondagem espelha o comportamento em que você chama a padaria a cada 20 minutos para verificar se o bolo está pronto. O padrão de webhook reflete o comportamento em que a padaria solicita seu número de telefone para que possa chamá-lo quando o bolo estiver pronto.

Para obter exemplos, visite o [repositório GitHub dos aplicativos lógicos](https://github.com/logicappsio). Além disso, saiba mais sobre [a medição de uso para ações](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Executar tarefas de execução longa com o padrão de ação de sondagem

Para que sua API Execute tarefas que poderiam ser executadas por mais tempo do que o [tempo limite da solicitação](./logic-apps-limits-and-config.md), você pode usar o padrão de sondagem assíncrona. Esse padrão tem sua API para trabalhar em um thread separado, mas manter uma conexão ativa com o mecanismo de aplicativos lógicos. Dessa forma, o aplicativo lógico não atingirá o tempo limite ou continuará com a próxima etapa no fluxo de trabalho antes que sua API termine de funcionar.

Aqui está o padrão geral:

1. Certifique-se de que o mecanismo saiba que sua API aceitou a solicitação e iniciou o trabalho.
2. Quando o mecanismo faz solicitações subsequentes para o status do trabalho, permita que o mecanismo saiba quando a sua API conclui a tarefa.
3. Retornar dados relevantes para o mecanismo para que o fluxo de trabalho do aplicativo lógico possa continuar.

<a name="bakery-polling-action"></a>Agora, aplique a analogia da padaria anterior ao padrão de sondagem e imagine que você chame uma padaria e solicite um bolo personalizado para entrega. O processo para fazer o bolo levará tempo e você não quer esperar pelo telefone enquanto a padaria trabalha no bolo. A padaria confirma seu pedido e você chama a cada 20 minutos para o status do bolo. Após 20 minutos, você chama a padaria, mas informa que seu bolo não está pronto e que você deve chamar em outros 20 minutos. Esse processo de backup continua até que você chame, e a padaria informa que seu pedido está pronto e entrega seu bolo. 

Então, vamos mapear esse padrão de sondagem de volta. A padaria representa sua API personalizada, enquanto você, o cliente de bolo, representa o mecanismo de aplicativos lógicos. Quando o mecanismo chama sua API com uma solicitação, sua API confirma a solicitação e responde com o intervalo de tempo quando o mecanismo pode verificar o status do trabalho. O mecanismo continua verificando o status do trabalho até que sua API responda que o trabalho está concluído e retorna dados para seu aplicativo lógico, o que continua o fluxo. 

![Padrão de ação de sondagem](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Aqui estão as etapas específicas para a sua API seguir, descritas na perspectiva da API:

1. Quando sua API recebe uma solicitação HTTP para iniciar o trabalho, retorna imediatamente uma resposta HTTP `202 ACCEPTED` com o cabeçalho `location` descrito mais adiante nesta etapa. Essa resposta permite que o mecanismo de aplicativos lógicos saiba que sua API obteve a solicitação, aceitou a carga de solicitação (entrada de dados) e agora está processando. 
   
   A resposta `202 ACCEPTED` deve incluir estes cabeçalhos:
   
   * *Obrigatório*: um cabeçalho de `location` que especifica o caminho absoluto para uma URL em que o mecanismo de aplicativos lógicos pode verificar o status do trabalho da sua API

   * *Opcional*: um cabeçalho de `retry-after` que especifica o número de segundos que o mecanismo deve aguardar antes de verificar a URL de `location` para o status do trabalho. 

     Por padrão, o mecanismo verifica a cada 20 segundos. Para especificar um intervalo diferente, inclua o cabeçalho `retry-after` e o número de segundos até a próxima sondagem.

2. Após o tempo especificado passar, o mecanismo de aplicativos lógicos sonda a URL de `location` para verificar o status do trabalho. Sua API deve executar essas verificações e retornar essas respostas:
   
   * Se o trabalho for concluído, retorne uma resposta HTTP `200 OK`, juntamente com a carga de resposta (entrada para a próxima etapa).

   * Se o trabalho ainda estiver sendo processado, retorne outra resposta de `202 ACCEPTED` HTTP, mas com os mesmos cabeçalhos da resposta original.

Quando sua API segue esse padrão, você não precisa fazer nada na definição de fluxo de trabalho do aplicativo lógico para continuar verificando o status do Job. Quando o mecanismo recebe uma resposta HTTP `202 ACCEPTED` e um cabeçalho de `location` válido, o mecanismo respeita o padrão assíncrono e verifica o cabeçalho `location` até que sua API retorne uma resposta diferente de 202.

> [!TIP]
> Para obter um exemplo de padrão assíncrono, examine este [exemplo de resposta do controlador assíncrono no GitHub](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Executar tarefas de execução longa com o padrão de ação de webhook

Como alternativa, você pode usar o padrão de webhook para tarefas de longa execução e processamento assíncrono. Esse padrão tem o aplicativo lógico pausar e aguardar um "retorno de chamada" de sua API para concluir o processamento antes de continuar o fluxo de trabalho. Esse retorno de chamada é um HTTP POST que envia uma mensagem a uma URL quando ocorre um evento. 

<a name="bakery-webhook-action"></a>Agora, aplique a analogia da padaria anterior ao padrão de webhook e imagine que você chame uma padaria e solicite um bolo personalizado para entrega. O processo para fazer o bolo levará tempo e você não quer esperar pelo telefone enquanto a padaria trabalha no bolo. A padaria confirma seu pedido, mas, desta vez, você concede seu número de telefone para que ele possa chamá-lo quando o bolo for concluído. Desta vez, a padaria informa quando seu pedido está pronto e entrega seu bolo.

Quando mapeamos esse padrão de webhook de volta, a padaria representa a API personalizada, enquanto você, o cliente de bolo, representa o mecanismo de aplicativos lógicos. O mecanismo chama sua API com uma solicitação e inclui uma URL de "retorno de chamada".
Quando o trabalho é concluído, sua API usa a URL para notificar o mecanismo e retornar dados para seu aplicativo lógico, que continua o fluxo de trabalho. 

Para esse padrão, configure dois pontos de extremidade em seu controlador: `subscribe` e `unsubscribe`

*  ponto de extremidade `subscribe`: quando a execução atinge a ação da API no fluxo de trabalho, o mecanismo de aplicativos lógicos chama o ponto de extremidade `subscribe`. Esta etapa faz com que o aplicativo lógico crie uma URL de retorno de chamada que sua API armazena e, em seguida, aguarde o retorno de chamada da sua API quando o trabalho for concluído. Em seguida, sua API chama novamente com um HTTP POST para a URL e passa qualquer conteúdo e cabeçalho retornados como entrada para o aplicativo lógico.

* `unsubscribe` ponto de extremidade: se a execução do aplicativo lógico for cancelada, o mecanismo dos aplicativos lógicos chamará o ponto de extremidade `unsubscribe`. Sua API pode cancelar o registro da URL de retorno de chamada e interromper todos os processos conforme necessário.

![Padrão de ação de webhook](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Atualmente, o designer de aplicativo lógico não dá suporte à descoberta de pontos de extremidade de webhook por meio do Swagger. Portanto, para esse padrão, você precisa adicionar uma [ação de **webhook** ](../connectors/connectors-native-webhook.md) e especificar a URL, os cabeçalhos e o corpo da solicitação. Consulte também [ações e gatilhos de fluxo de trabalho](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). Para passar a URL de retorno de chamada, você pode usar a função de fluxo de trabalho `@listCallbackUrl()` em qualquer um dos campos anteriores, conforme necessário.

> [!TIP]
> Para um padrão de webhook de exemplo, examine este [exemplo de gatilho de webhook no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Padrões de gatilho

Sua API personalizada pode atuar como um [*gatilho*](./logic-apps-overview.md#logic-app-concepts) que inicia um aplicativo lógico quando novos dados ou um evento atendem a uma condição especificada. Esse gatilho pode verificar regularmente ou aguardar e escutar, para novos dados ou eventos em seu ponto de extremidade de serviço. Se novos dados ou um evento atenderem à condição especificada, o gatilho será acionado e iniciará o aplicativo lógico, que está ouvindo esse gatilho. Para iniciar aplicativos lógicos dessa forma, sua API pode seguir o [*gatilho de sondagem*](#polling-triggers) ou o padrão de [*gatilho de webhook*](#webhook-triggers) . Esses padrões são semelhantes às suas contrapartes para [ações de sondagem](#async-pattern) e [ações de webhook](#webhook-actions). Além disso, saiba mais sobre [a medição de uso para gatilhos](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Verificar se há novos dados ou eventos regularmente com o padrão de gatilho de sondagem

Um *gatilho de sondagem* funciona muito como a [ação de sondagem](#async-pattern) descrita anteriormente neste tópico. O mecanismo de aplicativos lógicos chama periodicamente e verifica o ponto de extremidade do gatilho para novos dados ou eventos. Se o mecanismo encontrar novos dados ou um evento que atenda à condição especificada, o gatilho será acionado. Em seguida, o mecanismo cria uma instância de aplicativo lógico que processa os dados como entrada. 

![Padrão de gatilho de sondagem](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Cada solicitação de sondagem conta como uma execução de ação, mesmo quando nenhuma instância de aplicativo lógico é criada. Para evitar o processamento dos mesmos dados várias vezes, o gatilho deve limpar os dados que já foram lidos e passados para o aplicativo lógico.

Aqui estão as etapas específicas para um gatilho de sondagem, descrito na perspectiva da API:

| Encontrou novos dados ou eventos?  | Resposta da API | 
| ------------------------- | ------------ |
| Considerado | Retornar um status de `200 OK` HTTP com a carga de resposta (entrada para a próxima etapa). <br/>Essa resposta cria uma instância de aplicativo lógico e inicia o fluxo de trabalho. | 
| Não encontrado | Retornar um status de `202 ACCEPTED` HTTP com um cabeçalho `location` e um cabeçalho `retry-after`. <br/>Para gatilhos, o cabeçalho de `location` também deve conter um parâmetro de consulta `triggerState`, que geralmente é um "carimbo de data/hora". Sua API pode usar esse identificador para acompanhar a última vez que o aplicativo lógico foi disparado. | 
||| 

Por exemplo, para verificar periodicamente se há novos arquivos no serviço, você pode criar um gatilho de sondagem que tenha estes comportamentos:

| A solicitação inclui `triggerState`? | Resposta da API | 
| -------------------------------- | -------------| 
| Não | Retorne um status de `202 ACCEPTED` HTTP mais um cabeçalho de `location` com `triggerState` definido como a hora atual e o intervalo de `retry-after` para 15 segundos. | 
| Sim | Verifique o serviço em busca de arquivos adicionados após o `DateTime` para `triggerState`. | 
||| 

| Número de arquivos encontrados | Resposta da API | 
| --------------------- | -------------| 
| Arquivo único | Retorne um status de `200 OK` HTTP e a carga de conteúdo, atualize `triggerState` para o `DateTime` do arquivo retornado e defina `retry-after` intervalo como 15 segundos. | 
| Vários arquivos | Retorne um arquivo por vez e um status de `200 OK` HTTP, atualize `triggerState`e defina o intervalo de `retry-after` como 0 segundos. </br>Essas etapas permitem que o mecanismo saiba que mais dados estão disponíveis e que o mecanismo deve solicitar imediatamente os dados da URL no cabeçalho `location`. | 
| Nenhum arquivo | Retorne um status de `202 ACCEPTED` HTTP, não altere `triggerState`e defina o intervalo de `retry-after` como 15 segundos. | 
||| 

> [!TIP]
> Para obter um padrão de gatilho de sondagem de exemplo, examine este [exemplo de controlador de gatilho de sondagem no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Aguardar e escutar novos dados ou eventos com o padrão de gatilho de webhook

Um gatilho de webhook é um *gatilho de push* que aguarda e escuta novos dados ou eventos em seu ponto de extremidade de serviço. Se novos dados ou um evento atenderem à condição especificada, o gatilho será acionado e criará uma instância de aplicativo lógico, que processará os dados como entrada.
Os gatilhos de webhook agem de forma muito parecida com as [ações de webhook](#webhook-actions) descritas anteriormente neste tópico e são configuradas com `subscribe` e `unsubscribe` pontos de extremidade. 

* ponto de extremidade `subscribe`: quando você adiciona e salva um gatilho de webhook em seu aplicativo lógico, o mecanismo de aplicativos lógicos chama o ponto de extremidade `subscribe`. Esta etapa faz com que o aplicativo lógico crie uma URL de retorno de chamada que sua API armazena. Quando há novos dados ou um evento que atende à condição especificada, sua API chama novamente com um HTTP POST para a URL. A carga de conteúdo e os cabeçalhos são aprovados como entrada para o aplicativo lógico.

* `unsubscribe` ponto de extremidade: se o gatilho de webhook ou o aplicativo lógico inteiro for excluído, o mecanismo de aplicativos lógicos chamará o ponto de extremidade `unsubscribe`. Sua API pode cancelar o registro da URL de retorno de chamada e interromper todos os processos conforme necessário.

![Padrão de gatilho de webhook](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Atualmente, o designer de aplicativo lógico não dá suporte à descoberta de pontos de extremidade de webhook por meio do Swagger. Portanto, para esse padrão, você precisa adicionar um [gatilho de **webhook** ](../connectors/connectors-native-webhook.md) e especificar a URL, os cabeçalhos e o corpo da solicitação. Confira também [gatilho HTTPWebhook](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). Para passar a URL de retorno de chamada, você pode usar a função de fluxo de trabalho `@listCallbackUrl()` em qualquer um dos campos anteriores, conforme necessário.
>
> Para evitar o processamento dos mesmos dados várias vezes, o gatilho deve limpar os dados que já foram lidos e passados para o aplicativo lógico.

> [!TIP]
> Para um padrão de webhook de exemplo, examine este [exemplo de controlador de gatilho de webhook no GitHub](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="secure-calls-to-your-apis-from-logic-apps"></a>Proteger chamadas para suas APIs de aplicativos lógicos

Depois de criar suas APIs personalizadas, configure a autenticação para suas APIs para que você possa chamá-las com segurança dos aplicativos lógicos. Saiba [como proteger chamadas para APIs personalizadas de aplicativos lógicos](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Implantar e chamar suas APIs

Depois de configurar a autenticação, configure a implantação para suas APIs. Saiba [como implantar e chamar APIs personalizadas de aplicativos lógicos](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Publicar APIs personalizadas no Azure

Para disponibilizar suas APIs personalizadas para outros usuários de aplicativos lógicos no Azure, você deve adicionar segurança e registrá-las como conectores de aplicativos lógicos. Para obter mais informações, veja [Descrição geral dos conectores personalizados](../logic-apps/custom-connector-overview.md). 

Para disponibilizar suas APIs personalizadas para todos os usuários em aplicativos lógicos, Power Automate e Microsoft Power apps, você deve adicionar segurança, registrar suas APIs como conectores de aplicativos lógicos e indicar seus conectores para o [programa Microsoft Azure certificado](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Obter suporte

* Para obter ajuda específica com APIs personalizadas, entre em contato com [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ajudar a melhorar o Logic Apps, vote ou submeta ideais no [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>Passos seguintes

* [Lidar com erros e exceções](../logic-apps/logic-apps-exception-handling.md)
* [Chamar, disparar ou aninhar aplicativos lógicos com pontos de extremidade HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Medição de uso para ações e gatilhos](../logic-apps/logic-apps-pricing.md)
