---
title: Perguntas & respostas sobre a aplicação Microsoft Authenticator - Azure AD
description: Perguntas e respostas frequentes (PERGUNTAS Frequentes) sobre a app microsoft Authentication e verificação de dois fatores.
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 12/09/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: a1307aa19c66e3f6fcb71f677e6c67551d2a01f9
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928329"
---
# <a name="frequently-asked-questions-faq-about-the-microsoft-authenticator-app"></a>Perguntas frequentes (FAQ) sobre a aplicação Microsoft Authenticator

Este artigo responde a perguntas comuns sobre a aplicação Microsoft Authenticator. Se não vir uma resposta à sua pergunta, vá ao fórum de aplicações do [Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp).

A aplicação Microsoft Authenticator substituiu a aplicação Azure Authenticator, e é a aplicação recomendada quando utiliza a Autenticação Multi-Factor AD Azure. A aplicação Microsoft Authenticator está disponível para [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) e [iOS.](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="registering-a-device"></a>Registo de um dispositivo

**P:** O registo de um dispositivo que concorda em dar à empresa ou ao serviço acesso ao meu dispositivo?

**R:** Registar um dispositivo dá acesso aos serviços da sua organização e não permite o acesso da sua organização ao seu dispositivo.

## <a name="too-many-app-permissions"></a>Demasiadas permissões de aplicativos

**P:** Porque é que a aplicação solicita tantas permissões?

**R**: Aqui está a lista completa de permissões que podem ser pedidas e como são usadas pela app. As permissões específicas que vê dependerão do tipo de telefone que tem.

- **Localização**. Às vezes, a sua organização quer saber a sua localização antes de permitir o acesso a determinados recursos. A aplicação só solicitará esta permissão se a sua organização tiver uma política que exija localização. 

### <a name="legacy-apns-support-deprecated"></a>Apoio legado APNs deprecado

**P:** Como a interface binária legado do serviço de notificação apple push está a ser depreciada em novembro de 2020, como posso continuar a utilizar o Microsoft Authenticator/Fator de Telefone para iniciar sedições?

**R**: A [Apple anunciou a depreciação](https://developer.apple.com/news/?id=11042019a) de notificações push que utilizam a sua interface binária para dispositivos iOS, como as utilizadas pelo Fator Telefónico. Para continuar a receber notificações push, recomendamos que os utilizadores atualizem a sua app Authenticator para a versão mais recente da app. Entretanto, pode trabalhar em torno dele verificando manualmente as notificações na aplicação Authenticator.

### <a name="app-lock-feature"></a>Funcionalidade de Bloqueio de Aplicativos

**P:** O que é o Bloqueio de Aplicações, e como posso usá-lo para me ajudar a manter-me mais seguro?

**A**: O Bloqueio de Aplicações ajuda a manter os seus códigos de verificação únicos, informações de aplicações e configurações de aplicações mais seguras. Quando o Bloqueio de Aplicações estiver ativado, ser-lhe-á pedido que autenerá usando o PIN do dispositivo ou biométrico sempre que abrir o Autenticador. O App Lock também ajuda a garantir que é o único que pode aprovar notificações solicitando o seu PIN ou biométrico sempre que aprovar uma notificação de entrada. Pode ligar ou desligar o Bloqueio de Aplicações na página Definições do Autenticador. Por predefinição, o Bloqueio de Aplicações é ligado quando configura um PIN ou biométrico no seu dispositivo.<br><br>Infelizmente, não há garantias de que o App Lock impeça alguém de aceder ao Autenticador. Isto porque o registo do dispositivo pode acontecer em outros locais fora do Authenticator, como é o caso das definições de conta Android ou da aplicação Portal da Empresa.

### <a name="windows-mobile-retired"></a>Windows Mobile aposentado

**P:** Tenho um dispositivo Windows Mobile e o Autenticador Microsoft no Windows Mobile foi precotado. Posso continuar a autenticar usando a app?

**R**: Todas as autenticações que utilizem o Autenticador Microsoft no Windows Mobile serão retiradas após 15 de julho de 2020. Recomendamos vivamente que utilize um método de autenticação alternativo para evitar ser bloqueado fora das suas contas.<br>As opções alternativas para utilizadores empresariais incluem:<br><ul><li>Configuração do Microsoft Authenticator para [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) ou [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>[Configuração de SMS](multi-factor-authentication-setup-phone-number.md) para receber códigos de verificação.</li><li>Configurar o número de telefone para receber [chamadas telefónicas para verificar a sua identidade](multi-factor-authentication-setup-office-phone.md).</li></ul><br>As opções alternativas para utilizadores pessoais da conta microsoft incluem:<br><ul><li>Configuração do Microsoft Authenticator para [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) ou [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>Configurar um método de login alternativo (SMS ou e-mail) atualizando as suas informações de segurança a partir da página microsoft [Account Security](https://account.microsoft.com/security/).</li></ul>

### <a name="android-screenshots"></a>Imagens android

**P:** Posso tirar fotografias dos meus códigos de senha única (OTP) no Autenticador Android?

**R**: Começando com o lançamento 6.2003.1704 do Android Autenticador, por padrão todos os códigos OTP são ocultas sempre que uma imagem do Autenticador é tirada. Se quiser ver os seus códigos OTP em imagens ou permitir que outras aplicações capturem o ecrã Authenticator, pode. Basta ligar a definição **de Captura** de Ecrã no Autenticador e reiniciar a aplicação.

### <a name="delete-stored-data"></a>Eliminar dados armazenados

**P:** Que dados o Autenticador armazena em meu nome e como posso eliminá-lo?

**R**: A aplicação Authenticator recolhe três tipos de informação:<ul><li>Informações de conta que fornece quando adiciona a sua conta. Estes dados podem ser removidos removendo a sua conta.</li><li>Dados de registo de diagnóstico que permanecem apenas na aplicação até que selecione **Enviar Registos** do menu **de Ajuda** da aplicação para enviar registos para a Microsoft. Estes registos podem conter dados pessoais, tais como endereços de e-mail, endereços de servidor ou endereços IP. Também podem conter dados do dispositivo, como o nome do dispositivo e a versão do sistema operativo. Todos os dados pessoais recolhidos estão limitados a informações necessárias para ajudar a resolver problemas com as aplicações. Pode navegar nestes ficheiros de registo na aplicação a qualquer momento para ver a informação a ser recolhida. Se enviar os seus ficheiros de registo, os engenheiros de aplicações de autenticação utilizarão apenas para resolver problemas reportados pelo cliente.</li><li>Dados de utilização não pessoalmente identificáveis, tais "começaram a adicionar fluxo de conta/conta adicionada com sucesso", ou "notificação aprovada". Estes dados são parte integrante das nossas decisões de engenharia. O seu uso ajuda-nos a determinar onde podemos melhorar as aplicações de formas que são importantes para si. Vê uma notificação desta recolha de dados quando utiliza a app pela primeira vez. Informa-o então que pode ser desligado na página **de Definições** da aplicação. Pode ligar ou desligar esta definição a qualquer momento.</li></ul>

### <a name="codes-in-the-app"></a>Códigos na aplicação

**P:** Para que são os códigos da aplicação?

**R**: Quando abrir o Autenticador, verá as suas contas adicionadas como azulejos. O seu trabalho ou contas escolares e as suas contas pessoais da Microsoft terão números de seis ou oito dígitos visíveis na visão completa do ecrã da conta (acedida tocando no azulejo da conta). Para outras contas, verá um número de seis ou oito **dígitos** na página contas da aplicação.<br>Usará estes códigos como senha de uso único para verificar se é quem diz ser. Depois de iniciar sômeduca com o seu nome de utilizador e senha, irá digitar o código de verificação que está associado a essa conta. Por exemplo, se estiveres a fazer a assinatura da Katy na tua conta Contoso, tocas no azulejo da conta e depois usas o código de verificação 895823. Para a conta do Outlook, seguiria os mesmos passos.<br>Toque no azulejo da conta contoso.<br>![Azulejos de conta na aplicação Authenticator](media/user-help-auth-app-faq/katy-signin.png)<br>Depois de tocar no azulejo da conta Contoso, o código de verificação é visível em ecrã completo.<br>![Código de verificação no azulejo da conta no Autenticador](media/user-help-auth-app-faq/verification-code.png)

### <a name="countdown-timer"></a>Temporizador de contagem regressiva

**P:** Por que o número ao lado do código continua em contagem dece contagem rebatída?

**R**: Pode ver um temporizador de 30 segundos a contar ao lado do seu código de verificação ativo. Este temporizador é para que nunca assine o mesmo código duas vezes. Ao contrário de uma senha, não queremos que se lembre deste número. A ideia é que só alguém com acesso ao seu telefone saiba o seu código.

### <a name="grayed-account-tile"></a>Azulejo de conta cinza

**P:** Por que a minha conta é cinza?

**R**: Algumas organizações exigem que o Autenticador trabalhe com uma única assinatura e proteja os recursos organizacionais. Nesta situação, a conta não é usada para verificação em duas etapas e aparece como cinza ou inativa. Este tipo de conta é frequentemente chamado de conta "corretor".

### <a name="device-registration"></a>Registo de dispositivo

**P:** O que é o registo do dispositivo?
**R**: O seu org poderá exigir que registe o dispositivo para rastrear o acesso a recursos seguros, como ficheiros e aplicações. Podem também recorrer ao Acesso Condicional para reduzir o risco de acesso indesejado a esses recursos. Pode não registar o seu dispositivo em **Definições,** mas pode perder acesso a e-mails no Outlook, ficheiros no OneDrive e perderá a capacidade de utilizar o registo do telefone.

### <a name="verification-codes-when-connected"></a>Códigos de verificação quando ligados

**P:** Preciso de estar ligado à Internet ou à minha rede para obter e utilizar os códigos de verificação?

**R**: Os códigos não exigem que você esteja na Internet ou ligado a dados, por isso não precisa de serviço telefónico para iniciar sôm. Além disso, como a aplicação deixa de funcionar assim que a fechar, não vai drenar a bateria.

### <a name="no-notifications-when-app-is-closed"></a>Sem notificações quando a app está fechada

**P:** Por que só recebo notificações quando a aplicação está aberta? Quando a aplicação está fechada, não recebo notificações.

**R:** Se estiver a receber notificações, mas não um alerta, mesmo com o seu toque ligado, deverá verificar as definições da sua aplicação. Certifique-se de que a aplicação está ligada para usar som ou para vibrar para notificações. Se não receber notificações, deverá verificar as seguintes condições:<ul><li>O seu telefone está no modo "Não Perturbar ou Acalmar"? Estes modos podem impedir que as aplicações enviem notificações.</li><li>Pode receber notificações de outras aplicações? Caso contrário, pode ser um problema com as ligações de rede no seu telemóvel, ou com o canal de notificações do Android ou da Apple. Pode tentar resolver as suas ligações de rede através das definições do seu telefone. Poderá ter de falar com o seu fornecedor de serviços para ajudar no canal de notificações Android ou Apple.</li><li>Pode receber notificações de algumas contas na app, mas não noutras? Se sim, remova a conta problemática da sua app, adicione-a novamente permitindo notificações e veja se isso corrige o problema.</li></ul>Se tentou todos estes passos e ainda está com problemas, recomendamos o envio dos seus ficheiros de registo para diagnósticos. Abra a aplicação, vá para **Ajudar** e, em seguida, selecione **Enviar registos**. Depois disso, vá ao fórum da [aplicação Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) e diga-nos o problema que está a ver e os passos que tentou.

### <a name="switch-to-push-notifications"></a>Mudar para notificações push

**P:** Estou a usar os códigos de verificação na aplicação, mas como posso mudar para as notificações push?

**R**: Pode configurar notificações para o seu trabalho ou conta escolar (se permitido pelo seu administrador) ou para a sua conta pessoal da Microsoft. As notificações não funcionarão para contas de terceiros, como o Google ou o Facebook.<br>Para mudar a sua conta pessoal para notificações, terá de voltar a registar o seu dispositivo com a conta. Vá para **adicionar conta**, selecione Personal **Microsoft Account**, e, em seguida, inscreva-se usando o seu nome de utilizador e senha.<br>Para o seu trabalho ou conta escolar, a sua organização decide se permite ou não notificações de um clique.

### <a name="notifications-for-other-accounts"></a>Notificações de outras contas

**P:** As notificações funcionam para contas não Microsoft?

**R:** Não, as notificações só funcionam com contas da Microsoft e contas do Azure Ative Directory. Se o seu trabalho ou escola utilizar contas AD Azure, eles são capazes de desligar esta funcionalidade.

### <a name="backup-and-recovery"></a>Cópia de segurança e recuperação

**P:** Tenho um novo dispositivo ou restabelece o meu dispositivo a partir de uma cópia de segurança. Como posso configurar as minhas contas no Autenticador de novo?

**R:** Se tiver ligado **cloud backup** no seu dispositivo antigo, pode utilizar a sua cópia de segurança antiga para recuperar as credenciais da sua conta no seu novo iOS ou num dispositivo Android. Para mais informações, consulte a [Cópia de Segurança e recupere as credenciais de conta com artigo Authenticator.](user-help-auth-app-backup-recovery.md)

### <a name="lost-device"></a>Dispositivo perdido

**P:** Perdi o meu dispositivo ou mudei-me para um novo dispositivo. Como posso garantir que as notificações não continuem a ir para o meu antigo dispositivo?

**R**: Adicionar Autenticador ao seu novo dispositivo não remove automaticamente a aplicação do seu antigo dispositivo. Mesmo apagar a aplicação do seu antigo dispositivo não é suficiente. Ambos devem eliminar a aplicação do seu antigo dispositivo e dizer à Microsoft ou à sua organização para esquecer e não registar o dispositivo antigo.<ul><li>**Para remover a aplicação de um dispositivo usando uma conta pessoal da Microsoft.** Vá à área de verificação em duas etapas da sua página [de Segurança da Conta](https://account.microsoft.com/security)e   opte por desativar a verificação do seu dispositivo antigo.</li><li>**Para remover a aplicação de um dispositivo utilizando uma conta Microsoft de trabalho ou escola.** Vá à área de verificação em duas etapas da sua [página MyApps](https://myapps.microsoft.com/) ou do portal personalizado da sua organização para desativar a verificação do seu antigo dispositivo.</li></ul>

### <a name="remove-account-from-app"></a>Remover conta da app

**P:** Como removo uma conta da aplicação?

**R**: Toque no azulejo da conta que pretende remover da aplicação para ver o ecrã completo da conta. Toque **na conta Remover** para remover a conta da aplicação.<br>Se tiver um dispositivo registado na sua organização, poderá precisar de um passo extra para remover a sua conta. Nestes dispositivos, o Autenticador é automaticamente registado como administrador do dispositivo. Se pretender desinstalar completamente a aplicação, tem de desinsusar a aplicação primeiro nas definições da aplicação.

### <a name="too-many-permissions"></a>Demasiadas permissões

**P:** Porque é que a aplicação solicita tantas permissões?

**R**: Aqui está a lista completa de permissões que podem ser pedidas e como são usadas pela app. As permissões específicas que vê dependerão do tipo de telefone que tem.<ul><li>**Use hardware biométrico.** Alguns trabalhos e contas escolares requerem um PIN adicional sempre que verificar a sua identidade. A aplicação requer o seu consentimento para usar reconhecimento biométrico ou facial em vez de introduzir o PIN.</li><li>**A câmara.** Usado para digitalizar códigos QR quando você adiciona uma conta de trabalho, escola ou não-Microsoft.</li><li>**Contactos e telefone.** A aplicação requer esta permissão para procurar trabalho ou escola contas microsoft no seu telefone e adicioná-las à aplicação para si.</li><li>**SMS.** Usado para garantir que o seu número de telefone corresponde ao número registado quando iniciar sôs com a sua conta pessoal da Microsoft pela primeira vez. Enviamos uma mensagem de texto para o telefone no qual instalou a aplicação que inclui um código de verificação de 6-8 dígitos. Não é necessário encontrar este código e introduzi-lo porque o Autenticador o encontra automaticamente na mensagem de texto.</li><li>**Desenhe outras aplicações.** A notificação que obtém que verifica a sua identidade também é exibida em qualquer outra aplicação de execução.</li><li>**Receba os dados da internet.** Esta permissão é necessária para o envio de notificações.</li><li>**Evite que o telefone durma.** Se registar o seu dispositivo junto da sua organização, a sua organização pode alterar esta política no seu telemóvel.</li><li>**Controle a vibração.** Pode escolher se pretende uma vibração sempre que receber uma notificação para verificar a sua identidade.</li><li>**Use o hardware de impressões digitais.** Alguns trabalhos e contas escolares requerem um PIN adicional sempre que verificar a sua identidade. Para facilitar o processo, permitimos que utilize a sua impressão digital em vez de introduzir o PIN.</li><li> **Ver ligações de rede.** Quando adiciona uma conta Microsoft, a aplicação requer ligação rede/internet.</li><li>**Leia o conteúdo do seu armazenamento.** Esta permissão só é utilizada quando reporta um problema técnico através das definições da aplicação. Algumas informações do seu armazenamento são recolhidas para diagnosticar o problema.</li><li>**Acesso total à rede.** Esta permissão é necessária para o envio de notificações para verificar a sua identidade.</li><li>**Correr na startup.** Se reiniciar o seu telefone, esta permissão garante que continua a receber notificações para verificar a sua identidade.</li></ul>

### <a name="approve-requests-without-unlocking"></a>Aprovar pedidos sem desbloquear

**P:** Porque é que o Autenticador permite aprovar um pedido sem desbloquear o dispositivo?

**R:** Não é preciso desbloquear o seu dispositivo para aprovar pedidos de verificação, porque tudo o que precisa de provar é que tem o telemóvel consigo. A verificação em duas etapas requer provas de duas coisas, uma coisa que sabes, e uma coisa que tens. O que sabe é a sua senha. O que tem é o seu telemóvel (configurado com Autenticador e registado como uma prova de autenticação multi-factor.) Assim, ter o telefone e aprovar o pedido satisfaz os critérios para o segundo fator de autenticação.

### <a name="activity-notifications"></a>Notificações de atividades

**P:** Por que estou a receber notificações sobre a minha atividade de conta?

**R**: As notificações de atividade são enviadas imediatamente para o Autenticador sempre que é feita uma alteração nas suas contas pessoais da Microsoft, ajudando a mantê-lo mais seguro. Anteriormente enviamos estas notificações apenas por e-mail e SMS. Para obter mais informações sobre estas notificações de atividade, consulte [o que acontece se houver um insusitado na sua conta.](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in) Para alterar onde recebe as suas notificações, [inscreva-se no Local onde podemos contactá-lo com](https://account.live.com/SecurityNotifications/Update) a página de alertas de conta não crítica da sua conta.

### <a name="one-time-passcodes"></a>Códigos de acesso únicos

**P:** As minhas senhas únicas não estão a funcionar. O que devo fazer?

**R**: Certifique-se de que a data e a hora do seu dispositivo estão corretas e que estão a ser sincronizadas automaticamente. Se a data e a hora estiverem erradas, ou fora de sincronização, o código não funcionará.

### <a name="windows-10-mobile"></a>Windows 10 Mobile

**P:** O sistema operativo Móvel Windows 10 foi deprecado em dezembro de 2019. Será que o Autenticador Microsoft nos sistemas operativos Windows Mobile também será depreciado?

**R**: Autenticador em todos os sistemas operativos Windows Mobile não será suportado após 28 de fevereiro de 2020. Os utilizadores não serão elegíveis para receber quaisquer novas atualizações para a aplicação postar a data acima referida. Depois de 28 de fevereiro de 2020, os serviços da Microsoft que atualmente suportam autenticações utilizando o Microsoft Authenticator em todos os sistemas operativos Windows Mobile começarão a retirar o seu suporte. Para autenticar os serviços da Microsoft, encorajamos todos os nossos utilizadores a mudarem para um mecanismo de autenticação alternativo antes desta data.

### <a name="default-mail-app"></a>Aplicativo de correio predefinido

**P:** Ao iniciar sessão na minha conta de trabalho ou escola utilizando a aplicação de correio padrão que vem com o iOS, sou solicitado pelo Autenticador para as minhas informações de verificação de segurança. Depois de inserir essa informação e voltar à aplicação de correio, recebo um erro. Que posso eu fazer?

**R**: Isto provavelmente acontece porque o seu início de sposição e a sua aplicação de correio estão ocorrendo em duas aplicações diferentes, fazendo com que o processo inicial de início de sing-in deixe de funcionar e falhe. Para tentar corrigir isto, recomendamos que selecione o ícone **Safari** no lado inferior direito do ecrã enquanto se inscreve na sua aplicação de correio. Ao mudar-se para o Safari, todo o processo de inscrição acontece numa única aplicação, permitindo-lhe iniciar sação na app com sucesso.

### <a name="apple-watch-watchos-7"></a>Apple Watch watchOS 7

**P:** Porque estou a ter problemas com o Apple Watch no watchOS 7?

**R:** Existe um problema com a aprovação de notificações no watchOS 7, e estamos a trabalhar com a Apple para que isto seja corrigido. Entretanto, quaisquer notificações que exijam a aplicação watchOS do Microsoft Authenticator devem ser aprovadas no seu telemóvel.

### <a name="apple-watch-doesnt-show-accounts"></a>Apple Watch não mostra contas

**P:** Porque é que todas as minhas contas não aparecem quando abro o Autenticador no meu Apple Watch?

**R**: O autenticador suporta apenas contas pessoais ou escolares ou de trabalho da Microsoft com notificações push na aplicação de acompanhantes do Apple Watch. Para as suas outras contas, como o Google ou o Facebook, tem de abrir a aplicação Authenticator no seu telemóvel para ver os seus códigos de verificação.

### <a name="apple-watch-notifications"></a>Notificações do Apple Watch

**P:** Por que não posso aprovar ou negar notificações no meu Apple Watch?

**R**: Em primeiro lugar, certifique-se de que atualizou para a versão Autenticadora 6.0.0 ou superior no seu iPhone. Depois disso, abra a aplicação de acompanhante microsoft Authenticator no seu Apple Watch e procure contas com um botão **Configurar** por baixo deles. Preencha o processo de configuração para aprovar notificações para essas contas.

### <a name="apple-watch-communication-error"></a>Erro de comunicação do Apple Watch

**P:** Estou a ter um erro de comunicação entre o Apple Watch e o meu telemóvel. O que posso fazer para resolver problemas?

**R**: Este erro acontece quando o ecrã do Relógio dorme antes de terminar de comunicar com o seu telefone.<br><b>Se o erro acontecer durante a configuração:</b><br>Tente executar novamente a configuração, certificando-se de que mantém o relógio acordado até que o processo esteja concluído. Ao mesmo tempo, abra a aplicação no seu telemóvel e responda a quaisquer indicações que apareçam.<br>Se o seu telefone e o Watch ainda não estiverem a comunicar, pode experimentar as seguintes ações:<ol><li>Force deixe a aplicação de telefone Microsoft Authenticator e abra-a novamente no seu iPhone.</li><li>Force a saída da aplicação complementar no seu Apple Watch.<ol><li> Abra a app microsoft Authenticator companion no seu Relógio</li><li>Segure o botão lateral até aparecer o ecrã **de paragem.**</li><li>Solte o botão lateral e mantenha premida a Coroa Digital para forçar a saída da aplicação ativa.</li></ol></li><li>Desligue o Bluetooth e o Wi-Fi tanto para o seu telemóvel como para o relógio e, em seguida, volte a ligá-los.</li><li>Reinicie o iPhone e o Relógio.</li></ol><b>Se o erro ocorrer quando está a tentar aprovar uma notificação:</b><br>Da próxima vez que tentar aprovar uma notificação no Apple Watch, mantenha o ecrã acordado até que o pedido esteja completo e ouça o som que indica que foi bem sucedido.

### <a name="apple-watch-companion-app-not-syncing"></a>App de acompanhante do Apple Watch não sincronizada

**P:** Porque é que a aplicação companheira do Microsoft Authenticator para o Apple Watch não está a sincronizar ou a aparecer no meu relógio?

**R**: Se a aplicação não aparecer no seu Relógio, experimente as seguintes ações: <ol><li>Certifique-se de que o relógio está a correr o watchOS 4.0 ou superior.</li><li>Sync your Watch de novo.</li></ol>

### <a name="apple-watch-companion-app-crashed"></a>App de acompanhante do Apple Watch despenhou-se

**P:** A minha aplicação de acompanhante apple watch despenhou-se. Posso enviar-lhe os meus registos de acidentes para que possa investigar?

**R:** Primeiro tem de se certificar de que escolheu partilhar as suas análises connosco. Se é utilizador do TestFlight, já se inscreveu. Caso contrário, pode ir a **Definições > Privacidade > Analytics** e selecionar tanto a análise do **iPhone & Watch partilhada** como a partilha com as opções de **Desenvolvedores de Aplicações.**<br>Depois de se inscrever, pode tentar reproduzir o seu acidente para que os seus registos de acidentes sejam automaticamente enviados para nós para investigação. No entanto, se não conseguir reproduzir o seu crash, pode copiar manualmente os seus ficheiros de registo e enviá-los para nós.<ol><li>Abra a aplicação Watch no seu telefone, vá a **Definições > General**, e, em seguida, clique em **Copy Watch Analytics**.</li><li>Encontre a falha correspondente em **Definições > Privacidade > Analytics > Analytics Data**, e, em seguida, copie manualmente todo o texto.</li><li>Abra o Autenticador no seu telemóvel e cole esse texto copiado para a caixa de texto **Partilhar com Programadores de Aplicações** na página **Enviar registos.**</li></ol>

## <a name="autofill-for-consumers"></a>Preenchimento automático para consumidores

**P:** O que é autofill no Autenticador?

**R**: A aplicação Authenticator agora armazena e preencha automaticamente senhas em aplicações e websites que visita no seu telefone. Pode utilizar o Preenchimento Automático para sincronizar e preencher automaticamente as suas palavras-passe nos seus dispositivos iOS e Android. Depois de configurar a aplicação Authenticator como fornecedor de preenchimento automático no seu telemóvel, oferece-se para guardar as suas palavras-passe quando as insere numa página de sposição de site ou aplicação. As palavras-passe são guardadas como parte da [sua conta Microsoft](https://account.microsoft.com/account) e também estão disponíveis quando iniciar sôs o teste no Microsoft Edge com a sua conta Microsoft.

**P:** Que informação pode preencher automaticamente o autenticador para mim?

**R**: O autenticador pode preencher automaticamente nomes de utilizador e palavras-passe em sites e aplicações que visitar no seu telefone.

**P:** Como posso ligar a senha de preenchimento automático no Autenticador no meu telemóvel?

**A**: Siga estes passos:

1. Abra a aplicação Authenticator.
1. Nas **Definições** em **Versão Beta,** ligue o **preenchimento automático**.
1. No separador **Palavras-passe** no Autenticador, selecione **Iniciar sôr-se com a Microsoft** e iniciar sôm-se utilizando a sua conta [Microsoft.](https://account.microsoft.com/account) Atualmente, esta funcionalidade suporta apenas contas da Microsoft e ainda não suporta contas de trabalho ou escolas.

**P:** Como faço do Autenticador o fornecedor de preenchimento automático predefinido no meu telefone?

**A**: Siga estes passos:

1. Abra **as definições** do autenticador e, em **Versão Beta,** ligue **o preenchimento automático**.
1. No **separador Palavras-Passe** dentro da aplicação, inscreva-se na utilização [da sua conta Microsoft.](https://account.microsoft.com/account)
1. Faça um dos seguintes:

   - No iOS, em **Definições**, selecione Como ligar o **Preenchimento Automático** na secção de definições de preenchimento automático para aprender a configurar o Autenticador como o fornecedor de preenchimento automático predefinido.
   - No Android, em **Definições**, selecione **set as Autofill provider** na secção de definições de preenchimento automático para definir o Autenticador como o fornecedor de preenchimento automático predefinido.

**P:** E se o interruptor **de preenchimento automático** estiver acinzentado para mim em Definições?

**R**: O preenchimento automático encontra-se atualmente em versão beta e ainda não foi habilitado para todas as organizações ou tipos de contas. Se o interruptor **de preenchimento automático** nas **Definições** estiver acinzentado para si, é provável que esteja a utilizar a aplicação Authenticator com a sua conta de trabalho. Pode utilizar esta funcionalidade num dispositivo onde a sua conta de trabalho não é adicionada. Se a sua organização funcionar com a Microsoft, o interruptor **de preenchimento automático** será ativado mesmo quando uma conta de trabalho é adicionada ao Autenticador.

**P:** Como paro de sincronizar palavras-passe?

**R**: Para parar de sincronizar palavras-passe na aplicação Authenticator, abra a conta De **configurações automáticas de**  >  **configurações automáticas**  >  **Sync**. No ecrã seguinte, pode selecionar em **Stop sync e remover todos os dados de preenchimento automático**. Isto removerá palavras-passe e outros dados de preenchimento automático do dispositivo. A remoção de dados de preenchimento automático não afeta a autenticação de vários fatores.

**P:** Como estão as minhas palavras-passe protegidas pela aplicação Authenticator?

**R**: A aplicação Authenticator já fornece um alto nível de segurança para a autenticação de vários fatores e gestão de conta, e a mesma barra de alta segurança também é estendida para gerir as suas palavras-passe.

- **A autenticação forte é necessária pela aplicação Authenticator**: A inscrição no Autenticador requer um segundo fator. Isto significa que as suas palavras-passe dentro da aplicação Authenticator não podem ser acedidas mesmo que alguém tenha a sua palavra-passe da conta Microsoft.
- **Os dados de preenchimento automático estão protegidos com biometria e senha**: Antes de poder preencher automaticamente a palavra-passe numa aplicação ou site, o Autenticador requer uma senha biométrica ou de dispositivo. Isto garante que, mesmo que alguém tenha acesso ao seu dispositivo, não pode preencher ou ver a sua palavra-passe, uma vez que não poderá fornecer o PIN biométrico ou dispositivo. Além disso, um utilizador não pode abrir a página Passwords a menos que forneça biométrico ou PIN, mesmo que desativam o Bloqueio de Aplicações nas definições de aplicações.
- **Palavras-passe encriptadas no dispositivo**: As palavras-passe no dispositivo são encriptadas e as chaves de encriptação/desencriptação nunca são armazenadas e geradas sempre no ar. As palavras-passe só são desencriptadas quando o utilizador quiser, isto é, durante o preenchimento automático ou quando o utilizador quer ver a palavra-passe, ambas requerem biométrico ou PIN.
- **Segurança na nuvem e na rede**: As suas palavras-passe na nuvem são encriptadas e desencriptadas apenas quando chegam ao seu dispositivo. As palavras-passe são sincronizadas sobre uma ligação HTTPS protegida pelo SSL, que garante que nenhum intruso pode escutar dados sensíveis quando está a ser sincronizado. Também garantimos que verificamos a sanidade dos dados que estão a ser sincronizados através da rede utilizando funções de hashed criptográfico (especificamente, código de autenticação de mensagens baseadas em haxixe).

## <a name="autofill-for-it-admins"></a>Preenchimento automático para administradores de TI

**P:** Os meus funcionários ou alunos poderão utilizar o preenchimento automático de passwords na aplicação Authenticator?

**A:** Não. A funcionalidade de preenchimento automático encontra-se atualmente em versão beta e ainda não foi ativada para todas as organizações ou tipos de contas. Se o seu funcionário ou aluno tiver adicionado a sua conta de trabalho ou escola na aplicação Microsoft Authenticator, as palavras-passe não serão acessíveis a eles. A única exceção a esta restrição é quando o seu empregado ou aluno adiciona a sua conta de trabalho ou escola na autenticação multi-factor baseada na nuvem da Microsoft como [uma conta externa ou de terceiros.](user-help-auth-app-add-non-ms-account.md)

**P:** Posso disponibilizar o recurso de preenchimento automático aos meus funcionários (ou estudantes)?

**A:** Sim. Para ativar os seus funcionários ou alunos, a sua empresa ou escola pode ser adicionada a uma lista de autorizações. Contacte o seu suporte ou o contacto da Microsoft para ser adicionado à lista de autorizações. Além disso, se for administrador de TI para a sua organização, também pode preencher um formulário para expressar o seu interesse em aderir à [empresa de lista de admissão para preenchimento automático em Autenticador.](https://aka.ms/RequestAutofillInAuthenticator)

**P:** O trabalho dos meus utilizadores ou a palavra-passe da conta escolar será automaticamente sincronizado?

**A:** Não. O preenchimento automático da palavra-passe não sincroniza o trabalho ou a senha de conta escolar para os seus utilizadores. Quando os utilizadores visitam um site ou uma aplicação, o Autenticador oferecerá para guardar a palavra-passe para esse site ou app, e a palavra-passe é guardada apenas quando o utilizador o escolher.
  
**P:** Posso permitir a lista apenas de certos utilizadores da minha organização para o Autofill?

**A:** Não. As empresas só podem permitir o preenchimento automático de senhas para todos ou nenhum dos seus empregados neste momento. Vamos expandir gradualmente estes controlos.

**P:** E se o meu funcionário ou aluno tiver múltiplas contas de trabalho ou escola? Por exemplo, o meu empregado tem contas de várias empresas ou escolas no seu Autenticador Microsoft.

**R**: Todas as empresas ou escolas adicionadas na aplicação Authenticator devem ser listadas para Autofill em Autenticador para que o proprietário da aplicação possa usá-la. A única exceção a esta restrição é quando o seu empregado ou aluno adiciona a sua conta de trabalho ou escola na autenticação multi-factor baseada na nuvem da Microsoft como [uma conta externa ou de terceiros.](user-help-auth-app-add-non-ms-account.md)

## <a name="next-steps"></a>Passos seguintes

- Se tiver dificuldades em obter o seu código de verificação para a sua conta pessoal da Microsoft, consulte a secção de problemas de verificação do código de verificação de resolução de **problemas** da informação de segurança da conta da Microsoft & artigo de [códigos de verificação.](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)

- Se quiser mais informações sobre a verificação em duas etapas, consulte [Configurar a minha conta para verificação em duas etapas](multi-factor-authentication-end-user-first-time.md)

- Se quiser mais informações sobre informações de segurança, consulte [a visão geral da informação de segurança (pré-visualização)](./security-info-setup-signin.md)

- Se a sua pergunta não foi respondida aqui, queremos ouvir-te. Vá ao fórum da [aplicação Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp) para publicar a sua pergunta e obter ajuda da comunidade, ou deixe um comentário nesta página.
