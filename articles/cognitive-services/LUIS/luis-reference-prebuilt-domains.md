---
title: Referência de domínio pré-concebida
titleSuffix: Azure
description: Referência para os domínios criados previamente, que são coleções pré-criados de objetivos e entidades do Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: e1e579233a5ad1af1ef8ee84019cd995959d3b2b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433778"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Referência de domínio pré-criado para a sua aplicação LUIS
Esta referência disponibiliza informações sobre o [domínios pré-concebidos](luis-how-to-use-prebuilt-domains.md), que são coleções pré-criados de objetivos e entidades que oferece o LUIS.

[Domínios personalizados](luis-how-to-start-new-app.md), por outro lado, começar com nenhum intenções e modelos. Pode adicionar qualquer intenções de domínio pré-criado e entidades para um modelo personalizado.

## <a name="list-of-prebuilt-domains"></a>Lista de domínios pré-concebidos
LUIS oferece 20 domínios pré-concebidos. 

| Domínio pré-criado | Descrição | Idiomas Suportados |
| ---------------- |-----------------------|:------:|
| Calendário | O domínio de calendário fornece intenções e entidades para adicionar, eliminar, ou editar um compromisso, a verificar a disponibilidade de participantes e encontrar informações sobre um evento de calendário.| en-US<br/> zh-CN |
| Câmara | O domínio de câmera fornece intenções e entidades para tirar fotografias, vídeos de gravação e vídeo de difusão para uma aplicação.| en-US |
| Comunicação | Envio de mensagens e efetuar chamadas telefónicas.| en-US <br/> zh-CN |
| entretenimento  | Processamento de consultas relacionadas com de músicas, filmes e TV.| en-US |
| Eventos | Reserva de bilhetes para concertos, festivals, jogos de desportos e comédia mostra.| en-US |
| Adequação | Processamento de pedidos relacionados com atividades de adequação de controlo.| en-US |
| Jogos | Processamento de pedidos relacionados com a parte de jogo num jogo multijogador.| en-US |
| HomeAutomation | Controlar dispositivos domésticos inteligentes, como luzes e aplicações.| en-US<br/> zh-CN |
| MovieTickets | Reserva de pedidos de filmes numa linha de cinema.| en-US |
| Música | Reprodução de música num player de música.| en-US<br/> zh-CN |
| Nota | O domínio de nota fornece intenções e entidades relacionadas com a criação, edição e encontrar notas.| en-US<br/> zh-CN |
| OnDevice | O domínio de OnDevice fornece intenções e entidades relacionadas para controlar o dispositivo.| en-US<br/> zh-CN |
| Locais  | Processamento de consultas relacionadas com de locais como as empresas, instituições, restaurantes, espaços públicos e endereços.| en-US<br/> zh-CN |
| Lembrete | Processamento de pedidos relacionados com a criação, edição e encontrar lembretes.| en-US<br/> zh-CN |
| RestaurantReservation | Processamento de pedidos para gerir as reservas do restaurante.| en-US<br/> zh-CN |
| Táxis | Processamento de reservas para um táxi.| en-US<br/> zh-CN |
| Traduzir | A tradução de texto para um idioma de destino.| en-US<br/> zh-CN |
| PROGRAMAS DE TV | Controlar TVs.| en-US |
| Serviços Públicos  | Pedidos de processamento que são comuns em muitos domínios, como "ajuda", "Repetir", "começar novamente".| en-US |
| Meteorologia | Obter relatórios meteorológicos e previsões.| en-US<br/> zh-CN |
| Web | Navegar para um Web site.| en-US<br/> zh-CN |

Para obter mais detalhes sobre cada domínio, consulte as secções que se seguem.

## <a name="calendar"></a>Calendário 

O domínio de calendário fornece intenções e entidades relacionadas às entradas de calendário. Os objetivos de calendário incluem adicionar, eliminar ou editar um compromisso, a verificar a disponibilidade e encontrar informações sobre uma entrada no calendário ou compromisso.

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Adicionar | Adicione um novo item de uso individual para o calendário.| Agende um compromisso com Lisa em 2 pm, aos domingos <br/><br/>Desejo agendar uma reunião<br/><br/>Preciso configurar uma reunião|
| CheckAvailability | Conheça a disponibilidade para um compromisso ou reunião no calendário do usuário ou o calendário de outra pessoa.| Quando é Jim disponível para corresponder às? <br/><br/>Mostrar quando Carol está disponível amanhã<br/><br/>Chris é gratuito no Sábado?|
| Eliminar | Pedido para eliminar uma entrada no calendário.| Cancele a minha marcação com Carol. <br/><br/>Eliminar a minha reunião 9h<br/>|
| Editar | O pedido para alterar uma reunião existente ou a entrada no calendário.| Mova a minha reunião 9h para 10 am.<br/><br/>Eu quero atualizar a minha agenda.<br/><br/>Reagende minha reunião com Ryan.|
| Localizar | Exiba meu calendário semanal.| Encontre no dentista rever compromisso. <br/><br/>Mostrar Calendário<br/>|

### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Localização | Localização do item de calendário, a reunião ou compromisso. Os endereços, cidades e regiões são bons exemplos de localizações.| 209 Nashville Gym <br/><br/>897 casa de pancake<br/><br/>Garage|
| Requerente | O título de uma reunião ou compromisso.| Compromisso no dentista <br/><br/>Almoço com Leonor<br/><br/>Compromissos de médico|

## <a name="camera"></a>Câmara 
O domínio de câmera fornece intenções e entidades relacionadas ao uso de uma câmera. Dos objetivos abrangem capturando uma foto, selfie, captura de ecrã ou um vídeo e transmissão de vídeo a uma aplicação.

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| CapturePhoto| Capture uma foto.| Tirar uma fotografia<br/><br/>captura|
| CaptureScreenshot | Capture uma captura de ecrã.| Tire a captura de ecrã.<br/><br/>uma captura do ecrã.|
| CaptureSelfie | Capture uma selfie.| Tirar uma selfie <br/><br/>tirar uma foto de mim |
| CaptureVideo | Inicie a gravação de vídeo.| Iniciar gravação <br/><br/>Iniciar gravação|
| StartBroadcasting| Inicie o vídeo de difusão.| Iniciar a difusão para Facebook|
| StopBroadcasting| Pare o vídeo de difusão.| Parar de difusão|
| StopVideoRecording| Pare a gravação de um vídeo.| Isso é o suficiente<br/><br/>Parar Gravação|

### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| AppName | O nome de um aplicativo para transmitir o vídeo para.| OneNote<br/><br/>Facebook<br/><br/>Skype|


## <a name="communication"></a>Comunicação 
O domínio de comunicação fornece intenções e entidades relacionadas ao e-mail, mensagens e chamadas telefónicas.

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| AddContact| Adicione um novo contacto a lista do usuário de contatos.|Adicionar novo contacto <br/><br/>Este número de guardar e colocar o nome como Camila|
| AddMore| Adicione mais um e-mail ou o texto, como parte de uma composição sequencial de e-mail ou texto.|Adicione mais para texto <br/><br/>Adicione mais ao corpo do correio eletrónico|
| Resposta| Responda a uma chamada de telefone de entrada.|A chamada de resposta <br/><br/>Selecionado|
| AssignContactNickname| Atribua uma alcunha para um contato.|Alterar o Luís a dad <br/>Editar alcunha de Jim<br/>Adicionar alcunha para Patti Owens|
| CallVoiceMail| Ligue-se para o correio de voz do utilizador.|Ligar-me a minha caixa de correio de voz <br/>Correio de voz<br/>Chamar caixa postal|
| CheckIMStatus| Verificar o estado de um contacto Skype.|É o estado online de Jim definido imediatamente? <br/>Está disponível para conversar com o Carol?|
| Confirmar| Certifique-se uma ação.|Sim<br/>Okay<br/>Tudo bem<br/>Confirmo que eu queira envie este e-mail.<br/>|
| Acesso telefónico| Fazer uma chamada telefónica.|Jim de chamada<br/>Marque o 311<br/>|
| FindContact| Localize informações de contacto por nome.|Localizar o número de Carol<br/>Mostre-me o número de Carol<br/>|
| FindSpeedDial| Determinar o número de acesso telefónico de velocidade que um número de telefone está definido para e vice-versa.|O que é o meu número de acesso telefónico 5?<br/>É necessário aumentar o conjunto de velocidade?<br/>O que é o número de marcação para 941-5555-333?|
| GetForwardingsStatus| Obter o estado atual de encaminhamento de chamada.|Meu encaminhamento de chamada é ativado?<br/>Diga-me se o meu estado de chamada está ativada ou desativada<br/>|
| GoBack| Volte ao passo anterior.|Voltar ao twitter<br/>Volte uma etapa<br/>Retroceder|
| Ignorar| Ignore uma chamada de entrada.|Não responder<br/>Ignorar chamada|
| IgnoreWithMessage| Ignorar uma chamada de entrada e responder com texto em vez disso.|Não responder a essa chamada, mas em vez disso, a enviar uma mensagem.<br/>Ignorar e enviar uma mensagem de texto novamente.|
| PressKey| Pressione um botão ou um número no teclado.|Estrela de marcação.<br/>Prima 1 2 3.|
| ReadAloud| Leia uma mensagem ou envie um e-mail ao utilizador.|Leia o texto.<br/>O que ela falou na mensagem?|
| TurnForwardingOff| Fazer uma chamada telefónica.|<br/><br/>|
| Redial| Redial ou chamar um número novamente.|Redial.<br/>Redial minha última chamada.|
| Rejeitar| Rejeite uma chamada de entrada.|Rejeitar a chamada<br/>Não pode responder agora<br/>Não está disponível no momento e chamará novamente mais tarde.|
| SendEmail| Envie um e-mail. Este visa aplica-se ao e-mail, mas não as mensagens de texto.|Mensagem de e-mail a possibilidade de Mike: Mike, que o jantar na semana passada foi esplêndida.<br/>Envie um e-mail para Bernardo<br/>|
| SendMessage| Envie uma mensagem de texto ou uma mensagem instantânea.|Enviar mensagem de texto para o Chris e Camila|
| SetSpeedDial| Defina um atalho de discagem de velocidade para o número de telefone de um contacto.|Defina discagem um para Carol.<br/>Configure a discagem para o mom.|
| ShowNext| Consulte o próximo item, por exemplo, uma lista de mensagens de texto ou e-mails.|Mostre a próxima Sílaba.<br/>Vá para a página seguinte.|
| ShowPrevious| Consulte o item anterior, por exemplo, uma lista de mensagens de texto ou e-mails.|Mostre anterior.<br/>Anterior<br/>Aceda à anterior.|
| StartOver| Iniciar o sistema ao longo ou iniciar uma nova sessão.|Recomeçar<br/>Nova sessão<br/>restart|
| TurnForwardingOff| Desative o encaminhamento de chamada.|Pare de reencaminhar minhas chamadas<br/>Optar por desativar o encaminhamento de chamada|
| TurnForwardingOn| Desative o telefone do orador.|Encaminhar as minhas chamadas para 3333<br/>Mudar de encaminhamento de chamada para 3333|
| TurnSpeakerOff| Desative o telefone do orador.|Leve-me desativar orador.<br/>Desative viva-voz.<br/>|
| TurnSpeakerOn| Ative o telefone do orador.|Modo de viva-voz.<br/>Coloque o viva-voz em.<br/>|

### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| AudioDeviceType | Tipo de dispositivo de áudio (orador, headset, microfone, etc.).| Orador<br/>Mãos-livres<br/>Bluetooth|
| Categoria | A categoria de uma mensagem ou e-mail.| Importante<br/>Alta prioridade|
| ContactAttribute | Um atributo do contato para que o utilizador consultas sobre.| Aos aniversários<br/>Endereço<br/>Número de telefone|
| ContactName | O nome de um destinatário de contacto ou de mensagem.| Camila<br/>Jim<br/>Chris|
| EmailSubject | O texto usado como a linha de assunto para uma mensagem de e-mail.| RE: interessante história|
| Linha | A linha que o usuário deseja usar para fazer uma chamada ou envie um e-mail/texto de.| Linha de trabalho<br/>Célula britânica<br/>Skype|
| Mensagem | A mensagem a enviar um e-mail ou texto.| Era ótimo reunião hoje mesmo. Vemo-o novamente em breve!|
| MessageType | O nome de um destinatário de contacto ou de mensagem.| Texto<br/>Email|
| OrderReference | A posição ordinal ou relativa numa lista, identificando um item a obter. Por exemplo, "last" ou "recentes" em "Qual foi a última mensagem enviei?"| Último<br/>Recente|
| SenderName | O nome do remetente.| Patti Owens|

## <a name="entertainment"></a>entretenimento  
Mostra o domínio fornece intenções e entidades relacionadas com a pesquisa de filmes, músicas, jogos e TV de entretenimento.

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Pesquisa| Mostra a pesquisa para filmes, músicas, aplicações, jogos e programas de TV.|Procure na loja Halo.<br/>Pesquisa de Avatar.|

### <a name="entities"></a>Entidades
| Nome de entidades | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| ContentRating | Classificação como G ou R para filmes de conteúdo de mídia.|Vídeo de crianças.<br/>PG classificada.|
| Genre | O gênero de um filme, o jogo, a aplicação ou a música.|Comedies<br/>Dramas<br/>Engraçado|
| Palavra-chave| Uma palavra-chave de pesquisa genérico especificando um atributo a não existe nas ranhuras de suporte de dados mais específicas.|Soundtracks<br/>Rio de lua<br/>Amelia Earhart|
| Idioma | Idioma utilizado na mídia, como um idioma falado de filme ou música.|Francês<br/>Português<br/>Coreano|
| MediaFormat | O adicional tipo técnico especial em que o suporte de dados está formatado.|HD filmes<br/>Filmes 3D<br/>Disponível para download|
| MediaSource | O mercado para adquirir o suporte de dados ou arquivo.|Netflix<br/>Primos|
| MediaSubTypes| Tipos de suportes de dados mais pequenos do que os filmes e jogos.|Demonstrações<br/>DLC<br/>Trailers|
| Nationality| O país onde foi criada um filme, mostrar ou música.|Francês<br/>Alemão<br/>Coreano|
| Pessoa| O ator, diretor, produtor, músico ou artista associados a um filme, uma aplicação, jogo ou programa de TV.|Madonna<br/>Stanley Kubrick|
| Função| Função desempenhada por uma pessoa na criação de suportes de dados.|Faz grandes<br/>Relativos ao<br/>Por|
| Cargo| O nome de um filme, aplicação, jogos, programa de TV ou música.|Amigos<br/>Minecraft|
| Tipo| O formato de tipo ou suporte de dados de um filme, aplicação, jogos, programa de TV ou música.|Música<br/>MovieTV <br/>Mostra|
| UserRating| Estrela de utilizador ou de classificação de miniaturas.|5 estrelas<br/>3 estrelas<br/>4 estrelas|

## <a name="events"></a>Eventos 
O domínio de eventos fornece intenções e entidades relacionadas com pedidos de reserva para eventos como concertos, festivals, jogos de desportos e comédia mostra.

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Livro| Comprar bilhetes para um evento.|Eu gostaria de comprar um pedido de suporte para a symphony este fim de semana.|


### <a name="entities"></a>Entidades
| Nome de entidades | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Endereço | Localização do evento ou endereço. |Da Palo Alto<br/>300 112th Ave SE <br/> Seattle |
| Nome | O nome de um evento.|Shakespeare no parque|
| PlaceName| O nome de localização do evento.|Louvre<br/>Casa<br/>Broadway|
| PlaceType | O tipo da localização do evento será mantido na.|Café<br/>Theater<br/>Biblioteca|
| Tipo | O tipo de um evento.|Conjunto<br/>Jogo de desporto|

## <a name="fitness"></a>Adequação 
O domínio de adequação fornece intenções e entidades relacionadas às atividades de adequação de controlo. Os objetivos incluem a guardar notas, o restante tempo ou distância ou guardar resultados de atividade.

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| AddNote| Adiciona notas suplementares para uma atividade controlada.|A dificuldade desta execução era 6/10<br/>O terreno que eu esteja em execução no é asfalto<br/>Estou usando uma bicicleta 3|
|GetRemaining| Obtém a hora ou a distância restantes para uma atividade.|Quanto tempo até o próximo lap?<br/>O número de milhas são restantes na minha execução? Quanto tempo para a divisão?|
| LogActivity| Guardar ou log os resultados da atividade concluída.|Guardar a minha última execução<br/>Inicie sessão meu movimentação da manhã de Sábado<br/>armazenar meus swim anterior|
| LogWeight| Guardar ou iniciar sessão atuais de peso do utilizador.|Guardar a minha atuais de peso<br/>Inicie agora minha peso<br/>armazenar meus atuais de peso corpo|

### <a name="entities"></a>Entidades
| Nome de entidades | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| ActivityType | O tipo de atividade para controlar. |Executar<br/>A pé<br/>Retomar<br/>Ciclo de |
| Comida | Um tipo de alimento para controlar num aplicativo de adequação. |Banana<br/>Salmão<br/>Agite de proteínas|
| MealType| O tipo de refeição para controlar num Estado de funcionamento ou adequação a uma aplicação.|Café da manhã<br/>Jantar<br/>Almoço<br/>Supper|
| Medida| Um tipo de medições de tempo, a distância ou peso, para utilização numa aplicação adequação ou estado de funcionamento.|Quilómetros de distância<br/>Quilómetros<br/>Minutos<br/>Quilogramas|
| Number | Uma quantidade numérica para utilização numa aplicação adequação ou estado de funcionamento.|19<br/>três<br/>200<br/>um|
| StatType | Um tipo de estatística com dados agregados, para utilização numa aplicação adequação ou estado de funcionamento.|Soma<br/>Média<br/>Máximo<br/>Mínimo|

## <a name="gaming"></a>Jogos 
O domínio de jogos fornece intenções e entidades relacionadas com a gestão de uma parte de jogo num jogo multijogador.

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| InviteParty| Convide um contato para associar uma parte de jogos.|Convidar este leitor à minha parte<br/>Venha ao meu grupo<br/>Junte-se a minha modo|
|LeaveParty| Obtém a hora ou a distância restantes para uma atividade.|Eu sou horizontalmente<br/>Estou deixando essa parte para outro<br/>Eu estou seja encerrado|
| StartParty| Inicie uma parte de jogos num jogo multijogador.|Cara vamos iniciar uma parte confiável<br/>iniciar uma parte confiável<br/>deve começar por um modo|

### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Contacto| Um nome de contacto para utilizar num jogo multijogador.|Camila<br/>Jim|


## <a name="homeautomation"></a>HomeAutomation 
O domínio de HomeAutomation fornece intenções e entidades relacionadas para controlar a dispositivos inteligentes de raiz como luzes e aplicações.

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| TurnOff| Desativar, fechar ou desbloquear um dispositivo.|Desativar as luzes<br/>Parar o café<br/>Porta da garagem fechar|
|TurnOn| Ative um dispositivo ou defina o dispositivo para a definição específica ou modo.|Ativar o meu criador de café<br/>Pode ativar no meu criador de café?<br/>Defina o termóstato como 72 graus.|


### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Dispositivo | Um tipo de dispositivo que pode ser ativado ou desativado.|Criador de café<br/>termóstato<br/>luzes|
| Operação | O estado a definir do dispositivo.|bloqueio<br/>abre<br/>em<br/>desativado|
| Espaço | A localização ou o espaço que o dispositivo estiver no.|sala de estar<br/>carvalho<br/>cozinha|

## <a name="movietickets"></a>MovieTickets 
O domínio de MovieTickets fornece intenções e entidades relacionadas com pedidos de reserva para filmes numa linha de cinema.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Reservar me dois pedidos de suporte para Captain Omar e os dois Musketeers|
|Cancelar pedidos de suporte|
|Quando está a mostrar Captain Omar?|

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Livro | Comprar ingressos de filme.|Reservar me dois pedidos de suporte para Captain Omar e os dois musketeers<br/>Quero comprar um pedido de suporte para o filme de amanhã<br/>Quero um pedido de suporte para Captain Omar parte 2 quarta-feira seguinte|
|GetShowTime| Obtenha o showtime de um filme.|Quando está a mostrar Captain Omar?|


### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Endereço | O endereço de uma linha de cinema.|Da Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| MovieTitle | O título de um filme.|Ciclo de vida do instalador de plataforma<br/>Jogos de fome<br/>Concepção|
| PlaceName | O nome de um cinema ou de cinema.|Amir de cinema<br/>Alexandria Theater<br/>Teatro de Nova Iorque|
| PlaceType | O tipo de localização de que um filme está visível no.|cinema<br/>cinema<br/>IMAX cinema|

## <a name="music"></a>Música 
O domínio de música fornece intenções e entidades relacionadas com a reprodução de música num player de música.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|reproduzir Sinfonia n|
|aumentar o volume de faixa|
|Avance para a próximo música|

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| DecreaseVolume | Diminua o volume do dispositivo.|diminuir o volume de faixa<br/>volume baixo|
| IncreaseVolume | Aumente o volume do dispositivo.|aumentar o volume de faixa<br/>volume de cópia de segurança|
| Desativar som |Mudo a reprodução de música.|Música Mute (mudo)<br/>Colocar a faixa em Desativar som<br/>Música Mute (mudo) |
| Colocar em pausa | Colocar em pausa a reprodução de música.|Colocar em pausa<br/>Música de colocar em pausa<br/>Roteiro de colocar em pausa|
| PlayMusic | Reproduza música num dispositivo.|reproduzir Kevin Durant<br/>reproduzir o paraíso por Coldplay<br/>reproduzir Hello da Adele|
| Repetir |Repita a reprodução de música.|Repita a música<br/>Reproduzir o ganho de faixa<br/>Repita a música|
| Retomar | Retome a reprodução de música.|Retomar a música<br/>Iniciar novamente o música<br/>Unpause|
| SkipBack | Ignore, com versões anteriores, uma faixa.|Avance para a próximo música<br/>Reprodução da música seguinte|
| SkipForward |Passar por um Roteiro.|Reprodução da música anterior<br/>Voltar para a faixa anterior |
| Parar | Pare uma ação de reprodução de música. |Pare reprodução este álbum.|
| Ativar o som | Ativar o som um dispositivo de reprodução de música.| Ativar o som.|

### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| ArtistName | O ator, diretor, produtor, escritor, músico ou artista associado suporte para reproduzir num dispositivo.|Elvis Presley<br/>Taylor Swift<br/>Adele<br/>Mozart|
| Genre | O gênero da música que está a ser solicitada.|Música de país<br/>Clássicos Broadway<br/>Reproduzir a minhas músicas clássicas do período Baroque|

## <a name="note"></a>Nota 
O domínio de nota fornece intenções e entidades relacionadas com a criação, edição e encontrar notas.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Adicionar ao meu groceries nota lettuce grelhado pão café|
|Verifique bananas de acessar a minha lista de|
|Remover todos os itens da minha lista de férias|

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| AddToNote | Adicione informações a uma nota.|Adicionar ao meu groceries nota lettuce grelhado pão café<br/>Adicionar à minha lista de tarefas<br/>Adicionar cupcakes ao meu Wunderlist|
| CheckOffItem | Verifique os itens a partir de uma nota já existente.|Verifique bananas de acessar a minha lista de<br/>Marcar bolo de queijo na minha lista de compras, como feito de feriado|
| Limpar | Limpe todos os itens de uma nota já existente.|Remover todos os itens da minha lista de férias<br/>Limpar todos na minha lista de leitura|
| Confirmar | Certifique-se uma ação de uma nota.|Há problema por mim<br/>sim<br/>Estou a confirmar a manter todos os itens de listas|
| Criar | Crie uma nova nota. | Criar uma lista<br/>Tenha em atenção lembrar-me o que Jason é na cidade de primeira semana de Maio|
| Eliminar | Elimine uma nota de toda. |Eliminar a minha lista de férias <br/>Eliminar minha nota groceries|
| DeleteNoteItem | Elimine itens de uma nota já existente.| Eliminar chips de acessar a minha lista de<br/>Remover canetas da minha instituição de ensino a lista de compras|
| ReadAloud | Uma lista de leitura em voz alta.|Leia-me primeiro<br/>Leia-me os detalhes|
| ShowNext | Ver o próximo item numa lista de notas.|Mostrar a próxima Sílaba<br/>Página seguinte<br/>Seguinte|

### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| AppName | O nome da aplicação de anotações.|Wunderlist<br/>OneNote|
| ContactName | O nome de um contacto numa nota.|Camila<br/>Jim<br/>Chris|
| Origem de dados | Localização das notas.|OneDrive<br/>Google docs<br/>meu computador|
| Tipo de dados | O tipo de ficheiro ou documento, normalmente associados à programas de software específico.|Slides<br/>Folha de cálculo<br/>Folha de cálculo|
| Texto | O texto de uma nota ou lembrete.|Esticar antes de movimentação<br/>amanhã longo prazo.|
| Cargo | Título de uma nota.|groceries<br/>pessoas para chamar<br/>tarefas pendentes|

## <a name="ondevice"></a>OnDevice 
O domínio de OnDevice fornece intenções e entidades relacionadas para controlar o dispositivo.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Feche o leitor de vídeo|
|Cancelar a reprodução|
|Pode fazer com que a tela recém-exibidas?|


### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| AreYouListening | Pergunte se o dispositivo está a escutar.|eles estão no?<br/>estão escutando?|
|CloseApplication|Feche a aplicação de dispositivo.|Feche o leitor de vídeo|
|FileBug|Registre um bug no dispositivo.|Registre um bug.<br/>Registre um bug para mim?<br/>Deixe-me comunicar este erro|
|GoBack|Peça ao voltar uma etapa ou retornar para o passo anterior.|Voltar atrás.<br/>Aceda ao ecrã anterior<br/>Go parar de volta à escuta|
|Ajuda| Pedir ajuda.|Ajudar.<br/>Olá<br/>O que fazer?<br/>Preciso de ajuda| 
|LocateDevice|Localize o dispositivo.|Pode localizar o meu telefone<br/>Localizar iphone de José<br/>Encontrar o meu telefone|
|Início de sessão|Inicie sessão serviço com o dispositivo.|Início de sessão.<br/>Início de sessão de Facebook<br/>Inicie sessão no LinkedIn|
|Fim de sessão|Inicie sessão fora de um serviço a utilizar o dispositivo.|Terminar meu telefone<br/>Inicie sessão no twitter<br/>Terminar sessão|
|MainMenu|Visualize o menu principal de um dispositivo.|Menu do modo de exibição.|
|OpenApplication|Abra uma aplicação no dispositivo.|Abra o alarme.<br/>Ativar o câmara<br/>Inicie o calendário|
|OpenSetting|Abra uma definição no dispositivo.|Abra as definições de rede.|
|PairDevice|Emparelhe o dispositivo.|Vocês podem me ajudar na emparelhamento de Bluetooth sinal para o telefone<br/>Ative o bluetooth e associe-o com o laptop<br/>Sinal de Bluetooth de par para meu laptop|
|Desligado | Desative o dispositivo.|Desligo meu computador<br/>Encerrar<br/>Desativar o meu celular|
|QueryBattery|Obtenha informações sobre a vida útil da bateria.|Mostre-me vida útil da bateria.<br/>O que é o meu estado de bateria<br/>Agora, a quantidade de bateria restante?<br/>Mostre-me da bateria|
|QueryWifi|Obtenha informações sobre o Wi-Fi.|Obter as informações de Wi-Fi.|
|Reiniciar|Reinicie o dispositivo.|Reinicie.|
|RingDevice| Peça o dispositivo para toque, para poder encontrá-lo quando ele perdeu.|Tocar o meu telefone.| 
|SetBrightness|Defina o brilho do dispositivo.|Brilho de conjunto para média<br/>Brilho de conjunto para alta<br/>Brilho de conjunto à baixa|
|SetupDevice|Inicie a configuração do dispositivo.|Quero instalar o programa de configuração do SO<br/>Programa de configuração.<br/>Fazer o programa de configuração para mim|
|ShowAppBar|Mostre uma barra de aplicativo.|Mostre-me a barra de aplicativo<br/>Tente da barra de aplicativo<br/>Deixe-me ver a barra de aplicativo|
|ShowContextMenu|Mostre um menu de contexto.|Deixe-me ver o menu de contexto<br/>Menu de contexto.<br/>Pode me mostrar o menu de contexto|
|Modo de suspensão|Coloca o dispositivo no modo de suspensão.|Ir para o modo de suspensão<br/>Modo de suspensão<br/>Modo de suspensão meu computador|
|SwitchApplication|Mude a aplicação para utilizar no dispositivo.|Mude para o meu leitor de multimédia.|
|TurnDownBrightness|Abaixar o brilho do dispositivo.|Dim na tela.|
|TurnOffSetting|Desative uma definição de dispositivo.|Desativar o Bluetooth<br/>Desativar dados<br/>Desligar o bluetooth|
|TurnOnSetting|Ative uma definição de dispositivo.|Ativado <br/> Desativado|
|TurnUpBrightness|Ative o brilho do dispositivo.|Pode fazer com que a tela recém-exibidas?|

### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| AppName | Nome de uma aplicação no dispositivo.|SoundCloud<br/>YouTube|
| BrightnessLevel | Defina o nível de luminosidade no dispositivo.|Percentagem de cem<br/>Cinquenta<br/>40%|
| ContactName | O nome de um contacto no dispositivo.|Paul<br/>Máx. de Marlen|
| deviceType | O tipo de dispositivo. |Telefone<br/>Kindle<br/>Computador portátil|
| mediaType | O tipo de suporte de dados processado pelo dispositivo.|Música<br/>Filmes<br/>Programas de TV|
| SettingType | Um tipo de definição ou no painel de definições que o usuário quer editar.|Wi-Fi<br/>Rede sem fios<br/>Esquema de cores<br/>Centro de notificações|

## <a name="places"></a>Locais  
O domínio de lugares fornece objetivos para processar consultas relacionadas com de locais como as empresas, instituição, restaurantes, espaços públicos e endereços.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Guardar esta localização nos meus Favoritos|
|Distância é feriado Scritpt?|
|A que hora Safeway fechar?|


### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| AddFavoritePlace | Adicione uma localização à lista de Favoritos do usuário.|Guardar esta localização nos meus Favoritos<br/>Adicionar este endereço para meus Favoritos|
|CheckAccident|Pergunte se existe um acidente num caminho especificado.|Existe um acidente no 880?<br/>Mostrar informações de acidente|
|CheckAreaTraffic|Verifique o tráfego para uma área geral ou autoestrada, não numa rota especificada.|Tráfego em Seattle<br/>O que é o tráfego, como em Seattle?|
|CheckIntoPlace|Verifique local através de redes sociais.|Verificar-me Foursquare<br/>Check-in aqui|
|CheckRouteTraffic| Verifique o tráfego de uma rota específica especificado pelo utilizador.|Como é o tráfego para Mashiko?<br/>Mostre-me o tráfego para Kirkland<br/>Como é o tráfego para Seattle?| 
|Confirmar|Certifique-se uma ação de um local.|Certifique-se minha reserva do restaurante.|
|Sair|Ação para sair de uma tarefa de um local.|Sair.<br/>Sair, fornecendo-me as direções|
|FindPlace|Pesquisar um local (empresas, instituição, restaurante, espaço público, endereço).|Onde posso encontrar a biblioteca mais próxima?<br/>Encontre-me um restaurante italiano bom na vista das regiões montanhosas|
|GetAddress| Pedir o endereço de um local.|Mostrar o endereço do Guu Robson Rua<br/>O que é o endereço da Starbucks mais próximo?| 
|GetDistance|Fazer sobre distância até um local específico.|Distância é feriado Scritpt?<br/>a distância é Bellevue quadrado aqui<br/>o que é a distância para Tahoe|
|GetHours|Fazer sobre as horas de funcionamento para um local.|A que hora Safeway fechar?<br/>Quais são as horas para a home page Depot?<br/>Starbucks ainda está aberto?|
|GetMenu|Peça para os itens de menu de um restaurante.|Zucca serve nada vegan?<br/>O que há no menu na Sizzler<br/>Mostrar menu do Applebee|
|GetPhoneNumber| Pedir o número de telefone de um local.|O que é o número de telefone da Starbucks mais próximo?<br/>Atribua o número para a home page de depósito| 
|GetPriceRange| Pede para o intervalo de preço de um local.|Zucca é barato?<br/>É a metade do preço Cineplex às quartas-feiras?<br/>Qual é um custo de jantar lobster todo em Sizzler?|
|GetReviews|Pedir revisões de um local.|Mostre-me as revisões da fábrica de bolos<br/>Leia críticas Cineplex no Yelp|
|GetRoute|Pedir as direções para um local.|Como ir Bellevue quadrado<br/>Mostre-me a maneira mais curta para 8 e 59th a partir daqui<br/>-Me obter direções da AC de modo de exibição das regiões montanhosas|
|GetStarRating|Pedir a classificação em estrelas de um local.|Como é classificado Zucca, de acordo com Yelp?<br/>Quantas estrelas tem o Lavanderia francês?<br/>É bom aquarium no Monterrey?|
|GetTransportationSchedule|Obtenha o agendamento de barramento para um local.|O tempo é o barramento seguinte para Moro?<br/>Mostre-me os barramentos em King County|
|GetTravelTime|Pedir o tempo de deslocação para um destino especificado.|Quanto tempo necessário para chegar à San Francisco aqui<br/>O que é o tempo de viagem para Denver de SF|
|MakeCall|Fazer uma chamada telefónica para um local.|Chamar o mom<br/>Eu gostaria de fazer uma chamada do Skype a Ana<br/>Jim de chamada|
|MakeReservation|Pedir uma reserva para um restaurante ou de outra empresa.|Reservar em Zucca para dois para tonight<br/>Uma tabela do livro para amanhã<br/>Tabela 3 da Palo Alto a 8 do|
|MapQuestions|Pedir informações sobre as direções ou se um caminho especificado vai para um destino.|13 é transmitido por meio de Moro?<br/>Pode demorar 880 para Oakland?|
|Classificação|Obter a descrição de classificação de um restaurante ou local.|Quantas estrelas tem o Scritpt Contoso?|
|ReadAloud|Uma lista de locais de leitura em voz alta.|Leia-me primeiro<br/>Leia-me os detalhes|
|SelectItem|Escolha um item de uma lista de opções de um local ou locais.|Escolher a segunda<br/>Selecione o primeiro|
|ShowMap|Mostre um mapa de uma área.|Mostrar um mapa para o segundo cenário<br/>Mostrar mapa<br/>Localizar o San Francisco no mapa|
|ShowNext|Mostra o item seguinte de uma série.|Mostrar a próxima Sílaba<br/>Vá para a página seguinte|
|ShowPrevious|Mostra o item anterior de uma série.|Mostrar anterior<br/>anterior<br/>Aceda a anterior|
|StartOver|Reinicie a aplicação ou iniciar uma nova sessão.|Recomeçar<br/>Nova sessão<br/>
restart|
|TakesReservations|Pergunte se um lugar aceita as reservas.|A Galeria de arte aceitar as reservas<br/>É possível tomar uma reserva no jardim seco

### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| AbsoluteLocation | A localização ou o endereço de um local.|Da Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| Características | Os objetivos características e benefícios de um local.|Kids comer gratuitamente<br/>frente marítima<br/>estacionamento gratuito|
| Atmosfera | A atmosfera de um local.|amigáveis para crianças<br/>restaurante casual<br/>sporty|
| Cuisine | O cuisine de um local. |Mediterrâneo<br/>Italiano<br/>Rupia|
| DestinationAddress| Uma localização de destino ou endereço.|Da Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationPlaceName| O nome de um destino que é uma empresa, restaurante, attraction público ou instituição.|Central park<br/>Safeway<br/>Walmart|
| DestinationPlaceType | O tipo de um destino que é um comercial, restaurante, attraction público ou instituição. |Restaurante<br/>Opera<br/>cinema|
| distância | A distância para um local.|15 quilómetros<br/>5 quilómetros<br/>10 quilómetros de distância|
| MealType | Tipo de refeição como café da manhã ou almoço. |Café da manhã<br/>Jantar<br/>Almoço<br/>Supper|
| OpenStatus | Indica se um lugar é aberto ou fechado.|Aberto<br/>Fechado<br/>abrir|
| PlaceName | O nome de um local.|Fábrica de bolos|
| PlaceType | O tipo de um local.|Café<br/>Theater<br/>Biblioteca|
| PreferredRoute | A rota preferida especificada pelo utilizador. | 101 <br/>202 <br/>Rota 401|
| Produto | O produto oferecido um local. | Roupas<br/>Câmeras digitais ASR<br/>Peixe atualizados | 
| PublicTransportationRoute | O nome da rota transporte público que o utilizador está a procurar. | Formação de corredor Nordeste<br/>Rota de barramento 3 X |
| Classificação | A classificação de um local. | 5 estrelas<br/>3 estrelas<br/>4 estrelas|
| RouteAvoidanceCriteria | Critérios para evitar rotas específicas, como evitar acidentes, construções ou portagens | Portagens <br/>Construções<br/>Rota 11|
| ServiceProvided | Este é o serviço fornecido por uma empresa ou local, como haircut, snow plowing, landscaping. | haircut<br/>Banqueiro<br/>técnico|
| TransportationCompany | O nome de um provedor de transporte.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | O tipo de transporte.|Barramento<br/>Preparar<br/>Driving|

## <a name="reminder"></a>Lembrete 
O domínio de lembrete fornece intenções e entidades para criar, editar e encontrar lembretes.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Alterar a minha entrevista para 9h amanhã|
|Lembrar-me comprar milk no meu caminho de volta a home page|
|Pode verificar se tenho um lembrete sobre aniversário de Christine?|


### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Alterar| Altere um lembrete.|Alterar a minha entrevista para 9h amanhã<br/>Mover o meu lembrete de atribuição para amanhã|
| Criar| Crie um novo lembrete.|Criar um lembrete<br/>Lembrar-me comprar milk<br/>Quero se lembrar de chamar Rebecca quando estou em casa|
| Eliminar | Elimine um lembrete.|Eliminar o meu lembrete de imagem<br/>Eliminar este lembrete|
| Localizar | Encontre um lembrete.|É necessário um lembrete sobre minha aniversário?<br/>Pode verificar se tenho um lembrete sobre aniversário de Christine?|

### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Texto | A descrição de texto de um lembrete.|Pegue a limpeza de dry<br/>remover o meu carro no Centro de serviço|

## <a name="restaurantreservation"></a>RestaurantReservation 
O domínio de RestaurantReservation fornece intenções e entidades relacionadas com a gestão de reservas do restaurante.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Reservar em Zucca para dois para tonight|
|Livro uma tabela do BJ para amanhã|
|Tabela para 3 da Palo Alto em 7|

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Reserva | Pedir uma reserva de um restaurante. |Reservar em Zucca para dois para tonight<br/>Uma tabela do livro para amanhã<br/>Tabela para 3 da Palo Alto em 7|

### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Endereço| Uma localização do evento ou o endereço para uma reserva.|Da Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| Características | Um atributo que descreve as características de um local.|modo de exibição do Oceano<br/>Não fumar|
| AppName | O nome de um aplicativo para fazer as reservas.|OpenTable<br/>Yelp<br/>TripAdvisor|
| Atmosfera | Uma descrição da atmosfera de um restaurante ou noutro local.|romantic<br/>casual<br/>bom para grupos|
| Cuisine | Um tipo de comida, cuisine ou cuisine nationality. |Chinês<br/>Italiano<br/>Peso|
| MealType | Um tipo de refeição associado a uma reserva.|Café da manhã<br/>Jantar<br/>Almoço<br/>Supper|
| PlaceName | O nome de um comercial, restaurante, da atração pública ou instituição.|IHOP<br/>Fábrica de bolos<br/>Louvre|
| PlaceType | O tipo de um comercial, restaurante, da atração pública ou instituição.|Restaurante<br/>Opera<br/>cinema|
| Classificação | A classificação de um local ou um restaurante.|5 estrelas<br/>3 estrelas<br/>4 estrelas|

## <a name="taxi"></a>Táxis 
 
O domínio de táxis fornece intenções e entidades para criar e gerir reservas de táxis.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Obtenha-me um cab às 15:00|
|Quanto mais é necessário aguardar a minha táxis?|
|Cancelar o meu Uber|

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Livro | Chame um táxi. |Obtenha-me um cab<br/>Encontrar um táxi<br/>Reservar-me um uber x|
| Cancelar | Cancele uma ação de reserva um táxi.|Cancelar o meu táxis<br/>Cancelar o meu Uber|
| Roteiro | Controle uma rota de táxis.|Quanto mais é necessário aguardar a minha táxis?<br/>Onde posso encontrar minha Uber?|

### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Endereço| O endereço associado a reserva um táxi. |Da Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationAddress| Uma localização de destino ou endereço. |Da Palo Alto<br/>300 112th Ave SE<br/>Seattle|
| DestinationPlaceName | O nome de um destino que é um comercial, restaurante, attraction público ou instituição. |Central Park<br/>Safeway<br/>Walmart|
| DestinationPlaceType | O tipo de um destino que é um comercial, restaurante, attraction público ou instituição. |Restaurante<br/>Opera<br/>cinema|
| PlaceName | Nome do comercial, restaurante, da atração pública ou instituição. |Central Park<br/>Safeway<br/>Walmart|
| PlaceType| O tipo de local numa solicitação para reservar um táxi.|Restaurante<br/>Opera<br/>cinema|
| TransportationCompany | O nome de um provedor de transporte.|Amtrak<br/>Acela<br/>Greyhound|
| TransportationType | O tipo de transporte.|Barramento<br/>Preparar<br/>Driving|

## <a name="translate"></a>Traduzir 
O domínio de Translate fornece intenções e entidades relacionadas com a tradução de texto para um idioma de destino.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Traduzir para francês|
|Traduzir o hello para o alemão|
|Traduza esta frase para inglês|


### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Traduzir| Traduza texto para outro idioma.|Traduzir para francês<br/>Traduzir o hello para o alemão|


### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| TargetLanguage | O idioma de destino de uma tradução.|Francês<br/>Alemão<br/>Coreano|
| Texto | O texto a traduzir.|Olá, Mundo<br/>Bom dia<br/>Boa noite|

## <a name="tv"></a>PROGRAMAS DE TV 
 
O domínio de TV fornece intenções e entidades para controlar TVs.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Canal de comutador para a BBC|
|Guia de TV show|
|Assista a National geográfica|

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| ChangeChannel| Altere um canal numa TV.|Canal de alteração, a CNN<br/>Canal de comutador para a BBC<br/>Ir para o canal de 4|
| ShowGuide| Mostre o guia de TV.|Guia de TV show<br/>o que está no canal de filme agora?<br/>Mostrar a minha lista de programa|
| WatchTV| Pergunte-se assistir a um canal de TV.|Quero ver o canal de Disney<br/>Ir para programas de TV.<br/>Assista a National geográfica|

### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| ChannelName | O nome de um canal de TV.|CNN<br/>BBC<br/>Canal de filmes|

## <a name="utilities"></a>Serviços Públicos  
O domínio de utilitários fornece objetivos para tarefas comuns para várias tarefas, como saudações, cancelamento, confirmação, ajuda, repetição, navegação, iniciar e parar.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Voltar ao Twitter|
|Ajude|
|Repita a última pergunta.|


### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Cancelar | Cancele uma ação.|Cancelar a mensagem<br/>Não quero enviar o e-mail já|
| Confirmar | Certifique-se uma ação.|Sim AH confirmo<br/>Bom, que eu estou confirmar<br/>OK. eu estou confirmar|
| FinishTask | Conclua uma tarefa iniciado do utilizador.|Terminei<br/>Terminei<br/>Isso é feito|
| GoBack | Volte uma etapa ou regressar ao passo anterior.|Voltar ao Twitter<br/>Volte uma etapa<br/>Retroceder|
| Ajuda | Pedido para obter ajuda.|Ajude<br/>abrir a ajuda<br/>Obter ajuda|
| Repetir | Repetir uma ação.|Repita a última pergunta.<br/>Repita os últimos música|
| ShowNext | Mostra o item seguinte de uma série. |Mostrar a próxima Sílaba<br/>Vá para a página seguinte|
| ShowPrevious | Mostra o item anterior de uma série.|Mostrar anterior|
| StartOver | Reinicie a aplicação ou iniciar uma nova sessão.|Recomeçar<br/>Nova sessão<br/>restart|
| Parar | Pare uma ação.| Parar dizendo que este problema<br/>Encerrar a cópia de segurança<br/>Parar.|

## <a name="weather"></a>Meteorologia 
O domínio de Meteorologia fornece intenções e entidades para obter relatórios meteorológicos e previsões.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|informações sobre o clima em Londres em Setembro|
|O que? s o 10 dias previsão?|
|O que é a temperatura média na Índia em Setembro?|


### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| GetCondition | Obter históricos fatos relacionados com Meteorologia. |informações sobre o clima em Londres em Setembro<br/>O que é a temperatura média na Índia em Setembro?|
| GetForecast | Obter a meteorologia atual e a previsão da daqui a alguns dias. |Como é a meteorologia hoje em dia?<br/>O que é o dia 10 de previsão?<br/>Como é que a meteorologia vai ser este fim de semana?|

### <a name="entities"></a>Entidades
| Nome da entidade | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Localização| A localização absoluta de um pedido de Meteorologia.|Seattle<br/>Paris<br/>Da Palo Alto|

## <a name="web"></a>Web 
O domínio da Web fornece um objetivo para navegar para um Web site.

### <a name="examples"></a>Exemplos

|Exemplos|
|--|
|Navegue para facebook.com|
|Vá para www.twitter.com|
|Navegue para www.bing.com|

### <a name="intents"></a>Intenções
| Nome de intenção | Descrição | Exemplos |
| ---------------- |-----------------------|----|
| Navegar | Um pedido para navegar para um Web site especificado. |Navegue para facebook.com<br/>Vá para www.twitter.com|

