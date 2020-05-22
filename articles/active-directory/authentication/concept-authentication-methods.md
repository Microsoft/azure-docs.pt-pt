---
title: Métodos e funcionalidades de autenticação - Diretório Ativo Azure
description: Conheça os diferentes métodos de autenticação e funcionalidades disponíveis no Azure Ative Directory para ajudar a melhorar e garantir eventos de entrada
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 642f2705f54fe8f84cfde7ff039c9a723be59595
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770964"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Que métodos de autenticação e verificação estão disponíveis no Diretório Ativo do Azure?

Como parte da experiência de entrada para contas no Azure Ative Directory (Azure AD), existem diferentes formas de um utilizador se autenticar. Um nome de utilizador e uma palavra-passe é a forma mais comum de um utilizador fornecer credenciais. Com funcionalidades modernas de autenticação e segurança em Azure AD, essa senha básica pode ser complementada ou substituída por métodos adicionais de autenticação.

Um utilizador em AD Azure pode optar por autenticar utilizando um dos seguintes métodos de autenticação:

* Nome de utilizador tradicional e senha
* Microsoft Autenticador App sem palavras-passe
* Ficha de hardware do JURAMENTO ou chave de segurança FIDO2
* SMS sem senha sem palavras-de-passe

Muitas contas em AD Azure estão ativadas para reset de senha de autosserviço (SSPR) ou Autenticação Multi-Factor Azure. Estas funcionalidades incluem métodos de verificação adicionais, tais como um telefonema ou questões de segurança. Recomenda-se que exija que os utilizadores registem vários métodos de verificação. Quando um método não está disponível para um utilizador, pode optar por autenticar com outro método.

O quadro seguinte descreve quais os métodos disponíveis para a autenticação primária ou secundária:

| Método | Autenticação primária | Autenticação secundária |
| --- | --- | --- |
| [Palavra-passe](#password) | Sim | |
| [Aplicação Microsoft Authenticator](#microsoft-authenticator-app) | Sim (pré-visualização) | MFA e SSPR |
| [Teclas de segurança FIDO2 (pré-visualização)](#fido2-security-keys) | Sim | Só de MFA |
| [Fichas de software OATH](#oath-software-tokens) | Não | MFA |
| [Fichas de hardware DO JURAMENTO (pré-visualização)](#oath-hardware-tokens-preview) | Sim | MFA |
| [SMS](#phone-options) | Sim (pré-visualização) | MFA e SSPR |
| [Chamada de voz](#phone-options) | Não | MFA e SSPR |
| [Questões de segurança](#security-questions) | Não | SSPR-only |
| [Endereço de correio eletrónico](#email-address) | Não | SSPR-only |
| [Palavras-passe da aplicação](#app-passwords) | Não | MFA apenas em certos casos |

Este artigo descreve estes diferentes métodos de autenticação e verificação disponíveis em Azure AD e quaisquer limitações ou restrições específicas.

![Métodos de autenticação em uso no ecrã de início de sessão](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>Palavra-passe

Uma senha azure AD é frequentemente um dos métodos primários de autenticação. Não é possível desativar o método de autenticação da palavra-passe.

Mesmo que utilize um método de autenticação como o [sms de entrada quando](howto-authentication-sms-signin.md) o utilizador não usa a sua palavra-passe para assinar, uma palavra-passe permanece como um método de autenticação disponível.

## <a name="microsoft-authenticator-app"></a>Aplicação Microsoft Authenticator

A aplicação Authenticator fornece um nível adicional de segurança para o seu trabalho ou conta escolar Azure AD ou a sua conta Microsoft e está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594)e [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Com a aplicação Microsoft Authenticator, os utilizadores podem autenticar de forma sem palavras-passe durante o início de sessão, ou como uma opção de verificação adicional durante o reset de palavras-passe de self-service (SSPR) ou eventos de autenticação multi-factor Azure.

Os utilizadores podem receber uma notificação através da aplicação móvel para que eles aprovem ou neguem, ou utilizem a aplicação Authenticator para gerar um código de verificação DOPR que possa ser introduzido numa interface de entrada. Se ativar um código de notificação e verificação, os utilizadores que registam a aplicação Autenticador podem utilizar qualquer um dos métodos para verificar a sua identidade.

Para utilizar a aplicação Authenticator num pedido de início de sessão em vez de uma combinação de nome de utilizador e palavra-passe, consulte Ativar o [acesso sem palavras-passe com a aplicação Microsoft Authenticator (pré-visualização)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Os utilizadores não têm a opção de registar a sua aplicação móvel quando permitem o SSPR. Em vez disso, os utilizadores podem registar a sua aplicação móvel no registo combinado de informações de [https://aka.ms/mfasetup](https://aka.ms/mfasetup) segurança em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

### <a name="notification-through-mobile-app"></a>Notificação através de aplicação móvel

A aplicação Authenticator pode ajudar a prevenir o acesso não autorizado a contas e a parar transações fraudulentas, empurrando uma notificação para o seu smartphone ou tablet. Os utilizadores visualizam a notificação e, se for legítimo, selecione **Verificar**. Caso contrário, podem selecionar **Deny**.

![Screenshot de exemplo web browser solicitação para notificação de app Autenticador para completar processo de iniciar sessão](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Se a sua organização tem pessoal a trabalhar ou a viajar para a China, a Notificação através do método de *aplicação móvel* em dispositivos Android não funciona nesse país/região. Devem ser disponibilizados métodos de autenticação alternativos para esses utilizadores.

### <a name="verification-code-from-mobile-app"></a>Código de verificação da aplicação móvel

A aplicação Authenticator pode ser usada como um símbolo de software para gerar um código de verificação DOPR. Depois de introduzir o seu nome de utilizador e palavra-passe, introduza o código fornecido pela aplicação Autenticador na interface de entrada. O código de verificação proporciona uma segunda forma de autenticação.

Os utilizadores podem ter uma combinação de até cinco tokens de hardware DO JURAMENTO ou aplicações autenticadoras, como a aplicação Microsoft Authenticator, configurada para utilização a qualquer momento.

> [!WARNING]
> Para garantir o mais alto nível de segurança para o reset da palavra-passe self-service quando só é necessário um método para redefinir, um código de verificação é a única opção disponível para os utilizadores.
>
> Quando são necessários dois métodos, os utilizadores podem redefinir usando um código de notificação ou de verificação, para além de quaisquer outros métodos habilitados.

## <a name="fido2-security-keys"></a>Chaves de segurança FIDO2

A Aliança FIDO (Fast IDentity Online) ajuda a promover padrões de autenticação aberta e a reduzir o utilizador de senhas como forma de autenticação. Fido2 é o mais recente padrão que incorpora a norma de autenticação web (WebAuthn).

Para utilizar as teclas de segurança FIDO2 num pedido de entrada em vez de uma combinação de nome de utilizador e palavra-passe, consulte Ativar o sinal de entrada da chave de [segurança FIDO2 sem palavras-passe (pré-visualização)](howto-authentication-passwordless-security-key.md).

Os utilizadores podem registar-se e, em seguida, selecionar uma chave de segurança FIDO2 na interface de entrada como principal meio de autenticação. Estas teclas de segurança FIDO2 são normalmente dispositivos USB, mas também podem usar Bluetooth ou NFC. Com um dispositivo de hardware que trata da autenticação, a segurança de uma conta é aumentada, uma vez que não há senha que possa ser exposta ou adivinhada.

As chaves de segurança FIDO2 em Azure AD estão atualmente em pré-visualização. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="oath-tokens"></a>Fichas do JURAMENTO

OATH TOTP (Time-based One Time Password) é um padrão aberto que especifica como os códigos de senha única (OTP) são gerados. O OATH TOTP pode ser implementado utilizando software ou hardware para gerar os códigos. A Azure AD não suporta o OATH HOTP, um padrão de geração de códigodiferente.

### <a name="oath-software-tokens"></a>Fichas de software OATH

Os tokens do Software OATH são aplicações tipicamente como a aplicação Microsoft Authenticator e outras aplicações autenticadoras. A Azure AD gera a chave secreta, ou semente, que é entrada na app e usada para gerar cada OTP.

A aplicação Autenticador aaplica automaticamente gera códigos quando configurada para fazer notificações push para que um utilizador tenha uma cópia de segurança mesmo que o seu dispositivo não tenha conectividade. Aplicações de terceiros que usam O JURAMENTO TOTP para gerar códigos também podem ser usadas.

Alguns tokens de hardware TOTP do JURAMENTO são programáveis, o que significa que não vêm com uma chave secreta ou semente pré-programada. Estes tokens de hardware programáveis podem ser configurados utilizando a chave secreta ou semente obtida a partir do fluxo de configuração de token do software. Os clientes podem comprar estes tokens ao fornecedor à sua escolha e usar a chave ou semente secretas no processo de configuração do seu fornecedor.

### <a name="oath-hardware-tokens-preview"></a>Fichas de hardware DO JURAMENTO (pré-visualização)

A Azure AD suporta a utilização de fichas SHA-1 OATH-TOTP que atualizam códigos a cada 30 ou 60 segundos. Os clientes podem comprar estes tokens ao fornecedor à sua escolha.

As fichas de hardware DO JURAMENTO TOTP normalmente vêm com uma chave secreta, ou semente, pré-programada no símbolo. Estas teclas devem ser introduzidas em AD Azure, conforme descrito nos seguintes passos. As teclas secretas estão limitadas a 128 caracteres, que podem não ser compatíveis com todas as fichas. A chave secreta só pode conter os caracteres *a-z* ou *A-Z* e os dígitos *1-7*, e deve ser codificado na *Base32*.

Tokens de hardware TOTP PRSI programáveis que podem ser resseados também podem ser configurados com Azure AD no fluxo de configuração de token software.

Os tokens de hardware do OATH são suportados como parte de uma pré-visualização pública. Para mais informações sobre pré-visualizações, consulte [Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) do Microsoft Azure

![Upload de fichas DE JURAMENTO para a lâmina de fichas do Juramento MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Uma vez adquiridas fichas, devem ser carregadas num formato de ficheiro separado de vírem (CSV), incluindo a UPN, número de série, chave secreta, intervalo de tempo, fabricante e modelo, como mostra o seguinte exemplo:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Certifique-se de que inclui a linha do cabeçalho no seu ficheiro CSV.

Uma vez devidamente formatado como ficheiro CSV, um administrador pode então iniciar sessão no portal Azure, navegar para **o Azure Ative Directory > Security > MFA > tokens DO JURAMENTO,** e carregar o ficheiro CSV resultante.

Dependendo do tamanho do ficheiro CSV, pode levar alguns minutos para processar. Selecione o botão **Refresh** para obter o estado atual. Se houver algum erro no ficheiro, pode descarregar um ficheiro CSV que lista quaisquer erros para que possa resolver. Os nomes de campo no ficheiro CSV descarregado são diferentes da versão carregada.

Uma vez resolvidos quaisquer erros, o administrador pode então ativar cada tecla selecionando **ativar** o símbolo e entrando no OTP apresentado no token.

Os utilizadores podem ter uma combinação de até cinco tokens de hardware DO JURAMENTO ou aplicações autenticadoras, como a aplicação Microsoft Authenticator, configurada para utilização a qualquer momento.

## <a name="phone-options"></a>Opções de telefone

Para autenticação direta utilizando mensagem de texto, pode [configurar e ativar os utilizadores para autenticação baseada em SMS (pré-visualização)](howto-authentication-sms-signin.md). A inscrição baseada em SMS é ótima para os trabalhadores da linha da frente. Com o sms de entrada, os utilizadores não precisam de saber um nome de utilizador e uma senha para aceder a aplicações e serviços. Em vez disso, o utilizador introduz o seu número de telemóvel registado, recebe uma mensagem de texto com um código de verificação e introduz-a na interface de entrada.

Os utilizadores também podem verificar-se usando um telemóvel ou telefone de escritório como forma secundária de autenticação usada durante a autenticação de multi-factors Do Azure ou reset de senha de autosserviço (SSPR).

Para funcionar corretamente, os números de telefone devem estar no formato *+CountryCode PhoneNumber*, por exemplo, *+1 4251234567*.

> [!NOTE]
> Tem de haver um espaço entre o código país/região e o número de telefone.
>
> O reset da palavra-passe não suporta extensões de telefone. Mesmo no formato *+1 4251234567X12345,* as extensões são removidas antes da chamada ser colocada.

### <a name="mobile-phone-verification"></a>Verificação do telemóvel

Para autenticação de multi-factores Azure ou SSPR, os utilizadores podem optar por receber uma mensagem de texto com um código de verificação para introduzir na interface de entrada ou receber uma chamada telefónica com uma solicitação para introduzir o seu código pin definido.

Se os utilizadores não querem que o seu número de telemóvel seja visível no diretório, mas que o queiram utilizar para redefinir palavras-passe, os administradores não devem preencher o número de telefone no diretório. Em vez disso, os utilizadores devem preencher o seu atributo do Telefone de **Autenticação** através do registo combinado de informações de segurança em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . Os administradores podem ver esta informação no perfil do utilizador, mas não é publicada em outro lugar.

![Screenshot do portal Azure que mostra métodos de autenticação com um número de telefone povoado](media/concept-authentication-methods/user-authentication-methods.png)

A Microsoft não garante a entrega rápida de SMS ou de autenticação rápida azure multi-factor com base em voz pelo mesmo número. No interesse dos nossos utilizadores, podemos adicionar ou remover códigos curtos a qualquer momento, à medida que fazemos ajustes de rotas para melhorar a entrega de SMS. A Microsoft não suporta códigos curtos para países/regiões além dos Estados Unidos e Canadá.

#### <a name="text-message-verification"></a>Verificação de mensagem de texto

Com verificação de mensagens de texto durante a Autenticação Multi-Factor SSPR ou Azure, um SMS é enviado para o número de telemóvel contendo um código de verificação. Para completar o processo de entrada, o código de verificação fornecido é introduzido na interface de entrada.

#### <a name="phone-call-verification"></a>Verificação de chamadas telefónicas

Com verificação de chamada telefónica durante a Autenticação SSPR ou Azure Multi-Factor, é feita uma chamada de voz automatizada para o número de telefone registado pelo utilizador. Para completar o processo de entrada, o utilizador é solicitado a introduzir o seu número de pino seguido de # no seu teclado.

### <a name="office-phone-verification"></a>Verificação telefónica de escritório

O atributo do telefone do escritório é gerido pelo administrador da AD Azure e não pode ser registado por um próprio utilizador.

Com verificação de chamada telefónica durante a Autenticação SSPR ou Azure Multi-Factor, é feita uma chamada de voz automatizada para o número de telefone registado pelo utilizador. Para completar o processo de entrada, o utilizador é solicitado a introduzir o seu número de pino seguido de # no seu teclado.

### <a name="troubleshooting-phone-options"></a>Troubleshooting phone options (Resolução de problemas das opções de telefone)

Se tiver problemas com a autenticação do telefone para a AD Azure, reveja as seguintes etapas de resolução de problemas:

* Id de chamada bloqueada num único dispositivo.
   * Reveja os números bloqueados configurados no dispositivo.
* Número de telefone errado ou código país/região incorreto, ou confusão entre o número de telefone pessoal versus número de telefone de trabalho.
   * Problemas entrem no objeto do utilizador e métodos de autenticação configurados. Certifique-se de que os números de telefone corretos estão registados.
* PIN errado introduzido.
   * Confirme que o utilizador utilizou o PIN correto como registado para a sua conta.
* Chamada reencaminhada para correio de voz.
   * Certifique-se de que o utilizador tem o telemóvel ligado e que o serviço está disponível na sua área ou utilize um método alternativo.
* O utilizador está bloqueado
   * Tenha um administrador da AD Azure desbloqueie o utilizador no portal Azure.
* O SMS não está inscrito no dispositivo.
   * Tenha o utilizador a alterar métodos ou a ativar SMS no dispositivo.
* Fornecedores de telecomunicações defeituosos, tais como nenhuma entrada de telefone detetada, problemas de tons DTMF em falta, id de chamada bloqueada em vários dispositivos, ou SMS bloqueados em vários dispositivos.
   * A Microsoft utiliza vários fornecedores de telecomunicações para encaminhar chamadas telefónicas e mensagens SMS para autenticação. Se vir algum dos problemas acima referidos, tente utilizar o método pelo menos cinco vezes dentro de 5 minutos e tenha as informações do utilizador disponíveis quando contactar o suporte da Microsoft.

## <a name="security-questions"></a>Perguntas de segurança

As questões de segurança não são usadas como método de autenticação durante um evento de inscrição. Em vez disso, as questões de segurança podem ser usadas durante o processo de reset de palavra-passe de autosserviço (SSPR) para confirmar quem você é. As contas do administrador não podem usar questões de segurança como método de verificação com o SSPR.

Quando os utilizadores se registam para o SSPR, são solicitados a escolher os métodos de autenticação a utilizar. Se optarem por usar questões de segurança, escolhem de um conjunto de perguntas para solicitar e, em seguida, fornecem as suas próprias respostas.

![Screenshot do portal Azure que mostra métodos de autenticação e opções para questões de segurança](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> As questões de segurança são armazenadas de forma privada e segura num objeto de utilizador no diretório e só podem ser respondidas pelos utilizadores durante o registo. Não há como um administrador ler ou modificar as perguntas ou respostas de um utilizador.

As questões de segurança podem ser menos seguras do que outros métodos, porque algumas pessoas podem saber as respostas às perguntas de outro utilizador. Se utilizar questões de segurança com sSPR, é aconselhável usá-las em conjunto com outro método. Um utilizador pode ser solicitado a utilizar a Aplicação autenticadora da Microsoft ou a autenticação do telefone para verificar a sua identidade durante o processo SSPR, e escolher questões de segurança apenas se não tiver o seu telemóvel ou dispositivo registado com eles.

### <a name="predefined-questions"></a>Questões predefinidas

As seguintes questões de segurança predefinidas estão disponíveis para utilização como método de verificação com sSPR. Todas estas questões de segurança são traduzidas e localizadas em todo o conjunto de idiomas do Office 365 com base no local do navegador do utilizador:

* Em que cidade conheceu o seu primeiro cônjuge/parceiro?
* Em que cidade os seus pais conheceram?
* Em que cidade vive o seu irmão mais próximo?
* Em que cidade nasceu o teu pai?
* Naquela cidade foi o seu primeiro emprego?
* Em que cidade nasceu a sua mãe?
* Em que cidade esteve no Ano Novo de 2000?
* Qual é o último nome do teu professor favorito no liceu?
* Qual é o nome de uma faculdade a que se candidatou, mas não frequentou?
* Qual é o nome do lugar onde realizou a sua primeira receção de casamento?
* Qual é o nome do meio do teu pai?
* Qual é a sua comida preferida?
* Qual é o primeiro e último nome da sua avó materna?
* Qual é o nome do meio da sua mãe?
* Qual é o aniversário e o ano do seu irmão mais velho? (por exemplo, novembro de 1985)
* Qual é o nome do meio do teu irmão mais velho?
* Qual é o primeiro e último nome do seu avô paterno?
* Qual é o nome do meio do teu irmão mais novo?
* Que escola frequentou no 6º ano?
* Qual foi o primeiro e último nome do seu melhor amigo de infância?
* Qual foi o primeiro e último nome do seu primeiro outro significativo?
* Qual era o último nome da tua professora preferida?
* Qual foi a estreia e modelo do seu primeiro carro ou moto?
* Qual era o nome da primeira escola que frequentou?
* Qual era o nome do hospital onde nasceu?
* Qual era o nome da rua da sua primeira casa de infância?
* Qual era o nome do seu herói de infância?
* Qual era o nome do seu animal de peluche favorito?
* Qual era o nome do seu primeiro animal de estimação?
* Qual era o seu apelido de infância?
* Qual era o teu desporto favorito no liceu?
* Qual foi o seu primeiro emprego?
* Quais foram os últimos quatro dígitos do seu número de telefone de infância?
* Quando erajovem, o que queria ser quando crescesse?
* Quem é a pessoa mais famosa que já conheceu?

### <a name="custom-security-questions"></a>Questões de segurança personalizadas

Para uma flexibilidade adicional, pode definir as suas próprias questões de segurança personalizadas. O comprimento máximo de uma questão de segurança personalizada é de 200 caracteres.

As questões de segurança personalizadas não são localizadas automaticamente, como com as questões de segurança padrão. Todas as perguntas personalizadas são apresentadas no mesmo idioma que são introduzidas na interface administrativa do utilizador, mesmo que o navegador local do utilizador seja diferente. Se precisa de perguntas localizadas, deve usar as perguntas predefinidas.

### <a name="security-question-requirements"></a>Requisitos de questões de segurança

Para questões de segurança padrão e personalizadas, aplicam-se os seguintes requisitos e limitações:

* O limite mínimo de caracteres da resposta é de três caracteres.
* O limite máximo de caracteres de resposta é de 40 caracteres.
* Os utilizadores não podem responder à mesma pergunta mais de uma vez.
* Os utilizadores não podem dar a mesma resposta a mais do que uma pergunta.
* Qualquer conjunto de caracteres pode ser usado para definir as perguntas e as respostas, incluindo caracteres Unicode.
* O número de perguntas definidas deve ser superior ou igual ao número de perguntas que foram necessárias para se registar.

## <a name="email-address"></a>Endereço de e-mail

Um endereço de e-mail não pode ser usado como um método de autenticação direta. O endereço de e-mail só está disponível como opção de verificação para redefinição da palavra-passe self-service (SSPR). Quando o endereço de e-mail é selecionado durante o SSPR, é enviado um e-mail ao utilizador para completar o processo de autenticação/verificação.

Durante o registo para sSPR, um utilizador fornece o endereço de e-mail para usar. Recomenda-se que utilizem uma conta de e-mail diferente da sua conta corporativa para garantir que podem aceder-lhe durante o SSPR.

## <a name="app-passwords"></a>Palavras-passe da aplicação

Algumas aplicações antigas e não-navegadoras não entendem pausas ou pausas no processo de autenticação. Se um utilizador estiver habilitado para a autenticação de vários fatores e tentar utilizar uma destas aplicações mais antigas e não-navegadoras, normalmente não conseguem autenticar com sucesso. Uma palavra-passe de aplicação permite que os utilizadores continuem a autenticar com sucesso com aplicações mais antigas e não-navegadoras sem interrupção.

Por padrão, os utilizadores não podem criar senhas de aplicação. Caso necessite de permitir que os utilizadores criem palavras-passe de aplicações, selecione os **utilizadores do Permitir em configurar palavras-passe para iniciar sessão em aplicações não navegadoras** de acordo com as *definições* do Serviço para as propriedades de Autenticação Multi-Factor do utilizador.

![Screenshot do portal Azure que mostra as definições de serviço para autenticação de vários fatores para permitir ao utilizador de palavras-passe de aplicações](media/concept-authentication-methods/app-password-authentication-method.png)

Se impor a autenticação multi-factor Azure utilizando políticas de Acesso Condicional e não através de MFA por utilizador, não pode criar palavras-passe de aplicação. Aplicações modernas que utilizam políticas de Acesso Condicional para controlar o acesso não precisam de senhas de aplicação.

Se a sua organização for federada para um único sign-on (SSO) com AD Azure e utilizar a Autenticação Multi-Factor Azure, aplicam-se as seguintes considerações:

* A palavra-passe da aplicação é verificada pela Azure AD, pelo que contorna a federação. A Federação só é utilizada na configuração de senhas de aplicação. Para utilizadores federados (SSO), as palavras-passe são armazenadas no ID organizacional. Se o utilizador deixar a empresa, essa informação tem de fluir para id organizacional utilizando o DirSync. Os eventos de desativação de conta ou de supressão podem demorar até três horas a sincronizar, o que atrasa a desativação/eliminação de palavras-passe de aplicações em AD Azure.
* As definições de Controlo de Acesso ao Cliente no local não são honradas por palavras-passe de aplicações.
* Não existe nenhuma capacidade de autenticação ou auditoria no local para senhas de aplicação.
* Certos desenhos arquitetónicos avançados podem exigir a utilização de uma combinação de nome de utilizador organizacional e palavras-passe e palavras-passe de aplicações quando utilizara autenticação multifactor, dependendo do local onde autenticam.
    * Para clientes que autenticam contra uma infraestrutura no local, utilizaria um nome de utilizador organizacional e uma palavra-passe.
    * Para clientes que autenticam contra o Azure AD, utilizaria a palavra-passe da aplicação.

## <a name="next-steps"></a>Passos seguintes

Para começar, consulte o tutorial para reset de [senha de autosserviço (SSPR)][tutorial-sspr] e [autenticação de multi-factores Azure][tutorial-azure-mfa].

Para saber mais sobre os conceitos SSPR, veja como funciona o reset de [palavra-passe autosserviço da Azure AD][concept-sspr].

Para saber mais sobre os conceitos de MFA, veja como funciona a [autenticação multi-factor Azure.][concept-mfa]

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md