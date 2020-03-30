---
title: Agendar tarefas recorrentes e fluxos de trabalho em Aplicações Lógicas Azure
description: Uma visão geral sobre agendar tarefas automatizadas recorrentes, processos e fluxos de trabalho com aplicações lógicas azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0f6ec158cf6ab855191e6796be3abec7d37439a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270566"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Agendar e executar tarefas, processos e fluxos de trabalho automatizados com o Azure Logic Apps

As Aplicações Lógicas ajudam-no a criar e executar tarefas e processos recorrentes automatizados numa programação. Ao criar um fluxo de trabalho de aplicação lógica que começa com um gatilho de recorrência incorporado ou gatilho de janela deslizante, que são gatilhos tipo Agenda, pode executar tarefas imediatamente, num momento posterior, ou num intervalo recorrente. Pode ligar para serviços dentro e fora do Azure, como pontos finais HTTP ou HTTPS, enviar mensagens para serviços Azure como Azure Storage e Azure Service Bus, ou enviar ficheiros para uma partilha de ficheiros. Com o gatilho recorrência, também pode configurar horários complexos e recorrências avançadas para executar tarefas. Para saber mais sobre os gatilhos e ações da Agenda incorporada, consulte os [gatilhos da Agenda](#schedule-triggers) e [as ações de Agenda](#schedule-actions). 

> [!TIP]
> Pode agendar e executar cargas de trabalho recorrentes sem criar uma aplicação lógica separada para cada trabalho programado e escorrer até ao limite dos fluxos de [trabalho por região e subscrição.](../logic-apps/logic-apps-limits-and-config.md#definition-limits) Em vez disso, pode usar o padrão de aplicação lógica que é criado pelo [modelo Azure QuickStart: Programador](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/)de trabalho de Apps Lógicas .
>
> O modelo de programador de trabalho de Apps Lógicas cria uma aplicação lógica CreateTimerJob que chama uma aplicação lógica TimerJob. Em seguida, pode ligar para a aplicação lógica CreateTimerJob como Uma API, fazendo um pedido HTTP e passando um horário como entrada para o pedido. Cada chamada para a aplicação lógica CreateTimerJob também chama a aplicação lógica TimerJob, que cria uma nova instância TimerJob que funciona continuamente com base no horário especificado ou até cumprir um limite especificado. Dessa forma, pode executar o tempo rJob quantas quiser sem se preocupar com os limites de fluxo de trabalho, porque os casos não são definições ou recursos de fluxo de aplicações de lógica individual.

Esta lista mostra algumas tarefas de exemplo que pode executar com os gatilhos incorporados da Agenda:

* Obtenha dados internos, tais como executar um procedimento armazenado SQL todos os dias.

* Obtenha dados externos, tais como retirar relatórios meteorológicos da NOAA a cada 15 minutos.

* Envie aos dados do relatório, como por e-mail um resumo para todas as encomendas superiores a um valor específico na semana passada.

* Os dados do processo, como a comprimeas as imagens de hoje, carregam todas as semanas durante as horas de ponta.

* Limpe os dados, tais como eliminar todos os tweets com mais de três meses.

* Arquivar dados, tais como empurrar faturas para um serviço de backup às 1:00 da manhã todos os dias durante os próximos nove meses.

Também pode utilizar as ações incorporadas da Agenda para interromper o seu fluxo de trabalho antes que a próxima ação seja executado, por exemplo:

* Aguarde até um dia de semana para enviar uma atualização de estado por e-mail.

* Atrase o fluxo de trabalho até que uma chamada http tenha tempo para terminar antes de retomar e recuperar o resultado.

Este artigo descreve as capacidades para os gatilhos e ações incorporadas da Agenda.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Agendar gatilhos

Pode iniciar o fluxo de trabalho da sua aplicação lógica utilizando o gatilho de recorrência ou o gatilho da janela deslizante, que não está associado a nenhum serviço ou sistema específico, por exemplo, o Office 365 Outlook ou o SQL Server. Estes gatilhos iniciam e executam o seu fluxo de trabalho com base na sua recorrência especificada, onde seleciona o intervalo e a frequência, como o número de segundos, minutos e horas para ambos os gatilhos, ou o número de dias, semanas ou meses para o gatilho de Recorrência. Também pode definir a data e hora de início, bem como o fuso horário. Cada vez que um gatilho dispara, as Apps Lógicas criam e executam uma nova instância de fluxo de trabalho para a sua aplicação lógica.

Aqui estão as diferenças entre estes gatilhos:

* **Recorrência**: Executa o seu fluxo de trabalho em intervalos de tempo regulares com base no seu horário especificado. Se as recorrências forem perdidas, o gatilho da Recorrência não processa as recorrências perdidas, mas reinicia as recorrências com o próximo intervalo programado. Pode especificar a data e a hora de início, bem como o fuso horário. Se selecionar "Dia", pode especificar horas do dia e minutos da hora, por exemplo, todos os dias às 2:30. Se selecionar "Week", também pode selecionar dias da semana, como quarta-feira e sábado. Para mais informações, consulte [Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de Recorrência](../connectors/connectors-native-recurrence.md).

* **Janela deslizante**: Executa o seu fluxo de trabalho em intervalos de tempo regulares que manuseiem dados em pedaços contínuos. Se falharem as recorrências, o gatilho da Janela Deslizante recua e processa as recorrências perdidas. Pode especificar uma data e hora de início, fuso horário e uma duração para atrasar cada recorrência no seu fluxo de trabalho. Este gatilho não tem opções para especificar dias, semanas e meses, horas do dia, minutos da hora e dias da semana. Para mais informações, consulte [Criar, agendar e executar tarefas e fluxos](../connectors/connectors-native-sliding-window.md)de trabalho recorrentes com o gatilho da Janela Deslizante .

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Agendar ações

Depois de qualquer ação no fluxo de trabalho da sua aplicação lógica, pode utilizar o Atraso e O Atraso até que as ações façam o seu fluxo de trabalho esperar antes que a próxima ação seja executado.

* **Atraso**: Aguarde a próxima ação para o número especificado de unidades de tempo, tais como segundos, minutos, horas, dias, semanas ou meses. Para mais informações, consulte [Atrasar a próxima ação nos fluxos de trabalho.](../connectors/connectors-native-delay.md)

* **Adie até**: Aguarde a próxima ação até à data e hora especificadas. Para mais informações, consulte [Atrasar a próxima ação nos fluxos de trabalho.](../connectors/connectors-native-delay.md)

## <a name="patterns-for-start-date-and-time"></a>Padrões para data e hora de início

<a name="start-time"></a>

Aqui estão alguns padrões que mostram como você pode controlar a recorrência com a data e hora de início, e como o serviço de Aplicações Lógicas executa estas recorrências:

| Hora de início | Periodicidade sem agenda | Recorrência com horário (apenas gatilho de recorrência) |
|------------|-----------------------------|----------------------------------------------------|
| {nenhum} | Executa a primeira carga de trabalho instantaneamente. <p>Executa futuras cargas de trabalho com base no último tempo de execução. | Executa a primeira carga de trabalho instantaneamente. <p>Executa futuras cargas de trabalho com base no horário especificado. |
| Hora de início no passado | **Gatilho de recorrência:** Calcula os tempos de execução com base no tempo de início especificado e descarta os tempos de execução passados. Executa a primeira carga de trabalho no próximo tempo de execução futuro. <p>Executa futuras cargas de trabalho com base em cálculos do último tempo de execução. <p><p>**Gatilho da janela deslizante:** Calcula os tempos de execução com base no tempo de início especificado e honra os tempos de execução passados. <p>Executa futuras cargas de trabalho com base nos cálculos da hora de início especificada. <p><p>Para mais explicações, consulte o exemplo seguindo esta tabela. | Executa a primeira carga de trabalho o *mais cedo* que a hora de início, com base no horário calculado a partir da hora de início. <p>Executa futuras cargas de trabalho com base no horário especificado. <p>**Nota:** Se especificar uma recorrência com um horário, mas não especificar horas ou minutos para o horário, então os tempos de execução futuros são calculados utilizando as horas ou minutos, respectivamente, a partir do primeiro tempo de funcionamento. |
| Hora de início no presente ou no futuro | Executa a primeira carga de trabalho na hora de início especificada. <p>Executa futuras cargas de trabalho com base em cálculos do último tempo de execução. | Executa a primeira carga de trabalho o *mais cedo* que a hora de início, com base no horário calculado a partir da hora de início. <p>Executa futuras cargas de trabalho com base no horário especificado. <p>**Nota:** Se especificar uma recorrência com um horário, mas não especificar horas ou minutos para o horário, então os tempos de execução futuros são calculados utilizando as horas ou minutos, respectivamente, a partir do primeiro tempo de funcionamento. |
||||

> [!IMPORTANT]
> Quando as recorrências não especificam opções avançadas de agendamento, as recorrências futuras são baseadas no último tempo de execução.
> Os tempos de início destas recorrências podem derivar devido a fatores como a latência durante as chamadas de armazenamento. Para garantir que a sua aplicação lógica não perca uma recorrência, especialmente quando a frequência é em dias ou mais, use uma destas opções:
> 
> * Dê um tempo de início para a recorrência.
> 
> * Especifique as horas e minutos para quando executar a recorrência utilizando as propriedades **At these hours** e At These **Minutes.**
> 
> * Utilize o [gatilho da janela deslizante](../connectors/connectors-native-sliding-window.md), em vez do gatilho de recorrência.

*Exemplo para o tempo de início passado e recorrência, mas sem horário*

Suponha que a data e a hora em vigor são 8 de setembro de 2017 às 13:00. Especifica a data e hora de início como 7 de setembro de 2017 às 14:00, que é no passado, e uma recorrência que decorre de dois em dois dias.

| Hora de início | Tempo atual | Recorrência | Agenda |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00 <br>(2017-09-**07** às 14:00) | 2017-09-**08**T13:00:00 <br>(2017-09-**08** às 13:00) | A cada dois dias | {nenhum} |
|||||

Para o gatilho recurrence, o motor Logic Apps calcula os tempos de funcionao com base no tempo de início, descarta os tempos de execução passados, utiliza o próximo tempo de arranque futuro para a primeira execução, e calcula futuras corridas com base no último tempo de execução.

Eis o que esta recorrência parece:

| Hora de início | Primeira corrida | Tempos de execução futuros |
|------------|----------------|------------------|
| 2017-09-**07** às 14:00 | 2017-09-**09** às 14:00 | 2017-09-**11** às 14:00 </br>2017-09-**13** às 14:00 </br>2017-09-**15** às 14:00 </br>e assim por diante... |
||||

Portanto, não importa o quão longe no passado especifique a hora de início, por exemplo, 2017-09-**05** às 14:00 ou 2017-09-01 às 14:00, a sua primeira corrida usa sempre a próxima hora de início.**01**

Para o gatilho da Janela Deslizante, o motor Logic Apps calcula os tempos de funcionamento com base no tempo de início, honra os tempos de funcionamento passados, utiliza o tempo de início para a primeira execução e calcula as futuras corridas com base no tempo de início.

Eis o que esta recorrência parece:

| Hora de início | Primeira corrida | Tempos de execução futuros |
|------------|----------------|------------------|
| 2017-09-**07** às 14:00 | 2017-09-**07** às 14:00 | 2017-09-**09** às 14:00 </br>2017-09-**11** às 14:00 </br>2017-09-**13** às 14:00 </br>2017-09-**15** às 14:00 </br>e assim por diante... |
||||

Portanto, não importa o quão longe no passado especifique a hora de início, por exemplo, 2017-09-**05** às 14:00 ou 2017-09-01 às 14:00, a sua primeira execução usa sempre a hora de início especificada.**01**

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Recorrências de exemplo

Aqui estão várias recorrências de exemplo que pode configurar para os gatilhos que suportam as opções:

| Acionador | Recorrência | Intervalo | Frequência | Hora de início | On these days (Nestes dias) | At these hours (A estas horas) | At these minutes (A estes minutos) | Nota |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Recorrência, <br>Janela Deslizante | Executar a cada 15 minutos (sem data e hora de início) | 15 | Minuto | {nenhum} | {Indisponível} | {nenhum} | {nenhum} | Este calendário começa imediatamente e calcula as futuras recorrências com base no último tempo de execução. |
| Recorrência, <br>Janela Deslizante | Executar a cada 15 minutos (com data e hora de início) | 15 | Minuto | *inícioData* T*startTime*Z | {Indisponível} | {nenhum} | {nenhum} | Este horário não começa *mais cedo* do que a data e hora de início especificadas, e depois calcula futuras recorrências com base no último tempo de execução. |
| Recorrência, <br>Janela Deslizante | Correr a cada hora, na hora (com data e hora de início) | 1 | Hora | *inícioData* Thh:00:00Z | {Indisponível} | {nenhum} | {nenhum} | Este horário não começa *mais cedo* do que a data e hora de início especificadas. As recorrências futuras funcionam a cada hora na marca de minutos "00", que é calculada a partir da hora de início. <p>Se a frequência for "Semana" ou "Mês", este horário funciona, respectivamente, apenas um dia por semana ou um dia por mês. |
| Recorrência, <br>Janela Deslizante | Correr a cada hora, todos os dias (sem data e hora de início) | 1 | Hora | {nenhum} | {Indisponível} | {nenhum} | {nenhum} | Este calendário começa imediatamente e calcula futuras recorrências com base no último tempo de execução. <p>Se a frequência for "Semana" ou "Mês", este horário funciona, respectivamente, apenas um dia por semana ou um dia por mês. |
| Recorrência, <br>Janela Deslizante | Correr a cada hora, todos os dias (com data e hora de início) | 1 | Hora | *inícioData* T*startTime*Z | {Indisponível} | {nenhum} | {nenhum} | Este horário não começa *mais cedo* do que a data e hora de início especificadas, e depois calcula futuras recorrências com base no último tempo de execução. <p>Se a frequência for "Semana" ou "Mês", este horário funciona, respectivamente, apenas um dia por semana ou um dia por mês. |
| Recorrência, <br>Janela Deslizante | Correr a cada 15 minutos depois da hora, a cada hora (com data de início e hora) | 1 | Hora | *inícioData* T00:15:00Z | {Indisponível} | {nenhum} | {nenhum} | Este horário não começa *mais cedo* do que a data e hora de início especificadas. As recorrências futuras correm na marca de minutos "15", que é calculada a partir da hora de início, assim às 00:15, 1:15 da manhã, 2:15 da manhã, e assim por diante. |
| Recorrência | Correr a cada 15 minutos depois da hora, a cada hora (sem data e hora de início) | 1 | Dia | {nenhum} | {Indisponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Este horário decorre às 00:15, 1:15 da manhã, 2:15 da manhã, e assim por diante. Além disso, este horário equivale a uma frequência de "Hora" e a uma hora de início com minutos "15". |
| Recorrência | Corra a cada 15 minutos com as marcas de minutos especificadas (sem data e hora de início). | 1 | Dia | {nenhum} | {Indisponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Este horário só começa na próxima marca de 15 minutos. |
| Recorrência | Corra diariamente às 8:00 *mais* a marca de minutos de quando você guarda a sua app lógica | 1 | Dia | {nenhum} | {Indisponível} | 8 | {nenhum} | Sem data e hora de início, este horário é executado com base no tempo em que guarda a aplicação lógica (operação PUT). |
| Recorrência | Correr diariamente às 8:00 am (com data de início e hora) | 1 | Dia | *inícioData* T08:00:00 | {Indisponível} | {nenhum} | {nenhum} | Este horário não começa *mais cedo* do que a data e hora de início especificadas. As ocorrências futuras decorrem diariamente às 8:00 da manhã. | 
| Recorrência | Correr diariamente às 8:30 am (sem data e hora de início) | 1 | Dia | {nenhum} | {Indisponível} | 8 | 30 | Este horário é às 8:30 todos os dias. |
| Recorrência | Corra diariamente às 8:30 e às 16:30 | 1 | Dia | {nenhum} | {Indisponível} | 8, 16 | 30 | |
| Recorrência | Corra diariamente às 8:30, 8:45, 16:30 e 16:45 | 1 | Dia | {nenhum} | {Indisponível} | 8, 16 | 30, 45 | |
| Recorrência | Corra todos os sábados às 17:00 (sem data e hora de início) | 1 | Week (Semana) | {nenhum} | "Sábado" | 17 | 00 | Este horário decorre todos os sábados às 17:00. |
| Recorrência | Corra todos os sábados às 17:00 (com data e hora de início) | 1 | Week (Semana) | *inícioData* T17:00:00 | "Sábado" | {nenhum} | {nenhum} | Este horário não começa *mais cedo* do que a data e hora de início especificadas, neste caso, 9 de setembro de 2017 às 17:00. As futuras recorrências decorrem todos os sábados às 17:00. |
| Recorrência | Corra todas as terças-feiras, quintas-feiras às 17:00 *mais* a marca de minutos de quando você guarda a sua app lógica| 1 | Week (Semana) | {nenhum} | "Terça-feira", "Quinta-feira" | 17 | {nenhum} | |
| Recorrência | Correr a cada hora durante o horário de trabalho | 1 | Week (Semana) | {nenhum} | Selecione todos os dias, exceto sábado e domingo. | Selecione as horas do dia que deseja. | Selecione os minutos da hora que desejar. | Por exemplo, se o seu horário de trabalho for das 8:00 às 17:00, então selecione "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" como as horas do dia. <p>Se o seu horário de trabalho for das 8:30 às 17:30, selecione as horas anteriores do dia mais "30" como minutos da hora. |
| Recorrência | Correr uma vez todos os dias nos fins de semana | 1 | Week (Semana) | {nenhum} | "Sábado", "Domingo" | Selecione as horas do dia que deseja. | Selecione quaisquer minutos da hora conforme apropriado. | Este horário decorre todos os sábados e domingos na programação especificada. |
| Recorrência | Correr a cada 15 minutos quinzenalmente às segundas-feiras apenas | 2 | Week (Semana) | {nenhum} | "Segunda-feira" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Este horário decorre todas as segundas-feiras a cada marca de 15 minutos. |
| Recorrência | Correr todos os meses | 1 | Mês | *inícioData* T*startTime*Z | {Indisponível} | {Indisponível} | {Indisponível} | Este horário não começa *mais cedo* do que a data e hora de início especificadas e calcula futuras recorrências na data e hora de início. Se não especificar a data e a hora de início, este horário utiliza a data e hora da criação. |
| Recorrência | Correr a cada hora por um dia por mês | 1 | Mês | {ver nota} | {Indisponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {ver nota} | Se não especificar a data e a hora de início, este horário utiliza a data e hora da criação. Para controlar as atas para o horário de recorrência, especifique as atas da hora, um tempo de início ou utilize o tempo de criação. Por exemplo, se a hora de início ou a hora de criação for em 8:25 am, este horário decorre às 8:25, 9:25 da manhã, 10:25 da manhã, e assim por diante. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Correr apenas uma vez

Se quiser executar a sua aplicação lógica apenas de uma vez no futuro, pode utilizar o modelo de **Scheduler: Run once jobs.** Depois de criar uma nova aplicação lógica, mas antes de abrir o Logic Apps Designer, na secção **Modelos,** a partir da lista **de categorias,** selecione **Agenda,** e depois selecione este modelo:

![Selecione modelo "Scheduler: Executar uma vez empregos"](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Ou, se puder iniciar a sua aplicação lógica com o **pedido Quando um pedido HTTP é recebido - Request** trigger, e passar o tempo de início como parâmetro para o gatilho. Para a primeira ação, utilize o **Delay até - Agendar** a ação e fornecer o tempo para quando a próxima ação começar a decorrer.

## <a name="next-steps"></a>Passos seguintes

* [Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de Recorrência](../connectors/connectors-native-recurrence.md)
* [Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho da Janela Deslizante](../connectors/connectors-native-sliding-window.md)
* [Pausa de fluxos de trabalho com ações de atraso](../connectors/connectors-native-delay.md)
