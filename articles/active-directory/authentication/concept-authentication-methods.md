---
title: Métodos de autenticação-Azure Active Directory
description: Métodos de autenticação disponíveis no Azure AD para MFA e SSPR
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b0c91357e5ab15b88c92b04fd0896b989e83953
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051440"
---
# <a name="what-are-authentication-methods"></a>O que são métodos de autenticação?

Como administrador, escolhendo métodos de autenticação para a autenticação multifator do Azure e redefinição de senha de autoatendimento (SSPR) é recomendável que você exija que os usuários registrem vários métodos de autenticação. Quando um método de autenticação não está disponível para um usuário, ele pode optar por autenticar com outro método.

Os administradores podem definir na política quais métodos de autenticação estão disponíveis para os usuários de SSPR e MFA. Alguns métodos de autenticação podem não estar disponíveis para todos os recursos. Para obter mais informações sobre como configurar suas políticas, consulte os artigos [como distribuir com êxito](howto-sspr-deployment.md) a redefinição de senha de autoatendimento e [planejar uma autenticação multifator do Azure baseada em nuvem](howto-mfa-getstarted.md)

A Microsoft recomenda aos administradores que permitam que os usuários selecionem mais do que o número mínimo necessário de métodos de autenticação, caso eles não tenham acesso a um.

|Método de Autenticação|Utilização|
| --- | --- |
| Palavra-passe | MFA e SSPR |
| Perguntas de segurança | Somente SSPR |
| Endereço de e-mail | Somente SSPR |
| Aplicação do Microsoft Authenticator | MFA e SSPR |
| Token de hardware OATH | Visualização pública para MFA e SSPR |
| SMS | MFA e SSPR |
| Chamada de voz | MFA e SSPR |
| Palavras-passe da aplicação | MFA somente em determinados casos |

![Métodos de autenticação em uso na tela de entrada](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Os tokens de hardware OATH para MFA e SSPR são recursos de visualização pública de Azure Active Directory. Para obter mais informações sobre visualizações, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>Palavra-passe

Sua senha do Azure AD é considerada um método de autenticação. É um método que **não pode ser desabilitado**.

## <a name="security-questions"></a>Perguntas de segurança

As perguntas de segurança estão disponíveis **somente na redefinição de senha de autoatendimento do Azure ad** para contas que não são de administrador.

Se você usar perguntas de segurança, é recomendável usá-las em conjunto com outro método. As perguntas de segurança podem ser menos seguras do que outros métodos, pois algumas pessoas podem saber as respostas para as perguntas de outro usuário.

> [!NOTE]
> As perguntas de segurança são armazenadas de forma privada e segura em um objeto de usuário no diretório e só podem ser respondidas pelos usuários durante o registro. Não há como um administrador ler ou modificar perguntas ou respostas de um usuário.
>

### <a name="predefined-questions"></a>Perguntas predefinidas

* Em qual cidade você atendia ao seu primeiro cônjuge/parceiro?
* Em que cidade seus pais se encontram?
* Em qual cidade seu irmão mais próximo vive?
* Em qual cidade seu pai nasceu?
* Em qual cidade seu primeiro trabalho foi?
* Em qual cidade sua mãe nasceu?
* Em que cidade você estava na 2000 do ano novo?
* Qual é o sobrenome de seu professor favorito na escola alta?
* Qual é o nome de uma faculdade à qual você se aplicou, mas não participou?
* Qual é o nome do local em que você ocupou sua primeira recepção de casamento?
* Qual é o segundo nome do seu pai?
* Qual é sua comida favorita?
* Qual é o nome e sobrenome de seu materna avó?
* Qual é o segundo nome da sua mãe?
* Qual é o mês e o ano de aniversário de seu irmão mais antigo? (por exemplo, novembro de 1985)
* Qual é o segundo nome de seu irmão mais antigo?
* Qual é o nome e sobrenome de seu avô paterno?
* Qual é o segundo nome de seu irmão mais jovem?
* Que escola você participou da sexta série?
* Qual o nome e sobrenome de seu melhor amigo de infância?
* Qual o nome e o sobrenome de seu primeiro e mais importante?
* Qual foi o sobrenome de seu professor de estudante de sua série favorita?
* Qual era a marca e o modelo de seu primeiro carro ou Motorcycle?
* Qual o nome da primeira escola que você participou?
* Qual foi o nome do hospital no qual você nasceu?
* Qual o nome da rua de sua primeira infância doméstica?
* Qual é o nome do seu herói de infância?
* Qual o nome do seu animal de inserida favorito?
* Qual é o nome do seu primeiro animal de estimação?
* Qual é seu apelido de infância?
* Qual era seu esporte favorito na escola alta?
* Qual foi seu primeiro trabalho?
* Quais foram os últimos quatro dígitos de seu número de telefone de infância?
* Quando você era jovem, o que você gostaria de ter quando cresceu?
* Quem é a pessoa mais famosa que você já conheceu?

Todas as perguntas de segurança predefinidas são traduzidas e localizadas no conjunto completo de idiomas do Office 365 com base na localidade do navegador do usuário.

### <a name="custom-security-questions"></a>Perguntas de segurança personalizadas

As perguntas de segurança personalizadas não estão localizadas. Todas as perguntas personalizadas são exibidas no mesmo idioma que são inseridas na interface do usuário administrativo, mesmo que a localidade do navegador do usuário seja diferente. Se precisar de perguntas localizadas, você deverá usar as perguntas predefinidas.

O comprimento máximo de uma pergunta de segurança personalizada é de 200 caracteres.

### <a name="security-question-requirements"></a>Requisitos de pergunta de segurança

* O limite mínimo de caracteres de resposta é de três caracteres.
* O limite máximo de caracteres de resposta é de 40 caracteres.
* Os usuários não podem responder à mesma pergunta mais de uma vez.
* Os usuários não podem fornecer a mesma resposta a mais de uma pergunta.
* Qualquer conjunto de caracteres pode ser usado para definir as perguntas e as respostas, incluindo caracteres Unicode.
* O número de perguntas definidas deve ser maior ou igual ao número de perguntas necessárias para se registrar.

## <a name="email-address"></a>Endereço de e-mail

O endereço de email está disponível **somente na redefinição de senha de autoatendimento do Azure ad**.

A Microsoft recomenda o uso de uma conta de email que não exija a senha do Azure AD do usuário para acessar o.

## <a name="microsoft-authenticator-app"></a>Aplicação do Microsoft Authenticator

O aplicativo Microsoft Authenticator fornece um nível adicional de segurança para sua conta corporativa ou de estudante do Azure AD ou sua conta Microsoft.

A aplicação Microsoft Authenticator está disponível para [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) e [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071).

> [!NOTE]
> Os usuários não terão a opção de registrar seu aplicativo móvel ao se registrar para a redefinição de senha de autoatendimento. Em vez disso, os usuários podem registrar seu [https://aka.ms/mfasetup](https://aka.ms/mfasetup) aplicativo móvel em ou na versão prévia do [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)registro de informações de segurança em.
>

### <a name="notification-through-mobile-app"></a>Notificação através de aplicação móvel

O aplicativo Microsoft Authenticator pode ajudar a impedir o acesso não autorizado a contas e interromper transações fraudulentas enviando uma notificação para o smartphone ou Tablet. Os usuários exibem a notificação e, se for legítima, selecione verificar. Caso contrário, eles poderão selecionar negar.

> [!WARNING]
> Para redefinição de senha de autoatendimento quando apenas um método é necessário para redefinição, o código de verificação é a única opção disponível aos usuários **para garantir o nível mais alto de segurança**.
>
> Quando dois métodos forem necessários, os usuários poderão redefinir usando **o** código de notificação **ou** de verificação, além de quaisquer outros métodos habilitados.
>

Se você habilitar o uso de notificação por meio do aplicativo móvel e do código de verificação do aplicativo móvel, os usuários que registrarem o aplicativo Microsoft Authenticator usando uma notificação poderão usar a notificação e o código para verificar sua identidade.

> [!NOTE]
> Se sua organização tiver funcionários trabalhando ou viajando para a China, a **notificação por meio** do método de aplicativo móvel em **dispositivos Android** não funcionará nesse país. Os métodos alternativos devem ser disponibilizados para esses usuários.

### <a name="verification-code-from-mobile-app"></a>Código de verificação da aplicação móvel

O aplicativo Microsoft Authenticator ou outros aplicativos de terceiros podem ser usados como um token de software para gerar um código de verificação OATH. Depois de inserir seu nome de usuário e senha, insira o código fornecido pelo aplicativo na tela de entrada. O código de verificação fornece uma segunda forma de autenticação.

> [!WARNING]
> Para redefinição de senha de autoatendimento quando apenas um método é necessário para redefinir o código de verificação é a única opção disponível aos usuários **para garantir o nível mais alto de segurança**.
>

Os usuários podem ter uma combinação de até cinco tokens de hardware OATH ou aplicativos autenticadores, como o Microsoft Authenticator aplicativo configurado para uso a qualquer momento.

## <a name="oath-hardware-tokens-public-preview"></a>Tokens de hardware OATH (visualização pública)

OATH é um padrão aberto que especifica como os códigos de senha de uso único (OTP) são gerados. O Azure AD dará suporte ao uso de tokens OATH-TOTP SHA-1 da variedade de 30 segundos ou de 60 segundos. Os clientes podem adquirir esses tokens do fornecedor de sua escolha. As chaves secretas são limitadas a 128 caracteres, que podem não ser compatíveis com todos os tokens. As chaves secretas precisam ser codificadas em Base32.

![Carregando tokens OATH para a folha de tokens OATH do servidor MFA](media/concept-authentication-methods/oath-tokens-azure-ad.png)

Tokens de hardware OATH têm suporte como parte de uma visualização pública. Para obter mais informações sobre visualizações, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Depois que os tokens são adquiridos, eles devem ser carregados em um formato de arquivo CSV (valores separados por vírgula), incluindo o UPN, o número de série, a chave secreta, o intervalo de tempo, o fabricante e o modelo, como mostra o exemplo a seguir.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Certifique-se de incluir a linha de cabeçalho no arquivo CSV, conforme mostrado acima.

Uma vez formatado corretamente como um arquivo CSV, um administrador pode entrar no portal do Azure e navegar até **Azure Active Directory**, **servidor MFA**, tokens **OATH**e carregar o arquivo CSV resultante.

Dependendo do tamanho do arquivo CSV, pode levar alguns minutos para ser processado. Clique no botão **Atualizar** para obter o status atual. Se houver erros no arquivo, você terá a opção de baixar um arquivo CSV listando os erros a serem resolvidos.

Depois que os erros forem resolvidos, o administrador poderá ativar cada chave clicando em **Ativar** para que o token seja ativado e inserindo a OTP exibida no token.

Os usuários podem ter uma combinação de até cinco tokens de hardware OATH ou aplicativos autenticadores, como o Microsoft Authenticator aplicativo configurado para uso a qualquer momento.

## <a name="phone-options"></a>Opções de telefone

### <a name="mobile-phone"></a>Telemóvel

Duas opções estão disponíveis para usuários com telefones celulares.

Se os usuários não desejarem que seu número de telefone celular fique visível no diretório, mas ainda quiser usá-lo para redefinição de senha, os administradores não deverão preenchê-lo no diretório. Os usuários devem preencher seu atributo de **telefone de autenticação** por meio do portal de registro de redefinição de [senha](https://aka.ms/ssprsetup). Os administradores podem ver essas informações no perfil do usuário, mas não são publicadas em outro lugar.

Para funcionar corretamente, os números de telefone devem estar no formato *+ CountryCode PhoneNumber*, por exemplo, + 1 4255551234.

> [!NOTE]
> Precisa haver um espaço entre o código do país e o número de telefone.
>
> A redefinição de senha não dá suporte a extensões de telefone. Mesmo no formato + 1 4255551234X12345, as extensões são removidas antes de a chamada ser colocada.

A Microsoft não garante a entrega de prompt de autenticação multifator baseada em voz ou SMS consistente pelo mesmo número. Ao interesse de nossos usuários, a Microsoft pode adicionar ou remover códigos curtos a qualquer momento à medida que fizermos ajustes de rota para melhorar a entrega do SMS. A Microsoft não oferece suporte a códigos curtos para países/regiões além do Estados Unidos e do Canadá.

#### <a name="text-message"></a>Mensagem SMS

Um SMS é enviado para o número de telefone celular que contém um código de verificação. Insira o código de verificação fornecido na interface de entrada para continuar.

#### <a name="phone-call"></a>Chamada telefónica

Uma chamada de voz automática é feita para o número de telefone que você fornece. Responda à chamada e pressione # no teclado do telefone para autenticar

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamada telefônica não estarão disponíveis para os usuários de MFA e SSPR em locatários gratuitos/de avaliação do Azure AD. As mensagens SMS não são afetadas por essa alteração. A chamada telefônica continuará disponível para os usuários em locatários pagos do Azure AD. Essa alteração afeta apenas os locatários gratuitos/de avaliação do Azure AD.

### <a name="office-phone"></a>Telefone do escritório

Uma chamada de voz automática é feita para o número de telefone que você fornece. Responda à chamada e pressione # no teclado do telefone para autenticar.

Para funcionar corretamente, os números de telefone devem estar no formato *+ CountryCode PhoneNumber*, por exemplo, + 1 4255551234.

O atributo de telefone comercial é gerenciado pelo seu administrador.

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamada telefônica não estarão disponíveis para os usuários de MFA e SSPR em locatários gratuitos/de avaliação do Azure AD. As mensagens SMS não são afetadas por essa alteração. A chamada telefônica continuará disponível para os usuários em locatários pagos do Azure AD. Essa alteração afeta apenas os locatários gratuitos/de avaliação do Azure AD.

> [!NOTE]
> Precisa haver um espaço entre o código do país e o número de telefone.
>
> A redefinição de senha não dá suporte a extensões de telefone. Mesmo no formato + 1 4255551234X12345, as extensões são removidas antes de a chamada ser colocada.

### <a name="troubleshooting-phone-options"></a>Solucionando problemas de opções de telefone

Problemas comuns relacionados a métodos de autenticação usando um número de telefone:

* ID de chamador bloqueada em um único dispositivo
   * Solucionar problemas de dispositivo
* Número de telefone errado, código de país incorreto, número de telefone residencial versus número de telefone comercial
   * Solucionar problemas de objeto de usuário e métodos de autenticação configurados. Verifique se os números de telefone corretos estão registrados.
* PIN inserido incorreto
   * Confirmar que o usuário usou o PIN correto registrado no servidor Azure MFA.
* Chamada encaminhada para a caixa postal
   * Verifique se o usuário tem o telefone ativado e se o serviço está disponível em sua área ou use um método alternativo.
* O utilizador está bloqueado
   * Fazer com que o administrador desbloqueie o usuário na portal do Azure.
* O SMS não está inscrito no dispositivo
   * Faça com que o usuário altere os métodos ou ative o SMS no dispositivo.
* Provedores de telecomunicações com falha (nenhuma entrada de telefone detectada, problemas de tons DTMF ausentes, ID de chamador bloqueada em vários dispositivos ou SMS bloqueado em vários dispositivos)
   * A Microsoft usa vários provedores de telecomunicações para rotear chamadas telefônicas e mensagens SMS para autenticação. Se você estiver vendo qualquer um dos problemas acima, faça com que o usuário tente usar o método pelo menos 5 vezes em 5 minutos e tenha as informações do usuário disponíveis ao entrar em contato com o suporte da Microsoft.

## <a name="app-passwords"></a>Palavras-passe de Aplicação

Determinados aplicativos sem navegador não dão suporte à autenticação multifator, se um usuário tiver sido habilitado para autenticação multifator e tentar usar aplicativos sem navegador, ele não poderá se autenticar. Uma senha de aplicativo permite que os usuários continuem a autenticar

Se você impor a autenticação multifator por meio de políticas de acesso condicional e não pela MFA por usuário, não será possível criar senhas de aplicativo. Os aplicativos que usam políticas de acesso condicional para controlar o acesso não precisam de senhas de aplicativo.

Se sua organização for federada para SSO com o Azure AD e você pretende usar o Azure MFA, lembre-se dos seguintes detalhes:

* A senha do aplicativo é verificada pelo Azure AD e, portanto, ignora a Federação. A Federação é usada somente ao configurar senhas de aplicativo. Para usuários federados (SSO), as senhas são armazenadas na ID organizacional. Se o usuário sair da empresa, essas informações precisarão fluir para a ID organizacional usando o DirSync. A desabilitação/exclusão da conta pode levar até três horas para ser sincronizada, o que atrasa a desabilitação/exclusão de senhas de aplicativo no Azure AD.
* As definições de Controlo de Acesso de Cliente no local não são honradas pela Palavra-passe de Aplicação.
* Nenhum recurso de registro/auditoria de autenticação local está disponível para senhas de aplicativo.
* Determinados designs arquitetônicos avançados podem exigir o uso de uma combinação de nome de usuário e senhas organizacionais e senhas de aplicativo ao usar a verificação em duas etapas com clientes, dependendo de onde eles se autenticam. Para clientes que se autenticam em uma infraestrutura local, você usaria um nome de usuário e uma senha da organização. Para clientes que se autenticam no Azure AD, você usaria a senha do aplicativo.
* Por padrão, os usuários não podem criar senhas de aplicativo. Se você precisar permitir que os usuários criem senhas de aplicativo, selecione a **opção permitir que os usuários criem senhas de aplicativo para entrar em aplicativos sem navegador** em configurações de serviço.

## <a name="next-steps"></a>Passos seguintes

[Habilitar a redefinição de senha de autoatendimento para sua organização](quickstart-sspr.md)

[Habilitar a autenticação multifator do Azure para sua organização](howto-mfa-getstarted.md)

[Habilitar o registro combinado em seu locatário](howto-registration-mfa-sspr-combined.md)

[Documentação de configuração do método de autenticação do usuário final](https://aka.ms/securityinfoguide)
