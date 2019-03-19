---
title: Métodos de autenticação do Azure AD
description: Os métodos de autenticação estão disponíveis no Azure AD para a MFA e o SSPR
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09f61f60a3885fbc58cddc4b46df11014057f54e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999485"
---
# <a name="what-are-authentication-methods"></a>Quais são os métodos de autenticação?

Reposição de palavra-passe self-service (SSPR) do AD e multi-factor Authentication (MFA) do Azure poderão pedir para obter informações adicionais, conhecidas como métodos de autenticação ou informações de segurança, para confirmar que se é que dizem que são quando usando os recursos associados.

Os administradores podem definir na diretiva de quais métodos de autenticação estão disponíveis para utilizadores do SSPR e o MFA. Alguns métodos de autenticação podem não estar disponíveis para todas as funcionalidades.

A Microsoft recomenda administradores assegura aos usuários para selecionar mais do que o número mínimo de métodos de autenticação necessário no caso de não têm acesso a um.

|Método de Autenticação|Utilização|
| --- | --- |
| Palavra-passe | MFA e o SSPR |
| Perguntas de segurança | Apenas a SSPR |
| Endereço de e-mail | Apenas a SSPR |
| Aplicação Microsoft Authenticator | MFA e pré-visualização pública para SSPR |
| Token OATH de Hardware | Pré-visualização pública para a MFA e o SSPR |
| SMS | MFA e o SSPR |
| Chamada de voz | MFA e o SSPR |
| Palavras-passe de aplicações | MFA apenas em determinados casos |

![Métodos de autenticação em uso no ecrã de início de sessão](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Tokens de Hardware OATH para MFA e o SSPR e o Mobile notificação de aplicação ou código de aplicação móvel como métodos de palavra-passe self-service do Azure AD reposição são funcionalidades de pré-visualização pública do Azure Active Directory. Para obter mais informações sobre pré-visualizações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>Palavra-passe

A palavra-passe do Azure AD é considerada um método de autenticação. É o único método que **não é possível desativar**.

## <a name="security-questions"></a>Perguntas de segurança

Perguntas de segurança estão disponíveis **apenas na palavra-passe self-service do Azure AD repor** às contas de não-administrador.

Se utilizar perguntas de segurança, recomendamos que utilize-os em conjunto com outro método. Perguntas de segurança podem ser menos seguras do que outros métodos, porque algumas pessoas podem saber as respostas a perguntas de outro utilizador.

> [!NOTE]
> Perguntas de segurança são armazenadas em privado e segura num objeto de utilizador no diretório e apenas podem ser respondidas por utilizadores durante o registo. Não é possível para um administrador ler ou modificar um utilizador perguntas ou respostas.
>

### <a name="predefined-questions"></a>Perguntas predefinidas

* Em que localidade conheceu o seu primeiro cônjuge/companheiro(a)?
* Em que localidade se conheceram os seus pais?
* Em que localidade mora o seu irmão/irmã mais próximo?
* Em que localidade nasceu o seu pai?
* Em que localidade teve o seu primeiro emprego?
* Em que localidade nasceu a sua mãe?
* Onde estava (localidade) na Passagem de Ano de 2000?
* Qual é o apelido do seu professor/professora preferido na secundária?
* Qual é o nome de uma universidade a que se candidatou, mas que não frequentou?
* Qual é o nome do local onde organizou a sua primeira festa de casamento?
* Qual é o segundo nome do seu pai?
* Qual é o seu prato preferido?
* Qual é o primeiro e último nome da sua avó materna?
* Qual é o segundo nome da sua mãe?
* O que é o mês de nascimento e ano de seu irmão mais antigo? (por exemplo, de Novembro de 1985)
* Qual é o segundo nome do seu irmão/irmã mais velho?
* Qual é o primeiro e último nome do seu avô paterno?
* Qual é o segundo nome do seu irmão/irmã mais novo?
* Qual foi a escola que frequentou durante o sexto ano?
* Qual era o primeiro e último nome do seu melhor amigo de infância?
* Qual era o primeiro e último nome do seu primeiro amor?
* Qual era o apelido do seu professor/professora preferido da primária?
* Qual era a marca e modelo do seu primeiro carro ou motociclo?
* Como se chamava a primeira escola que frequentou?
* Qual era o nome do hospital em que nasceu?
* Qual era o nome da rua da sua primeira casa de infância?
* Qual era o nome do seu herói de infância?
* Qual era o nome do seu peluche preferido?
* Qual era o nome do seu primeiro animal de estimação?
* Qual era a sua alcunha de infância?
* Qual era o seu desporto preferido na secundária?
* Qual foi o seu primeiro emprego?
* Quais eram os quatro últimos algarismos do seu número de telefone de infância?
* Quando era criança, o que queria ser quando crescesse?
* Como se chama a maior celebridade que já conheceu?

Todas as perguntas de segurança predefinidas são traduzidas e localizadas para o conjunto completo de idiomas do Office 365 com base na localidade do navegador do usuário.

### <a name="custom-security-questions"></a>Perguntas de segurança personalizadas

Perguntas de segurança personalizadas não estão localizadas. Todas as perguntas personalizadas são apresentadas no mesmo idioma à medida que são introduzidos na interface do utilizador administrativo, mesmo que a Localidade do navegador do usuário é diferente. Se tiver perguntas localizadas, deve usar as perguntas predefinidas.

O comprimento máximo de uma pergunta de segurança personalizada é 200 carateres.

### <a name="security-question-requirements"></a>Requisitos de pergunta de segurança

* O limite de caracteres de resposta mínimo é de três carateres.
* O limite de caracteres de resposta máximo é de 40 carateres.
* Os utilizadores não podem responder a mesma pergunta mais do que uma vez.
* Os usuários não é possível fornecer a mesma resposta à pergunta mais do que um.
* Qualquer conjunto de caracteres pode ser usado para definir as perguntas e respostas, incluindo os caracteres Unicode.
* O número de perguntas definidas tem de ser maior que ou igual ao número de perguntas que foram necessárias para se registar.

## <a name="email-address"></a>Endereço de e-mail

Endereço de e-mail está disponível **apenas na palavra-passe self-service do Azure AD repor**.

A Microsoft recomenda a utilização de uma conta de e-mail que não requer que a senha do usuário do Azure AD para aceder.

## <a name="microsoft-authenticator-app"></a>Aplicação Microsoft Authenticator

A aplicação Microsoft Authenticator oferece um nível adicional de segurança para o seu trabalho do Azure AD ou a conta escolar ou a sua conta Microsoft.

A aplicação Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) e [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071).

> [!NOTE]
> Os utilizadores não terão a opção para registar a aplicação móvel quando efetuar o registo para a reposição de palavra-passe self-service. Em vez disso, os utilizadores podem registar a sua aplicação móvel [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup) ou a pré-visualização de registo de informações de segurança por [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo).
>

### <a name="notification-through-mobile-app"></a>Notificação através de aplicação móvel

A aplicação Microsoft Authenticator pode ajudar a impedir o acesso não autorizado a contas e parar as transações fraudulentas ao enviar uma notificação para o seu smartphone ou tablet. Os utilizadores visualizar a notificação e se esta for legítima, selecionar verificar. Caso contrário, podem selecionar negar.

> [!WARNING]
> Para as senhas de auto-atendimento repostas quando apenas um método é necessário para a reposição, o código de verificação é a única opção disponível para os usuários **para garantir o nível mais elevado de segurança**.
>
> Quando dois métodos são necessários os utilizadores poderão repor usando **EITHER** notificação **ou** ativada de código de verificação, além de quaisquer outros métodos.
>

Se ativar a utilização de ambos os notificação através do código de verificação da aplicação móvel, os utilizadores que registar a aplicação Microsoft Authenticator usando uma notificação de aplicações móveis e são capazes de utilizar de notificação e o código para verificar a respetiva identidade.

### <a name="verification-code-from-mobile-app"></a>Código de verificação da aplicação móvel

A aplicação Microsoft Authenticator ou outras aplicações de terceiros podem ser utilizadas como um token de software para gerar um código de verificação OATH. Depois de introduzir o seu nome de utilizador e palavra-passe, introduza o código fornecido pela aplicação para o ecrã de início de sessão. O código de verificação fornece uma segunda forma de autenticação.

> [!WARNING]
> Palavra-passe self-service de reposição quando apenas um método é necessário para a reposição do código de verificação é a única opção disponível para os usuários **para garantir o nível mais elevado de segurança**.
>

Os utilizadores podem ter uma combinação de até 5 tokens de hardware OATH ou aplicações de autenticação como a aplicação Microsoft Authenticator configurada para utilização em qualquer altura.

## <a name="oath-hardware-tokens-public-preview"></a>Tokens OATH de hardware (pré-visualização pública)

OATH é um padrão aberto que especifica como uma única vez códigos de palavra-passe (OTP) são gerados. O Azure AD irá suportar a utilização de tokens OATH-TOTP SHA-1 da gama 30 segundos ou 60 segundos. Os clientes podem obter estes tokens do fornecedor de sua preferência. Tenha em atenção que as chaves secretas são limitadas a 128 carateres que podem não ser compatíveis com todos os tokens.

![OATH tokens a carregar para o painel de tokens OATH de servidor de MFA no portal do Azure](media/concept-authentication-methods/oath-tokens-azure-ad.png)

Tokens de hardware OATH estão a ser suportados como parte da pré-visualização pública. Para obter mais informações sobre pré-visualizações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Depois de tokens são adquiridos têm de ser carregados num formato de ficheiro de valores separados por vírgulas (CSV), incluindo o UPN, número de série, chave secreta, intervalo de tempo, fabricante e modelo do exemplo abaixo mostra.

```
upn,serial number,secret key,timeinterval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Certifique-se de que incluir a linha de cabeçalho no seu ficheiro CSV, conforme mostrado acima.

Uma vez corretamente formatado como um ficheiro CSV, um administrador pode, em seguida, inicie sessão no portal do Azure e navegue para **do Azure Active Directory**, **servidor MFA**, **OATH tokens**, e Carregue o ficheiro CSV resultante.

Dependendo do tamanho do ficheiro CSV, pode demorar alguns minutos a processar. Clique nas **atualizar** botão para obter o estado atual. Se existirem quaisquer erros no ficheiro, terá a opção para transferir um ficheiro CSV a listagem de todos os erros para que possa resolver.

Depois de todos os erros tiverem sido preparados, o administrador, em seguida, pode ativar cada chave clicando **Activate** para o token a ser ativado e inserindo a OTP apresentado no token.

Os utilizadores podem ter uma combinação de até 5 tokens de hardware OATH ou aplicações de autenticação como a aplicação Microsoft Authenticator configurada para utilização em qualquer altura.

## <a name="mobile-phone"></a>Número de telemóvel

Duas opções estão disponíveis para os utilizadores com celulares.

Se os usuários não querem seu número de telefone celular seja visível no diretório, mas continuam a querer usá-lo para a reposição de palavra-passe, os administradores não devem preenchê--lo no diretório. Os utilizadores devem preencher seus **telefone de autenticação** através do atributo a [portal de registo de reposição de palavra-passe](https://aka.ms/ssprsetup). Os administradores podem ver estas informações no perfil do usuário, mas não está publicada em outro lugar.

Para funcionarem corretamente, os números de telefone tem de ser no formato *+ indicativo do país PhoneNumber*, por exemplo, + 1 4255551234.

> [!NOTE]
> Deve haver um espaço entre o código de país e o número de telefone.
>
> Reposição de palavra-passe não suporta extensões telefónicas. Até mesmo no formato + 1 4255551234 X 12345, as extensões são removidas antes da chamada é feita.

### <a name="text-message"></a>Mensagem de texto

Um SMS é enviado para o número de telemóvel contendo um código de verificação. Introduza o código de verificação fornecido na interface de início de sessão para continuar.

### <a name="phone-call"></a>Chamada telefónica

Uma chamada de voz automatizada é feita para fornecer o número de telefone. Responder a chamada e prima # no teclado do telefone para autenticar

> [!IMPORTANT]
> A partir de Março de 2019 as opções de chamada telefónica não estarão disponível para os utilizadores do MFA e o SSPR em inquilinos gratuitos/avaliação do Azure AD. Mensagens SMS não são afetadas por esta alteração. Chamada telefónica continuará a estar disponíveis para os utilizadores na pago inquilinos do Azure AD. Esta alteração só afeta o inquilinos gratuitos/avaliação do Azure AD.

## <a name="office-phone"></a>Telefone do escritório

Uma chamada de voz automatizada é feita para fornecer o número de telefone. Responder a chamada e prime # no teclado do telefone para autenticar.

Para funcionarem corretamente, os números de telefone tem de ser no formato *+ indicativo do país PhoneNumber*, por exemplo, + 1 4255551234.

O atributo de telefone do office é gerido pelo seu administrador.

> [!IMPORTANT]
> A partir de Março de 2019 as opções de chamada telefónica não estarão disponível para os utilizadores do MFA e o SSPR em inquilinos gratuitos/avaliação do Azure AD. Mensagens SMS não são afetadas por esta alteração. Chamada telefónica continuará a estar disponíveis para os utilizadores na pago inquilinos do Azure AD. Esta alteração só afeta o inquilinos gratuitos/avaliação do Azure AD.

> [!NOTE]
> Deve haver um espaço entre o código de país e o número de telefone.
>
> Reposição de palavra-passe não suporta extensões telefónicas. Até mesmo no formato + 1 4255551234 X 12345, as extensões são removidas antes da chamada é feita.

## <a name="app-passwords"></a>Palavras-passe de Aplicação

Determinadas aplicações não baseadas no browser não suporta a autenticação multifator, se um utilizador tiver sido ativado para autenticação multifator e a tentativa de utilizar aplicações não baseadas no browser, eles não conseguem autenticar. Uma palavra-passe de aplicação permite que os utilizadores continuam a autenticar

Se impor o multi-factor Authentication através de políticas de acesso condicional e não através de MFA por utilizador, não é possível criar palavras-passe de aplicação. Aplicações que utilizam políticas de acesso condicional para controlar o acesso não é necessário palavras-passe de aplicação.

Se sua organização estiver federada para SSO com o Azure AD e vai estar a utilizar o MFA do Azure, em seguida, tenha em atenção os seguintes detalhes:

* A palavra-passe de aplicação é verificada pelo Azure AD e, por conseguinte, ignora o Federação. A Federação apenas é utilizada quando configurar as palavras-passe de aplicação. Para os utilizadores federados (SSO), as senhas são armazenadas no ID organizacional. Se o utilizador sai da empresa, tem de fluir para o ID organizacional utilizando DirSync essas informações. A desativação/eliminação de conta pode demorar até três horas para sincronização, o que atrasa a desativação/eliminação de palavras-passe de aplicação no Azure AD.
* As definições de Controlo de Acesso de Cliente no local não são honradas pela Palavra-passe de Aplicação.
* Sem capacidade de log/auditoria da autenticação no local está disponível para as palavras-passe de aplicação.
* Determinados designs de arquiteturais avançadas podem requerer a utilização de uma combinação de nome de utilizador organizacional e palavras-passe e palavras-passe de aplicação ao utilizar a verificação de dois passos com clientes, dependendo de onde se autenticar. Para clientes de autenticação em relação a uma infraestrutura no local, usaria um nome de utilizador organizacional e a palavra-passe. Para clientes que se autenticar no Azure AD, tem de utilizar a palavra-passe de aplicação.
* Por predefinição, os utilizadores não é possível criar palavras-passe de aplicação. Se tiver de permitir aos utilizadores criar palavras-passe aplicação, selecione o **permitir que os utilizadores criem palavras-passe de aplicação para se conectar a opção de aplicações não baseadas no browser** em definições de serviço.

## <a name="next-steps"></a>Passos Seguintes

[Ativar a reposição para a sua organização no palavra-passe self-service](quickstart-sspr.md)

[Ativar a multi-factor Authentication para a sua organização](howto-mfa-getstarted.md)

[Ativar o registo combinado no seu inquilino](howto-registration-mfa-sspr-combined.md)

[Documentação de configuração de método de autenticação de utilizador final](https://aka.ms/securityinfoguide)
