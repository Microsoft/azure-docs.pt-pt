---
title: Agendando tarefas e fluxos de trabalho recorrentes no aplicativo lógico do Azure
description: Uma visão geral sobre como agendar tarefas, processos e fluxos de trabalho automatizados recorrentes com aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0f6ec158cf6ab855191e6796be3abec7d37439a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456659"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Agendar e executar tarefas, processos e fluxos de trabalho automatizados recorrentes com aplicativos lógicos do Azure

Os aplicativos lógicos ajudam a criar e executar tarefas e processos recorrentes automatizados em um agendamento. Ao criar um fluxo de trabalho de aplicativo lógico que começa com um gatilho de recorrência interno ou um gatilho de janela deslizante, que são gatilhos de tipo agendado, você pode executar tarefas imediatamente, em um momento posterior ou em um intervalo recorrente. Você pode chamar serviços dentro e fora do Azure, como pontos de extremidade HTTP ou HTTPS, postar mensagens para serviços do Azure, como o armazenamento do Azure e o barramento de serviço do Azure, ou obter arquivos carregados em um compartilhamento de arquivos. Com o gatilho de recorrência, você também pode configurar agendas complexas e recorrências avançadas para executar tarefas. Para saber mais sobre os gatilhos e ações de agendamento internos, consulte [agendar gatilhos](#schedule-triggers) e [ações de agendamento](#schedule-actions). 

> [!TIP]
> Você pode agendar e executar cargas de trabalho recorrentes sem criar um aplicativo lógico separado para cada um dos trabalhos agendados e para a execução do [limite de fluxo de trabalho por região e assinatura](../logic-apps/logic-apps-limits-and-config.md#definition-limits). Em vez disso, você pode usar o padrão de aplicativo lógico que é criado pelo [modelo de início rápido do Azure: Agendador de trabalhos de aplicativos lógicos](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/).
>
> O modelo do Agendador de trabalhos dos aplicativos lógicos cria um aplicativo lógico CreateTimerJob que chama um aplicativo lógico TimerJob. Em seguida, você pode chamar o aplicativo lógico CreateTimerJob como uma API fazendo uma solicitação HTTP e passando uma agenda como entrada para a solicitação. Cada chamada para o aplicativo lógico CreateTimerJob também chama o aplicativo lógico TimerJob, que cria uma nova instância TimerJob que é executada continuamente com base na agenda especificada ou até atingir um limite especificado. Dessa forma, você pode executar tantas instâncias de TimerJob quanto desejar sem se preocupar com os limites de fluxo de trabalho porque as instâncias não são definições ou recursos de fluxo de trabalho de aplicativo lógico individual.

Esta lista mostra algumas tarefas de exemplo que você pode executar com os gatilhos internos de agendamento:

* Obter dados internos, como executar um procedimento armazenado SQL todos os dias.

* Obter dados externos, como os relatórios meteorológicos de pull do NOAA a cada 15 minutos.

* Envie dados de relatório, como enviar por email um resumo para todos os pedidos maiores que um valor específico na última semana.

* Processar dados, como compactar imagens carregadas hoje todos os dias da semana fora do horário de pico.

* Limpar dados, como excluir todos os tweets com mais de três meses.

* Arquive dados, como enviar faturas para um serviço de backup às 1:00, todos os dias para os próximos nove meses.

Você também pode usar as ações internas de agendamento para pausar o fluxo de trabalho antes da execução da próxima ação, por exemplo:

* Aguarde até que um dia da semana envie uma atualização de status por email.

* Atrase o fluxo de trabalho até que uma chamada HTTP tenha tempo para ser concluída antes de retomar e recuperar o resultado.

Este artigo descreve os recursos para o agendamento de gatilhos e ações internas.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Agendar gatilhos

Você pode iniciar o fluxo de trabalho do aplicativo lógico usando o gatilho de recorrência ou o gatilho de janela deslizante, que não está associado a nenhum serviço ou sistema específico, por exemplo, Office 365 Outlook ou SQL Server. Esses gatilhos iniciam e executam o fluxo de trabalho com base na recorrência especificada em que você seleciona o intervalo e a frequência, como o número de segundos, minutos e horas para ambos os gatilhos, ou o número de dias, semanas ou meses para o gatilho de recorrência. Você também pode definir a data e a hora de início, bem como o fuso horário. Cada vez que um gatilho é acionado, os aplicativos lógicos criam e executam uma nova instância de fluxo de trabalho para seu aplicativo lógico.

Estas são as diferenças entre esses gatilhos:

* **Recorrência**: executa o fluxo de trabalho em intervalos de tempo regulares com base no agendamento especificado. Se as recorrências forem perdidas, o gatilho de recorrência não processará as recorrências perdidas, mas reiniciará as recorrências com o próximo intervalo agendado. Você pode especificar uma data e hora de início, bem como o fuso horário. Se você selecionar "dia", poderá especificar horas do dia e minutos da hora, por exemplo, todos os dias às 2:30. Se você selecionar "semana", também poderá selecionar dias da semana, como quarta-feira e sábado. Para obter mais informações, consulte [criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de recorrência](../connectors/connectors-native-recurrence.md).

* **Janela deslizante**: executa o fluxo de trabalho em intervalos de tempo regulares que manipulam dados em partes contínuas. Se as recorrências forem perdidas, o gatilho da janela deslizante voltará e processará as recorrências perdidas. Você pode especificar uma data e hora de início, fuso horário e uma duração para atrasar cada recorrência em seu fluxo de trabalho. Esse gatilho não tem opções para especificar dias, semanas e meses, horas do dia, minutos da hora e dias da semana. Para obter mais informações, consulte [criar, agendar e executar tarefas recorrentes e fluxos de trabalho com o gatilho de janela deslizante](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Agendar ações

Após qualquer ação no fluxo de trabalho do aplicativo lógico, você pode usar o atraso e o atraso até as ações para fazer seu fluxo de trabalho aguardar antes da execução da próxima ação.

* **Atraso**: Aguarde para executar a próxima ação para o número especificado de unidades de tempo, como segundos, minutos, horas, dias, semanas ou meses. Para obter mais informações, consulte [atrasar a próxima ação em fluxos de trabalho](../connectors/connectors-native-delay.md).

* **Atraso até**: aguardar para executar a próxima ação até a data e a hora especificadas. Para obter mais informações, consulte [atrasar a próxima ação em fluxos de trabalho](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Padrões para data e hora de início

<a name="start-time"></a>

Aqui estão alguns padrões que mostram como você pode controlar a recorrência com a data e hora de início e como o serviço de aplicativos lógicos executa essas recorrências:

| Hora de início | Periodicidade sem agenda | Recorrência com agendamento (somente gatilho de recorrência) |
|------------|-----------------------------|----------------------------------------------------|
| None | Executa a primeira carga de trabalho instantaneamente. <p>Executa cargas de trabalho futuras com base na última hora de execução. | Executa a primeira carga de trabalho instantaneamente. <p>Executa cargas de trabalho futuras com base na agenda especificada. |
| Hora de início no passado | Gatilho de **recorrência** : calcula os tempos de execução com base na hora de início especificada e descarta os tempos de execução anteriores. Executa a primeira carga de trabalho no próximo tempo de execução futuro. <p>Executa cargas de trabalho futuras com base em cálculos do último tempo de execução. <p><p>Gatilho de **janela deslizante** : calcula os tempos de execução com base na hora de início especificada e respeita os tempos de execução anteriores. <p>Executa cargas de trabalho futuras com base em cálculos da hora de início especificada. <p><p>Para obter mais explicações, consulte o exemplo após esta tabela. | Executa a primeira carga de trabalho *não antes* da hora de início, com base na agenda calculada a partir da hora de início. <p>Executa cargas de trabalho futuras com base na agenda especificada. <p>**Observação:** Se você especificar uma recorrência com um agendamento, mas não especificar horas ou minutos para o agendamento, os tempos de execução futuros serão calculados usando as horas ou os minutos, respectivamente, da primeira hora de execução. |
| Hora de início no momento ou no futuro | Executa a primeira carga de trabalho na hora de início especificada. <p>Executa cargas de trabalho futuras com base em cálculos do último tempo de execução. | Executa a primeira carga de trabalho *não antes* da hora de início, com base na agenda calculada a partir da hora de início. <p>Executa cargas de trabalho futuras com base na agenda especificada. <p>**Observação:** Se você especificar uma recorrência com um agendamento, mas não especificar horas ou minutos para o agendamento, os tempos de execução futuros serão calculados usando as horas ou os minutos, respectivamente, da primeira hora de execução. |
||||

> [!IMPORTANT]
> Quando as recorrências não especificam opções de agendamento avançadas, as recorrências futuras são baseadas na última hora de execução.
> As horas de início para essas recorrências podem ser desligadas devido a fatores como latência durante chamadas de armazenamento. Para certificar-se de que seu aplicativo lógico não perca uma recorrência, especialmente quando a frequência estiver em dias ou mais, use uma destas opções:
> 
> * Forneça uma hora de início para a recorrência.
> 
> * Especifique as horas e os minutos para quando executar a recorrência usando as propriedades a **seguir horas** e **em minutos** .
> 
> * Use o [gatilho de janela deslizante](../connectors/connectors-native-sliding-window.md), em vez do gatilho de recorrência.

*Exemplo de hora de início e recorrência após, mas sem agendamento*

Suponha que a data e a hora atuais sejam 8 de setembro de 2017 às 1:00. Você especifica a data e a hora de início como 7 de setembro de 2017 às 2:00, que está no passado e uma recorrência que é executada a cada dois dias.

| Hora de início | Hora atual | Recorrência | Agenda |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09-**07** às 2:00 PM) | 2017-09-**08**T13:00:00Z <br>(2017-09-**08** às 1:00 PM) | A cada dois dias | None |
|||||

Para o gatilho de recorrência, o mecanismo de aplicativos lógicos calcula os tempos de execução com base na hora de início, descarta os tempos de execução anteriores, usa a próxima hora de início futura para a primeira execução e calcula as execuções futuras com base na hora da última execução.

Esta é a aparência desta recorrência:

| Hora de início | Hora da primeira execução | Tempos de execução futuros |
|------------|----------------|------------------|
| 2017-09-**07** às 2:00 | 2017-09-**09** às 2:00 | 2017-09-**11** às 2:00 </br>2017-09-**13** às 2:00 </br>2017-09-**15** às 2:00 </br>e assim por diante... |
||||

Portanto, não importa o quanto antes você especifica a hora de início, por exemplo, 2017-09-**05** às 2:00 pm ou 2017-09-**01** às 2:00 PM, sua primeira execução sempre usará a próxima hora de início futura.

Para o gatilho de janela deslizante, o mecanismo de aplicativos lógicos calcula os tempos de execução com base na hora de início, honra os tempos de execução anteriores, usa a hora de início para a primeira execução e calcula as execuções futuras com base na hora de início.

Esta é a aparência desta recorrência:

| Hora de início | Hora da primeira execução | Tempos de execução futuros |
|------------|----------------|------------------|
| 2017-09-**07** às 2:00 | 2017-09-**07** às 2:00 | 2017-09-**09** às 2:00 </br>2017-09-**11** às 2:00 </br>2017-09-**13** às 2:00 </br>2017-09-**15** às 2:00 </br>e assim por diante... |
||||

Portanto, não importa o quanto antes você especifica a hora de início, por exemplo, 2017-09-**05** às 2:00 pm ou 2017-09-**01** às 2:00 PM, sua primeira execução sempre usa a hora de início especificada.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Exemplos de recorrências

Aqui estão várias recorrências de exemplo que você pode configurar para os gatilhos que dão suporte às opções:

| Acionador | Recorrência | Intervalo | Frequência | Hora de início | Nestes dias | A estas horas | A estes minutos | Nota |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Recurrence <br>Janela Deslizante | Executar a cada 15 minutos (sem data e hora de início) | 15 | Minuto | None | {unavailable} | None | None | Essa agenda é iniciada imediatamente e calcula as recorrências futuras com base na hora da última execução. |
| Recurrence <br>Janela Deslizante | Executar a cada 15 minutos (com data e hora de início) | 15 | Minuto | *startDate*T*startTime*Z | {unavailable} | None | None | Essa agenda não é iniciada *antes* da data e hora de início especificadas e, em seguida, calcula as recorrências futuras com base na hora da última execução. |
| Recurrence <br>Janela Deslizante | Executar a cada hora, na hora (com data e hora de início) | 1 | Hora | *startDate*Thh:00:00Z | {unavailable} | None | None | Essa agenda não é iniciada *antes* da data e hora de início especificadas. As recorrências futuras são executadas a cada hora na marca de minuto "00", que é calculada a partir da hora de início. <p>Se a frequência for "Week" ou "month", essa agenda, respectivamente, será executada apenas um dia por semana ou um dia por mês. |
| Recurrence <br>Janela Deslizante | Executar a cada hora, todos os dias (sem data e hora de início) | 1 | Hora | None | {unavailable} | None | None | Essa agenda é iniciada imediatamente e calcula as recorrências futuras com base na hora da última execução. <p>Se a frequência for "Week" ou "month", essa agenda, respectivamente, será executada apenas um dia por semana ou um dia por mês. |
| Recurrence <br>Janela Deslizante | Executar a cada hora, todos os dias (com data e hora de início) | 1 | Hora | *startDate*T*startTime*Z | {unavailable} | None | None | Essa agenda não é iniciada *antes* da data e hora de início especificadas e, em seguida, calcula as recorrências futuras com base na hora da última execução. <p>Se a frequência for "Week" ou "month", essa agenda, respectivamente, será executada apenas um dia por semana ou um dia por mês. |
| Recurrence <br>Janela Deslizante | Executar a cada 15 minutos após a hora, a cada hora (com data e hora de início) | 1 | Hora | *startDate*T00:15:00Z | {unavailable} | None | None | Essa agenda não é iniciada *antes* da data e hora de início especificadas. As recorrências futuras são executadas na marca de "15" minutos, que é calculada a partir da hora de início, portanto às 00:15 A.M., 1:15 AM, 2:15 AM e assim por diante. |
| Recorrência | Executar a cada 15 minutos após a hora, a cada hora (sem data e hora de início) | 1 | Dia | None | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Essa agenda é executada às 00:15 A.M., 1:15, 2:15 AM e assim por diante. Além disso, esse agendamento é equivalente a uma frequência de "hora" e uma hora de início com "15" minutos. |
| Recorrência | Executar a cada 15 minutos nas marcas de minuto especificadas (sem data e hora de início). | 1 | Dia | None | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Essa agenda não é iniciada até a próxima marca de 15 minutos especificada. |
| Recorrência | Execute diariamente às 8:00 *mais* o minuto-Mark de quando você salva seu aplicativo lógico | 1 | Dia | None | {unavailable} | 8 | None | Sem uma data e hora de início, essa agenda é executada com base no tempo em que você salva o aplicativo lógico (operação PUT). |
| Recorrência | Executar diariamente às 8:00 (com data e hora de início) | 1 | Dia | *startDate*T08:00:00Z | {unavailable} | None | None | Essa agenda não é iniciada *antes* da data e hora de início especificadas. As ocorrências futuras são executadas diariamente às 8:00. | 
| Recorrência | Executar diariamente às 8:30 AM (sem data e hora de início) | 1 | Dia | None | {unavailable} | 8 | 30 | Essa agenda é executada às 8:30, todos os dias. |
| Recorrência | Executar diariamente às 8:30 AM e 4:30 PM | 1 | Dia | None | {unavailable} | 8, 16 | 30 | |
| Recorrência | Executar diariamente às 8:30, 8:45 AM, 4:30 PM e 4:45 PM | 1 | Dia | None | {unavailable} | 8, 16 | 30, 45 | |
| Recorrência | Executar a cada sábado às 17h (sem data e hora de início) | 1 | Semana | None | Sábado | 17 | 00 | Essa agenda é executada a cada sábado às 5:00 PM. |
| Recorrência | Executar todos os sábados às 17h (com data e hora de início) | 1 | Semana | *startDate*T17:00:00Z | Sábado | None | None | Esse agendamento não é iniciado *antes* da data e hora de início especificadas, neste caso, 9 de setembro de 2017 às 5:00 PM. As recorrências futuras são executadas a cada sábado às 5:00 PM. |
| Recorrência | Execute todas as terças-feiras às 17:00, *mais* o minuto-Mark de quando você salva seu aplicativo lógico| 1 | Semana | None | "Terça-feira", "quinta-feira" | 17 | None | |
| Recorrência | Executar a cada hora durante o horário de trabalho | 1 | Semana | None | Selecione todos os dias, exceto sábado e domingo. | Selecione as horas do dia que você deseja. | Selecione os minutos da hora que desejar. | Por exemplo, se seu horário de trabalho for de 8:00 a 5:00 PM, selecione "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" como as horas do dia. <p>Se seu horário de trabalho for de 8:30 a 5:30 PM, selecione as horas anteriores do dia mais "30" como minutos da hora. |
| Recorrência | Executar uma vez todos os dias nos fins de semana | 1 | Semana | None | "Sábado", "domingo" | Selecione as horas do dia que você deseja. | Selecione os minutos da hora conforme apropriado. | Essa agenda é executada a cada sábado e domingo no agendamento especificado. |
| Recorrência | Executar a cada 15 minutos quinzenalmente apenas nas segundas-feiras | 2 | Semana | None | Segunda-feira | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Essa agenda é executada a cada segunda-feira a cada marca de 15 minutos. |
| Recorrência | Executar todos os meses | 1 | Mês | *startDate*T*startTime*Z | {unavailable} | {unavailable} | {unavailable} | Essa agenda não é iniciada *antes* da data e hora de início especificadas e calcula as recorrências futuras na data e hora de início. Se você não especificar uma data e hora de início, essa agenda usará a data e a hora de criação. |
| Recorrência | Executar a cada hora por um dia por mês | 1 | Mês | {Veja a observação} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {Veja a observação} | Se você não especificar uma data e hora de início, essa agenda usará a data e a hora de criação. Para controlar os minutos da agenda de recorrência, especifique os minutos da hora, uma hora de início ou use a hora de criação. Por exemplo, se a hora de início ou a hora de criação for 8:25, essa agenda será executada às 8:25, 9:25 AM, 10:25 e assim por diante. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Executar apenas uma vez

Se você quiser executar seu aplicativo lógico somente de uma vez no futuro, poderá usar o modelo **Agendador: executar uma vez para trabalhos** . Depois de criar um novo aplicativo lógico, mas antes de abrir o designer de aplicativos lógicos, na seção **modelos** , na lista **categoria** , selecione **agenda**e, em seguida, selecione este modelo:

![Selecione o modelo "Agendador: executar após trabalhos"](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Ou, se você puder iniciar seu aplicativo lógico com o gatilho **quando uma solicitação HTTP é recebida-solicitação** e passar a hora de início como um parâmetro para o gatilho. Para a primeira ação, use a ação **atrasar até-agenda** e forneça o tempo para quando a próxima ação começar a ser executada.

## <a name="next-steps"></a>Passos seguintes

* [Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de recorrência](../connectors/connectors-native-recurrence.md)
* [Criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de janela deslizante](../connectors/connectors-native-sliding-window.md)
* [Pausar fluxos de trabalho com ações de atraso](../connectors/connectors-native-delay.md)
