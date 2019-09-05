---
title: Referência de domínio predefinido-LUIS
titleSuffix: Azure Cognitive Services
description: Referência para os domínios criados previamente, que são coleções pré-criados de objetivos e entidades do Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: b840f1ce42c9d7e4af8854a2c6bd7fd26f5b88e9
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307450"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Referência de domínio predefinida para seu aplicativo LUIS
Esta referência disponibiliza informações sobre o [domínios pré-concebidos](luis-how-to-use-prebuilt-domains.md), que são coleções pré-criados de objetivos e entidades que oferece o LUIS.

[Domínios personalizados](luis-how-to-start-new-app.md), por outro lado, começar com nenhum intenções e modelos. Pode adicionar qualquer intenções de domínio pré-criado e entidades para um modelo personalizado.

# <a name="supported-domains-across-cultures"></a>Domínios com suporte entre culturas

A tabela a seguir resume os domínios com suporte no momento. O suporte para inglês geralmente é mais completo do que outros. 

| Tipo de entidade       | EN-US      | ZH-CN   | DE    | FR     | ES    | it      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| [Calendário](#calendar)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Comunicação](#communication)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [e-mail](#email)           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [HomeAutomation](#homeautomation)           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Notas](#notes)      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Limita](#places)    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [RestaurantReservation](#restaurantreservation)   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [ToDo](#todo)     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Utilitários](#utilities)          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [MSNBC](#weather)        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| [Web](#web)    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

**Não há suporte para** domínios predefinidos no:

* Francês canadense
* Hindi
* Mexicano espanhol

# <a name="description-for-luis-prebuilt-domains"></a>Descrição para domínios predefinidos do LUIS
## <a name="calendar"></a>**Calendário**
O calendário é algo sobre reuniões pessoais e compromissos, não com evento público (por exemplo, o World Cup Schedules, o calendário de eventos de Seattle) ou o calendário genérico (por exemplo, que dia é hoje, o que começa, quando é dia do trabalho).
### <a name="intents"></a>**Tentativas**
Nome da intenção | Descrição | Exemplos
---------|----------|---------------
 AcceptEventEntry | Aceite um (n) compromisso/reunião/evento no calendário. | Aceitar um compromisso. <br> Aceitar o evento <br> aceite a reunião de hoje.
 Cancelar | Cancele a ação em andamento pelo assistente virtual, como cancelar o processo de criação de uma reunião. <br> ***Aviso**: Essa tentativa inclui principalmente a ação "Cancelar" no cenário de calendário. Se você precisar de uma expressão geral em "Cancelar", aproveite a intenção de "Cancelar" no domínio de **utilitários** . * | Tudo bem, basta cancelar o evento. <br> Não, eu apenas cancelo o compromisso.
 ChangeCalendarEntry | Alterar ou reagendar a entrada do calendário. | Reagendar minhas 6h compromisso amanhã a 2 p.m. <br> Reagendar compromisso do doutor por 5 PM <br> Reagendar almoço com Maria Olson para sexta-feira. <br> Alterar a hora do evento.
 CheckAvailability | Conheça a disponibilidade para um compromisso ou reunião no calendário do usuário ou o calendário de outra pessoa. | Quando é Jim disponível para corresponder às? <br> Mostrar quando Carol estiver disponível amanhã. <br> Chris é gratuito no Sábado?
 Confirmar | Confirme se uma operação/ação deve ser executada com base na intenção anterior. <br> ***Aviso**: Essa tentativa inclui principalmente a ação "confirmar" para o cenário de calendário. Se você precisar de mais expressões gerais em "Confirm", aproveite a intenção de "confirmar" no domínio de **utilitários** . *| Isso está correto, crie a reunião <br> Sim, obrigado, conecte-se à reunião.
 ConnectToMeeting | Conecte-se a uma reunião. | Conecte-me a 11:00 chamada de conferência com Andy. <br> Aceite a chamada de reunião de orçamento.
 ContactMeetingAttendees | Entre em contato com os participantes da reunião. | Informe a reunião que estou executando em relação à reunião de 3:00. <br> Notifique os colegas para a reunião de 8h que precisa iniciar às 8:30.
 CreateCalendarEntry | Adicione um novo item de uso individual para o calendário. | Crie uma reunião sobre como discutir problemas. <br> criar uma reunião comabc@microsoft.com
 DeleteCalendarEntry | Pedido para eliminar uma entrada no calendário.| Cancele a minha marcação com Carol. <br> Excluir as minhas 9h Atas. <br> Excluir meu evento.
  FindCalendarEntry | Abra o aplicativo de calendário ou pesquise a entrada do calendário. | Encontre no dentista rever compromisso. <br> Mostrar meu calendário. <br> O que há no meu calendário na quinta-feira?
 FindCalendarWhen | Verifique a hora em que a agenda ocorre. | Quando encontro com âmbar e Susan? <br> Quando tenho um brunch agendado? 
 FindCalendarWhere | Verifique o local em que a agenda ocorre. | Onde estão meus compromissos amanhã? <br>Onde estou enfrentando Stacy e Michael amanhã para jantar?
  FindCalendarWho | Verifique os participantes que participarão do agendamento de destino. | Quero os atendedores confirmados na reunião do amanhã em 2. <br> Jim estará na reunião de "próximas"?
 FindCalendarDetail | Verifique e mostre os detalhes da agenda. | Preciso que você forneça os detalhes da reunião agendada com meu colega de colegas.
 FindDuration | Verifique a duração. | Quanto tempo terá para pegar os mercados? <br> Quanto tempo tenho para almoçar?
 FindMeetingRoom | Encontre as salas de reunião disponíveis. | Quais salas de encontro eu tenho? <br> Um novo local de reunião, encontre um.
 GoBack | Voltar para a última etapa ou item.  <br> ***Aviso**: Consulte o domínio de **utilitários** para obter mais declarações geral do GoBack. * | Anterior <br> Voltar ao último email.
 Rejeitar | O usuário rejeita o assistente virtual proposto. <br> ***Aviso**: Consulte o domínio de **utilitários** para mais rejeitar declarações gerais. * | Não é necessário definir o evento. <br> Tenho outras coisas a fazer nesse momento.
ShowNext | Verifique o próximo evento. <br> ***Aviso**: Consulte o domínio de **utilitários** para obter mais da próxima declarações geral. * | Dê-me meu próximo evento. <br> O que vem a seguir no calendário?
 ShowPrevious | Verifique o evento anterior. <br> ***Aviso**: Consulte o domínio de **utilitários** para obter mais informações gerais anteriores declarações. * | Qual é o agendamento antes disso?
 TimeRemaining | Verifique o tempo restante até o próximo evento. | Exibir quanto tempo eu tenho antes das minhas reuniões. <br> Exibir a quantidade de tempo que tenho antes da minha próxima reunião começar.
 
### <a name="entities"></a>**Contabilidade**
Nome da Entidade | Tipo de entidade | Descrição | Exemplos | Ranhuras
-------|-----------------------|-------------|---------|--------
ContactName | personName | O nome de um participante de contato ou de reunião. | Conheça com **Betsy**. <br>  Conheça com **Aubrey** em 3 de julho às 19h | Betsy <br> Aubrey <br> Amy 
DestinationCalendar | único | O nome do calendário de destino. | almoço com o MOM Tuesday 12 **pessoal** <br> Usar meu **Google** agenda como meu calendário padrão. <br> Atualizar a classe Yoga para Mon qua às 15:00 lista no calendário **pessoal** . | Google <br> pessoais <br> empresa <br> principal
Duration | datetime | Duração de uma reunião, compromisso ou tempo restante. | Adicione à reunião de calendário de trabalho com Carlos para discutir detalhes de Scholarship amanhã às 11 horas por **20 minutos**. <br> Adicionar ao calendário um evento em metrô na sexta-feira, vou comer com Thomas por **uma hora** às 9 p.m. | uma hora <br> 2 dias <br> 20 minutos 
Término | datetime | Data de término de uma reunião ou um compromisso. | Calendário adicionar concerto a baixo da prefeitura Mary 3ª a **Mary quinta** | Mary quinta  
EndTime | datetime | Hora de término de uma reunião ou um compromisso. | Você pode torná-lo 2 30 para **três** | três 
Location | único | Localização do item de calendário, a reunião ou compromisso.  Os endereços, cidades e regiões são bons exemplos de localizações. | colocar uma reunião em **Fremont** para colocar o Tablet em Birmânia <br> reunião pro bono no **Edina** | 209 Nashville Gym <br> 897 casa de pancake <br> Garage 
MeetingRoom | único | Espaço para uma reunião ou um compromisso. | Adicionar à reunião de calendário de trabalho com Jake às 14h em seu **escritório** nesta sexta-feira | seu escritório <br> sala de conferência <br> Sala 2
MoveEarlierTimeSpan | datetime | A hora em que o usuário deseja mover uma reunião ou um compromisso anteriormente. | Mover minha data de almoço para frente em **30 minutos**. | 30 minutos <br> duas horas 
MoveLaterTimeSpan |  datetime | A hora em que o usuário deseja mover uma reunião ou um compromisso mais tarde. | Envie por push minha reunião com o orquídea-box de volta **4 horas**. | 4 horas <br> 15 minutos 
OrderReference | único | A posição ordinal ou relativa numa lista, identificando um item a obter. | Qual é o meu próximo compromisso para o amanhã? | seguinte
OriginalEndDate | datetime | Data de término original de uma reunião ou um compromisso. | Alterar minhas férias de terminando na **sexta-feira** até segunda-feira | Sexta-feira 
OriginalEndTime | datetime | Hora de término original de uma reunião ou um compromisso. | Faça o que termina em **3** ir até 4 | 3
OriginalStartDate | datetime | Data de início original de uma reunião ou um compromisso. | Alterar o compromisso de **amanhã**de 10 horas para quarta-feira às 9h  | amanhã 
OriginalStartTime | datetime | Hora de início original de uma reunião ou um compromisso. | Alterar o compromisso de amanhã de **10 horas** para quarta-feira às 9h | às 10 horas
PositionReference | ordinal | A posição absoluta em uma lista, identificando um item a ser recuperado. | O **segundo** | segundo <br> Não. 3 <br> número 5
RelationshipName | list | O nome da relação de um contato. | Adicionar almoço às 1:00 com minha **esposa** | Wife <br> marido <br> filial 
Slotattribute | único | O usuário de atributo deseja consultar ou editar. | alterar **local** do evento <br> Altere o **tempo** para sete horas | location <br> time 
Início | datetime | Data de início de uma reunião ou um compromisso. | Criar uma reunião na **quarta-feira** às 4 horas | Quarta-feira 
StartTime | datetime | Hora de início de uma reunião ou um compromisso. | criar uma reunião na quarta-feira às **4 horas** | 4 p.m.
Subject | simples, padrão. Outro | Assunto, como título de uma reunião ou um compromisso. | Qual é a hora da reunião de **preparação de terceiros** ? | Do dentista <br> Almoço com Leonor 
Message | simples, padrão. Outro | A mensagem a ser enviada aos participantes. | Alertar os participantes da reunião de jantar que vou **atrasar**. | Vou atrasar

## <a name="communication"></a>**Comunicação**
Solicitações para fazer chamadas, enviar textos/IMs, localizar/adicionar contatos e várias outras solicitações relacionadas à comunicação (geralmente de saída). _Somente o nome do contato_ declarações não pertence ao domínio de comunicação.

### <a name="intents"></a>**Tentativas**
Nome da intenção | Descrição | Exemplos
---------|----------|---------
AddContact | Adicione um novo contacto a lista do usuário de contatos. | Adicionar novo contato.  <br>   Salve esse número e coloque o nome como Jane.
Addflag | Adicionar sinalizador a um email | Sinalizar este email <br> Adicionar um sinalizador a este email.
AddMore | Adicione mais um e-mail ou o texto, como parte de uma composição sequencial de e-mail ou texto. | Adicione mais ao texto.  <br>   Adicione mais ao corpo do email.
Resposta | Responda a uma chamada de telefone de entrada. | Responda à chamada.  <br>   Escolha-o.
AssignContactNickname | Atribua uma alcunha para um contato. | Altere Julio para Dad.  <br>   Edite o apelido do Jim. <br>   Adicione apelido a Patti Owens.
Retorno | Retornar uma chamada telefônica. | Retorno de chamada.
CallVoiceMail | Ligue-se para o correio de voz do utilizador. | Conecte-me à minha caixa de correio de voz. <br>Caixa postal. <br>   Chamar correio de voz.
Cancelar | Cancelar uma operação. | Cancelar. <br>   Encerre-o.
CheckIMStatus | Verifique o status de um contato em mensagens instantâneas. | É o estado online de Jim definido imediatamente? 
CheckMessages | Verifique se há novas mensagens ou emails. | Marcar minha caixa de entrada <br>  Tenho um novo email?
Confirmar | Certifique-se uma ação. | Sim, enviá-lo. <br> Certo, confirmo que desejo enviar este email.
Chamada | Encerrar uma chamada telefônica. | Desligue a chamada. <br> Terminar.
FindContact | Localize informações de contacto por nome. | Localize o número de mum. <br>   Mostre-me o número de Carol.
FinishTask | Concluir a tarefa atual. | Pronto <br> Isso é tudo.
TurnForwardingOff | Desative o encaminhamento de chamada. | Parar de encaminhar minhas chamadas. <br> Desative o encaminhamento de chamada.
TurnForwardingOn | Ative o encaminhamento de chamadas. | Encaminhar as minhas chamadas para 3333 <br>  Alterne para o encaminhamento de chamada para 3333.
GetForwardingsStatus | Obter o estado atual de encaminhamento de chamada. | Meu encaminhamento de chamada é ativado? <br>   Diga-me se meu status de chamada estiver ativado ou desativado.
Getnotifications | Obtenha as notificações. | abrir minhas notificações <br>   Você pode verificar minhas notificações do Facebook no meu telefone
GoBack | Volte ao passo anterior. | Voltar ao twitter <br>   Volte uma etapa <br>   Retroceder
Ignorar | Ignore uma chamada de entrada. | Não responder <br>   Ignorar chamada
IgnoreWithMessage | Ignorar uma chamada de entrada e responder com texto em vez disso. | Não responder a essa chamada, mas em vez disso, a enviar uma mensagem. <br>   Ignorar e enviar uma mensagem de texto novamente.
Acesso telefónico | Fazer uma chamada telefónica. | Jim de chamada <br>   Disque 311.
FindSpeedDial | Determinar o número de speedial que um número de telefone está definido para e vice-versa. | O que é o meu número de acesso telefónico 5? <br>   É necessário aumentar o conjunto de velocidade? <br>   O que é o número de marcação para 941-5555-333?
Reencaminhar | Reencaminhar um e-mail | Encaminhe este email para Greg.
ReadAloud | Leia uma mensagem ou envie um e-mail ao utilizador. | Leia o texto. <br>   O que ela falou na mensagem?
PressKey | Pressione um botão ou um número no teclado. | Estrela de marcação. <br>   Prima 1 2 3.
QueryLastText | Consultar o último texto ou mensagem. | Quem me Textou? <br>   Quem me emailu recentemente?
Redial | Redial ou chamar um número novamente. | Redial. <br>   Redial minha última chamada.
Rejeitar | Rejeite uma chamada de entrada. | Rejeitar a chamada <br>   Não pode responder agora <br>   Não está disponível no momento e chamará novamente mais tarde.
Responder | Responder uma mensagem. | responder a desmorone de Lore <br>   responder digitando minha forma
SearchMessages | Pesquise as mensagens por algumas condições. | Você pode pesquisar meus emails enviados por Jane?
SendEmail | Envie um e-mail. Este visa aplica-se ao e-mail, mas não as mensagens de texto. | Email para Mike coisas: Mike, esse jantar da semana passada foi Splendid. <br>   Envie um email para Bob.
SendMessage | Envie uma mensagem de texto ou uma mensagem instantânea. | Enviar mensagem de texto para o Chris e Camila <br>   Greg da mensagem do Facebook <br>   
SetSpeedDial | Defina um atalho de discagem de velocidade para o número de telefone de um contacto. | Defina discagem um para Carol. <br>   Configure a discagem para o mom.
ShowCurrentNotification | Mostrar notificações atuais. | Há alguma nova notificação? <br> Mostre-me uma notificação.
ShowNext | Consulte o próximo item, por exemplo, uma lista de mensagens de texto ou e-mails. | Mostre a próxima Sílaba. <br>   Vá para a página seguinte.
ShowPrevious | Consulte o item anterior, por exemplo, uma lista de mensagens de texto ou e-mails. | Mostre anterior. <br>   Anterior. <br>   Aceda à anterior.
TurnSpeakerOff | Desative o telefone do orador. | Leve-me desativar orador. <br>   Desative viva-voz.
TurnSpeakerOn | Ative o telefone do orador. | Modo de viva-voz. <br>   Coloque o viva-voz em.

### <a name="entities"></a>**Contabilidade**
Nome da Entidade | Tipo de entidade | Descrição | Exemplos | Ranhuras
------|-------|----------|--------------|---------------
Attachment | único | O anexo que o usuário deseja enviar por texto ou email. | Enviar um **arquivo** por email do OneNote. <br> Envie meu **Doc** de manutenção para Katie. | file <br> Doc
AudioDeviceType | único | Tipo de dispositivo de áudio (palestrante, headset, microfone, etc.). | Responda usando as **mãos gratuitas**. <br> Disque novamente no **telefone do viva-voz**. | locutor <br> sem intervenção <br> tecnologia
Category | único | A categoria de uma mensagem ou email, a categoria deve ter uma definição clara no sistema de email, como "não lido", "sinalizador". Descrição c/o limpar definição, por exemplo, "novo" e "recente" não são categorias. | Marcar todos os emails como **lidos**  <br> Novo email de **alta prioridade** para Paul | importante <br> prioridade alta <br> ler
ContactAttribute | único | Um atributo do contato sobre as consultas do usuário.| Algum **aniversário** no mês que eu devo saber? | aniversário <br> endereço <br> número de telefone
ContactName | personName  | O nome de um destinatário de contacto ou de mensagem. | Enviar o email para **Stevens** | Stevens
Date/Time | datetime | DateTime de um email recebido. | Leia os emails de **hoje** <br> Quem me e-Mailu **hoje**? <br> Quem telefone às **19h**? | hoje <br> amanhã
DestinationPhone | único | O usuário de destino deseja chamar ou enviar um texto para. | Faça uma chamada para a **casa** <br> Enviar uma mensagem de texto para **página inicial** | internamente <br> home page
EmailAddress | email | O usuário de endereço de email deseja enviar ou consultar. | Enviar email paraMegan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | simples, padrão. Outro | O texto usado como a linha de assunto para uma mensagem de e-mail. | Redigir o email para David com o assunto **Ei**  | RE: interessante história
Chave | único | O usuário principal deseja pressionar. | Pressione a tecla de **espaço** . <br> Pressione **9** | agrava <br> asterisco <br> 8
Linha | único | O usuário de linha deseja usar para enviar um email ou um texto do. | Leia meu último email do **hotmail** . <br> Ligue para Peter por **celular**. <br> Chame o Dad usando minha linha de **trabalho** .| hotmail <br> Skype <br> Célula britânica
SenderName | personName | O nome do remetente. | Leia o email de **David** <br> Emails de Chanda | David <br> Chanda
FromRelationshipName | único | O nome do relacionamento do remetente. | Ler mensagem do **pai**. <br> Você pode ler todas as mensagens de texto do **Mom**? | Papai <br> Mom 
Message | simples, padrão. Outro |  A mensagem a enviar um e-mail ou texto.  | Enviar email dizendo "Estou**ocupado**". | Estou ocupado
OrderReference | único | A posição ordinal ou relativa numa lista, identificando um item a obter. | Qual foi a **última** mensagem que enviei? <br> Leia email da Nokia **mais recente** . <br> Ler **novas** mensagens de texto. | última <br> latest <br> versões <br> mais recente
PositionReference | simples, ordinal | A posição ordinal ou relativa numa lista, identificando um item a obter.| Qual foi a **primeira** mensagem que enviei? <br> O **terceiro** .| Primeiro <br> testa
phoneNumber | phoneNumber | O usuário do número de telefone deseja chamar ou enviar um texto para. | enviar um texto para **4 1 5 6 8 4 5 2 8 4** | 3525214446
RelationshipName | único | O nome da relação de um contato ou destinatário da mensagem. | Email para minha **esposa** | Wife
SearchTexts | simples, padrão. any | Os textos usados para filtrar emails ou mensagens | Pesquisar todos os emails que contenham "**Surface pro**" | Surface pro
Disca | único | A velocidade de discagem. | Chame **3 4 5**. <br> Defina a discagem **rápida.** | 345 <br> 5

## <a name="email"></a>**e-mail**
O email é um subdomínio do domínio de *comunicação* . Ele contém principalmente solicitações para enviar e receber mensagens por emails.
### <a name="intents"></a>**Tentativas**
Nome da intenção | Descrição | Exemplos
---------|----------|---------
AddMore | Adicione mais um e-mail ou o texto, como parte de uma composição sequencial de e-mail ou texto. | Adicione mais ao corpo do email.
Cancelar | Cancelar uma operação. <br> ***Aviso**: Consulte o domínio de **utilitários** para mais cancelar declarações geral. * | Cancelar. Não o envie. <br> Encerre-o.
CheckMessages | Verifique se há novas mensagens/emails sem consulta condicional. Se houver alguma condição, o declarações pertencerá à intenção de *SearchMessage* . | Marque minha caixa de entrada. <br> Tenho novos emails? 
Confirmar | Confirme uma ação em andamento relacionada ao processamento de emails. <br> ***Aviso**: Consulte o domínio de **utilitários** para saber mais declarações geral. * | Sim, enviá-lo. <br> Confirmo que eu queira envie este e-mail.
Eliminar | Exclua um email ou os emails filtrados por algumas condições. | Colocar o email na lixeira <br> Esvaziar minha caixa de entrada. <br> Remova o email de Mary.
ReadAloud | Leia uma mensagem ou envie um e-mail ao utilizador. <br> ***Aviso**: Consulte o domínio de **utilitários** para obter mais ReadAloud geral declarações. *  | Leia o email enviado por Mary.
Responder | Responder uma mensagem ao email atual. | Crie uma resposta para o email. <br> Responda digitando "Agradecemos muito, o que mais diz, João".
SearchMessages | Pesquise as mensagens por algumas condições, incluindo nome do remetente, hora e assunto. | Mostre-me as mensagens de Nisheen. <br> Você pode pesquisar meus emails intitulados "ABC"?
SendEmail | Envie um e-mail. | Email para Mike: Mike, esse jantar da semana passada foi Splendid. <br> Envie um email para Bob.
ShowNext | Consulte o próximo item (s) em uma lista de mensagens de texto ou emails. <br> ***Aviso**: Consulte o domínio de **utilitários** para obter mais da próxima declarações geral. * | Mostre a próxima Sílaba. <br> Vá para a página seguinte.
ShowPrevious | Mostrar os itens anteriores em uma lista de mensagens de texto ou emails. <br> ***Aviso**: Consulte o domínio de **utilitários** para obter mais informações gerais anteriores declarações. * | Mostre anterior. <br> Anterior. <br> Aceda à anterior.
Reencaminhar | Encaminhe um email. | Encaminhe este email para Greg.
Addflag | Adicionar sinalizador a um email. | Sinalizar este email <br> Adicionar um sinalizador a este email.
QueryLastText | Consulte o último email. | Quem enviou email para mim? <br> Quem me emailu recentemente?


### <a name="entities"></a>**Contabilidade**
Nome da Entidade | Tipo de entidade | Descrição | Exemplos | Ranhuras
------|-------|----------|--------------|---------------
Attachment | único | O anexo que o usuário deseja enviar por texto ou email. | Enviar um **arquivo** por email do OneNote. <br> Envie meu **Doc** de manutenção para Katie. | file <br> Doc
ContactName | personName  | O nome de um destinatário de contacto ou de mensagem. | Enviar o email para **Stevens** | Stevens
Date | datetime | Data de um email recebido. | Leia os emails de **hoje** <br> Quem me e-Mailu **hoje**? | hoje
EmailAddress | email | O usuário de endereço de email deseja enviar ou consultar. | Enviar email paraMegan.Flynn@MKF.com<br> abc@outlook.com 
EmailSubject | simples, padrão. Outro | O texto usado como a linha de assunto para uma mensagem de e-mail. | Redigir o email para David com o assunto **Ei**  | RE: interessante história
SenderName | personName | O nome do remetente. | Leia o email de **David** <br> Emails de Chanda | David <br> Chanda
FromRelationshipName | único | O nome do relacionamento do remetente. | Ler mensagem do **pai**. <br> Você pode ler todas as mensagens de texto do **Mom**? | Papai <br> Mom 
Message | simples, padrão. Outro |  A mensagem a enviar um e-mail ou texto.  | Enviar email dizendo "Estou**ocupado**". | Estou ocupado
Category | único | A categoria de uma mensagem ou email, a categoria deve ter uma definição clara no sistema de email, como "não lido", "sinalizador". Descrição c/o limpar definição, por exemplo, "novo" e "recente" não são categorias. | Marcar todos os emails como **lidos**  <br> Novo email de **alta prioridade** para Paul | importante <br> prioridade alta <br> ler
OrderReference | único | A posição ordinal ou relativa numa lista, identificando um item a obter. | Qual foi a **última** mensagem que enviei? <br> Leia email da Nokia **mais recente** . <br> Ler **novas** mensagens de texto. | última <br> latest <br> versões <br> mais recente
PositionReference | simples, ordinal | A posição ordinal ou relativa numa lista, identificando um item a obter.| Qual foi a **primeira** mensagem que enviei? <br> O **terceiro** .| Primeiro <br> testa
RelationshipName | único | O nome da relação de um contato ou destinatário da mensagem. | Email para minha **esposa** | Wife
Time | datetime | Time | Enviar email **hoje**. | noite
SearchTexts | simples, padrão. any | Os textos usados para filtrar emails ou mensagens | Pesquisar todos os emails que contenham "**Surface pro**" | Surface pro 
Linha | único | O usuário de linha deseja usar para enviar um email do. | Leia meu último email do **hotmail** . <br> Envie um email da minha **conta corporativa**.| hotmail <br> conta corporativa 

## <a name="homeautomation"></a>**HomeAutomation**
O domínio HomeAutomation fornece intenções e entidades relacionadas ao controle de dispositivos do Smart Home. Basicamente, damos suporte ao comando Control relacionado a luzes e ar-condicionado. Mas tem algumas capacidades gerais de generalização em outros dispositivos elétricos.
### <a name="supported-devices-and-properties"></a>**Dispositivos e propriedades com suporte**
Dispositivo | properties
-------|---------
Sensor de temperatura | Temperatura
Luz, lâmpada | Ligado, brilho, cor
TV, rádio | Ativado, volume
Ar condicionado (A/C), termostato | Desligado, temperatura, energia
SMPS | Ligado, energia
Imtomadas, DVD Player, Ice Maker etc. | Ativado

### <a name="intents"></a>**Tentativas**
Nome da intenção | Descrição | Exemplos
---------|----------|---------
 TurnOff | O usuário deseja desligar o dispositivo ou as configurações.  | Desative as luzes. <br> Desligar algo. <br> Algo apagado.
 TurnOn | Ative um dispositivo ou ative e defina o dispositivo para uma configuração ou modo específico. | Ligue a luz para 50%. <br> Ativar o meu café <br> Você pode ativar o meu café?
 Setdevice | O usuário deseja definir o dispositivo para uma configuração ou tipo específico.  | Defina o ar condicionado como 26 graus. <br> Vire as luzes azuis. <br> Chame essa luz como Nightlight.
 QueryState | O usuário solicita o estado de um dispositivo ou uma configuração.  | Qual é o termostato definido como? <br> A/C é ativada? <br> Qual é a temperatura do quarto?
 TurnUp | Ative as configurações ou o brilho dos dispositivos. | Clarear as luzes em 75 por cento. <br> O brilho aqui em 10%.  <br> Torne-o mais quente na sala de vida.
 TurnDown | Desligar mas não desligar um dispositivo, seja por meio de esmaecimento, da temperatura ou do brilho das luzes. | Desative a biblioteca por 44%. <br> Esmaecer as luzes. <br> Torne a sala mais fria.
### <a name="entities"></a>**Contabilidade**
Nome da Entidade | Tipo de entidade | Descrição | Exemplos
-------|----------|--------------|---------------
DeviceName | Lista | Texto definido pelo usuário para seus dispositivos. | Azul<br> Natal <br> Escritório
DeviceType | Lista | Dispositivos com suporte. | Noturna <br> Condicionador de ar <br> nightlight
Location | único | Local ou sala no qual o dispositivo está. | Estrelas<br> Cybercafé <br> Quartos
NumericalChange | único | Quantidade pela qual uma configuração é aumentada ou diminuída. <br> <br> _O slot aparece apenas com as intenções turn_up e turn_down._ | 3<br> 50%<br>
OrderReference | ordinal | A finalidade desse slot é capturar a seleção dos itens. Indica a posição do item em uma lista. | Primeiro<br> Segundo (a)
Quantificador | Lista | Quantificador indica quantas instâncias de uma determinada entidade são referenciadas. Por exemplo, "todos", "a cada", etc. | Todos<br> Cada<br> Tudo
Definição | Simples | A configuração para o qual o usuário deseja definir seu dispositivo, que inclui cena, nível, intensidade, cor, modo, temperatura, estado do dispositivo. | Azul<br> 72 <br> Mapa 
SettingType | Lista | A configuração do dispositivo em que o usuário está interessado. | Temperatura<br> 
Data/hora |  datetime | Hora e duração da operação de um dispositivo| 5 minutos <br> 3 p.m.
Unidade | Lista | Para marcar palavras como ' graus ', ' percent ', "Fahrenheit", "Celsius", cada termo de unidade deve ser marcado separadamente. | Graus<br> Percent


## <a name="notes"></a>**Notas**
Observação o domínio facilita a criação de anotações e a gravação de itens para os usuários.
### <a name="intents"></a>**Tentativas**
Nome da intenção | Descrição | Exemplos
---------|----------|---------
AddToNote | Adicione informações a uma nota aberta. | Adicione à minha nota de planejamento para entrar em contato com meu chefe sobre o projeto.
Limpar | Limpe todos os itens de uma nota já existente. | Remover todos os itens da minha nota de férias. <br>Limpar tudo da minha anotação de leitura.
Confirmar | Certifique-se uma ação de uma nota. <br> ***Aviso**: Essa tentativa inclui principalmente a ação "confirmar" para o cenário de observação. Se você precisar de mais expressões gerais em "Confirm", aproveite a intenção de "confirmar" no domínio de **utilitários** . * | Ele está OK para esta observação. <br>Estou confirmando a manutenção de todos os itens nas listas.
Criar | Crie uma nova nota. | Crie uma observação. <br>Observação para lembrar que Jason está na cidade da primeira semana de maio. 
Eliminar | Elimine uma nota de toda. | Excluir minha nota de férias. <br>Excluir minha nota de compras.
ReadAloud | Leia uma observação em voz alta. | Leia-me a primeira observação. <br>Leia-me os detalhes.
Fechar | Feche a nota atual. | Feche a observação. <br>Feche a nota atual.
Aberto | Abra uma nota pré-existente. | Abra minha nota de chamada. <br>Abra a nota "planejamento".
RemoveSentence | Remova uma frase para uma nota. | Remova a última frase. <br>Exclua os chips da minha nota de supermercado. <br>Remover canetas da minha nota de compra da escola
ChangeTitle | Altere o título da nota. | Nomeado esta observação como "planejamento".

### <a name="entities"></a>**Contabilidade**
Nome da Entidade | Tipo de entidade | Descrição | Exemplos 
------- | ------- | ------- | -------
Text | simples, padrão. Outro | O texto de uma nota ou lembrete. | Esticar antes de movimentação <br> amanhã longo prazo.
Cargo | simples, padrão. Outro | Título de uma nota. | groceries <br> pessoas para chamar <br> tarefas pendentes
CreationDate | datetimeV2 | Este slot é para quando o usuário solicita anotações criadas em uma determinada janela de data/hora. | 
Quantificador | Lista | Quando um usuário pede para executar uma ação nos itens ' all', ' a cada ' ou ' qualquer ' ou todo o texto em uma nota. | tudo <br> qualquer <br> cada
OrderReference | ordinal | O usuário deseja fazer ações com os itens ' primeiro ', ' último ', ' Avançar ', etc. | primeiro <br> última


## <a name="places"></a>**Limita**
Os locais incluem empresas, instituições, restaurantes, espaços públicos e endereços. O domínio dá suporte ao local localizando e consultando as informações em um local público, como local, horas operacionais e distância. 
### <a name="intents"></a>**Tentativas**
Nome da intenção | Descrição | Exemplos
---------|----------|---------
MakeCall | Fazer uma chamada telefónica para um local. | Applebees em 1000/200 Rojas St e Call.
FindPlace | Pesquisar um local (empresas, instituição, restaurante, espaço público, endereço). Mas não: <li> Somente nome de negócios: Starbucks <li> Somente nome do local: Seattle/Noruega <li> Cuisine, comida ou produto: Pizza/guacamole/italiano | Onde posso encontrar a biblioteca mais próxima? <br> Starbucks em Seattle. <br> Onde posso encontrar a biblioteca mais próxima? <br> 
GetAddress | Pedir o endereço de um local. | Mostre-me o endereço do Guu na rua do Robson. <br> O que é o endereço da Starbucks mais próximo?
GetDistance | Pergunte sobre a distância do local atual para um local específico. | Distância é feriado Scritpt?<br> Até onde Bellevue o quadrado aqui? <br> Qual é a distância para Tahoe?
GetPhoneNumber | Pedir o número de telefone de um local. | O que é o número de telefone da Starbucks mais próximo?<br> Forneça o número para a Home Depot. <br> Um número de telefone para Disneyland.
GetPriceRange | Solicitar o intervalo de por consumo de capita para um local. | Preço médio de J. sushi em Seattle. <br> É a metade do preço Cineplex às quartas-feiras? <br> Qual é um custo de jantar lobster todo em Sizzler?
GetStarRating | Pedir a classificação em estrelas de um local. | Como o Zucca é classificado?<br> Quantas estrelas tem o Lavanderia francês?<br> É bom aquarium no Monterrey?
GetHours | Fazer sobre as horas de funcionamento para um local. | A que hora Safeway fechar?<br> Quais são as horas para a home page Depot?<br> Starbucks ainda está aberto?
GetReviews | Pedir revisões de um local. | Mostre-me as revisões para o bolos Factory. <br> Leia as revisões do Cineplex. <br> Há alguma revisão recente para o Humperdinks?
GetMenu | Peça para os itens de menu de um restaurante. | Zucca serve nada vegan? <br> O que há no menu em Sizzler? <br> Mostre-me o menu do Applebee.
RatePlace | Classificar um local. | avaliação de 4 estrelas para pizza de máximo em Kimberly. <br> Forneça 4-Star para bonefish em TripAdvisor. <br> Crie uma revisão de 4 estrelas para la Hacienda.
AddFavoritePlace | O usuário deseja adicionar um local à sua lista de favoritos ou MVP. | Salvar este local em meus favoritos. <br> Adicionar melhor compra aos meus favoritos.

### <a name="entities"></a>**Contabilidade**
Entidades LUIS | Tipo de entidade | Descrição | Exemplos | Exemplos de expressão
--------------|-------------|-------------|----------|-------------------
AbsoluteLocation | único | A localização ou o endereço de um local. | Da Palo Alto <br> 300 112th Ave SE <br> Seattle | **1020 Middlefield Rd.** no **Palo Alto** <br> lojas de semente de pássaro em **Seattle** <br> Obtenha a distância daqui a **300 112Th ave se**.
Características | único | As características e os benefícios do objetivo de um local público. | frente marítima <br> estacionamento gratuito | Kirkland **orla marítima** frutos do mar. <br> Há algum **estacionamento livre** perto de mim?
Cuisine | único | Um tipo de comida, cuisine ou cuisine nationality. | Chinês <br> Italiano <br> Sushi <br> Noodle <br> | Ajude-me a encontrar um restaurante **chinês** . <br> Quais são as horas de abertura do restaurante **sushi** ? <br> Onde está a casa **Steak** mais próxima?
Date | datetime | DateTime ou Duration para a data do local de destino. | amanhã <br> hoje <br> 18:00 | Que tempo aquário fechar **amanhã**? <br> a oficina de bicicletas mais próxima que é aberta após **6 p.m.**
distância | dimensão | A distância para um local público da posição currenct do usuário. | 15 quilómetros <br> 10 milhas | uma loja de roupas dentro de **15 milhas** <br> Um restaurante de crianças com apenas **10 milhas** de distância
MealType | Lista | Tipo de refeição como café da manhã ou almoço. | Café da manhã <br> Jantar | Pesquisar **café da manhã** Greenwood Seattle <br> Encontre-me um lugar para fazer meu **almoço**.
Vizinha | Lista | Descreva um local próximo sem localização ou endereço absoluto. | mais próximo <br> nesta área <br> em nosso lugar | Encontre o restaurante indiano **mais próximo** . <br> Onde está meu Wetherspoon **local** ? <br> Um bom restaurantes **em nosso lugar**?
OpenStatus | Lista | Indica se um lugar é aberto ou fechado. | abre <br> Fechado | Que tempo o yogurt Land **fechou** hoje? <br> Quais são as horas de **abertura** para Costco?
PlaceName | único | O nome de um destino que é uma empresa, restaurante, attraction público ou instituição. O nome do local pode conter um LocalType se for usado em comum. | Central Park <br> Safeway <br> Walmart| Que tempo o **Safeway** farmácia abre? <br> O **Walmart** está aberto?
PlaceType | único | O tipo de um destino que é um comercial, restaurante, attraction público ou instituição. | Restaurante <br> Opera <br> cinema | **cinemas** em Cambridge <br> Há um **restaurante** ao meu redor?
PriceRange | único | A faixa de preço dos produtos ou serviço do local. | baratos <br> econômico <br> caro | Encontrar reparo de dispositivo **acessível** <br> O que é um lugar de pizza **barato** que está aberto agora?
Produto | único | O produto oferecido um local. | roupas <br> televisões | Onde está o melhor lugar para obter **comida**? <br> Encontre-me Kilbride leste procurando **televisões**.
Classificação | único | A classificação de um local. | 5 estrelas <br> Início <br> recomendá | Há **bons** lugares para sair e comer amanhã <br> **melhor** Restaurantes de Amsterdã <br> Liste as três **principais** lojas de pizza.


## <a name="restaurantreservation"></a>**RestaurantReservation**
O domínio de reserva de restaurantes dá suporte a tentativas de tratamento de reservas para restaurantes.
### <a name="intents"></a>**Tentativas**
Nome da intenção | Descrição | Exemplos
---------|----------|---------
Reserva | Pedir uma reserva de um restaurante. | Reserve em Zucca para dois para hoje. <br> Marque uma tabela para amanhã. <br> Tabela de 3 no Palo Alto às 7. <br> Faça uma reserva para 3 em vermelho Lobster.
DeleteReservation | Cancelar ou excluir uma reserva para um restaurante. | Cancele a reserva em Zucca amanhã noite. <br> Esquecer minha reserva de Lobster vermelha às 7:00 próxima sexta-feira. <br> Não preciso da reserva para Zucca, cancelo-a.
ChangeReservation | Alterar a hora, o local ou o número de pessoas para uma reserva de restaurantes existente. | Alterar minha reserva para 9 p.m. <br> Altere minha reserva de Zucca para Red Lobster. <br> 7 pessoas em vez de 6 para a reserva em Zucca.
Confirmar | Confirme uma ação relacionada à reserva. <br> ***Aviso**: Essa tentativa inclui principalmente a ação "confirmar" para o cenário de reserva de restaurantes. Se você precisar de mais expressões gerais em "Confirm", aproveite a intenção de "confirmar" no domínio de **utilitários** . * | Sim, fiz as reservas para hoje às 8h, no massas Maker. <br> Sim, basta desbooká-lo. <br> Confirme a reserva na barra sushi.
FindReservationDetail | Exibe as informações detalhadas de uma reserva existente. | Localizar minha reserva em renascimento San Diego <br> Mostrar a reserva amanhã. <br> Exibir detalhes da minha reserva para Zucca.
FindReservationWhere | Verifique o local absoluto da reserva. | Onde está o local de Zucca em minha reserva? <br> Mostrar a localidade da minha reserva amanhã.
FindReservationWhen | Verificar a hora exata da reserva | Quando é a reserva de Zucca para amanhã? <br> A hora para minha reserva em vermelho Lobster.
Rejeitar | O usuário rejeita o assistente virtual proposto no gerenciamento de uma reserva. <br> ***Observe**:P concessão consulte o domínio de **utilitários** para mais rejeitar declarações geral. * | Não é necessário definir o evento. | Não, não quero alterar a reserva. <br> Não, não o desmarque, fiz um erro.

### <a name="entities"></a>**Contabilidade**
Entidade LUIS | Tipo de entidade | Descrição | Exemplos
-------|------|---------|-------------------
Endereço | único | Uma localização do evento ou o endereço para uma reserva. | Da Palo Alto<br>300 112th Ave SE<br>Seattle
Atmosfera | Lista | Uma descrição da atmosfera de um restaurante. | romantic<br>casual<br>bom para grupos<br>Bom
Cuisine | único | Um tipo de comida, cuisine ou cuisine nationality. | Chinês<br>Italiano<br>Peso<br>Sushi<br>Noodle<br>steak
MealType | Lista | Um tipo de refeição associado a uma reserva. | Café da manhã<br>Jantar<br>Almoço<br>Supper
PlaceName | único | O nome de um restaurante | Zucca<br>Fábrica de bolos<br>Lobster vermelho
Classificação | único | A classificação de um local ou um restaurante. | 5 estrelas<br>3 estrelas<br>4-estrelas
NumberPeople | único | Número de pessoas para reserva | 3<br>seis
Time | datetime| O tempo para reserva de restaurantes | amanhã<br>noite<br>7:00 p.m.<br>Véspera de Natal


## <a name="todo"></a>**ToDo**
O domínio _todo_ fornece tipos de listas de tarefas para que os usuários adicionem, marquem e excluam seus itens tarefas pendentes.
### <a name="intents"></a>**Tentativas**
Nome da intenção | Descrição | Exemplos
---------|----------|---------
Addtodo | O usuário deseja adicionar itens de tarefa para qualquer um dos tipos de lista. |  Lembre-me de comprar o leite. <br> Adicione um item chamado "comprar leite" à minha lista de tarefas pendentes
Confirmar | O usuário deseja confirmar uma ação em andamento. O expressão contém um sinal explícito como "Sim" para confirmar uma operação. <br> <br> ***Aviso**: Essa tentativa inclui principalmente a ação "confirmar" para o cenário de tarefas pendentes. Se você precisar de mais expressões gerais em "Confirm", aproveite a intenção de "confirmar" no domínio de **utilitários** . * | Exclua a tarefa. <br> Tenho certeza de que desejo adicionar essa tarefa. <br> Sim, quero fazer isso.
Cancelar | O usuário deseja cancelar a ação em andamento.  <br> <br> ***Aviso**: Essa tentativa inclui principalmente a ação "confirmar" para o cenário de reserva de restaurantes. Se você precisar de mais expressões gerais em "Confirm", aproveite a intenção de "confirmar" no domínio de **utilitários** . * | Não, esqueça. <br> Basta cancelar a tarefa. <br> Não, não o adicione.
DeleteToDo | Exclua um item da lista de tarefas referindo-se a seu pedido ou exclua todos os itens pendentes. | Remover todas as tarefas. <br> Ajude-me a excluir o segundo.
MarkToDo | Marque uma tarefa como concluída ou feita referindo-se a seu conteúdo de ordem ou tarefa. | Marque a tarefa "comprar leite" como concluída. <br> Concluir a leitura da tarefa. <br> Concluir tudo.
ShowNextPage | Uma lista será dividida em várias páginas para exibição. Mostrar itens de lista na próxima página do usuário. | Você pode mostrar a próxima página na lista de compras? <br> O que há no próximo? <br> Passos seguintes?
Inpreviouspagetype | Mostrar itens de lista na página anterior do usuário. | Mostrar anterior. <br> Preciso verificar as tarefas anteriores.
Tarefas pendentes | Mostrar todos os itens na lista de tarefas pendentes. | Mostrar minha lista de compras. <br> Mostrar minha lista de compras.

### <a name="entities"></a>**Contabilidade**
Entidade LUIS | Tipo de entidade | Descrição | Exemplos
-------|------|---------|-------------------
ContainsAll | único | Representar todos ou todos os itens na lista de tarefas | Você pode ajudar a remover **todas as** tarefas. <br> Conclua **tudo**.
ordinal | ordinal | Uma referência ordinal ou numérica a um item. | Marque a **terceira** como concluída. <br> Excluir a **primeira** tarefa.
ListType | único | Tipo de lista de tarefas.  | Adicionar sapatos à minha lista de **compras** .
FoodOfGrocery | Lista | Detectar uma lista de itens de alimentos | Lembre-me de comprar o **leite**. <br> Adicione **reforçar** à minha lista de compras.
TaskContent | simples, padrão. any | Detectar o conteúdo de uma tarefa. | Lembre-me de **telefonar para minha mãe** . <br> Adicione o **aniversário de comemoração de João** à minha lista de tarefas pendentes


## <a name="utilities"></a>**Utilitários**
O domínio de _utilitários_ é um domínio geral entre todos os modelos predefinidos do _Luis_ que contém tentativas comuns e declarações em cenários de diferença.
### <a name="intents"></a>**Tentativas**
Nome da intenção | Descrição | Exemplos
---------|----------|---------
 Cancelar | Cancelar uma ação/solicitação/tarefa/serviço. | Cancele-o. <br> Não se preocupe, esqueça.
 Confirmar | Certifique-se uma ação. | Claro <br> Sim, por favor <br> Veja.
 Rejeitar | O usuário rejeita o assistente virtual proposto. | Não
 FinishTask | Conclua uma tarefa iniciado do utilizador. | Estou pronto. <br> Isso é tudo. <br> Terminar.
 GoBack | Volte uma etapa ou regressar ao passo anterior. | Volte uma etapa. <br> Voltar.
 Ajuda | Pedido para obter ajuda. | Ajuda. <br> Abra a ajuda.
 Repetir | Repetir uma ação. | Diga-o novamente.
 ShowNext | Mostrar ou informar o próximo item (ns). | Mostre a próxima Sílaba.
 ShowPrevious | Mostra o item anterior de uma série. | Mostrar anterior.
 StartOver | Reinicie a aplicação ou iniciar uma nova sessão. | Reiniciar.
 Parar | Peça ao assistente virtual para parar de conversar.  | Pare de dizer isso.
 SelectAny | O usuário pede para selecionar qualquer item ou resultado mostrado na tela. | Qualquer um deles. <br> Selecione qualquer um.
 SelectNone | O usuário seleciona nenhum dos itens existentes e solicita mais opções. | Dê-me outras sugestões. <br> Nenhum deles.
  SelectItem | O usuário pede para selecionar um item ou os resultados mostrados na tela. | Selecione a terceira. <br> Selecione o canto superior direito.
 Escalar | Peça a um serviço de atendimento ao cliente para lidar com os problemas. | Posso falar com uma pessoa?
 ReadAloud | Leia algo em voz alta para o usuário. | Leia esta página. <br> Leia em voz alta.

### <a name="entities"></a>**Contabilidade**
Entidade LUIS | Tipo de entidade | Descrição | Exemplos
------------|-------------|-------------|---------
ordinal | ordinal | Uma referência ordinal ou numérica a um item. | O **segundo** . <br> **Próximo** .
number | number | Quantidade de itens que o usuário deseja | Os próximos **3** itens
DirectionalReference | único | Um ponto de referência para o local na tela em que um item está localizado. | O correto <br> canto superior

## <a name="weather"></a>**MSNBC**
O clima de domínio concentra-se em verificar a condição e os avisos meteorológicos com localização e hora ou tempo de verificação por condições de clima.
### <a name="intents"></a>**Tentativas**
Nome da intenção | Descrição | Exemplos
---------|----------|---------
 CheckWeatherValue | Solicite clima ou um fator relacionado ao clima para um local na previsão ou informações anteriores. <br> Os fatores relacionados ao clima incluem: <li> temperatura </li> <li> chuva/neve/precipitação </li> <li> seca/molhado/umidade </li> <li> neblina </li> <li> Índice UV </li> <li> polegadas de chuva/neve </li> | O que é o índice de qualidade de ar em Pequim? <br> Qual é o nível de chuva esperado em Seattle em março? <br> Registre a temperatura mais alta do Havaí.
 CheckWeatherTime | Solicite o tempo dos fatores previstos ou históricos relacionados ao clima para um local. | Quando espera-se que ela seja Rain? <br> Um bom momento para ir para o Canadá <br> Quando é o mês mais interessantes em Minnesota?
 QueryWeather | Pergunte sobre a condição do clima ou os fatores relacionados ao clima para um local específico para o qual uma resposta "Sim, não ou talvez" é esperada ou solicite a recomendação de atividade que depende da condição do clima. | Ele será chuva amanhã? <br> Está ensolarado hoje? <br> Pode ser muito cedo ir para o Alasca?
 ChangeTemperatureUnit | Altere a unidade de clima, incluindo Celsius, Kelvin e Fahrenheit. | Converter em Celsius. <br> Posso obtê-lo em Kelvin?
 GetWeatherAdvisory | Obtenha comunicados meteorológicos ou alertas de um local específico. | Há comunicados meteorológicos em Memphis? <br> Há alertas meteorológicos para minha área?

### <a name="entities"></a>**Contabilidade**
Entidade LUIS | Tipo de entidade | Descrição | Exemplos
------------|-------------|-------------|---------
Location | gráfico | O local absoluto ou implícito para uma solicitação do clima. | Palo Alto<br>Xangai<br>Seattle<br>Delvina<br>
Date/Time   | datetime | DateTime ou Duration para consultar o clima. | Novembro<br>por hora<br>manhã<br>Este fim de semana<br>10 dias<br>
AdditionalWeatherCondition | list | Palavra de descrição adicional para meteorologia, como a velocidade ou direção do vento. | direction<br>Rápido<br>rápida
Histórico | único | Palavras de descrição da condição de clima histórica, incluindo média de casos arestados de, no período de tempo anterior. | atrasado<br>histórico/história<br>sazonais<br>melhor tempo<br>já registrado
PrecipitationUnit | dimensão | O precipitação de neve ou chuva. | 5 polegadas<br>6 cm
SuitableFor | único | A descrição de uma atividade humana sob uma condição de clima, que é comum quando os usuários consultam conselhos de atividade que dependem da condição do clima. | saco<br>termo<br>Swimming
TemperatureUnit |temperatura | temperatura | 18 Celsius<br>7 Kelvin graus
WeatherRange | único | A condição específica de temperatura, vento e outras condições de clima entre um período de tempo | máximo<br>elevada<br>pequena<br>média alta<br>Mais elevado
WeatherCondition | único | Descrição da condição de clima | ensolarado<br>Redundant<br>reinamento<br>temperatura<br>neve<br>acesso frequente
WindDirectionUnit | list | As palavras de direção do vento | Unidos<br>Sul<br>Ásia<br>noroeste<br>Nordeste


## <a name="web"></a>**Web**
O domínio da Web fornece a intenção e as entidades para pesquisar um site.
### <a name="intents"></a>**Tentativas**
Nome da intenção | Descrição | Exemplos
---------|----------|---------
 Pesquisa da | Uma solicitação para navegar para um site especificado ou Pesquisar em um mecanismo de pesquisa. | Superfície de pesquisa no google.com. <br> Localizar música de aniversário feliz na Web <br> Vá para www.twitter.com.

### <a name="entities"></a>**Contabilidade**
Entidade LUIS | Tipo de entidade | Descrição | Exemplos
------------|-------------|-------------|---------
SearchEngine | Lista | O usuário do mecanismo de pesquisa deseja usar. | Bing <br> Google
ProcurarTexto | simples, padrão. Outro | O usuário de texto deseja pesquisar. <br> _Marque "amigos no Facebook" como ProcurarTexto se o site após "in" não for um mecanismo de pesquisa. A URL também deve marcar como ProcurarTexto._ | Filmes <br> Aprendizagem aprofundada <br> Tom de cruzeiro
Associar | url | O link do site. | www.twitter.com

