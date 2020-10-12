---
title: Agendamento de tarefas recorrentes e fluxos de trabalho em Azure Logic Apps
description: Uma visão geral sobre o agendamento de tarefas, processos e fluxos de trabalho automáticos recorrentes com apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: deli, jonfan, logicappspm
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: a5f01e81564561fe43ef6e55e6e9b3b67d6e1d77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84945618"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Agendar e executar tarefas, processos e fluxos de trabalho automatizados com o Azure Logic Apps

As Aplicações Lógicas ajudam-no a criar e executar tarefas e processos automáticos recorrentes num horário. Ao criar um fluxo de trabalho de aplicações lógicas que começa com um gatilho de recorrência incorporado ou um gatilho de janela deslizante, que são gatilhos do tipo Agenda, pode executar tarefas imediatamente, numa hora posterior, ou num intervalo recorrente. Pode ligar para serviços dentro e fora do Azure, tais como pontos finais HTTP ou HTTPS, enviar mensagens para serviços Azure, como Azure Storage e Azure Service Bus, ou obter ficheiros enviados para uma partilha de ficheiros. Com o gatilho de Recorrência, também pode configurar horários complexos e recorrências avançadas para executar tarefas. Para saber mais sobre os gatilhos e ações da Agenda incorporada, consulte [os gatilhos](#schedule-triggers) da Agenda e [agendar ações.](#schedule-actions) 

> [!TIP]
> Pode agendar e executar cargas de trabalho recorrentes sem criar uma aplicação lógica separada para cada trabalho programado e escorrê-lo até ao [limite dos fluxos de trabalho por região e subscrição.](../logic-apps/logic-apps-limits-and-config.md#definition-limits) Em vez disso, pode utilizar o padrão de aplicação lógica que é criado pelo [modelo Azure QuickStart: Agendador de tarefas de Apps Lógicas](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/).
>
> O modelo de agendador de tarefas Logic Apps cria uma aplicação lógica CreateTimerJob que chama uma aplicação lógica TimerJob. Em seguida, pode ligar para a aplicação lógica CreateTimerJob como API, fazendo um pedido HTTP e passando um horário como entrada para o pedido. Cada chamada para a aplicação lógica CreateTimerJob também chama a aplicação lógica TimerJob, que cria uma nova instância TimerJob que funciona continuamente com base na programação especificada ou até cumprir um limite especificado. Desta forma, pode executar tantos casos timerJob quanto quiser sem se preocupar com os limites do fluxo de trabalho, porque as instâncias não são definições ou recursos individuais de fluxo de aplicações lógicas.

Esta lista mostra algumas tarefas exemplo que pode executar com os gatilhos incorporados da Agenda:

* Obtenha dados internos, tais como executar um procedimento ML armazenado todos os dias.

* Obtenha dados externos, tais como os relatórios meteorológicos da NOAA a cada 15 minutos.

* Envie dados do relatório, como por exemplo, um resumo para todas as encomendas superiores a um valor específico na semana passada.

* Processar dados, como comprimir as imagens de hoje em dia durante as horas de ponta.

* Limpe os dados, tais como eliminar todos os tweets com mais de três meses.

* Dados de arquivo, tais como empurrar faturas para um serviço de backup à 1:00 todos os dias durante os próximos nove meses.

Também pode utilizar as ações incorporadas da Agenda para interromper o seu fluxo de trabalho antes da próxima ação, por exemplo:

* Aguarde até um dia de semana para enviar uma atualização de estado por e-mail.

* Atrase o fluxo de trabalho até que uma chamada HTTP tenha tempo para terminar antes de retomar e recuperar o resultado.

Este artigo descreve as capacidades para os gatilhos e ações incorporados da Agenda.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Detonadores de horários

Pode iniciar o fluxo de trabalho da aplicação lógica utilizando o gatilho de Recorrência ou o gatilho da janela deslizante, que não está associado a nenhum serviço ou sistema específico. Estes gatilhos iniciam e executam o seu fluxo de trabalho com base na sua recorrência especificada onde seleciona o intervalo e a frequência, como o número de segundos, minutos, horas, dias, semanas ou meses. Também pode definir a data e hora de início, bem como o fuso horário. Cada vez que um gatilho dispara, a Logic Apps cria e executa uma nova instância de fluxo de trabalho para a sua aplicação lógica.

Aqui estão as diferenças entre estes gatilhos:

* **Recorrência**: Executa o seu fluxo de trabalho em intervalos de tempo regulares com base no seu horário especificado. Se as recorrências forem perdidas, por exemplo, devido a perturbações ou fluxos de trabalho desativados, o gatilho de Recorrência não processa as recorrências perdidas, mas reinicia as recorrências com o intervalo programado seguinte. Pode especificar uma data e hora de início, bem como o fuso horário. Se selecionar "Day", pode especificar horas do dia e minutos da hora, por exemplo, todos os dias às 2:30. Se selecionar "Week", também pode selecionar dias da semana, como quarta-feira e sábado. Para obter mais informações, consulte [Criar, programar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de Recorrência](../connectors/connectors-native-recurrence.md).

* **Janela deslizante**: Executa o seu fluxo de trabalho em intervalos de tempo regulares que manuseiam dados em pedaços contínuos. Se forem perdidas recorrências, por exemplo, devido a perturbações ou fluxos de trabalho desativado, o gatilho da janela deslizante recua e processa as recorrências perdidas. Pode especificar uma data de início e hora, fuso horário e uma duração para atrasar cada recorrência no seu fluxo de trabalho. Este gatilho não suporta horários avançados, por exemplo, horas específicas do dia, minutos da hora e dias da semana. Para obter mais informações, consulte [Criar, programar e executar tarefas e fluxos de trabalho recorrentes com o gatilho da janela deslizante](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Agendar ações

Depois de qualquer ação no seu fluxo de trabalho de aplicações lógicas, pode utilizar as ações De atraso e atraso até que o seu fluxo de trabalho aguarde antes que a próxima ação seja executado.

* **Atraso**: Aguarde para executar a próxima ação para o número especificado de unidades de tempo, tais como segundos, minutos, horas, dias, semanas ou meses. Para obter mais informações, consulte [Delay the next action in workflows](../connectors/connectors-native-delay.md).

* **Atrase até:** Aguarde para executar a próxima ação até à data e hora especificadas. Para obter mais informações, consulte [Delay the next action in workflows](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Padrões para data e hora de início

<a name="start-time"></a>

Aqui estão alguns padrões que mostram como pode controlar a recorrência com a data e hora de início, e como o serviço De Aplicações Lógicas executa estas recorrências:

| Hora de início | Periodicidade sem agenda | Recorrência com horário (apenas gatilho de recorrência) |
|------------|-----------------------------|----------------------------------------------------|
| {nenhum} | Executa a primeira carga de trabalho instantaneamente. <p>Executa cargas de trabalho futuras com base no último tempo de execução. | Executa a primeira carga de trabalho instantaneamente. <p>Executa cargas de trabalho futuras com base no calendário especificado. |
| Hora de início no passado | **Trigger de recorrência:** Calcula os tempos de execução com base na hora de início especificada e descarta os tempos de execução passados. Executa a primeira carga de trabalho no próximo tempo de execução futuro. <p>Executa cargas de trabalho futuras com base em cálculos do último tempo de execução. <p><p>**Desacionar** o gatilho da janela: Calcula os tempos de funcionamento com base na hora de início especificada e honra os tempos de execução passados. <p>Executa cargas de trabalho futuras com base em cálculos a partir da hora de início especificada. <p><p>Para mais explicações, consulte o exemplo que se segue a esta tabela. | Executa a primeira carga de trabalho *o mais cedo* do que a hora de início, com base no horário calculado a partir da hora de início. <p>Executa cargas de trabalho futuras com base no calendário especificado. <p>**Nota:** Se especificar uma recorrência com um horário, mas não especificar horas ou minutos para o horário, então os tempos de execução futuros são calculados usando as horas ou minutos, respectivamente, a partir do primeiro tempo de execução. |
| Hora de início no presente ou no futuro | Executa a primeira carga de trabalho na hora de início especificada. <p>Executa cargas de trabalho futuras com base em cálculos do último tempo de execução. | Executa a primeira carga de trabalho *o mais cedo* do que a hora de início, com base no horário calculado a partir da hora de início. <p>Executa cargas de trabalho futuras com base no calendário especificado. <p>**Nota:** Se especificar uma recorrência com um horário, mas não especificar horas ou minutos para o horário, então os tempos de execução futuros são calculados usando as horas ou minutos, respectivamente, a partir do primeiro tempo de execução. |
||||

> [!IMPORTANT]
> Quando as recorrências não especificam opções avançadas de agendamento, as recorrências futuras são baseadas no último tempo de execução.
> Os tempos de início destas recorrências podem derivar devido a fatores como a latência durante as chamadas de armazenamento. Para garantir que a sua aplicação lógica não perca uma recorrência, especialmente quando a frequência é em dias ou mais, use uma destas opções:
> 
> * Providencie uma hora de início para a recorrência.
> 
> * Especifique as horas e minutos para quando executar a recorrência utilizando o **At estas horas** e as propriedades **destes minutos.**
> 
> * Utilize o [gatilho da janela deslizante](../connectors/connectors-native-sliding-window.md), em vez do gatilho de recorrência.

*Exemplo para hora de início e recorrência passadas, mas sem horário*

Suponha que a data e hora atuais é 8 de setembro de 2017 às 13:00. Especifica a data e hora de início como 7 de setembro de 2017 às 14:00, que é no passado, e uma recorrência que se estende de dois em dois dias.

| Hora de início | Tempo atual | Recorrência | Agenda |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09-07 às 14:00)**07** | 2017-09-**08**T13:00:00Z <br>(2017-09-08 às 13:00)**08** | A cada dois dias | {nenhum} |
|||||

Para o gatilho de Recorrência, o motor Logic Apps calcula os tempos de funcionamento com base na hora de início, descarta os tempos de execução passados, utiliza o próximo tempo de início futuro para a primeira execução e calcula as corridas futuras com base no último tempo de execução.

Eis o que esta recorrência parece:

| Hora de início | Primeira vez | Tempos de execução futuros |
|------------|----------------|------------------|
| 2017-09-07 às 14:00**07** | 2017-09-09 às 14:00**09** | 2017-09-11 às 14:00**11** </br>2017-09-**13** às 14:00 </br>2017-09-**15** às 14:00 </br>e assim por diante... |
||||

Por isso, não importa o quão longe no passado especifique a hora de início, por exemplo, 2017-09-05 às 14:00 ou 2017-09-01 às**14:00,** a sua primeira corrida usa sempre a próxima hora de início do futuro.**05**

Para o gatilho da Janela Deslizante, o motor De Aplicações Lógicas calcula os tempos de funcionamento com base na hora de início, honra os tempos de execução passados, utiliza o tempo de início para a primeira execução e calcula as corridas futuras com base na hora de início.

Eis o que esta recorrência parece:

| Hora de início | Primeira vez | Tempos de execução futuros |
|------------|----------------|------------------|
| 2017-09-07 às 14:00**07** | 2017-09-07 às 14:00**07** | 2017-09-09 às 14:00**09** </br>2017-09-11 às 14:00**11** </br>2017-09-**13** às 14:00 </br>2017-09-**15** às 14:00 </br>e assim por diante... |
||||

Assim, não importa o quão longe no passado especifique a hora de início, por exemplo, 2017-09-05 às 14:00 ou 2017-09-01 às**14:00,** a sua primeira corrida usa sempre a hora de início especificada.**05**

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Recorrências de exemplo

Aqui estão várias recorrências de exemplo que pode configurar para os gatilhos que suportam as opções:

| Acionador | Recorrência | Intervalo | Frequência | Hora de início | On these days (Nestes dias) | At these hours (A estas horas) | At these minutes (A estes minutos) | Nota |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Recorrência, <br>Janela Deslizante | Executar a cada 15 minutos (sem data de início e hora) | 15 | Minuto | {nenhum} | {indisponível} | {nenhum} | {nenhum} | Este horário começa imediatamente e calcula as recorrências futuras com base no último tempo de execução. |
| Recorrência, <br>Janela Deslizante | Executar a cada 15 minutos (com data de início e hora) | 15 | Minuto | *startDate* T*startTime*Z | {indisponível} | {nenhum} | {nenhum} | Este horário não começa mais *cedo* do que a data e hora de início especificadas, e depois calcula as recorrências futuras com base no último tempo de execução. |
| Recorrência, <br>Janela Deslizante | Corra a cada hora, na hora (com data de início e hora) | 1 | Hora | *startDate* Thh:00:00Z | {indisponível} | {nenhum} | {nenhum} | Este horário não começa mais *cedo* do que a data e hora de início especificadas. As recorrências futuras são executadas a cada hora na marca de minutos "00", que é calculada a partir da hora de início. <p>Se a frequência for "Semana" ou "Mês", este horário funciona, respectivamente, apenas um dia por semana ou um dia por mês. |
| Recorrência, <br>Janela Deslizante | Corra todas as horas, todos os dias (sem data e hora de início) | 1 | Hora | {nenhum} | {indisponível} | {nenhum} | {nenhum} | Este horário começa imediatamente e calcula as recorrências futuras com base no último tempo de execução. <p>Se a frequência for "Semana" ou "Mês", este horário funciona, respectivamente, apenas um dia por semana ou um dia por mês. |
| Recorrência, <br>Janela Deslizante | Corra todas as horas, todos os dias (com data e hora de início) | 1 | Hora | *startDate* T*startTime*Z | {indisponível} | {nenhum} | {nenhum} | Este horário não começa mais *cedo* do que a data e hora de início especificadas, e depois calcula as recorrências futuras com base no último tempo de execução. <p>Se a frequência for "Semana" ou "Mês", este horário funciona, respectivamente, apenas um dia por semana ou um dia por mês. |
| Recorrência, <br>Janela Deslizante | Corra a cada 15 minutos depois da hora, a cada hora (com data de início e hora) | 1 | Hora | *startDate* T00:15:00Z | {indisponível} | {nenhum} | {nenhum} | Este horário não começa mais *cedo* do que a data e hora de início especificadas. As futuras recorrências correm na marca dos minutos "15", que é calculada a partir da hora de início, assim às 00:15, 1:15 am, 2:15 AM, e assim por diante. |
| Recorrência | Corra a cada 15 minutos depois da hora, a cada hora (sem data de início e hora) | 1 | Dia | {nenhum} | {indisponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Este horário é às 00:15, 1:15, 2:15 da manhã, e assim por diante. Além disso, este horário equivale a uma frequência de "Hora" e uma hora de início com "15" minutos. |
| Recorrência | Executar a cada 15 minutos nas notas de minutos especificadas (sem data e hora de início). | 1 | Dia | {nenhum} | {indisponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Este horário só começa na marca de 15 minutos. |
| Recorrência | Corra diariamente às *8:00 mais* a marca de minutos de quando guardar a sua aplicação lógica | 1 | Dia | {nenhum} | {indisponível} | 8 | {nenhum} | Sem data e hora de início, este horário é executado com base no momento em que guarda a aplicação lógica (operação PUT). |
| Recorrência | Corra diariamente às 8:00 da manhã (com data de início e hora) | 1 | Dia | *startDate* T08:00:00Z | {indisponível} | {nenhum} | {nenhum} | Este horário não começa mais *cedo* do que a data e hora de início especificadas. As ocorrências futuras decorrem diariamente às 8h00. | 
| Recorrência | Corra diariamente às 8:00 da manhã (sem data de início e hora) | 1 | Dia | {nenhum} | {indisponível} | 8 | 00 | Este horário é às 8:00 todos os dias. |
| Recorrência | Corra diariamente às 8:00 e 16:00 | 1 | Dia | {nenhum} | {indisponível} | 8, 16 | 0 | |
| Recorrência | Corra diariamente às 8:30 da manhã, 8:45, 16:30 e 16:45 | 1 | Dia | {nenhum} | {indisponível} | 8, 16 | 30, 45 | |
| Recorrência | Corra todos os sábados às 17:00 (sem data de início e hora) | 1 | Semana | {nenhum} | "Sábado" | 17 | 0 | Este horário é executado todos os sábados às 17:00. |
| Recorrência | Corra todos os sábados às 17:00 (com data de início e hora) | 1 | Semana | *startDate* T17:00:00Z | "Sábado" | {nenhum} | {nenhum} | Este horário não começa mais *cedo* do que a data e hora de início especificadas, neste caso, 9 de setembro de 2017 às 17:00. As futuras recorrências são todas as sábados às 17:00. |
| Recorrência | Corra todas as terças, quintas-feiras às 17:00 *mais* a marca de minutos de quando guardar a sua aplicação lógica| 1 | Semana | {nenhum} | "Terça-feira", "Quinta-feira" | 17 | {nenhum} | |
| Recorrência | Corra a cada hora durante o horário de trabalho. | 1 | Semana | {nenhum} | Selecione todos os dias, exceto sábado e domingo. | Selecione as horas do dia que deseja. | Selecione todos os minutos da hora que quiser. | Por exemplo, se o seu horário de trabalho for das 8:00 às 17:00, então selecione "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" como as horas do dia *mais* "0" como minutos da hora. |
| Recorrência | Corra uma vez todos os dias nos fins de semana | 1 | Semana | {nenhum} | "Sábado", "Domingo" | Selecione as horas do dia que deseja. | Selecione quaisquer minutos da hora conforme apropriado. | Este horário é executado todos os sábados e domingos no horário especificado. |
| Recorrência | Corra a cada 15 minutos quinzenalmente apenas às segundas-feiras | 2 | Semana | {nenhum} | "Segunda-feira" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Este horário funciona todas as segundas-feiras a cada 15 minutos. |
| Recorrência | Corra todos os meses | 1 | Mensal | *startDate* T*startTime*Z | {indisponível} | {indisponível} | {indisponível} | Este horário não começa mais *cedo* do que a data e hora de início especificadas e calcula as recorrências futuras na data e hora de início. Se não especificar uma data e hora de início, este programa utiliza a data e hora da criação. |
| Recorrência | Corra a cada hora por um dia por mês | 1 | Mensal | {ver nota} | {indisponível} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {ver nota} | Se não especificar uma data e hora de início, este programa utiliza a data e hora da criação. Para controlar as atas para o horário de recorrência, especifique as atas da hora, uma hora de início ou use o tempo de criação. Por exemplo, se a hora de início ou criação for 8:25 AM, este horário é às 8:25, 9:25 AM, 10:25 AM, e assim por diante. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Executar apenas uma vez

Se quiser executar a sua aplicação lógica apenas de uma vez no futuro, pode utilizar o **Modelo de Programador: Executar uma vez** modelo de empregos. Depois de criar uma nova aplicação lógica, mas antes de abrir o Design de Aplicações **Lógicas,** na secção Modelos, a partir da lista de **categorias,** selecione **Agendar**e, em seguida, selecione este modelo:

![Selecione o modelo "Scheduler: Run once jobs"](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Ou, se puder iniciar a sua aplicação lógica com o **Quando um pedido HTTP é recebido - Solicite** o gatilho e passe a hora de início como parâmetro para o gatilho. Para a primeira ação, utilize o **Delay até - Agendar** a ação e fornecer o tempo para quando a próxima ação começar a funcionar.

## <a name="next-steps"></a>Passos seguintes

* [Criar, programar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de Recorrência](../connectors/connectors-native-recurrence.md)
* [Criar, programar e executar tarefas e fluxos de trabalho recorrentes com o gatilho da janela deslizante](../connectors/connectors-native-sliding-window.md)
* [Pausa nos fluxos de trabalho com ações de atraso](../connectors/connectors-native-delay.md)
