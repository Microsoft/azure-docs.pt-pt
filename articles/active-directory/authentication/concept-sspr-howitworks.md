---
title: Palavra-passe de autosserviço reiniciada mergulho profundo - Azure Ative Directory
description: Como funciona o reset da palavra-passe de autosserviço
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b19c80378aa40a7f791a3eb61130b013217ddee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74848583"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Como funciona: Azure AD autosserviço palavra-passe reposto

Como funciona o reset da palavra-passe de autosserviço (SSPR) ? O que significa esta opção na interface? Continuar a ler Para saber mais sobre a Azure Ative Directory (Azure AD) SSPR.

## <a name="how-does-the-password-reset-portal-work"></a>Como funciona o portal de reset da palavra-passe?

Quando um utilizador vai ao portal de reset da palavra-passe, é iniciado um fluxo de trabalho para determinar:

   * Como deve a página ser localizada?
   * A conta de utilizador é válida?
   * A que organização pertence o utilizador?
   * Onde é gerida a palavra-passe do utilizador?
   * O utilizador tem licença para utilizar a funcionalidade?

Leia os seguintes passos para saber sobre a lógica por trás da página de reset da palavra-passe:

1. O utilizador seleciona o link **'Não pode aceder'** à sua conta ou vai diretamente para [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) .
   * Com base no local do navegador, a experiência é renderizada no idioma apropriado. A experiência de reset da palavra-passe está localizada nas mesmas línguas que o Office 365 suporta.
   * Para ver o portal de reset da palavra-passe num apêndice de idioma localizado diferente "?mkt=" até ao final do URL de reset da palavra-passe com o exemplo que segue a posição para o espanhol [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .
2. O utilizador introduz um ID do utilizador e passa uma captcha.
3. A Azure AD verifica se o utilizador é capaz de utilizar esta funcionalidade fazendo as seguintes verificações:
   * Verifica se o utilizador tem esta funcionalidade ativada e tem uma licença AD Azure atribuída.
     * Se o utilizador não tiver esta funcionalidade ativada ou tiver uma licença atribuída, o utilizador é solicitado a contactar o seu administrador para redefinir a sua palavra-passe.
   * Verifica se o utilizador tem os métodos de autenticação certos definidos na sua conta de acordo com a política do administrador.
     * Se a apólice necessitar apenas de um método, então assegura que o utilizador tem os dados adequados definidos para pelo menos um dos métodos de autenticação ativados pela política do administrador.
       * Se os métodos de autenticação não estiverem configurados, o utilizador é aconselhado a contactar o seu administrador para redefinir a sua palavra-passe.
     * Se a apólice necessitar de dois métodos, então assegura que o utilizador tem os dados adequados definidos para pelo menos dois dos métodos de autenticação ativados pela política do administrador.
       * Se os métodos de autenticação não estiverem configurados, o utilizador é aconselhado a contactar o seu administrador para redefinir a sua palavra-passe.
     * Se um papel de administrador Azure for atribuído ao utilizador, então a forte política de senha de dois portões é aplicada. Mais informações sobre esta política podem ser encontradas na secção [Desemfinição das diferenças de política](concept-sspr-policy.md#administrator-reset-policy-differences)do Administrador .
   * Verifique se a palavra-passe do utilizador é gerida no local (autenticação federada, de passagem ou sincronizada com hash de palavra-passe).
     * Se o writeback for implementado e a palavra-passe do utilizador for gerida no local, então o utilizador pode proceder à autenticação e reposição da sua palavra-passe.
     * Se o writeback não for implementado e a palavra-passe do utilizador for gerida no local, então o utilizador é solicitado a contactar o seu administrador para redefinir a sua palavra-passe.
4. Se for determinado que o utilizador é capaz de redefinir com sucesso a sua palavra-passe, então o utilizador é guiado através do processo de reset.

## <a name="authentication-methods"></a>Métodos de autenticação

Se o SSPR estiver ativado, deve selecionar pelo menos uma das seguintes opções para os métodos de autenticação. Às vezes ouve-se estas opções referidas como "portões". Recomendamos vivamente que **escolha dois ou mais métodos de autenticação** para que os seus utilizadores tenham mais flexibilidade caso não possam aceder a um quando precisam. Detalhes adicionais sobre os métodos listados abaixo podem ser encontrados no artigo [Quais são os métodos de autenticação?](concept-authentication-methods.md)

* Notificação de aplicação móvel
* Código da aplicação móvel
* E-mail
* Número de telemóvel
* Telefone do escritório
* Perguntas de segurança

Os utilizadores só podem redefinir a sua palavra-passe se tiverem dados presentes nos métodos de autenticação que o administrador ativou.

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamadas telefónicas não estarão disponíveis para os utilizadores de MFA e SSPR em inquilinos gratuitos/experimentais da Azure AD. As mensagens SMS não são afetadas por esta alteração. O telefonema continuará disponível para os utilizadores dos inquilinos pagos da AD Azure. Esta alteração só afeta inquilinos gratuitos/experimentais da Azure AD.

> [!WARNING]
> As funções de Administrador Azure atribuídas serão obrigadas a utilizar métodos tal como definidos na secção [As diferenças de política de reposição do administrador](concept-sspr-policy.md#administrator-reset-policy-differences).

![Seleção de métodos de autenticação no portal Azure][Authentication]

### <a name="number-of-authentication-methods-required"></a>Número de métodos de autenticação necessários

Esta opção determina o número mínimo dos métodos de autenticação disponíveis ou os portões que um utilizador deve passar para reiniciar ou desbloquear a sua palavra-passe. Pode ser definido para um ou dois.

Os utilizadores podem optar por fornecer mais métodos de autenticação se o administrador permitir esse método de autenticação.

Se um utilizador não tiver os métodos mínimos exigidos registados, vê uma página de erro que os direciona a solicitar que um administrador reinicie a sua palavra-passe.

#### <a name="mobile-app-and-sspr"></a>Aplicativo móvel e SSPR

Ao utilizar uma aplicação móvel, como a aplicação Microsoft Authenticator, como um método para reiniciar a palavra-passe, deve estar ciente das seguintes ressalvas:

* Quando os administradores exigem que um método seja utilizado para redefinir uma palavra-passe, o código de verificação é a única opção disponível.
* Quando os administradores exigem que sejam utilizados dois métodos para redefinir uma palavra-passe, os utilizadores podem utilizar o código de notificação **ou** de verificação de **qualquer** outro método ativado.

| Número de métodos necessários para repor | Um | Dois |
| :---: | :---: | :---: |
| Funcionalidades de aplicativo móvel disponíveis | Código | Código ou Notificação |

Os utilizadores não têm a opção de registar a sua aplicação móvel ao registarem-se para redefinição da palavra-passe de autosserviço a partir de [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Os utilizadores podem registar a sua aplicação móvel [https://aka.ms/mfasetup](https://aka.ms/mfasetup) em, ou na nova pré-visualização do registo de informações de segurança em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!WARNING]
> Tem de ativar o [registo convergente para reinicialização da palavra-passe de autosserviço e a autenticação multi-factor Azure (pré-visualização pública)](concept-registration-mfa-sspr-converged.md) antes que os utilizadores possam aceder à nova experiência em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> A aplicação autenticadora não pode ser selecionada como o único método de autenticação ao configurar uma política de 1 portão. Da mesma forma, a aplicação autenticadora e apenas um método adicional não podem ser selecionados ao configurar uma política de 2 portas.
> Em seguida, ao configurar as políticas SSPR que incluem a aplicação do autenticador como método, deve ser selecionado pelo menos um método adicional ao configurar uma política de 1-porta, e pelo menos dois métodos adicionais devem ser selecionados ao configurar uma política de 2 portas.
> A razão para este requisito é porque a experiência de registo SSPR atual não inclui a opção de registar a app autenticadora. A opção de registo da aplicação autenticadora está incluída no novo [registo converged para reset de senha de autosserviço e autenticação multi-factor Azure (pré-visualização pública)](concept-registration-mfa-sspr-converged.md).
> Permitir políticas que utilizem apenas a aplicação autenticadora (para políticas de 1 porta), ou a aplicação autenticadora e apenas um método adicional (para políticas de dois portões), pode levar a que os utilizadores sejam impedidos de se registarem para SSPR até terem sido configurados para utilizar a nova experiência de registo.

### <a name="change-authentication-methods"></a>Alterar métodos de autenticação

Se começar com uma política que tenha apenas um método de autenticação necessário para reiniciar ou desbloquear registado e alterar isso para dois métodos, o que acontece?

| Número de métodos registados | Número de métodos necessários | Resultado |
| :---: | :---: | :---: |
| 1 ou mais | 1 | **Capaz** de reiniciar ou desbloquear |
| 1 | 2 | **Incapaz de** reiniciar ou desbloquear |
| 2 ou mais | 2 | **Capaz** de reiniciar ou desbloquear |

Se alterar os tipos de métodos de autenticação que um utilizador pode utilizar, poderá inadvertidamente impedir que os utilizadores possam utilizar sSPR se não tiverem a quantidade mínima de dados disponíveis.

Exemplo:
1. A política original está configurada com dois métodos de autenticação necessários. Usa apenas o número de telefone do escritório e as questões de segurança. 
2. O administrador altera a política para deixar de usar as questões de segurança, mas permite o uso de um telemóvel e de um e-mail alternativo.
3. Os utilizadores sem o telemóvel ou campos de e-mail alternativos povoados não podem redefinir as suas palavras-passe.

## <a name="registration"></a>Registo

### <a name="require-users-to-register-when-they-sign-in"></a>Exigir que os utilizadores se registem quando se inscrevem

Ativar esta opção requer que um utilizador preencha o registo de reset da palavra-passe se iniciar súms em quaisquer aplicações usando Azure AD. Este fluxo de trabalho inclui as seguintes aplicações:

* Office 365
* Portal do Azure
* Painel de Acesso
* Candidaturas federadas
* Aplicações personalizadas usando Azure AD

Ao necessitar de registo, os utilizadores podem registar-se manualmente. Podem visitar [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) ou selecionar o Registo para o link de reset de **palavra-passe** no **separador Perfil** no Painel de Acesso.

> [!NOTE]
> Os utilizadores podem descartar o portal de registo de redefinição de palavra-passe selecionando **cancelar** ou fechando a janela. Mas são solicitados a registarem-se sempre que se inscrevem até completarem o seu registo.
>
> Esta interrupção não quebra a ligação do utilizador se já estiverem assinadas.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Definir o número de dias antes de os utilizadores serem convidados a reconfirmar as suas informações de autenticação

Esta opção determina o período de tempo entre a definição e a reconfirmação das informações de autenticação e só está disponível se permitir que os **utilizadores do Requera se registem ao assinar em** opção.

Os valores válidos são de 0 a 730 dias, com "0" o que significa que os utilizadores nunca são convidados a reconfirmar as suas informações de autenticação.

## <a name="notifications"></a>Notificações

### <a name="notify-users-on-password-resets"></a>Notificar os utilizadores sobre reposições de palavras-passe

Se esta opção for definida como **Sim,** os utilizadores que reporem a sua palavra-passe recebem um e-mail notificando-os de que a sua palavra-passe foi alterada. O e-mail é enviado através do portal SSPR para os seus endereços de e-mail primários e alternativos que estão em arquivo no Azure AD. Ninguém mais é notificado do evento de reset.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notifique todos os administradores quando outros administradores redefinirem as suas palavras-passe

Se esta opção estiver definida como **Sim,** *todos os administradores* recebem um e-mail para o seu endereço de e-mail principal no ficheiro AZure AD. O e-mail notifica-os de que outro administrador alterou a sua palavra-passe utilizando o SSPR.

Exemplo: Há quatro administradores num ambiente. O administrador A reinicia a sua palavra-passe utilizando o SSPR. Os administradores B, C e D recebem um e-mail alertando-os da redefinição da palavra-passe.

## <a name="on-premises-integration"></a>Integração no local

Se instalar, configurar e ativar o Azure AD Connect, tem as seguintes opções adicionais para integrações no local. Se estas opções estiverem acinzentadas, a reversão não foi devidamente configurada. Para obter mais informações, consulte configurar a gravação [da palavra-passe](howto-sspr-writeback.md).

![A validação da gravação de passwords está ativada e funciona][Writeback]

Esta página fornece-lhe um estado rápido do cliente de writeback no local, uma das seguintes mensagens é exibida com base na configuração atual:

* O seu cliente de writeback no local está a funcionar.
* O Azure AD está online e está ligado ao seu cliente de writeback no local. No entanto, parece que a versão instalada do Azure AD Connect está desatualizada. Considere [atualizar o Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) para garantir que tem as mais recentes funcionalidades de conectividade e correções importantes de bugs.
* Infelizmente, não podemos verificar o estado do seu cliente no local porque a versão instalada do Azure AD Connect está desatualizada. [Atualize o Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) para poder verificar o estado da sua ligação.
* Infelizmente, parece que não podemos ligar-nos ao seu cliente no local. [Resolução de problemas Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) para restaurar a ligação.
* Infelizmente, não podemos ligar-nos ao seu cliente de writeback no local porque a gravação de passwords não foi devidamente configurada. [Configurar a gravação da palavra-passe](howto-sspr-writeback.md) para restaurar a ligação.
* Infelizmente, parece que não podemos ligar-nos ao seu cliente no local. Isto pode ser devido a problemas temporários do nosso lado. Se o problema persistir, [o Problema Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) para restaurar a ligação.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Descreva palavras-passe para o seu diretório no local

Este controlo determina se a gravação de palavras-passe está ativada para este diretório. Se o recuo estiver ligado, indica o estado do serviço de reversão no local. Este controlo é útil se pretender desativar temporariamente a gravação de palavra-passe sem ter de reconfigurar o Azure AD Connect.

* Se o interruptor estiver definido para **Sim,** então o writeback está ativado e os utilizadores federados, de autenticação pass-through ou de hash de palavra-passe são capazes de redefinir as suas palavras-passe.
* Se o interruptor estiver definido para **Nº,** então o writeback é desativado e os utilizadores federados, de autenticação pass-through ou de hash de palavra-passe não são capazes de redefinir as suas palavras-passe.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Permitir que os utilizadores desbloqueiem contas sem redefinir a sua palavra-passe

Este controlo designa se os utilizadores que visitam o portal de reset de palavra-passe devem ter a opção de desbloquear as suas contas de Ative Directory no local sem terem de redefinir a sua palavra-passe. Por padrão, o Azure AD desbloqueia as contas quando executa uma redefinição de palavra-passe. Usa esta definição para separar as duas operações.

* Se definido para **Sim**, então os utilizadores têm a opção de redefinir a sua palavra-passe e desbloquear a conta, ou de desbloquear a sua conta sem ter de redefinir a palavra-passe.
* Se estiver definido para **Nº**, os utilizadores só poderão executar uma redefinição combinada de passwords e o funcionamento do desbloqueio da conta.

### <a name="on-premises-active-directory-password-filters"></a>Filtros de senha de direção ativa no local

O reset da palavra-passe de autosserviço AD Azure executa o equivalente a um reset de palavra-passe iniciado por administração no Ative Directory. Se estiver a utilizar um filtro de senha de terceiros para impor regras de senha personalizadas, e necessitar que este filtro de palavra-passe seja verificado durante o reset da palavra-passe AD AD, certifique-se de que a solução de filtro de senha de terceiros está configurada para ser aplicada no cenário de reset da palavra-passe admin. [A proteção de senha AD do Windows Server Ative Directory](concept-password-ban-bad-on-premises.md) é suportada por padrão.

## <a name="password-reset-for-b2b-users"></a>Reset de palavra-passe para utilizadores B2B

O reset e a alteração da palavra-passe são totalmente suportados em todas as configurações business-to-business (B2B). O reset da palavra-passe do utilizador B2B é suportado nos seguintes três casos:

* **Utilizadores de uma organização parceira com um inquilino Azure AD existente**: Se a organização com quem está em parceria tem um inquilino AZure AD existente, *respeitamos quaisquer políticas de reset de palavra-passe que estejam habilitadas nesse inquilino.* Para que a palavra-passe seja reposta para funcionar, a organização parceira apenas precisa de se certificar de que o Azure AD SSPR está ativado. Não existe qualquer custo adicional para os clientes do Office 365, e pode ser ativado seguindo os passos no nosso Get start com o guia [de gestão de passwords.](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords)
* **Utilizadores que se inscrevem através** de inscrição de self-service: Se a organização com quem está em parceria usou a função [de inscrição de autosserviço](../users-groups-roles/directory-self-service-signup.md) para entrar num inquilino, deixamos-os redefinir a palavra-passe com o e-mail que registaram.
* **Utilizadores B2B**: Quaisquer novos utilizadores B2B criados através da utilização das novas [capacidades Azure AD B2B](../active-directory-b2b-what-is-azure-ad-b2b.md) também poderão redefinir as suas palavras-passe com o e-mail que registaram durante o processo de convite.

Para testar este cenário, vá a https://passwordreset.microsoftonline.com um destes utilizadores parceiros. Se tiverem um e-mail alternativo ou um e-mail de autenticação definido, o reset da palavra-passe funciona como esperado.

> [!NOTE]
> As contas da Microsoft a quem foi concedido acesso ao seu inquilino Azure AD, como as de Hotmail.com, Outlook.com ou outros endereços de e-mail pessoais, não são capazes de usar Azure AD SSPR. Precisam de redefinir a sua palavra-passe utilizando as informações encontradas no [When you can't't iniciar sôm no seu](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo de conta Microsoft.

## <a name="next-steps"></a>Próximos passos

Os artigos seguintes disponibilizam informações adicionais relativamente à reposição de palavras-passe através do Azure AD:

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Redefinir ou alterar a sua palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Acho que algo está partido. Como é que resolvo a SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Métodos de autenticação do Azure AD disponíveis e a quantidade necessária"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "Configuração de writeback de integração de passwords no local e informações de resolução de problemas"
