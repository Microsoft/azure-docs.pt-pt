---
title: Agendar e executar tarefas automatizadas e fluxos de trabalho com o Azure Logic Apps | Documentos da Microsoft
description: Automatize tarefas agendadas e recorrentes com o conector de periodicidade no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
tags: connectors
ms.topic: article
ms.date: 01/08/2019
ms.openlocfilehash: eb22539d1f433e396935f82e4cb3786d5699d21a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083959"
---
# <a name="create-and-run-recurring-tasks-and-workflows-with-azure-logic-apps"></a>Criar e executar tarefas recorrentes e fluxos de trabalho com o Azure Logic Apps

Para agendar ações, cargas de trabalho ou processos executados regularmente, criar um fluxo de trabalho de aplicação lógica que começa com o **Schedule - Recurrence** [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts). Pode definir uma data e hora para iniciar o fluxo de trabalho e uma agenda de periodicidade para a execução de tarefas, como nestes exemplos e muito mais:

* Obter dados internos: [Executar um procedimento SQL armazenado](../connectors/connectors-create-api-sqlazure.md) todos os dias.
* Obter dados externos: Obter relatórios de meteorologia da NOAA a cada 15 minutos.
* Dados de relatório: Um resumo de todas as encomendas de maiores do que uma quantidade específica de e-mail na última semana.
* Processamento de dados: Comprima imagens carregadas de hoje em dia de cada dia da semana durante as horas de pico.
* Limpe os dados: Elimine todos os tweets com mais de três meses.
* Dados de arquivo: Envie notas fiscais para um serviço de cópia de segurança de todos os meses.

Este acionador suporta vários padrões, por exemplo:

* Executar imediatamente e repetir cada *n* o número de segundos, minutos, horas, dias semanas ou meses.
* Inicie num momento específico, em seguida, executar e repetir cada *n* o número de segundos, minutos, horas, dias, semanas ou meses.
* Execute e repetida numa ou mais vezes por dia, por exemplo, às 8:00 e as 17:00.
* Execute e repetir todas as semanas, mas apenas para dias específicos, como Sábado e Domingo.
* Execute e repetir todas as semanas, mas apenas para dias específicos e horas, por exemplo, de segunda a sexta, às 8:00 e as 17:00.

Quando o acionador de periodicidade é acionado cada vez, o Logic Apps cria e executa uma nova instância do seu fluxo de trabalho de aplicação lógica. 

Para acionar a sua aplicação lógica e executar apenas uma vez no futuro, consulte [executar tarefas de uma única vez](#run-once) mais adiante neste tópico.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Caso contrário, pode [inscrever-se numa subscrição Pay As You Go](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="add-a-recurrence-trigger-to-your-logic-app"></a>Adicionar um acionador de periodicidade para a sua aplicação lógica

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Criar uma aplicação lógica em branco ou Saiba [como criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Depois de aparecer um estruturador de aplicações lógicas, na caixa de pesquisa, escolha **todos os**. Na caixa de pesquisa, introduza "recurrence" como o filtro. Na lista de disparadores, selecione este acionador: **Periodicidade - agenda** 

   ![Selecione o acionador "– de periodicidade"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

   Este acionador agora é a primeira etapa na sua aplicação lógica.

3. Defina o intervalo e a frequência da periodicidade. Neste exemplo, defina estas propriedades para executar o fluxo de trabalho todas as semanas. 

   ![Definir intervalo e a frequência](./media/connectors-native-recurrence/recurrence-trigger-details.png)

4. Para obter mais opções de agendamento, escolha **Mostrar opções avançadas**. 

   ![Mais opções](./media/connectors-native-recurrence/recurrence-trigger-more-options.png)

5. Agora pode definir estas opções: 

   * Defina uma data de início e hora para a ativação do acionador. 
   Se especificar uma data de início e hora, também pode aplicar um fuso horário. 

   * Se selecionar "Dia" ou "Week" para a frequência, pode selecionar horas específicas para a periodicidade. 

   * Se selecionar "Week", pode selecionar demasiado dias específicos da semana.
   
   ![Opções de agendamento avançadas](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   Por exemplo, suponha que hoje é segunda-feira, 4 de Setembro de 2017. 
   O acionador de periodicidade seguintes não dispara *antes* que a data de início e a hora, que é a segunda-feira, 18 de Setembro de 2017 em 8 4:00 PST. 
   No entanto, o agendamento de periodicidade é definido para 10:30, 12 17:30 e 2 17:30, apenas às segundas-feiras. Então, o é a primeira vez que o acionador é acionado e cria uma instância de fluxo de trabalho de aplicação lógica é parte 10:30. 
   Para saber mais sobre como o trabalho de tempos de início, vê-los [iniciar exemplos de tempo](#start-time).
   Execuções futuras acontecem em 12 17:30 e 2 17:30, no mesmo dia. 
   Cada periodicidade cria sua própria instância do fluxo de trabalho. Depois disso, a agenda toda repete tudo sobre novamente próxima segunda-feira. 
   [*Quais são algumas outras ocorrências de exemplo?*](#example-recurrences)

   ![Exemplo de programação avançado](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > O acionador mostra uma pré-visualização para a periodicidade especificada só quando selecionar "Dia" ou "Week" como a frequência.
   
6. Agora, crie o seu fluxo de trabalho restante com as ações ou instruções de controlo de fluxo. Para obter mais ações que pode adicionar, ver [conectores](../connectors/apis-list.md). 

## <a name="trigger-details"></a>Detalhes do acionador

Pode configurar estas propriedades para o acionador de periodicidade.

| Name | Necessário | Nome da propriedade | Type | Descrição | 
|----- | -------- | ------------- | ---- | ----------- | 
| **Frequência** | Sim | frequência | String | A unidade de tempo para a periodicidade: **Segunda**, **minuto**, **hora**, **dia**, **semana**, ou **mês** | 
| **Intervalo** | Sim | intervalo | Número inteiro | Um número inteiro que descreve a frequência com que o fluxo de trabalho é executada com base na frequência. <p>O intervalo predefinido é 1. Seguem-se os intervalos mínimos e máximo: <p>-Mês: 1-16 meses </br>-Dia: dias de 1 a 500 </br>-Hora: 1-12 000 horas </br>-Minuto: 1-72,000 minutos </br>-Segundo: 1-9,999,999 segundos<p>Por exemplo, se o intervalo é de 6 e a frequência é "Mês", em seguida, a periodicidade é a cada 6 meses. | 
| **Time zone** (Fuso horário) | Não | timeZone | String | Aplica-se apenas quando especificar uma hora de início porque este acionador não aceita [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que pretende aplicar. | 
| **Start time** (Hora de início) | Não | startTime | String | Forneça uma hora de início no seguinte formato: <p>AAAA-MM-ddTHH se selecionar um fuso horário <p>-ou- <p>AAAA-MM-: ssZ se não selecionar um fuso horário <p>Por exemplo, se quiser 18 de Setembro de 2017, às 14:00, em seguida, especifique "2017-09-18T14:00:00" e selecione um fuso horário, como a hora do Pacífico. Em alternativa, especificar "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Nota:** A hora de início tem de seguir a [especificação de tempo de data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) na [formato de hora UTC data](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem uma [posun UTC místního](https://en.wikipedia.org/wiki/UTC_offset). Se não selecionar um fuso horário, tem de adicionar a letra "Z" no final, sem quaisquer espaços. Este "Z" refere-se para o equivalente [tempo nautical](https://en.wikipedia.org/wiki/Nautical_time). <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto para agendas complexas, o acionador não dispara qualquer mais cedo do que a hora de início. [*Quais são as formas que posso usar a data de início e hora?*](#start-time) | 
| **On these days** (Nestes dias) | Não | weekDays | Cadeia de caracteres ou matriz de cadeia de caracteres | Se selecionar "Week", pode selecionar um ou mais dias, quando quiser executar o fluxo de trabalho: **Segunda-feira**, **Terça-feira**, **quarta-feira**, **Quinta-feira**, **sexta-feira**, **Sábado**, e **Domingo** | 
| **At these hours** (A estas horas) | Não | hours | Número inteiro ou matriz de números inteiros | Se selecionar "Dia" ou "Week", pode selecionar números inteiros de um ou mais de 0 e 23 horas do dia que deseja executar o fluxo de trabalho. <p>Por exemplo, se especificar "10", "12" e "14", obter 10 AM e PM 2 como as marcas de hora 12 PM. | 
| **At these minutes** (A estes minutos) | Não | minutes | Número inteiro ou matriz de números inteiros | Se selecionar "Dia" ou "Week", pode selecionar números inteiros de um ou mais de 0 e 59 minutos da hora quando quiser executar o fluxo de trabalho. <p>Por exemplo, pode especificar "30" como a marca de minuto e usando o exemplo anterior de horas do dia, obtém 10:30, 12 17:30 e 2 17:30. | 
||||| 

## <a name="json-example"></a>Exemplo de JSON

Eis um exemplo [definição de Acionador de periodicidade](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger):

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="faq"></a>FAQ

<a name="run-once"></a>

**P:** E se eu quiser executar uma aplicação lógica, uma vez apenas no futuro? </br>
**R:** Para acionar a sua aplicação lógica e executar uma vez sem recorrente, pode usar o **Scheduler: Executar tarefas de uma vez** modelo. Depois de criar uma nova aplicação lógica, mas antes de abrir o estruturador do Logic Apps, no **modelos** secção, da **categoria** lista, selecione **agenda**e, em seguida, selecione o modelo:

![Selecione "Scheduler: Modelo de executar uma vez tarefas"](./media/connectors-native-recurrence/choose-run-once-template.png)

Ou, se estiver a utilizar um modelo de aplicação lógica em branco, iniciar a aplicação lógica com o **pedido de HTTP de quando é recebido - pedido** acionador. Hora de início do acionador de passar como parâmetro. Para a próxima etapa, adicione a **atraso até – agendar** ação e fornecer a hora de quando a próxima ação entrar em execução.

<a name="example-recurrences"></a>

**P:** Quais são outras agendas de periodicidade de exemplo? </br>
**R:** Eis mais exemplos:

| Recorrência | Intervalo | Frequência | Hora de início | Nestes dias | A estas horas | A estes minutos | Nota |
| ---------- | -------- | --------- | ---------- | ------------- | -------------- | ---------------- | ---- |
| Executar a cada 15 minutos (sem data de início e hora) | 15 | Minuto | {num} | {unavailable} | {num} | {num} | Esta agenda começa imediatamente, em seguida, calcula recorrências futuros com base na última hora de execução. | 
| Executar a cada 15 minutos (com data de início e hora) | 15 | Minuto | *startDate*T*startTime*Z | {unavailable} | {num} | {num} | Esta agenda não começa *antes* que a data de início especificada e a hora, em seguida, calcula recorrências futuros com base na última hora de execução. | 
| Executar hora a hora, à hora certa (com data de início e hora) | 1 | Hora | *startDate*Thh:00:00Z | {unavailable} | {num} | {num} | Esta agenda não começa *antes* especificado de data e hora de início. Recorrências futuras executam a cada hora na marca de minuto "00". <p>Se a frequência for "Week" ou "Month", esta agenda respectivamente é executado apenas um dia por semana ou um dia por mês. | 
| Executar hora a hora, diariamente (sem data de início e hora) | 1 | Hora | {num} | {unavailable} | {num} | {num} | Esta agenda começa imediatamente e calcula recorrências futuros com base na última hora de execução. <p>Se a frequência for "Week" ou "Month", esta agenda respectivamente é executado apenas um dia por semana ou um dia por mês. | 
| Executar hora a hora, diariamente (com data de início e hora) | 1 | Hora | *startDate*T*startTime*Z | {unavailable} | {num} | {num} | Esta agenda não começa *antes* que a data de início especificada e a hora, em seguida, calcula recorrências futuros com base na última hora de execução. <p>Se a frequência for "Week" ou "Month", esta agenda respectivamente é executado apenas um dia por semana ou um dia por mês. | 
| Executar a cada 15 minutos decorridos desde a hora, a cada hora (com data de início e hora) | 1 | Hora | *startDate*T00:15:00Z | {unavailable} | {num} | {num} | Esta agenda não começa *antes* que o especificado começar a data e hora, em execução em 00:15 AM, 1:15 AM, 2:15 AM e assim por diante. | 
| Executar a cada 15 minutos após a hora, a cada hora (sem data de início e hora) | 1 | Dia | {num} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Esta agenda é executada às 00:15 AM, 1:15 AM, 2:15 AM, e assim por diante. Além disso, esta agenda equivale a uma frequência de "Hour" e uma hora de início com "15" minutos. | 
| Executar a cada 15 minutos na marca de 15 minutos (sem data de início e hora) | 1 | Dia | {num} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esta agenda não iniciada até a próxima especificado marca de 15 minutos. | 
| Executar às 8:00, todos os dias (sem data de início e hora) | 1 | Dia | {num} | {unavailable} | 8 | {num} | Esta agenda é executada às 8:00, todos os dias, com base na agenda especificada. | 
| Executar às 8:00, todos os dias (com data de início e hora) | 1 | Dia | *startDate*T08:00:00Z | {unavailable} | {num} | {num} | Esta agenda é executada 8:00, todos os dias, com base na hora de início especificada. | 
| Executar às 8:00: 30 todos os dias (sem data de início e hora) | 1 | Dia | {num} | {unavailable} | 8 | 30 | Esta agenda é executado às 8:00: 30 todos os dias, com base na agenda especificada. | 
| Executar às 8:00: 30 todos os dias (com data de início e hora) | 1 | Dia | *startDate*T08:30:00Z | {unavailable} | {num} | {num} | Esta agenda é iniciado na data de início especificada às 8:00: 30. | 
| Executar às 8 às 9:30h e 4:30 PM todos os dias | 1 | Dia | {num} | {unavailable} | 8, 16 | 30 | | 
| Executar às 8 às 11:30, 8 45 AM, 4:30 PM e 4 às 17:45 todos os dias | 1 | Dia | {num} | {unavailable} | 8, 16 | 30, 45 | | 
| Executar todos os sábados às 17:00, (sem data de início e hora) | 1 | Week (Semana) | {num} | "Sábado" | 17 | 00 | Esta agenda é executada todos os sábados às 17:00: 00. | 
| Executar todos os sábados às 17:00 (com data de início e hora) | 1 | Week (Semana) | *startDate*T17:00:00Z | "Sábado" | {num} | {num} | Esta agenda não começa *antes* especificado que data e hora de início, neste caso, 9 de Setembro de 2017, às 17:00. Recorrências futuras executam todos os sábados às 17:00. | 
| Executar toda Terça-feira, Quinta-feira às 17:00 | 1 | Week (Semana) | {num} | "Terça-feira", "Quinta-feira" | 17 | {num} | Esta agenda é executada todas as Terças e Quintas-feiras às 17:00. | 
| Executar a cada hora durante o horário de trabalho | 1 | Week (Semana) | {num} | Selecione todos os dias, exceto Sábado e Domingo. | Selecione as horas do dia que pretende. | Selecione qualquer minutos da hora em que pretende. | Por exemplo, se as horas de trabalho são 8 da Manhã para 5:21 horas, em seguida, selecione "8, 9, 10, 11, 12, 13, 14, 15, 16 e 17" como as horas do dia. <p>Se as horas de trabalho são 8 às 9:30h para 5:30 PM, selecione as horas anteriores do dia mais "30" como minutos da hora. | 
| Executar uma vez, todos os dias de semana | 1 | Week (Semana) | {num} | "Sábado", "Domingo" | Selecione as horas do dia que pretende. | Selecione qualquer minutos da hora conforme apropriado. | Esta agenda é executada cada Sábado e Domingo à agenda especificada. | 
| Executar a cada 15 minutos bissemanais apenas às segundas-feiras | 2 | Week (Semana) | {num} | "Segunda-feira" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esta agenda é executada todas as outras segunda-feira à marca cada 15 minutos. | 
| Executar hora a hora durante um dia por mês | 1 | Mês | {consulte nota} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {consulte nota} | Se não especificar uma data de início e hora, esta agenda utiliza a data de criação e a hora. Para controlar os minutos para a agenda de periodicidade, especifique os minutos da hora, uma hora de início, ou utilizar a hora de criação. Por exemplo, se o início, hora ou a hora de criação é 8:25 AM, executa esta agenda às: 25 8:00, às 09:: 25, 10 25 AM, e assim por diante. | 
||||||||| 

<a name="start-time"></a>

**P:** Quais são as formas que posso usar a data de início e hora? </br>
**R:** Aqui estão alguns padrões que mostram como pode controlar a periodicidade com a data de início e a hora e como o motor do Logic Apps executa estas recorrências:

| Hora de início | Periodicidade sem agenda | Periodicidade com agenda | 
| ---------- | --------------------------- | ------------------------ | 
| {num} | Executa a primeira carga de trabalho de forma instantânea. <p>Executa cargas de trabalho futuras, com base na última hora de execução. | Executa a primeira carga de trabalho de forma instantânea. <p>Executa cargas de trabalho futuras, com base na agenda especificada. | 
| Hora de início no passado | Calcula a tempos de execução com base na hora de início especificada e tempos de rejeições últimos a ser executados. Executa a primeira carga de trabalho para o futuro próximo tempo de execução. <p>Executa cargas de trabalho futuras, com base nos cálculos da última hora de execução. <p>Para mais explicações, veja o exemplo a seguir a esta tabela. | É executada a carga de trabalho primeiro *não é acionado antes* à hora de início, com base na agenda calculada a partir da hora de início. <p>Executa cargas de trabalho futuras, com base na agenda especificada. <p>**Nota:** Se especificar uma periodicidade com base numa agenda, mas não especificar horas ou minutos para a agenda, tempos de execução futuros são calculados usando as horas ou minutos, respectivamente, desde o momento de execução primeiro. | 
| Hora de início no momento ou no futuro | É executada a carga de trabalho primeiro à hora de início especificada. <p>Executa cargas de trabalho futuras, com base nos cálculos da última hora de execução. | É executada a carga de trabalho primeiro *não é acionado antes* à hora de início, com base na agenda calculada a partir da hora de início. <p>Executa cargas de trabalho futuras, com base na agenda especificada. <p>**Nota:** Se especificar uma periodicidade com base numa agenda, mas não especificar horas ou minutos para a agenda, tempos de execução futuros são calculados usando as horas ou minutos, respectivamente, desde o momento de execução primeiro. | 
||||

**Exemplo de uma hora de início nos últimos com periodicidade, mas sem agenda** 

| Hora de início | Hora atual | Recorrência | Agenda |
| ---------- | ------------ | ---------- | -------- | 
| 2017-09-**07**T14:00:00Z | 2017-09-**08**T13:00:00Z | Todas as 2 dias | {num} | 
||||| 

Neste cenário, o Logic Apps motor calcula executar vezes com base na hora de início,. sys descartará passado tempos de execução e utiliza o futuro da próxima hora de início para a primeira execução. Após essa primeira execução, execuções futuras baseiam-se na agenda calculada a partir da hora de início. Eis o aspeto esta periodicidade:

| Hora de início | Tempo de execução em primeiro lugar | Futuro tempos de execução | 
| ---------- | ------------ | ---------- | 
| 2017-09 -**07** às 14:00 | 2017-09 -**09** às 14:00 | 2017-09 -**11** às 14:00 </br>2017-09 -**13** às 14:00 </br>2017-09 -**15** às 14:00 </br>e assim por diante...
||||

Portanto, para este cenário, não questão até que ponto no passado, especificar o início de tempo, por exemplo, 2017-09 -**05** às 14:00 ou 2017-09 -**01** às 14:00, o primeiro tempo de execução é o mesmo.

## <a name="next-steps"></a>Passos Seguintes

* [Ações de fluxo de trabalho e acionadores](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger)
* [Conectores](../connectors/apis-list.md)
