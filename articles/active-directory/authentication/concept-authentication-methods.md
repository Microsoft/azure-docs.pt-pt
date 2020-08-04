---
title: Métodos e funcionalidades de autenticação - Azure Ative Directory
description: Conheça os diferentes métodos e funcionalidades de autenticação disponíveis no Azure Ative Directory para ajudar a melhorar e garantir eventos de entrada
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 05283c02c2e5f95e22beb6fbeaad7a99e42ee6aa
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87540840"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Que métodos de autenticação e verificação estão disponíveis no Azure Active Directory?

Como parte da experiência de inscrição para contas no Azure Ative Directory (Azure AD), existem diferentes formas de um utilizador se autenticar. Um nome de utilizador e palavra-passe é a forma mais comum de um utilizador fornecer credenciais historicamente. Com as funcionalidades modernas de autenticação e segurança no Azure AD, essa palavra-passe básica pode ser complementada ou substituída por métodos de autenticação adicionais.

Um utilizador em Azure AD pode optar por autenticar utilizando um dos seguintes métodos de autenticação:

* Nome de utilizador tradicional e senha
* Microsoft Authenticator App sem palavras-passe sem palavras-passe
* Token de hardware da OATH ou chave de segurança FIDO2
* Sing-in sem palavras-passe com base em SMS

Muitas contas em Azure AD estão ativadas para reset de senha de autosserviço (SSPR) ou autenticação multi-factor Azure. Estas funcionalidades incluem métodos de verificação adicionais, como uma chamada telefónica ou questões de segurança. Recomenda-se que exija que os utilizadores registem vários métodos de verificação. Quando um método não está disponível para um utilizador, pode optar por autenticar com outro método.

O quadro que se segue descreve quais os métodos disponíveis para a autenticação primária ou secundária:

| Método | Autenticação primária | Autenticação secundária |
| --- | --- | --- |
| [Palavra-passe](#password) | Sim | |
| [Aplicação Microsoft Authenticator](#microsoft-authenticator-app) | Sim (pré-visualização) | MFA e SSPR |
| [Teclas de segurança FIDO2 (pré-visualização)](#fido2-security-keys) | Sim | Apenas MFA |
| [Fichas de software OATH](#oath-software-tokens) | Não | MFA |
| [Fichas de hardware do OATH (pré-visualização)](#oath-hardware-tokens-preview) | Sim | MFA |
| [SMS](#phone-options) | Sim (pré-visualização) | MFA e SSPR |
| [Chamada de voz](#phone-options) | Não | MFA e SSPR |
| [Perguntas de segurança](#security-questions) | Não | Apenas SSPR |
| [Endereço de e-mail](#email-address) | Não | Apenas SSPR |
| [Palavras-passe da aplicação](#app-passwords) | Não | MFA apenas em certos casos |

Este artigo descreve estes diferentes métodos de autenticação e verificação disponíveis no AZure AD e quaisquer limitações ou restrições específicas.

![Métodos de autenticação em uso no ecrã de inscrição](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>Palavra-passe

Uma palavra-passe AD AZure é frequentemente um dos principais métodos de autenticação. Não é possível desativar o método de autenticação de palavras-passe.

Mesmo que utilize um método de autenticação, como o de entrada por SMS, quando o utilizador não utiliza a sua [palavra-passe](howto-authentication-sms-signin.md) para assinar, uma palavra-passe permanece como um método de autenticação disponível.

## <a name="microsoft-authenticator-app"></a>Aplicação Microsoft Authenticator

A aplicação Authenticator fornece um nível adicional de segurança ao seu trabalho AD ou conta escolar do Azure ou à sua conta Microsoft e está disponível para [Android,](https://go.microsoft.com/fwlink/?linkid=866594) [iOS](https://go.microsoft.com/fwlink/?linkid=866594)e [Windows Phone.](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) Com a aplicação Microsoft Authenticator, os utilizadores podem autenticar de forma sem palavras-passe durante o sent-in ou como uma opção de verificação adicional durante o reset da palavra-passe de autosserviço (SSPR) ou a autenticação multi-factor Azure.

Os utilizadores podem receber uma notificação através da aplicação móvel para que possam aprovar ou negar, ou utilizar a aplicação Authenticator para gerar um código de verificação do OATH que pode ser introduzido numa interface de entrada. Se ativar um código de notificação e verificação, os utilizadores que registem a aplicação Authenticator podem utilizar qualquer método para verificar a sua identidade.

Para utilizar a aplicação Authenticator num pedido de entrada de dados em vez de uma combinação de nome de utilizador e palavra-passe, consulte [Ativar o início de sção sem palavras-passe com a aplicação Microsoft Authenticator (pré-visualização)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Os utilizadores não têm a opção de registar a sua aplicação móvel quando ativam o SSPR. Em vez disso, os utilizadores podem registar a sua aplicação móvel no [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ou como parte do registo combinado de informações de segurança em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

### <a name="notification-through-mobile-app"></a>Notificação através de aplicação móvel

A aplicação Authenticator pode ajudar a impedir o acesso não autorizado a contas e a impedir transações fraudulentas, empurrando uma notificação para o seu smartphone ou tablet. Os utilizadores visualizam a notificação e, se for legítima, **selecione Verificar**. Caso contrário, podem selecionar **Deny**.

![Screenshot de exemplo pedido de navegador web para notificação de aplicação autenticador para completar processo de sação](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Se a sua organização tem pessoal a trabalhar ou a viajar para a China, a notificação através do método *de aplicação móvel* em dispositivos Android não funciona nesse país/região, uma vez que os serviços de jogo da Google (incluindo notificações push) estão bloqueados na região. No entanto, a notificação do iOS funciona. Para dispositivos Android, devem ser disponibilizados métodos alternativos de autenticação para esses utilizadores.

### <a name="verification-code-from-mobile-app"></a>Código de verificação da aplicação móvel

A aplicação Authenticator pode ser usada como um símbolo de software para gerar um código de verificação do OATH. Depois de introduzir o seu nome de utilizador e senha, introduza o código fornecido pela aplicação Authenticator na interface de entrada. O código de verificação proporciona uma segunda forma de autenticação.

Os utilizadores podem ter uma combinação de até cinco fichas de hardware OATH ou aplicações autenticadoras, como a aplicação Microsoft Authenticator, configurada para ser utilizada a qualquer momento.

> [!WARNING]
> Para garantir o mais alto nível de segurança para a redefinição de senha de autosserviço quando apenas um método é necessário para o reset, um código de verificação é a única opção disponível para os utilizadores.
>
> Quando são necessários dois métodos, os utilizadores podem reiniciar usando um código de notificação ou verificação para além de quaisquer outros métodos ativados.

## <a name="fido2-security-keys"></a>Chaves de segurança FIDO2

A Fido (Fast IDentity Online) Alliance ajuda a promover padrões de autenticação aberta e a reduzir o utilizador de palavras-passe como forma de autenticação. FIDO2 é o mais recente padrão que incorpora o padrão de autenticação web (WebAuthn).

Para utilizar as teclas de segurança FIDO2 num pedido de inscrição em vez de um nome de utilizador e uma combinação de palavra-passe, consulte [Ativar o início de entrada da chave de segurança FIDO2 sem palavras-passe (pré-visualização)](howto-authentication-passwordless-security-key.md).

Os utilizadores podem registar-se e, em seguida, selecionar uma chave de segurança FIDO2 na interface de entrada como principal meio de autenticação. Estas teclas de segurança FIDO2 são normalmente dispositivos USB, mas também podem utilizar Bluetooth ou NFC. Com um dispositivo de hardware que trata da autenticação, a segurança de uma conta é aumentada, uma vez que não há nenhuma senha que possa ser exposta ou adivinhada.

As chaves de segurança FIDO2 em Azure AD estão atualmente em pré-visualização. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="oath-tokens"></a>Fichas do OATH

OATH TOTP (Password de Tempo Único) é um padrão aberto que especifica como os códigos de senha única (OTP) são gerados. O OATH TOTP pode ser implementado usando software ou hardware para gerar os códigos. A Azure AD não suporta o OATH HOTP, um padrão diferente de geração de código.

### <a name="oath-software-tokens"></a>Fichas de software OATH

Os tokens de OATH do software são normalmente aplicações como a app Microsoft Authenticator e outras aplicações autenticadoras. O Azure AD gera a chave secreta, ou semente, que é a entrada na app e usada para gerar cada OTP.

A aplicação Authenticator gera automaticamente códigos quando configurado para fazer notificações push para que um utilizador tenha uma cópia de segurança mesmo que o seu dispositivo não tenha conectividade. Aplicações de terceiros que usam OATH TOTP para gerar códigos também podem ser usadas.

Algumas fichas de hardware OATH TOTP são programáveis, o que significa que não vêm com uma chave secreta ou sementes pré-programadas. Estes tokens de hardware programáveis podem ser configurados usando a chave secreta ou semente obtida a partir do fluxo de configuração do token do software. Os clientes podem comprar estes tokens ao fornecedor à sua escolha e usar a chave secreta ou a semente no processo de configuração do seu fornecedor.

### <a name="oath-hardware-tokens-preview"></a>Fichas de hardware do OATH (pré-visualização)

A Azure AD suporta a utilização de fichas OATH-TOTP SHA-1 que atualizam códigos a cada 30 ou 60 segundos. Os clientes podem comprar estes tokens ao fornecedor à sua escolha.

Os tokens de hardware OATH TOTP normalmente vêm com uma chave secreta, ou sementes, pré-programadas no token. Estas teclas devem ser inseridas no AD Azure, conforme descrito nos passos seguintes. As teclas secretas estão limitadas a 128 caracteres, o que pode não ser compatível com todos os tokens. A chave secreta só pode conter os caracteres *a-z* ou *A-Z* e os dígitos *1-7*, e deve ser codificado na *Base32*.

As fichas de hardware do OATH TOTP programáveis que podem ser ressequidas também podem ser configuradas com Azure AD no fluxo de configuração do token do software.

As fichas de hardware da OATH são suportadas como parte de uma pré-visualização pública. Para obter mais informações sobre pré-visualizações, consulte [Termos Complementares de Utilização para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

![Upload de fichas de OATH para a lâmina de fichas de OATH do MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Uma vez adquiridas as fichas, devem ser carregadas num formato de ficheiro de valores separados por vírgula (CSV), incluindo a UPN, número de série, chave secreta, intervalo de tempo, fabricante e modelo como mostrado no seguinte exemplo:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Certifique-se de que inclui a linha do cabeçalho no seu ficheiro CSV.

Uma vez devidamente formatado como um ficheiro CSV, um administrador pode então iniciar sedutar no portal Azure, navegar para **o Azure Ative Directory > Security > MFA > tokens OATH**, e carregar o ficheiro CSV resultante.

Dependendo do tamanho do ficheiro CSV, pode levar alguns minutos a processar. Selecione o botão **'Renovar'** para obter o estado atual. Se houver algum erro no ficheiro, pode descarregar um ficheiro CSV que enumera quaisquer erros para resolver. Os nomes de campo no ficheiro CSV descarregado são diferentes da versão carregada.

Uma vez abordados quaisquer erros, o administrador pode então ativar cada tecla selecionando **ativar** para o token e introduzindo o OTP exibido no token.

Os utilizadores podem ter uma combinação de até cinco fichas de hardware OATH ou aplicações autenticadoras, como a aplicação Microsoft Authenticator, configurada para ser utilizada a qualquer momento.

## <a name="phone-options"></a>Opções de telefone

Para autenticação direta utilizando mensagem de texto, pode [configurar e ativar os utilizadores para autenticação baseada em SMS(pré-visualização)](howto-authentication-sms-signin.md). O sinal de sôs-in baseado em SMS é ótimo para os trabalhadores da linha da frente. Com o registo baseado em SMS, os utilizadores não precisam de saber um nome de utilizador e palavra-passe para aceder a aplicações e serviços. Em vez disso, o utilizador introduz o seu número de telemóvel registado, recebe uma mensagem de texto com um código de verificação e introduz-a na interface de entrada.

Os utilizadores também podem verificar-se usando um telemóvel ou telefone de escritório como forma secundária de autenticação usada durante a autenticação multi-factor Azure ou redefinição de senha de autosserviço (SSPR).

Para funcionar corretamente, os números de telefone devem estar no formato *+CountryCode PhoneNumber,* por exemplo, *+1 4251234567*.

> [!NOTE]
> Tem de haver um espaço entre o código país/região e o número de telefone.
>
> O reset da palavra-passe não suporta extensões de telefone. Mesmo no formato *+1 4251234567X12345,* as extensões são removidas antes da chamada ser feita.

### <a name="mobile-phone-verification"></a>Verificação do telemóvel

Para a autenticação multi-factor Azure ou SSPR, os utilizadores podem optar por receber uma mensagem de texto com um código de verificação para introduzir na interface de entrada ou receber uma chamada telefónica com um pedido para introduzir o seu código pin definido.

Se os utilizadores não quiserem que o seu número de telemóvel seja visível no diretório, mas que queiram usá-lo para reset de palavra-passe, os administradores não devem preencher o número de telefone no diretório. Em vez disso, os utilizadores devem preencher o seu atributo **Authentication Phone** através do registo combinado de informações de segurança em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . Os administradores podem ver esta informação no perfil do utilizador, mas não são publicadas em outro lugar.

![Screenshot do portal Azure que mostra métodos de autenticação com um número de telefone preenchido](media/concept-authentication-methods/user-authentication-methods.png)

A Microsoft não garante uma entrega rápida de solicitação de dados Azure multi-factor consistente ou baseada em voz pelo mesmo número. No interesse dos nossos utilizadores, podemos adicionar ou remover códigos curtos a qualquer momento à medida que fazemos ajustes de rota para melhorar a entrega de SMS. A Microsoft não suporta códigos curtos para países/regiões além dos Estados Unidos e Canadá.

#### <a name="text-message-verification"></a>Verificação de mensagens de texto

Com a verificação de mensagens de texto durante a autenticação de SSPR ou Azure Multi-Factor Authentication, é enviado um SMS para o número de telemóvel que contém um código de verificação. Para completar o processo de inscrição, o código de verificação fornecido é introduzido na interface de entrada.

#### <a name="phone-call-verification"></a>Verificação de chamadas telefónicas

Com a verificação de chamadas telefónicas durante a Autenticação Multi-Factor SSPR ou Azure, é feita uma chamada de voz automatizada para o número de telefone registado pelo utilizador. Para completar o processo de inscrição, o utilizador é solicitado a introduzir o seu número de pino seguido de # no teclado.

### <a name="office-phone-verification"></a>Verificação do telefone do escritório

O atributo do telefone do escritório é gerido pelo administrador AZure AD e não pode ser registado por um utilizador.

Com a verificação de chamadas telefónicas durante a Autenticação Multi-Factor SSPR ou Azure, é feita uma chamada de voz automatizada para o número de telefone registado pelo utilizador. Para completar o processo de inscrição, o utilizador é solicitado a introduzir o seu número de pino seguido de # no teclado.

### <a name="troubleshooting-phone-options"></a>Troubleshooting phone options (Resolução de problemas das opções de telefone)

Se tiver problemas com a autenticação do telefone para a Azure AD, reveja as seguintes etapas de resolução de problemas:

* Identificação de chamada bloqueada num único dispositivo.
   * Reveja quaisquer números bloqueados configurados no dispositivo.
* Número de telefone errado ou código de país/região incorreto, ou confusão entre o número de telefone pessoal versus número de telefone de trabalho.
   * Resolução de problemas do objeto do utilizador e métodos de autenticação configurados. Certifique-se de que os números de telefone corretos estão registados.
* PIN errado entrou.
   * Confirme que o utilizador utilizou o PIN correto como registado para a sua conta.
* Chamada reencaminhada para a caixa postal.
   * Certifique-se de que o utilizador tem o telemóvel ligado e que o serviço está disponível na sua área, ou utilize um método alternativo.
* O utilizador está bloqueado
   * Tenha um administrador AD Azure desbloqueie o utilizador no portal Azure.
* O SMS não é subscrito no dispositivo.
   * Que o utilizador altere os métodos ou ative SMS no dispositivo.
* Fornecedores de telecomunicações defeituosos, tais como nenhuma entrada de telefone detetada, problemas de tons DTMF em falta, ID de chamada bloqueado em vários dispositivos ou SMS bloqueados em vários dispositivos.
   * A Microsoft utiliza vários fornecedores de telecomunicações para encaminhar chamadas telefónicas e mensagens SMS para autenticação. Se vir algum dos problemas acima, tenha um utilizador que tente utilizar o método pelo menos cinco vezes no prazo de 5 minutos e tenha as informações desse utilizador disponíveis quando contactar o suporte da Microsoft.

## <a name="security-questions"></a>Perguntas de segurança

As questões de segurança não são usadas como um método de autenticação durante um evento de inscrição. Em vez disso, as questões de segurança podem ser usadas durante o processo de reset da palavra-passe de autosserviço (SSPR) para confirmar quem é. As contas do administrador não podem usar questões de segurança como método de verificação com sSPR.

Quando os utilizadores se registam na SSPR, são solicitados a escolher os métodos de autenticação a utilizar. Se optarem por usar questões de segurança, escolhem de um conjunto de perguntas para solicitar e, em seguida, fornecer as suas próprias respostas.

![Screenshot do portal Azure que mostra métodos de autenticação e opções para questões de segurança](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> As questões de segurança são armazenadas de forma privada e segura num objeto de utilizador no diretório e só podem ser respondidas pelos utilizadores durante o registo. Não há como um administrador ler ou modificar as perguntas ou respostas de um utilizador.

As questões de segurança podem ser menos seguras do que outros métodos, porque algumas pessoas podem saber as respostas às perguntas de outro utilizador. Se utilizar questões de segurança com sSPR, é aconselhável usá-las em conjunto com outro método. Um utilizador pode ser solicitado a utilizar a App autenticadora da Microsoft ou a autenticação do telefone para verificar a sua identidade durante o processo SSPR, e escolher questões de segurança apenas se não tiver o seu telemóvel ou dispositivo registado com eles.

### <a name="predefined-questions"></a>Perguntas predefinidas

As seguintes questões de segurança predefinidas estão disponíveis para utilização como método de verificação com a SSPR. Todas estas questões de segurança são traduzidas e localizadas no conjunto completo de idiomas do Office 365 com base no local do navegador do utilizador:

* Em que cidade conheceu o seu primeiro cônjuge/parceiro?
* Em que cidade os seus pais se conheceram?
* Em que cidade vive o seu irmão mais próximo?
* Em que cidade nasceu o seu pai?
* Em que cidade foi o seu primeiro emprego?
* Em que cidade nasceu a sua mãe?
* Em que cidade estava no Ano Novo de 2000?
* Qual é o último nome do teu professor favorito no liceu?
* Qual é o nome de uma faculdade a que se candidatou, mas não frequentou?
* Qual é o nome do lugar onde fez a sua primeira receção de casamento?
* Qual é o nome do meio do seu pai?
* Qual é a sua comida favorita?
* Qual é o primeiro e último nome da sua avó materna?
* Qual é o nome do meio da sua mãe?
* Qual é o aniversário do seu irmão mais velho mês e ano? (por exemplo, novembro de 1985)
* Qual é o nome do meio do seu irmão mais velho?
* Qual é o primeiro e último nome do seu avô paterno?
* Qual é o nome do meio do seu irmão mais novo?
* Que escola frequentaste no 6º ano?
* Qual foi o primeiro e último nome do seu melhor amigo de infância?
* Qual foi o primeiro e último nome do seu primeiro outro significativo?
* Qual era o último nome da tua professora favorita da escola?
* Qual foi a ideia e modelo do seu primeiro carro ou moto?
* Qual era o nome da primeira escola que frequentaste?
* Qual era o nome do hospital em que nasceu?
* Qual era o nome da rua da sua primeira casa de infância?
* Qual era o nome do seu herói de infância?
* Qual era o nome do seu animal de peluche favorito?
* Qual era o nome do seu primeiro animal de estimação?
* Qual era o seu apelido de infância?
* Qual era o teu desporto favorito no liceu?
* Qual foi o seu primeiro emprego?
* Quais foram os últimos quatro dígitos do seu número de telefone de infância?
* Quando era jovem, o que queria ser quando crescesse?
* Quem é a pessoa mais famosa que já conheceu?

### <a name="custom-security-questions"></a>Questões de segurança personalizadas

Para uma flexibilidade adicional, pode definir as suas próprias questões de segurança personalizadas. O comprimento máximo de uma questão de segurança personalizada é de 200 caracteres.

As questões de segurança personalizadas não são automaticamente localizadas, como com as questões de segurança predefinidos. Todas as questões personalizadas são apresentadas no mesmo idioma que são inseridas na interface administrativa do utilizador, mesmo que o local do navegador do utilizador seja diferente. Se precisar de perguntas localizadas, deve utilizar as perguntas predefinidas.

### <a name="security-question-requirements"></a>Requisitos de questões de segurança

Para questões de segurança padrão e personalizadas, aplicam-se os seguintes requisitos e limitações:

* O limite mínimo de caracteres de resposta é de três caracteres.
* O limite máximo de caracteres de resposta é de 40 caracteres.
* Os utilizadores não conseguem responder à mesma pergunta mais do que uma vez.
* Os utilizadores não podem dar a mesma resposta a mais do que uma pergunta.
* Qualquer conjunto de caracteres pode ser usado para definir as perguntas e as respostas, incluindo caracteres Unicode.
* O número de perguntas definidas deve ser superior ou igual ao número de perguntas que foram obrigadas a registar-se.

## <a name="email-address"></a>Endereço de e-mail

Um endereço de e-mail não pode ser usado como um método de autenticação direta. O endereço de e-mail só está disponível como opção de verificação para reset de senha de autosserviço (SSPR). Quando o endereço de e-mail é selecionado durante o SSPR, é enviado um e-mail ao utilizador para completar o processo de autenticação/verificação.

Durante o registo da SSPR, um utilizador fornece o endereço de e-mail para utilizar. Recomenda-se que utilizem uma conta de e-mail diferente da sua conta corporativa para se certificarem de que podem aceder à conta durante a SSPR.

## <a name="app-passwords"></a>Palavras-passe da aplicação

Algumas aplicações mais antigas e não-navegadores não entendem pausas ou quebras no processo de autenticação. Se um utilizador estiver habilitado para a autenticação de vários fatores e tentar utilizar uma destas aplicações mais antigas e não-navegadores, normalmente não conseguem autenticar com sucesso. Uma palavra-passe da aplicação permite que os utilizadores continuem a autenticar com sucesso aplicações mais antigas e não-navegadores sem interrupção.

Por padrão, os utilizadores não podem criar senhas de aplicação. Se precisar de permitir que os utilizadores criem senhas de aplicação, selecione os **utilizadores de Permitir que criem palavras-passe de aplicações para iniciar sessão em aplicações não-navegador** nas *definições* de Serviço para as propriedades de autenticação multi-factor do utilizador.

![Screenshot do portal Azure que mostra as definições de serviço para autenticação de vários fatores para permitir ao utilizador de senhas de aplicação](media/concept-authentication-methods/app-password-authentication-method.png)

Se aplicar a autenticação multi-factor Azure utilizando políticas de Acesso Condicional e não através de MFA por utilizador, não pode criar senhas de aplicação. As aplicações modernas que utilizam políticas de Acesso Condicional para controlar o acesso não precisam de senhas de aplicação.

Se a sua organização for federada para um único sign-on (SSO) com Azure AD e utilizar a autenticação multi-factor Azure, aplicam-se as seguintes considerações:

* A palavra-passe da aplicação é verificada pela Azure AD, por isso contorna a federação. A Federação só é utilizada na configuração de senhas de aplicação. Para os utilizadores federados (SSO), as palavras-passe são armazenadas no ID organizacional. Se o utilizador deixar a empresa, essa informação tem de fluir para iD organizacional usando o DirSync. Os eventos de desativação ou eliminação de contas podem demorar até três horas a sincronizar, o que atrasa a desativação/eliminação de senhas de aplicação em Azure AD.
* As definições de Controlo de Acesso ao Cliente no local não são honradas por palavras-passe de aplicações.
* Não há capacidade de autenticação ou auditoria no local para senhas de aplicação.
* Certos desenhos arquitetônicos avançados podem exigir a utilização de uma combinação de nome de utilizador organizacional e palavras-passe e passwords de aplicação quando se utilizam a autenticação de vários fatores, dependendo do local onde autenticam.
    * Para clientes que autenticam uma infraestrutura no local, utilizaria um nome de utilizador organizacional e uma palavra-passe.
    * Para clientes que autenticam contra a Azure AD, utilizaria a palavra-passe da aplicação.

## <a name="next-steps"></a>Passos seguintes

Para começar, consulte o [tutorial para reset de senha de autosserviço (SSPR)][tutorial-sspr] e [autenticação multi-factor Azure][tutorial-azure-mfa].

Para saber mais sobre os conceitos SSPR, consulte [como funciona o reset da palavra-passe de autosserviço AZure AD][concept-sspr].

Para saber mais sobre os conceitos de MFA, consulte [como funciona a autenticação multi-factor Azure.][concept-mfa]

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
