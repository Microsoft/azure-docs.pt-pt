---
title: Palavra-passe de autosserviço reiniciada mergulho profundo - Azure Ative Directory
description: Como funciona o reset da palavra-passe de autosserviço
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa1c2627917bfe386c488470f6a78db4c51f2ec
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92363679"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Como funciona: Reposição personalizada de palavra-passe do AAD

O Azure Ative Directory (Azure AD) redefiniu a palavra-passe de autosserviço (SSPR) dá aos utilizadores a capacidade de alterar ou redefinir a sua palavra-passe, sem qualquer administrador ou envolvimento do balcão de ajuda. Se a conta de um utilizador estiver bloqueada ou se esquecerem da sua palavra-passe, podem seguir as instruções para se desbloquearem e voltarem ao trabalho. Esta capacidade reduz as chamadas de secretária de ajuda e a perda de produtividade quando um utilizador não pode iniciar sômata ao seu dispositivo ou a uma aplicação.

> [!IMPORTANT]
> Este artigo conceptual explica a um administrador como a palavra-passe de autosserviço funciona. Se é um utilizador final já registado para redefinição da palavra-passe de autosserviço e precisa de voltar à sua conta, vá a [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Se a sua equipa de TI não tiver ativado a capacidade de redefinir a sua própria palavra-passe, contacte o seu helpdesk para obter assistência adicional.

## <a name="how-does-the-password-reset-process-work"></a>Como funciona o processo de reset da palavra-passe?

Um utilizador pode reiniciar ou alterar a sua palavra-passe utilizando o [portal SSPR](https://aka.ms/sspr). Devem primeiro ter registado os seus métodos de autenticação pretendidos. Quando um utilizador acede ao portal SSPR, a plataforma Azure considera os seguintes fatores:

* Como deve a página ser localizada?
* A conta de utilizador é válida?
* A que organização pertence o utilizador?
* Onde é gerida a palavra-passe do utilizador?
* O utilizador tem licença para utilizar a funcionalidade?

Quando um utilizador seleciona o link **'Não pode aceder'** a uma aplicação ou página a partir de uma aplicação ou página, ou vai diretamente [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) para, o idioma utilizado no portal SSPR baseia-se nas seguintes opções:

* Por padrão, o local do navegador é utilizado para exibir o SSPR no idioma apropriado. A experiência de reset da palavra-passe está localizada nos mesmos idiomas que o [Microsoft 365 suporta.](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec)
* Se pretender ligar ao SSPR num idioma localizado específico, `?mkt=` apenda-se ao final do URL de reset da palavra-passe juntamente com o local necessário.
    * Por exemplo, para especificar a localidade espanhola *es-us,* use `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .

Depois de o portal SSPR ser apresentado no idioma requerido, o utilizador é solicitado a introduzir um ID do utilizador e passar um captcha. A Azure AD verifica agora que o utilizador é capaz de utilizar a SSPR fazendo as seguintes verificações:

* Verifica se o utilizador tem SSPR ativado e é-lhe atribuída uma licença AZure AD.
  * Se o utilizador não estiver habilitado para sSPR ou não tiver uma licença atribuída, o utilizador é solicitado a contactar o seu administrador para redefinir a sua palavra-passe.
* Verifica se o utilizador tem os métodos de autenticação certos definidos na sua conta de acordo com a política do administrador.
  * Se a apólice necessitar apenas de um método, verifique se o utilizador tem os dados adequados definidos para pelo menos um dos métodos de autenticação ativados pela política do administrador.
    * Se os métodos de autenticação não estiverem configurados, o utilizador é aconselhado a contactar o seu administrador para redefinir a sua palavra-passe.
  * Se a apólice necessitar de dois métodos, verifique se o utilizador tem os dados adequados definidos para pelo menos dois dos métodos de autenticação ativados pela política do administrador.
    * Se os métodos de autenticação não estiverem configurados, o utilizador é aconselhado a contactar o seu administrador para redefinir a sua palavra-passe.
  * Se um papel de administrador Azure for atribuído ao utilizador, então a forte política de senha de dois portões é aplicada. Para obter mais informações, consulte [as diferenças de política de reposição do administrador](concept-sspr-policy.md#administrator-reset-policy-differences).
* Verifique se a palavra-passe do utilizador é gerida no local, como se o inquilino da AD AZure está a utilizar a autenticação federada, pass-through ou a sincronização de hash de palavra-passe:
  * Se a gravação do SSPR estiver configurada e a palavra-passe do utilizador for gerida no local, o utilizador pode proceder à autenticação e reposição da sua palavra-passe.
  * Se a gravação do SSPR não for implementada e a palavra-passe do utilizador for gerida no local, o utilizador é solicitado a contactar o seu administrador para redefinir a sua palavra-passe.

Se todas as verificações anteriores forem concluídas com sucesso, o utilizador é guiado através do processo para reiniciar ou alterar a sua palavra-passe.

> [!NOTE]
> A SSPR pode enviar notificações por e-mail aos utilizadores como parte do processo de reset da palavra-passe. Estes e-mails são enviados usando o serviço de retransmissão SMTP, que opera em modo ativo em várias regiões.
>
> Os serviços de retransmissão SMTP recebem e processam o corpo de e-mail, mas não o armazenem. O corpo do e-mail SSPR que pode potencialmente conter informações fornecidas pelo cliente não está armazenado nos registos de serviço de retransmissão SMTP. Os registos contêm apenas metadados de protocolo.

Para começar com a SSPR, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Permitir o reset da palavra-passe de autosserviço (SSPR)](tutorial-enable-sspr.md)

## <a name="registration-options"></a>Opções de inscrição

Antes de os utilizadores poderem redefinir ou alterar a sua palavra-passe utilizando o SSPR, devem registar-se e os métodos de autenticação a utilizar. Como mencionado na secção anterior, um utilizador deve estar registado para SSPR, e ter uma licença adequada aplicada.

### <a name="require-users-to-register-when-they-sign-in"></a>Exigir que os utilizadores se registem quando se inscrevem

Pode ativar a opção de exigir que um utilizador preencha o registo SSPR se iniciar súm em quaisquer aplicações usando Azure AD. Este fluxo de trabalho inclui as seguintes aplicações:

* Microsoft 365
* Portal do Azure
* Painel de Acesso
* Candidaturas federadas
* Aplicações personalizadas usando Azure AD

Quando não necessita de registo, os utilizadores não são solicitados durante a entrada, mas podem registar-se manualmente. Os utilizadores podem visitar [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) ou selecionar o Registo para o link de reset de **palavra-passe** no **separador Perfil** no Painel de Acesso.

![Opções de inscrição para SSPR no portal Azure][Registration]

> [!NOTE]
> Os utilizadores podem dispensar o portal de registo SSPR selecionando **cancelar** ou fechando a janela. No entanto, são solicitados a registarem-se sempre que se inscrevem até completarem o registo.
>
> Esta interrupção para se registar para SSPR não quebra a ligação do utilizador se já estiverem assinadas.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Definir o número de dias antes de os utilizadores serem convidados a reconfirmar as suas informações de autenticação

Para garantir que os métodos de autenticação estão corretos quando são necessários para redefinir ou alterar a sua palavra-passe, pode exigir que os utilizadores confirmem as suas informações registadas após um determinado período de tempo. Esta opção só está disponível se permitir que os **utilizadores do Requererem** o registo ao assinarem na opção.

Os valores válidos para solicitar ao utilizador que confirme os seus métodos registados são de *0* a *730* dias. Definir este valor para *0* significa que os utilizadores nunca são convidados a confirmar as suas informações de autenticação.

## <a name="authentication-methods"></a>Métodos de autenticação

Quando um utilizador está ativado para SSPR, deve registar pelo menos um método de autenticação. Recomendamos vivamente que escolha dois ou mais métodos de autenticação para que os seus utilizadores tenham mais flexibilidade caso não consigam aceder a um método quando precisam. Para mais informações, consulte [o que são métodos de autenticação?](concept-authentication-methods.md)

Estão disponíveis os seguintes métodos de autenticação para a SSPR:

* Notificação da aplicação móvel
* Código da aplicação móvel
* E-mail
* Número de telemóvel
* Telefone do escritório
* Perguntas de segurança

Os utilizadores só podem redefinir a sua palavra-passe se tiverem registado um método de autenticação que o administrador ativou.

> [!WARNING]
> As funções de *administrador* Azure atribuídas são obrigadas a utilizar métodos definidos na secção [Desemfinindo as diferenças de política](concept-sspr-policy.md#administrator-reset-policy-differences)do administrador .

![Seleção de métodos de autenticação no portal Azure][Authentication]

### <a name="number-of-authentication-methods-required"></a>Número de métodos de autenticação necessários

Pode configurar o número dos métodos de autenticação disponíveis que um utilizador deve fornecer para redefinir ou desbloquear a sua palavra-passe. Este valor pode ser definido para *um* ou *dois*.

Os utilizadores podem, e devem, registar vários métodos de autenticação. Mais uma vez, é altamente recomendado que os utilizadores registem dois ou mais métodos de autenticação para que tenham mais flexibilidade no caso de não conseguirem aceder a um método quando precisam.

Se um utilizador não tiver o número mínimo de métodos necessários registados quando tenta utilizar o SSPR, vê uma página de erro que os direciona a solicitar que um administrador reinicie a sua palavra-passe. Tenha cuidado se aumentar o número de métodos necessários de um para dois se tiver utilizadores existentes registados para SSPR e eles não puderem usar a funcionalidade. Para obter mais informações, consulte a secção seguinte para [alterar métodos de autenticação.](#change-authentication-methods)

#### <a name="mobile-app-and-sspr"></a>Aplicativo móvel e SSPR

Ao utilizar uma aplicação móvel como método para reset de palavra-passe, como a aplicação Microsoft Authenticator, aplicam-se as seguintes considerações:

* Quando os administradores exigem que um método seja utilizado para redefinir uma palavra-passe, o código de verificação é a única opção disponível.
* Quando os administradores exigem que sejam utilizados dois métodos para redefinir uma palavra-passe, os utilizadores podem utilizar o código de notificação **ou** verificação, além de quaisquer outros métodos habilitados.

| Número de métodos necessários para repor | Um | Dois |
| :---: | :---: | :---: |
| Funcionalidades de aplicativo móvel disponíveis | Código | Código ou Notificação |

Os utilizadores não têm a opção de registar a sua aplicação móvel ao registarem-se para a redefinição da palavra-passe de autosserviço a partir de [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Os utilizadores podem registar a sua aplicação móvel [https://aka.ms/mfasetup](https://aka.ms/mfasetup) em, ou no registo combinado de informações de segurança em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> A aplicação Authenticator não pode ser selecionada como o único método de autenticação quando é necessário apenas um método. Da mesma forma, a aplicação Authenticator e apenas um método adicional não podem ser selecionados quando se requer dois métodos.
>
> Ao configurar as políticas SSPR que incluem a aplicação Authenticator como método, pelo menos um método adicional deve ser selecionado quando um método é necessário, e pelo menos dois métodos adicionais devem ser selecionados quando são necessários configurar dois métodos.
>
> Este requisito deve-se ao facto de a experiência de registo SSPR atual não incluir a opção de registo da aplicação autenticadora. A opção de registo da aplicação autenticadora está incluída na nova [experiência de registo combinado.](./concept-registration-mfa-sspr-combined.md)
>
> Permitir políticas que utilizem apenas a aplicação Authenticator (quando é necessário um método), ou a aplicação Authenticator e apenas um método adicional (quando são necessários dois métodos), pode levar a que os utilizadores sejam impedidos de se registarem para SSPR até estarem configurados para utilizarem a nova experiência de registo combinado.

### <a name="change-authentication-methods"></a>Alterar métodos de autenticação

Se começar com uma política que tenha apenas um método de autenticação necessário para reiniciar ou desbloquear registado e alterar isso para dois métodos, o que acontece?

| Número de métodos registados | Número de métodos necessários | Resultado |
| :---: | :---: | :---: |
| 1 ou mais | 1 | **Capaz** de reiniciar ou desbloquear |
| 1 | 2 | **Incapaz de** reiniciar ou desbloquear |
| 2 ou mais | 2 | **Capaz** de reiniciar ou desbloquear |

A alteração dos métodos de autenticação disponíveis também pode causar problemas aos utilizadores. Se alterar os tipos de métodos de autenticação que um utilizador pode utilizar, poderá inadvertidamente impedir que os utilizadores possam utilizar sSPR se não tiverem a quantidade mínima de dados disponíveis.

Considere o seguinte cenário de exemplo:

1. A política original está configurada com dois métodos de autenticação necessários. Usa apenas o número de telefone do escritório e as questões de segurança.
1. O administrador altera a política para deixar de usar as questões de segurança, mas permite o uso de um telemóvel e de um e-mail alternativo.
1. Os utilizadores sem o telemóvel ou os campos de e-mail alternativos agora não podem redefinir as suas palavras-passe.

## <a name="notifications"></a>Notificações

Para melhorar a consciencialização sobre os eventos de palavra-passe, a SSPR permite-lhe configurar notificações tanto para os utilizadores como para administradores de identidade.

### <a name="notify-users-on-password-resets"></a>Notificar os utilizadores sobre reposições de palavras-passe

Se esta opção for definida como **Sim,** os utilizadores que reporem a sua palavra-passe recebem um e-mail notificando-os de que a sua palavra-passe foi alterada. O e-mail é enviado através do portal SSPR para os seus endereços de e-mail primários e alternativos que são armazenados em Azure AD. Ninguém mais é notificado do evento de reset.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notifique todos os administradores quando outros administradores redefinirem as suas palavras-passe

Se esta opção for definida como **Sim,** todos os outros administradores da Azure recebem um e-mail para o seu endereço de e-mail primário armazenado em Azure AD. O e-mail notifica-os de que outro administrador alterou a sua palavra-passe utilizando o SSPR.

Considere o seguinte cenário de exemplo:

* Há quatro administradores num ambiente.
* O administrador *A* reinicia a sua palavra-passe utilizando o SSPR.
* Os administradores *B,* *C*e *D* recebem um e-mail alertando-os da redefinição da palavra-passe.

## <a name="on-premises-integration"></a>Integração no local

Se tiver um ambiente híbrido, pode configurar o Azure AD Connect para escrever eventos de mudança de palavra-passe de Azure AD para um diretório no local.

![A validação da gravação de passwords está ativada e funciona][Writeback]

A AZure AD verifica a sua conectividade híbrida atual e fornece uma das seguintes mensagens no portal Azure:

* O seu cliente no local está a funcionar.
* O Azure AD está online e está ligado ao seu cliente de writeback no local. No entanto, parece que a versão instalada do Azure AD Connect está desatualizada. Considere [atualizar o Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) para garantir que tem as mais recentes funcionalidades de conectividade e correções importantes de bugs.
* Infelizmente, não podemos verificar o estado do seu cliente no local porque a versão instalada do Azure AD Connect está desatualizada. [Atualize o Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) para poder verificar o estado da sua ligação.
* Infelizmente, parece que não podemos ligar-nos ao seu cliente no local. [Resolução de problemas Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) para restaurar a ligação.
* Infelizmente, não podemos ligar-nos ao seu cliente de writeback no local porque a gravação de passwords não foi devidamente configurada. [Configurar a gravação da palavra-passe](./tutorial-enable-sspr-writeback.md) para restaurar a ligação.
* Infelizmente, parece que não podemos ligar-nos ao seu cliente no local. Isto pode ser devido a problemas temporários do nosso lado. Se o problema persistir, [o Problema Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) para restaurar a ligação.

Para começar com a gravação da SSPR, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Ativar o reset da palavra-passe de autosserviço (SSPR)](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Descreva palavras-passe para o seu diretório no local

Pode ativar a gravação de palavras-passe utilizando o portal Azure. Também pode desativar temporariamente a gravação de palavra-passe sem ter de reconfigurar o Azure AD Connect.

* Se a opção estiver definida como **Sim,** então a gravação está ativada. Os utilizadores federados, de autenticação pass-through ou sincronizados com hash de palavra-passe são capazes de redefinir as suas palavras-passe.
* Se a opção estiver definida como **Nº,** a gravação é desativada. A autenticação federada, pass-through ou os utilizadores sincronizados com hash de palavra-passe não conseguem redefinir as suas palavras-passe.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Permitir que os utilizadores desbloqueiem contas sem redefinir a sua palavra-passe

Por padrão, o Azure AD desbloqueia as contas quando executa uma redefinição de palavra-passe. Para proporcionar flexibilidade, pode optar por permitir que os utilizadores desbloqueiem as suas contas no local sem terem de redefinir a sua palavra-passe. Utilize esta definição para separar as duas operações.

* Se definido para **Sim**, os utilizadores têm a opção de redefinir a sua palavra-passe e desbloquear a conta, ou desbloquear a sua conta sem ter de redefinir a palavra-passe.
* Se definido para **Nº**, os utilizadores só poderão executar uma redefinição combinada de passwords e o funcionamento do desbloqueio da conta.

### <a name="on-premises-active-directory-password-filters"></a>Filtros de senha de direção ativa no local

A SSPR executa o equivalente a uma redefinição de senha iniciada por administração no Ative Directory. Se utilizar um filtro de senha de terceiros para impor regras de senha personalizadas e necessitar que este filtro de palavra-passe seja verificado durante o reset da palavra-passe Ad Ad, certifique-se de que a solução de filtro de senha de terceiros está configurada para ser aplicada no cenário de reset da palavra-passe admin. [A proteção da palavra-passe AD AZure para Serviços de Domínio de Diretório Ativo](concept-password-ban-bad-on-premises.md) é suportada por padrão.

## <a name="password-reset-for-b2b-users"></a>Reset de palavra-passe para utilizadores B2B

O reset e a alteração da palavra-passe são totalmente suportados em todas as configurações business-to-business (B2B). O reset da palavra-passe do utilizador B2B é suportado nos seguintes três casos:

* **Utilizadores de uma organização parceira com um inquilino Azure AD existente**: Se a organização com quem se associa tem um inquilino AZure AD existente, respeitamos quaisquer políticas de reset de palavra-passe que estejam habilitadas nesse inquilino. Para que a palavra-passe seja reposta para funcionar, a organização parceira apenas precisa de se certificar de que o Azure AD SSPR está ativado. Não existe qualquer custo adicional para os clientes microsoft 365.
* **Utilizadores que se inscrevem através** de inscrição de self-service: Se a organização com quem se associa usou a função [de inscrição de self-service](../enterprise-users/directory-self-service-signup.md) para entrar num inquilino, deixamos que repusessem a palavra-passe com o e-mail que registaram.
* **Utilizadores B2B**: Quaisquer novos utilizadores B2B criados através da utilização das novas [capacidades Azure AD B2B](../external-identities/what-is-b2b.md) também podem redefinir as suas palavras-passe com o e-mail que registaram durante o processo de convite.

Para testar este cenário, vá a https://passwordreset.microsoftonline.com um destes utilizadores parceiros. Se tiverem um e-mail alternativo ou um e-mail de autenticação definido, o reset da palavra-passe funciona como esperado.

> [!NOTE]
> As contas da Microsoft a quem foi concedido acesso ao seu inquilino Azure AD, como as de Hotmail.com, Outlook.com ou outros endereços de e-mail pessoais, não são capazes de usar Azure AD SSPR. Precisam de redefinir a sua palavra-passe utilizando as informações encontradas no [When you can't't iniciar sôm no seu](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo de conta Microsoft.

## <a name="next-steps"></a>Passos seguintes

Para começar com a SSPR, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Permitir o reset da palavra-passe de autosserviço (SSPR)](tutorial-enable-sspr.md)

Os artigos seguintes disponibilizam informações adicionais relativamente à reposição de palavras-passe através do Azure AD:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Métodos de autenticação do Azure AD disponíveis e a quantidade necessária"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "Configurar opções de registo SSPR no portal Azure"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Integração no local para a SSPR no portal Azure"