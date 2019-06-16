---
title: Agendamento de tarefas recorrentes e fluxos de trabalho no Azure Logic Apps
description: Uma visão geral sobre o agendamento de tarefas automatizadas recorrentes, processos e fluxos de trabalho com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 7f15dc5b28a44dc8405e2f0524913e6012ebe380
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66356055"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Agendar e executar tarefas automatizadas recorrentes, processos e fluxos de trabalho com o Azure Logic Apps

O Logic Apps ajuda-o a criar e executar tarefas recorrentes automatizadas e processos com base numa agenda. Criando um fluxo de trabalho de aplicação lógica que começa com um incorporada periodicidade ou janela deslizante acionador, que são os acionadores de tipo de agenda, pode executar tarefas imediatamente, num momento posterior, ou num intervalo periódico. Pode chamar serviços dentro e fora do Azure, como pontos finais HTTP ou HTTPS, publicar mensagens aos serviços do Azure, como o armazenamento do Azure e o Azure Service Bus ou obter ficheiros carregados para uma partilha de ficheiros. Com o acionador de periodicidade, também pode configurar agendas complexas e avançadas recorrências para a execução de tarefas. Para obter mais informações sobre os acionadores de agenda internos e as ações, consulte [agendamento e execução recorrente, automatizado, tarefas e fluxos de trabalho com o Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Aqui estão alguns exemplos que mostram os tipos de tarefas que pode executar:

* Obter dados internos, como executar um procedimento SQL armazenado todos os dias.

* Obter dados externos, como solicitação Meteorologia relatórios da NOAA a cada 15 minutos.

* Envie dados de relatório, tais como e-mail, um resumo de todas as encomendas de maiores do que uma quantidade específica na última semana.

* Processar dados, como compress hoje tem imagens carregadas cada dia da semana durante as horas de pico.

* Limpe dados, como eliminar todos os tweets com mais de três meses.

* Arquivar dados, como notas fiscais de push a um serviço de cópia de segurança em 1:00 todos os dias durante os próximos nove meses.

Também pode utilizar as ações internas de agenda para colocar em pausa o fluxo de trabalho antes da próxima ação é executada, por exemplo:

* Aguarde até um dia da semana para enviar uma atualização de estado por e-mail.

* Atrasar o fluxo de trabalho até que uma chamada HTTP tenha tempo termine antes de a retomar e obter o resultado.

Este artigo descreve as capacidades para os acionadores de agenda internos e as ações.

## <a name="schedule-triggers"></a>Acionadores de agenda

Pode iniciar o fluxo de trabalho de aplicação lógica com o acionador de periodicidade ou acionador de janela deslizante, que não estão associadas a qualquer serviço específico ou o sistema, por exemplo, o Outlook do Office 365 ou o SQL Server. Estes acionadores iniciar e executam o fluxo de trabalho com base na sua periodicidade especificados em que selecione o intervalo e a frequência, como o número de segundos, minutos e horas para os acionadores ou o número de dias, semanas ou meses para o acionador de periodicidade. Também pode definir a data de início e hora, bem como o fuso horário. Sempre que um acionador é acionado, o Logic Apps cria e executa uma nova instância de fluxo de trabalho para a aplicação lógica.

Estas são as diferenças entre estes acionadores:

* **Periodicidade**: Executa o fluxo de trabalho em intervalos de tempo regulares com base na agenda especificada. Se recorrências seja perdidas, o acionador de periodicidade não processa as recorrências em falta, mas reinicia recorrências com o próximo intervalo agendado. Pode especificar uma data de início e hora, bem como o fuso horário. Se selecionar "Dia", pode especificar horas do dia e minutos da hora, por exemplo, todos os dias às 2 minutos e 30. Se selecionar "Week", também pode selecionar dias da semana, por exemplo, quarta-feira e Sábado. Para obter mais informações, consulte [criação, a agenda e a execução periódicas de tarefas e fluxos de trabalho com o acionador de periodicidade](../connectors/connectors-native-recurrence.md).

* **Janela deslizante**: Executa o fluxo de trabalho em intervalos de tempo regulares que tratam dados em segmentos contínuos. Se recorrências seja perdidas, o acionador de janela deslizante volta e processa as recorrências em falta. Pode especificar uma data de início e hora, fuso horário e uma duração para atrasar a cada periodicidade no fluxo de trabalho. Este acionador não tem opções para especificar os dias, semanas e meses, horas do dia, os minutos da hora e dias da semana. Para obter mais informações, consulte [criação, a agenda e a execução periódicas de tarefas e fluxos de trabalho com o acionador de janela deslizante](../connectors/connectors-native-sliding-window.md).

## <a name="schedule-actions"></a>Agendar ações

Depois de qualquer ação no seu fluxo de trabalho de aplicação lógica, pode utilizar as ações de atraso e de atraso até para tornar o seu fluxo de trabalho aguardar antes das execuções de ação seguintes.

* **atraso**: Aguarde para ser executada a próxima ação para o número especificado de unidades de tempo, como segundos, minutos, horas, dias, semanas ou meses. Para obter mais informações, consulte [atrasar a próxima ação em fluxos de trabalho](../connectors/connectors-native-delay.md).

* **Atraso até**: Aguarde para ser executada a próxima ação até a data e hora especificadas. Para obter mais informações, consulte [atrasar a próxima ação em fluxos de trabalho](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Data e hora de início de padrões para

<a name="start-time"></a>

Aqui estão alguns padrões que mostram como pode controlar a periodicidade com a data de início e a hora e como os serviços de aplicações lógicas é executado nestes recorrências:

| Hora de início | Periodicidade sem agenda | Periodicidade com agenda (acionador de periodicidade apenas) |
|------------|-----------------------------|----------------------------------------------------|
| {num} | Executa a primeira carga de trabalho de forma instantânea. <p>Executa cargas de trabalho futuras, com base na última hora de execução. | Executa a primeira carga de trabalho de forma instantânea. <p>Executa cargas de trabalho futuras, com base na agenda especificada. |
| Hora de início no passado | **Periodicidade** acionador: Calcula a tempos de execução com base na hora de início especificada e tempos de rejeições últimos a ser executados. Executa a primeira carga de trabalho para o futuro próximo tempo de execução. <p>Executa cargas de trabalho futuras, com base nos cálculos da última hora de execução. <p><p>**Janela deslizante** acionador: Calcula a tempos de execução com base na hora de início especificada e vezes nos últimos honras executar. <p>Executa cargas de trabalho futuras, com base nos cálculos da hora de início especificada. <p><p>Para mais explicações, veja o exemplo a seguir a esta tabela. | É executada a carga de trabalho primeiro *não é acionado antes* à hora de início, com base na agenda calculada a partir da hora de início. <p>Executa cargas de trabalho futuras, com base na agenda especificada. <p>**Nota:** Se especificar uma periodicidade com base numa agenda, mas não especificar horas ou minutos para a agenda, tempos de execução futuros são calculados usando as horas ou minutos, respectivamente, desde o momento de execução primeiro. |
| Hora de início no momento ou no futuro | É executada a carga de trabalho primeiro à hora de início especificada. <p>Executa cargas de trabalho futuras, com base nos cálculos da última hora de execução. | É executada a carga de trabalho primeiro *não é acionado antes* à hora de início, com base na agenda calculada a partir da hora de início. <p>Executa cargas de trabalho futuras, com base na agenda especificada. <p>**Nota:** Se especificar uma periodicidade com base numa agenda, mas não especificar horas ou minutos para a agenda, tempos de execução futuros são calculados usando as horas ou minutos, respectivamente, desde o momento de execução primeiro. |
||||

*Exemplo de última hora de início e periodicidade mas sem agenda*

Suponha que a data e hora atuais é de 8 de Setembro de 2017 em 1 21 horas. Especifique a data de início e a hora como 7 de Setembro de 2017, às 14:00, que é no passado e uma periodicidade que é executado a cada 2 dias.

| Hora de início | Hora atual | Periodicidade | Agenda |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09 -**07** às 14:00) | 2017-09-**08**T13:00:00Z <br>(2017-09 -**08** em 1 21 horas) | Todas as 2 dias | {num} |
|||||

Para o acionador de periodicidade,. sys descartará o Logic Apps motor calcula executar vezes com base na hora de início, em tempos de execução, utiliza o futuro da próxima hora de início para a primeira execução e calcula o futuro é executada com base no tempo de execução último.

Eis o aspeto esta periodicidade:

| Hora de início | Tempo de execução em primeiro lugar | Futuro tempos de execução |
|------------|----------------|------------------|
| 2017-09 -**07** às 14:00 | 2017-09 -**09** às 14:00 | 2017-09 -**11** às 14:00 </br>2017-09 -**13** às 14:00 </br>2017-09 -**15** às 14:00 </br>e assim por diante... |
||||

Sim, quer até que ponto no passado, especifique a hora de início, por exemplo, 2017-09 -**05** às 14:00 ou 2017-09 -**01** às 14:00, o primeiro execute sempre utiliza o futuro da próxima hora de início.

Para o acionador de janela deslizante, o Logic Apps motor calcula executar vezes com base na hora de início, honras nos últimos tempos de execução, utiliza a hora de início da primeira execução e calcula execuções futuras com base na hora de início.

Eis o aspeto esta periodicidade:

| Hora de início | Tempo de execução em primeiro lugar | Futuro tempos de execução |
|------------|----------------|------------------|
| 2017-09 -**07** às 14:00 | 2017-09 -**07** às 14:00 | 2017-09 -**09** às 14:00 </br>2017-09 -**11** às 14:00 </br>2017-09 -**13** às 14:00 </br>2017-09 -**15** às 14:00 </br>e assim por diante... |
||||

Sim, quer até que ponto no passado, especifique a hora de início, por exemplo, 2017-09 -**05** às 14:00 ou 2017-09 -**01** às 14:00, o primeiro execute sempre utiliza especificado hora de início.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Recorrências de exemplo

Seguem-se vários recorrências de exemplo que pode configurar para os acionadores que suportam as opções:

| Acionador | Periodicidade | Interval | Frequência | Hora de início | Nestes dias | A estas horas | A estes minutos | Nota |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Periodicidade, <br>Janela Deslizante | Executar a cada 15 minutos (sem data de início e hora) | 15 | Minuto | {num} | {unavailable} | {num} | {num} | Esta agenda começa imediatamente, em seguida, calcula recorrências futuros com base na última hora de execução. |
| Periodicidade, <br>Janela Deslizante | Executar a cada 15 minutos (com data de início e hora) | 15 | Minuto | *startDate*T*startTime*Z | {unavailable} | {num} | {num} | Esta agenda não começa *antes* que a data de início especificada e a hora, em seguida, calcula recorrências futuros com base na última hora de execução. |
| Periodicidade, <br>Janela Deslizante | Executar hora a hora, à hora certa (com data de início e hora) | 1 | Hora | *startDate*Thh:00:00Z | {unavailable} | {num} | {num} | Esta agenda não começa *antes* especificado de data e hora de início. Recorrências futuras executam a cada hora na marca de minuto "00", que é calculada a partir da hora de início. <p>Se a frequência for "Week" ou "Month", esta agenda respectivamente é executado apenas um dia por semana ou um dia por mês. |
| Periodicidade, <br>Janela Deslizante | Executar hora a hora, diariamente (sem data de início e hora) | 1 | Hora | {num} | {unavailable} | {num} | {num} | Esta agenda começa imediatamente e calcula recorrências futuros com base na última hora de execução. <p>Se a frequência for "Week" ou "Month", esta agenda respectivamente é executado apenas um dia por semana ou um dia por mês. |
| Periodicidade, <br>Janela Deslizante | Executar hora a hora, diariamente (com data de início e hora) | 1 | Hora | *startDate*T*startTime*Z | {unavailable} | {num} | {num} | Esta agenda não começa *antes* que a data de início especificada e a hora, em seguida, calcula recorrências futuros com base na última hora de execução. <p>Se a frequência for "Week" ou "Month", esta agenda respectivamente é executado apenas um dia por semana ou um dia por mês. |
| Periodicidade, <br>Janela Deslizante | Executar a cada 15 minutos decorridos desde a hora, a cada hora (com data de início e hora) | 1 | Hora | *startDate*T00:15:00Z | {unavailable} | {num} | {num} | Esta agenda não começa *antes* especificado de data e hora de início. Futuras recorrências executadas na marca de minuto "15", que é calculado a partir do início do tempo, isso às 00:15 AM, 1:15 AM, 2:15 AM e assim por diante. |
| Periodicidade | Executar a cada 15 minutos após a hora, a cada hora (sem data de início e hora) | 1 | Dia | {num} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Esta agenda é executada às 00:15 AM, 1:15 AM, 2:15 AM, e assim por diante. Além disso, esta agenda equivale a uma frequência de "Hour" e uma hora de início com "15" minutos. |
| Periodicidade | Execute a cada 15 minutos as marcas de minutos especificadas (sem data de início e hora). | 1 | Dia | {num} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esta agenda não iniciada até a próxima especificado marca de 15 minutos. |
| Periodicidade | Executar às 8:00, todos os dias (sem data de início e hora) | 1 | Dia | {num} | {unavailable} | 8 | {num} | Esta agenda é executada às 8:00, todos os dias, com base na agenda especificada. |
| Periodicidade | Executar às 8:00, todos os dias (com data de início e hora) | 1 | Dia | *startDate*T08:00:00Z | {unavailable} | {num} | {num} | Esta agenda é executada 8:00, todos os dias, com base na hora de início especificada. | 
| Periodicidade | Executar às 8:00: 30 todos os dias (sem data de início e hora) | 1 | Dia | {num} | {unavailable} | 8 | 30 | Esta agenda é executado às 8:00: 30 todos os dias, com base na agenda especificada. |
| Periodicidade | Executar às 8:00: 30 todos os dias (com data de início e hora) | 1 | Dia | *startDate*T08:30:00Z | {unavailable} | {num} | {num} | Esta agenda é iniciado na data de início especificada às 8:00: 30. |
| Periodicidade | Executar às 8 às 9:30h e 4:30 PM todos os dias | 1 | Dia | {num} | {unavailable} | 8, 16 | 30 | |
| Periodicidade | Executar às 8 às 11:30, 8 45 AM, 4:30 PM e 4 às 17:45 todos os dias | 1 | Dia | {num} | {unavailable} | 8, 16 | 30, 45 | |
| Periodicidade | Executar todos os sábados às 17:00, (sem data de início e hora) | 1 | Week (Semana) | {num} | "Sábado" | 17 | 00 | Esta agenda é executada todos os sábados às 17:00: 00. |
| Periodicidade | Executar todos os sábados às 17:00 (com data de início e hora) | 1 | Week (Semana) | *startDate*T17:00:00Z | "Sábado" | {num} | {num} | Esta agenda não começa *antes* especificado que data e hora de início, neste caso, 9 de Setembro de 2017, às 17:00. Recorrências futuras executam todos os sábados às 17:00. |
| Periodicidade | Executar toda Terça-feira, Quinta-feira às 17:00 | 1 | Week (Semana) | {num} | "Terça-feira", "Quinta-feira" | 17 | {num} | Esta agenda é executada todas as Terças e Quintas-feiras às 17:00. |
| Periodicidade | Executar a cada hora durante o horário de trabalho | 1 | Week (Semana) | {num} | Selecione todos os dias, exceto Sábado e Domingo. | Selecione as horas do dia que pretende. | Selecione qualquer minutos da hora em que pretende. | Por exemplo, se as horas de trabalho são 8 da Manhã para 5:21 horas, em seguida, selecione "8, 9, 10, 11, 12, 13, 14, 15, 16 e 17" como as horas do dia. <p>Se as horas de trabalho são 8 às 9:30h para 5:30 PM, selecione as horas anteriores do dia mais "30" como minutos da hora. |
| Periodicidade | Executar uma vez, todos os dias de semana | 1 | Week (Semana) | {num} | "Sábado", "Domingo" | Selecione as horas do dia que pretende. | Selecione qualquer minutos da hora conforme apropriado. | Esta agenda é executada cada Sábado e Domingo à agenda especificada. |
| Periodicidade | Executar a cada 15 minutos bissemanais apenas às segundas-feiras | 2 | Week (Semana) | {num} | "Segunda-feira" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Esta agenda é executada todas as outras segunda-feira à marca cada 15 minutos. |
| Periodicidade | Executar todos os meses | 1 | Mês | *startDate*T*startTime*Z | {unavailable} | {unavailable} | {unavailable} | Esta agenda não começa *antes* que o especificado, data e hora de início e calcula recorrências futuras à data de início e hora. Se não especificar uma data de início e hora, esta agenda utiliza a data de criação e a hora. |
| Periodicidade | Executar hora a hora durante um dia por mês | 1 | Mês | {consulte nota} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {consulte nota} | Se não especificar uma data de início e hora, esta agenda utiliza a data de criação e a hora. Para controlar os minutos para a agenda de periodicidade, especifique os minutos da hora, uma hora de início, ou utilizar a hora de criação. Por exemplo, se o início, hora ou a hora de criação é 8:25 AM, executa esta agenda às: 25 8:00, às 09:: 25, 10 25 AM, e assim por diante. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Execute apenas uma vez

Se quiser executar a aplicação lógica só simultaneamente no futuro, pode utilizar o **Scheduler: Executar tarefas de uma vez** modelo. Depois de criar uma nova aplicação lógica, mas antes de abrir o estruturador de aplicações lógicas, no **modelos** secção, da **categoria** lista, selecione **agenda**e, em seguida, selecione Este modelo:

![Selecione "Scheduler: Modelo de executar uma vez tarefas"](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Ou, se pode começar a sua aplicação lógica com o **pedido de HTTP de quando é recebido - pedido** acionar e passar a hora de início como um parâmetro para o acionador. Para a primeira ação, utilize o **atraso até – agendar** ação e fornecer a hora de quando a próxima ação entrar em execução.

## <a name="next-steps"></a>Passos Seguintes

* [Criar, agendar e executar tarefas recorrentes e fluxos de trabalho com o acionador de periodicidade](../connectors/connectors-native-recurrence.md)
* [Criar, agendar e executar tarefas recorrentes e fluxos de trabalho com o acionador de janela deslizante](../connectors/connectors-native-sliding-window.md)
* [Colocar em pausa fluxos de trabalho com ações de atraso](../connectors/connectors-native-delay.md)