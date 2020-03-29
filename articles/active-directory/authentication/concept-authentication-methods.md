---
title: Métodos de autenticação - Diretório Ativo Azure
description: Métodos de autenticação disponíveis em Azure AD para MFA e SSPR
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a82c69575e82a7cf397955f08c3f114e449ba6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968778"
---
# <a name="what-are-authentication-methods"></a>O que são métodos de autenticação?

Como administrador, escolhendo métodos de autenticação para autenticação multi-factor Azure e reset de senha de autosserviço (SSPR), recomenda-se que exija que os utilizadores registem vários métodos de autenticação. Quando um método de autenticação não está disponível para um utilizador, pode optar por autenticar com outro método.

Os administradores podem definir na política quais os métodos de autenticação disponíveis para os utilizadores de SSPR e MFA. Alguns métodos de autenticação podem não estar disponíveis para todas as funcionalidades. Para obter mais informações sobre a configuração das suas políticas, consulte os artigos Como lançar com sucesso o reset de [senha de autosserviço](howto-sspr-deployment.md) e [planejando uma autenticação multi-factor Azure baseada na nuvem](howto-mfa-getstarted.md)

A Microsoft recomenda vivamente que os administradores permitam aos utilizadores selecionar mais do que o número mínimo exigido de métodos de autenticação caso não tenham acesso a um.

|Método de autenticação|Utilização|
| --- | --- |
| Palavra-passe | MFA e SSPR |
| Perguntas de segurança | SSPR apenas |
| Endereço de e-mail | SSPR apenas |
| Aplicação Microsoft Authenticator | MFA e SSPR |
| Ficha de hardware do JURAMENTO | Pré-visualização pública para MFA e SSPR |
| SMS | MFA e SSPR |
| Chamada de voz | MFA e SSPR |
| Palavras-passe da aplicação | MFA apenas em certos casos |

![Métodos de autenticação em uso no ecrã de início de sessão](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Fichas de hardware do OATH para MFA e SSPR são funcionalidades de pré-visualização pública do Diretório Ativo Azure. Para mais informações sobre pré-visualizações, consulte [Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) do Microsoft Azure|
|     |

## <a name="password"></a>Palavra-passe

A sua senha De AD Azure é considerada um método de autenticação. É o único método que **não pode ser desativado.**

## <a name="security-questions"></a>Perguntas de segurança

As questões de segurança só estão disponíveis **na palavra-passe do autosserviço Azure AD reset** para contas não administradoras.

Se utilizar questões de segurança, recomendamos usá-las em conjunto com outro método. As questões de segurança podem ser menos seguras do que outros métodos, porque algumas pessoas podem saber as respostas às perguntas de outro utilizador.

> [!NOTE]
> As questões de segurança são armazenadas de forma privada e segura num objeto de utilizador no diretório e só podem ser respondidas pelos utilizadores durante o registo. Não há forma de um administrador ler ou modificar as perguntas ou respostas de um utilizador.
>

### <a name="predefined-questions"></a>Questões predefinidas

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

Todas as questões de segurança predefinidas são traduzidas e localizadas em todo o conjunto de idiomas do Office 365 com base no local do navegador do utilizador.

### <a name="custom-security-questions"></a>Questões de segurança personalizadas

Questões de segurança personalizadas não estão localizadas. Todas as perguntas personalizadas são apresentadas no mesmo idioma que são introduzidas na interface administrativa do utilizador, mesmo que o navegador local do utilizador seja diferente. Se precisa de perguntas localizadas, deve usar as perguntas predefinidas.

O comprimento máximo de uma questão de segurança personalizada é de 200 caracteres.

### <a name="security-question-requirements"></a>Requisitos de questões de segurança

* O limite mínimo de caracteres da resposta é de três caracteres.
* O limite máximo de caracteres de resposta é de 40 caracteres.
* Os utilizadores não podem responder à mesma pergunta mais de uma vez.
* Os utilizadores não podem dar a mesma resposta a mais do que uma pergunta.
* Qualquer conjunto de caracteres pode ser usado para definir as perguntas e as respostas, incluindo caracteres Unicode.
* O número de perguntas definidas deve ser superior ou igual ao número de perguntas que foram necessárias para se registar.

## <a name="email-address"></a>Endereço de e-mail

O endereço de e-mail só está disponível no reset de **palavra-passe autosserviço Azure AD**.

A Microsoft recomenda o uso de uma conta de e-mail que não exigiria o acesso da senha azure AD do utilizador.

## <a name="microsoft-authenticator-app"></a>Aplicação Microsoft Authenticator

A aplicação Microsoft Authenticator fornece um nível adicional de segurança para o seu trabalho ou conta escolar azure ou para a sua conta Microsoft.

A aplicação Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) e [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

> [!NOTE]
> Os utilizadores não terão a opção de registar a sua aplicação móvel ao registarem-se para redefinir a palavra-passe de self-service. Em vez disso, os [https://aka.ms/mfasetup](https://aka.ms/mfasetup) utilizadores podem registar a [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)sua aplicação móvel na pré-visualização do registo de informações de segurança em .
>

### <a name="notification-through-mobile-app"></a>Notificação através de aplicação móvel

A aplicação Microsoft Authenticator pode ajudar a prevenir o acesso não autorizado a contas e a parar transações fraudulentas, empurrando uma notificação para o seu smartphone ou tablet. Os utilizadores visualizam a notificação e, se for legítimo, selecione Verificar. Caso contrário, podem selecionar O Deny.

> [!WARNING]
> Para redefinir a palavra-passe de autosserviço quando só é necessário um método para redefinir, o código de verificação é a única opção disponível para os utilizadores **garantirem o mais alto nível de segurança**.
>
> Quando são necessários dois métodos, os utilizadores poderão redefinir usando o código de verificação **de** notificação OU de **notificação,** para além de quaisquer outros métodos habilitados.
>

Se permitir a utilização de notificações através de aplicações móveis e código de verificação a partir de aplicações móveis, os utilizadores que registam a aplicação Microsoft Authenticator utilizando uma notificação podem utilizar a notificação e o código para verificar a sua identidade.

> [!NOTE]
> Se a sua organização tem pessoal a trabalhar ou a viajar para a China, a **Notificação através** do método de aplicação móvel em **dispositivos Android** não funciona nesse país. Devem ser disponibilizados métodos alternativos para esses utilizadores.

### <a name="verification-code-from-mobile-app"></a>Código de verificação da aplicação móvel

A aplicação Microsoft Authenticator ou outras aplicações de terceiros podem ser usadas como um símbolo de software para gerar um código de verificação DOPR. Depois de introduzir o seu nome de utilizador e palavra-passe, introduza o código fornecido pela aplicação no ecrã de entrada. O código de verificação proporciona uma segunda forma de autenticação.

> [!WARNING]
> Para redefinir a palavra-passe self-service quando só é necessário um método para redefinir o código de verificação é a única opção disponível para os utilizadores **garantirem o mais alto nível de segurança**.
>

Os utilizadores podem ter uma combinação de até cinco tokens de hardware DO JURAMENTO ou aplicações autenticadoras, como a aplicação Microsoft Authenticator configurada para utilização a qualquer momento.

## <a name="oath-hardware-tokens-public-preview"></a>Fichas de hardware DO JURAMENTO (pré-visualização pública)

O OATH é um padrão aberto que especifica como os códigos de senha única (OTP) são gerados. A Azure AD apoiará a utilização de tokens SHA-1 DE JURAMENTO-TOTP da variedade de 30 segundos ou 60 segundos. Os clientes podem adquirir estes tokens do fornecedor à sua escolha. As teclas secretas estão limitadas a 128 caracteres, que podem não ser compatíveis com todas as fichas. A chave secreta só pode conter os caracteres *a-z* ou *A-Z* e os dígitos *1-7*, e deve ser codificado na Base32.

![Upload de fichas DE JURAMENTO para a lâmina de fichas do Juramento MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Os tokens de hardware do OATH são suportados como parte de uma pré-visualização pública. Para mais informações sobre pré-visualizações, consulte [Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) do Microsoft Azure

Uma vez adquiridas fichas, devem ser carregadas num formato de ficheiro separado de vírem (CSV), incluindo a UPN, número de série, chave secreta, intervalo de tempo, fabricante e modelo, como mostra o seguinte exemplo:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Certifique-se de que inclui a linha do cabeçalho no seu ficheiro CSV.

Uma vez devidamente formatado como ficheiro CSV, um administrador pode então iniciar sessão no portal Azure, navegar para as**fichas**do**Juramento** > de**Segurança** > do >  **Diretório Ativo Azure,** e carregar o ficheiro CSV resultante.

Dependendo do tamanho do ficheiro CSV, pode levar alguns minutos para processar. Clique no botão **Refresh** para obter o estado atual. Se houver algum erro no ficheiro, terá a opção de descarregar um ficheiro CSV que enumera quaisquer erros para que possa resolver. Os nomes de campo no ficheiro CSV descarregado são diferentes da versão carregada.

Uma vez resolvidos quaisquer erros, o administrador pode então ativar cada tecla clicando em **Ativar** o token e introduzindo o OTP apresentado no token.

Os utilizadores podem ter uma combinação de até cinco tokens de hardware DO JURAMENTO ou aplicações autenticadoras, como a aplicação Microsoft Authenticator configurada para utilização a qualquer momento.

## <a name="phone-options"></a>Opções de telefone

### <a name="mobile-phone"></a>Número de telemóvel

Estão disponíveis duas opções para utilizadores com telemóveis.

Se os utilizadores não quiserem que o seu número de telemóvel seja visível no diretório, mas ainda assim querem usá-lo para redefinir palavras-passe, os administradores não devem povoá-lo no diretório. Os utilizadores devem preencher o seu atributo **do Telefone de Autenticação** através do portal de registo de [redefinição de palavra-passe](https://aka.ms/ssprsetup). Os administradores podem ver esta informação no perfil do utilizador, mas não é publicada em outro lugar.

Para funcionar corretamente, os números de telefone devem estar no formato *+CountryCode PhoneNumber*, por exemplo, +1 4255551234.

> [!NOTE]
> Tem de haver um espaço entre o código do país e o número de telefone.
>
> O reset da palavra-passe não suporta extensões de telefone. Mesmo no formato +1 4255551234X12345, as extensões são removidas antes da chamada ser colocada.

A Microsoft não garante a entrega rápida de SMS ou Multi-Factor de Autenticação com base em Voz pelo mesmo número. No interesse dos nossos utilizadores, a Microsoft pode adicionar ou remover códigos Curtos a qualquer momento, à medida que fazemos ajustes de rotas para melhorar a entrega de SMS. A Microsoft não suporta códigos curtos para países/regiões além dos Estados Unidos e Canadá.

#### <a name="text-message"></a>Mensagem de texto

Um SMS é enviado para o número de telemóvel contendo um código de verificação. Introduza o código de verificação fornecido na interface de entrada para continuar.

#### <a name="phone-call"></a>Chamada telefónica

Uma chamada de voz automatizada é feita para o número de telefone que fornece. Atender a chamada e pressionar # no teclado do telefone para autenticar

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamada telefónica não estarão disponíveis para os utilizadores de MFA e SSPR em inquilinos ads atos gratuitos/experimentais. As mensagens SMS não são afetadas por esta mudança. A chamada telefónica continuará disponível para os utilizadores em inquilinos da AD Azure pagos. Esta mudança só afeta os inquilinos azure free/trial.

### <a name="office-phone"></a>Telefone do escritório

Uma chamada de voz automatizada é feita para o número de telefone que fornece. Responda à chamada e pressione # no teclado do telefone para autenticar.

Para funcionar corretamente, os números de telefone devem estar no formato *+CountryCode PhoneNumber*, por exemplo, +1 4255551234.

O atributo do telefone do escritório é gerido pelo seu administrador.

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamada telefónica não estarão disponíveis para os utilizadores de MFA e SSPR em inquilinos ads atos gratuitos/experimentais. As mensagens SMS não são afetadas por esta mudança. A chamada telefónica continuará disponível para os utilizadores em inquilinos da AD Azure pagos. Esta mudança só afeta os inquilinos azure free/trial.

> [!NOTE]
> Tem de haver um espaço entre o código do país e o número de telefone.
>
> O reset da palavra-passe não suporta extensões de telefone. Mesmo no formato +1 4255551234X12345, as extensões são removidas antes da chamada ser colocada.

### <a name="troubleshooting-phone-options"></a>Troubleshooting phone options (Resolução de problemas das opções de telefone)

Problemas comuns relacionados com métodos de autenticação utilizando um número de telefone:

* ID de chamada bloqueada num único dispositivo
   * Dispositivo de resolução de problemas
* Número de telefone errado, código de país incorreto, número de telefone doméstico versus número de telefone de trabalho
   * Problemas de resolução de objetos de utilizador e métodos de autenticação configurados. Certifique-se de que os números de telefone corretos estão registados.
* PIN errado introduzido
   * Confirme que o utilizador utilizou o PIN correto registado no Servidor Azure MFA.
* Chamada reencaminhada para correio de voz
   * Certifique-se de que o utilizador tem telefone ligado e que o serviço está disponível na sua área ou utilize método alternativo.
* O utilizador está bloqueado
   * Mande o administrador desbloquear o utilizador no portal Azure.
* SMS não é subscrito no dispositivo
   * Tenha o utilizador a alterar métodos ou a ativar SMS no dispositivo.
* Fornecedores de telecomunicações defeituosos (Nenhuma entrada de telefone detetada, problemas de tons DTMF em falta, id de chamada bloqueada em vários dispositivos, ou SMS bloqueado em vários dispositivos)
   * A Microsoft utiliza vários fornecedores de telecomunicações para encaminhar chamadas telefónicas e mensagens SMS para autenticação. Se estiver a ver algum dos problemas acima referidos, o utilizador tem uma tentativa de utilização do método pelo menos 5 vezes dentro de 5 minutos e tem as informações do utilizador disponíveis quando contactar o suporte da Microsoft.

## <a name="app-passwords"></a>Palavras-passe para aplicações

Algumas aplicações não-navegador não suportam a autenticação de vários fatores, se um utilizador tiver sido ativado para autenticação multifactor e tentar usar aplicações não-navegadoras, não conseguem autenticar. Uma palavra-passe de aplicação permite que os utilizadores continuem a autenticar

Se impor a autenticação multi-factor através de políticas de acesso condicional e não através de MFA por utilizador, não pode criar senhas de aplicação. As aplicações que utilizam as políticas de Acesso Condicional para controlar o acesso não necessitam de senhas de aplicação.

Se a sua organização for federada para SSO com AD Azure e você vai usar O MFA Azure, então esteja ciente dos seguintes detalhes:

* A palavra-passe da aplicação é verificada pela Azure AD e, portanto, contorna a federação. A Federação só é utilizada na configuração de senhas de aplicação. Para utilizadores federados (SSO), as palavras-passe são armazenadas no ID organizacional. Se o utilizador deixar a empresa, essa informação tem de fluir para id organizacional utilizando o DirSync. A desativação/eliminação da conta pode demorar até três horas a sincronizar, o que atrasa a desativação/eliminação de senhas de aplicação em AD Azure.
* As definições de Controlo de Acesso de Cliente no local não são honradas pela Palavra-passe de Aplicação.
* Não existe nenhuma capacidade de autenticação/auditoria de autenticação no local para senhas de aplicação.
* Certos desenhos arquitetónicos avançados podem exigir a utilização de uma combinação de nomede utilizador organizacional e palavras-passe e palavras-passe de aplicações quando se utiliza uma verificação em duas etapas com os clientes, dependendo do local onde autenticam. Para clientes que autenticam contra uma infraestrutura no local, utilizaria um nome de utilizador organizacional e uma palavra-passe. Para clientes que autenticam contra o Azure AD, utilizaria a palavra-passe da aplicação.
* Por padrão, os utilizadores não podem criar senhas de aplicação. Caso seja necessário permitir que os utilizadores criem palavras-passe de apps, selecione os **utilizadores do Permitir em configurar palavras-passe para iniciar sessão na opção de aplicações não-navegadoras** nas definições do serviço.

## <a name="next-steps"></a>Passos seguintes

[Ativar o reset da palavra-passe de autosserviço para a sua organização](quickstart-sspr.md)

[Ativar a autenticação multi-factor Azure para a sua organização](howto-mfa-getstarted.md)

[Permitir o registo combinado no seu inquilino](howto-registration-mfa-sspr-combined.md)

[Documentação do método de autenticação do utilizador final](https://aka.ms/securityinfoguide)
