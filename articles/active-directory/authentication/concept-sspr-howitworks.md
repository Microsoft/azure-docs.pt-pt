---
title: Palavra-passe self-service reset deep dive - Azure Ative Directory
description: Como funciona o reset da palavra-passe self-service
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74848583"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Como funciona: Reset de senha de autosserviço da Azure AD

Como funciona o reset da palavra-passe self-service (SSPR) ? O que significa esta opção na interface? Continuar a ler Para saber mais sobre o Azure Ative Directory (Azure AD) SSPR.

## <a name="how-does-the-password-reset-portal-work"></a>Como funciona o portal de reset da palavra-passe?

Quando um utilizador vai ao portal de reset da palavra-passe, um fluxo de trabalho é iniciado para determinar:

   * Como deve a página ser localizada?
   * A conta de utilizador é válida?
   * A que organização pertence o utilizador?
   * Onde é gerida a palavra-passe do utilizador?
   * O utilizador tem licença para utilizar a funcionalidade?

Leia os seguintes passos para conhecer a lógica por trás da página de reset da palavra-passe:

1. O utilizador seleciona o **link can't access your account** ou vai diretamente para [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
   * Com base no local do navegador, a experiência é prestada no idioma apropriado. A experiência de reset de palavra-passe está localizada nos mesmos idiomas que o Office 365 suporta.
   * Para ver o portal de redefinição de palavra-passe num apêndice de idioma localizado diferente "?mkt=" [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us)até ao final do URL de reset de palavra-passe com o exemplo que segue a localização para o espanhol .
2. O utilizador introduz uma identificação do utilizador e passa por uma captcha.
3. A Azure AD verifica que o utilizador é capaz de utilizar esta funcionalidade fazendo as seguintes verificações:
   * Verifica se o utilizador tem esta funcionalidade ativada e tem uma licença Azure AD atribuída.
     * Se o utilizador não tiver esta funcionalidade ativada ou tiver uma licença atribuída, o utilizador é solicitado a contactar o seu administrador para redefinir a sua palavra-passe.
   * Verifica se o utilizador tem os métodos de autenticação certos definidos na sua conta de acordo com a política do administrador.
     * Se a apólice necessitar apenas de um método, então garante que o utilizador tem os dados adequados definidos para pelo menos um dos métodos de autenticação ativados pela política do administrador.
       * Se os métodos de autenticação não estiverem configurados, o utilizador é aconselhado a contactar o seu administrador para redefinir a sua palavra-passe.
     * Se a apólice necessitar de dois métodos, então garante que o utilizador tem os dados adequados definidos para pelo menos dois dos métodos de autenticação ativados pela política do administrador.
       * Se os métodos de autenticação não estiverem configurados, o utilizador é aconselhado a contactar o seu administrador para redefinir a sua palavra-passe.
     * Se for atribuída uma função de administrador Azure ao utilizador, a forte política de senha de dois portas é aplicada. Mais informações sobre esta política podem ser encontradas na secção [Redefinir as diferenças](concept-sspr-policy.md#administrator-reset-policy-differences)de política do Administrador.
   * Verifica se a palavra-passe do utilizador é gerida no local (autenticação federada, pass-through ou palavra-passe sincronizada).
     * Se a recompra for implementada e a palavra-passe do utilizador for gerida no local, então o utilizador pode proceder à autenticação e redefinir a sua palavra-passe.
     * Se a recompra não for implementada e a palavra-passe do utilizador for gerida no local, o utilizador é solicitado a contactar o seu administrador para redefinir a sua palavra-passe.
4. Se for determinado que o utilizador é capaz de redefinir com sucesso a sua palavra-passe, então o utilizador é guiado através do processo de reset.

## <a name="authentication-methods"></a>Métodos de autenticação

Se o SSPR estiver ativado, deve selecionar pelo menos uma das seguintes opções para os métodos de autenticação. Às vezes ouve-se estas opções referidas como "portões". Recomendamos vivamente que **escolha dois ou mais métodos** de autenticação para que os seus utilizadores tenham mais flexibilidade no caso de não conseguirem aceder a um quando precisam. Detalhes adicionais sobre os métodos listados abaixo podem ser encontrados no artigo Quais são os métodos de [autenticação?](concept-authentication-methods.md)

* Notificação de aplicação móvel
* Código da aplicação móvel
* Email
* Número de telemóvel
* Telefone do escritório
* Perguntas de segurança

Os utilizadores só podem redefinir a sua palavra-passe se tiverem dados presentes nos métodos de autenticação que o administrador ativou.

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamada telefónica não estarão disponíveis para os utilizadores de MFA e SSPR em inquilinos ads atos gratuitos/experimentais. As mensagens SMS não são afetadas por esta mudança. A chamada telefónica continuará disponível para os utilizadores em inquilinos da AD Azure pagos. Esta mudança só afeta os inquilinos azure free/trial.

> [!WARNING]
> As funções atribuídas ao Administrador Azure serão necessárias para utilizar métodos definidos na secção [Redefinir as diferenças](concept-sspr-policy.md#administrator-reset-policy-differences)de política do Administrador .

![Seleção de métodos de autenticação no portal Azure][Authentication]

### <a name="number-of-authentication-methods-required"></a>Número de métodos de autenticação exigidos

Esta opção determina o número mínimo de métodos ou portões de autenticação disponíveis por que um utilizador deve passar para redefinir ou desbloquear a sua palavra-passe. Pode ser definido para um ou dois.

Os utilizadores podem optar por fornecer mais métodos de autenticação se o administrador ativar esse método de autenticação.

Se um utilizador não tiver os métodos mínimos necessários registados, vê uma página de erro que os direciona para solicitar que um administrador redefinir a sua palavra-passe.

#### <a name="mobile-app-and-sspr"></a>Aplicativo móvel e SSPR

Ao utilizar uma aplicação móvel, como a aplicação Microsoft Authenticator, como método de reset de palavras-passe, deve estar ciente das seguintes ressalvas:

* Quando os administradores exigem que um método seja utilizado para redefinir uma palavra-passe, o código de verificação é a única opção disponível.
* Quando os administradores exigem que sejam utilizados dois métodos para redefinir uma palavra-passe, os utilizadores podem utilizar o código de verificação **ou** notificação **ou** de notificação, para além de quaisquer outros métodos habilitados.

| Número de métodos necessários para redefinir | Um | Dois |
| :---: | :---: | :---: |
| Funcionalidades de aplicativos móveis disponíveis | Código | Código ou Notificação |

Os utilizadores não têm a opção de registar a sua aplicação móvel ao registarem-se para redefinir a palavra-passe de self-service a partir de [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup). Os utilizadores podem registar [https://aka.ms/mfasetup](https://aka.ms/mfasetup)a sua aplicação móvel [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)na pré-visualização do registo de informações de segurança em .

> [!WARNING]
> Tem de ativar o [registo convergente para reset de palavra-passe de autosserviço e autenticação de multifactor (pré-visualização pública)](concept-registration-mfa-sspr-converged.md) antes de os utilizadores poderem aceder à nova experiência em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

> [!IMPORTANT]
> A aplicação autenticadora não pode ser selecionada como o único método de autenticação ao configurar uma política de 1 portão. Da mesma forma, a aplicação autenticadora e apenas um método adicional não podem ser selecionados ao configurar uma política de 2 portas.
> Em seguida, ao configurar as políticas de SSPR que incluem a aplicação autenticadora como um método, pelo menos deve ser selecionado um método adicional ao configurar uma política de 1 portão, e pelo menos dois métodos adicionais devem ser selecionados ao configurar uma política de 2 portas.
> A razão para esta exigência é porque a experiência de registo SSPR atual não inclui a opção de registar a aplicação autenticadora. A opção de registar a aplicação autenticadora está incluída com o novo [registo convergente para reset de senha de autosserviço e autenticação de multi-factores Azure (pré-visualização pública)](concept-registration-mfa-sspr-converged.md).
> Permitir políticas que utilizem apenas a aplicação autenticadora (para políticas de 1 portal), ou a aplicação autenticadora e apenas um método adicional (para políticas de 2 portas), poderia levar a que os utilizadores fossem impedidos de se registarem para o SSPR até estarem configurados para utilizarem a nova experiência de registo.

### <a name="change-authentication-methods"></a>Alterar métodos de autenticação

Se começar com uma política que tenha apenas um método de autenticação necessário para redefinir ou desbloquear registado e mudar isso para dois métodos, o que acontece?

| Número de métodos registados | Número de métodos necessários | Resultado |
| :---: | :---: | :---: |
| 1 ou mais | 1 | **Capaz de** redefinir ou desbloquear |
| 1 | 2 | **Incapaz de** redefinir ou desbloquear |
| 2 ou mais | 2 | **Capaz de** redefinir ou desbloquear |

Se alterar os tipos de métodos de autenticação que um utilizador pode utilizar, poderá inadvertidamente impedir que os utilizadores possam utilizar o SSPR se não tiverem a quantidade mínima de dados disponíveis.

Exemplo:
1. A política original está configurada com dois métodos de autenticação necessários. Usa apenas o número de telefone do escritório e as questões de segurança. 
2. O administrador altera a política para deixar de usar as questões de segurança, mas permite o uso de um telemóvel e de um e-mail alternativo.
3. Os utilizadores sem telemóvel ou campos de e-mail alternativos povoados não podem redefinir as suas palavras-passe.

## <a name="registration"></a>Registo

### <a name="require-users-to-register-when-they-sign-in"></a>Exigir que os utilizadores se registem quando iniciarem o seu registo

Ativar esta opção requer que um utilizador complete o registo de redefinição da palavra-passe se iniciar qualquer aplicação utilizando a AD Azure. Este fluxo de trabalho inclui as seguintes aplicações:

* Office 365
* Portal do Azure
* Painel de Acesso
* Aplicações federadas
* Aplicações personalizadas usando Azure AD

Quando o registo é desativado, os utilizadores podem registar-se manualmente. Podem visitar [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) ou selecionar o **'Registo' para redefinir a palavra-passe** no separador **Perfil** no Painel de Acesso.

> [!NOTE]
> Os utilizadores podem descartar o portal de registo de redefinição da palavra-passe selecionando **cancelar** ou fechando a janela. Mas é-lhes solicitado que se registem sempre que iniciarem a sua inscrição até completarem o seu registo.
>
> Esta interrupção não quebra a ligação do utilizador se já estiver em si.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Detete o número de dias antes de os utilizadores serem convidados a reconfirmar as suas informações de autenticação

Esta opção determina o período de tempo entre a definição e a reconfirmação das informações de autenticação e só está disponível se permitir que os **utilizadores exijam o registo na hora** de iniciar a sua inscrição na opção.

Os valores válidos são de 0 a 730 dias, com "0" o que significa que os utilizadores nunca são convidados a confirmar a sua informação de autenticação.

## <a name="notifications"></a>Notificações

### <a name="notify-users-on-password-resets"></a>Notificar os utilizadores sobre reposições de palavras-passe

Se esta opção for definida para **Sim**, então os utilizadores que reajustarem a sua palavra-passe recebem um e-mail a notificá-los de que a sua palavra-passe foi alterada. O e-mail é enviado através do portal SSPR para os seus endereços de e-mail primários e alternativos que estão arquivados em Azure AD. Ninguém mais foi notificado do evento de reset.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notifique todos os administradores quando outros administradores redefinirem as suas palavras-passe

Se esta opção for definida para **Sim,** *então todos os administradores* recebem um e-mail para o seu endereço de e-mail principal em ficheiro em Azure AD. O e-mail notifica-os de que outro administrador alterou a sua palavra-passe utilizando o SSPR.

Exemplo: Há quatro administradores num ambiente. Administrador A repõe a sua palavra-passe utilizando o SSPR. Os administradores B, C e D recebem um e-mail alertando-os do reset da palavra-passe.

## <a name="on-premises-integration"></a>Integração no local

Se instalar, configurar e ativar o Azure AD Connect, tem as seguintes opções adicionais para integrações no local. Se estas opções estiverem cinzentas, então a reprodução não foi devidamente configurada. Para mais informações, consulte [A dinamamento da palavra-passe configurando](howto-sspr-writeback.md).

![A validação da redação da palavra-passe está ativada e a funcionar][Writeback]

Esta página fornece-lhe um estado rápido do cliente de redação no local, uma das seguintes mensagens é exibida com base na configuração atual:

* O seu cliente de recompra no local está a funcionar.
* A Azure AD está online e está ligada ao seu cliente de redação no local. No entanto, parece que a versão instalada do Azure AD Connect está desatualizada. Considere [atualizar o Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) para garantir que possui as mais recentes funcionalidades de conectividade e correções importantes de bugs.
* Infelizmente, não podemos verificar o seu estado de recompra no local porque a versão instalada do Azure AD Connect está desatualizada. [Atualize o Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) para poder verificar o seu estado de ligação.
* Infelizmente, parece que não podemos ligar-nos ao seu cliente de recompra no local. [Troubleshoot Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) para restaurar a ligação.
* Infelizmente, não podemos ligar-nos ao seu cliente de redação no local porque a redação de palavras-passe não foi devidamente configurada. Configure a reescrita da [palavra-passe](howto-sspr-writeback.md) para restaurar a ligação.
* Infelizmente, parece que não podemos ligar-nos ao seu cliente de recompra no local. Isto pode ser devido a problemas temporários da nossa parte. Se o problema persistir, [a Troubleshoot Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) para restaurar a ligação.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Escreva palavras-passe para o seu diretório no local

Este controlo determina se a reescrita da palavra-passe está ativada para este diretório. Se a redação estiver em funcionação, indica o estado do serviço de redação no local. Este controlo é útil se pretender desativar temporariamente a reescrita da palavra-passe sem ter de reconfigurar o Azure AD Connect.

* Se o interruptor estiver definido para **Sim**, então a reprodução está ativada e a autenticação federada, pass-through, ou os utilizadores sincronizados com hash de palavra-passe são capazes de redefinir as suas palavras-passe.
* Se o interruptor estiver definido para **Nº ,** então a reprodução é desativada e a autenticação federada, pass-through, ou os utilizadores sincronizados com hash de palavra-passe não são capazes de redefinir as suas palavras-passe.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Permitir que os utilizadores desbloqueiem contas sem redefinir a sua palavra-passe

Este controlo designa se os utilizadores que visitam o portal de reset de palavras-passe devem ter a opção de desbloquear as suas contas de Diretório Ativo no local sem terem de redefinir a sua palavra-passe. Por padrão, o Azure AD desbloqueia contas quando executa uma reposição de palavra-passe. Usa este cenário para separar essas duas operações.

* Se definido para **Sim**, então os utilizadores têm a opção de redefinir a sua palavra-passe e desbloquear a conta, ou desbloquear a sua conta sem ter de redefinir a palavra-passe.
* Se definido para **Nº ,** então os utilizadores só podem realizar uma reposição combinada da palavra-passe e desbloquear a conta.

### <a name="on-premises-active-directory-password-filters"></a>Filtros de senha de diretório ativo no local

O reset da palavra-passe autosserviço Azure AD executa o equivalente a uma redefinição de senha iniciada pela administração no Diretório Ativo. Se estiver a utilizar um filtro de palavra-passe de terceiros para impor regras de senha personalizadas e exigir que este filtro de senha seja verificado durante o reset da palavra-passe do autosserviço Azure AD, certifique-se de que a solução de filtro de senha de terceiros está configurada para ser aplicada no cenário de redefinição da palavra-passe de administração. [A proteção de palavras-passe Azure AD para o Diretório Ativo](concept-password-ban-bad-on-premises.md) do Windows Server é suportada por predefinição.

## <a name="password-reset-for-b2b-users"></a>Reset de palavra-passe para utilizadores B2B

O reset e a alteração da palavra-passe são totalmente suportados em todas as configurações de negócio-a-negócio (B2B). O reset da palavra-passe do utilizador B2B é suportado nos seguintes três casos:

* **Utilizadores de uma organização parceira com um inquilino Azure AD existente**: Se a organização com quem está a associar-se tem um inquilino Azure AD existente, *respeitamos quaisquer políticas de redefinição de senhas que estejam ativadas nesse inquilino.* Para que a palavra-passe seja recriada, a organização parceira apenas precisa de se certificar de que o Azure AD SSPR está ativado. Não existe um custo adicional para os clientes do Office 365, e pode ser ativado seguindo os passos no nosso Get started com guia de gestão de [passwords.](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords)
* **Utilizadores que se inscrevam através** de inscrição de self-service: Se a organização com quem está a associar-se usou a funcionalidade de inscrição de [self-service](../users-groups-roles/directory-self-service-signup.md) para entrar num inquilino, deixamos-os redefinir a palavra-passe com o e-mail que registaram.
* **Utilizadores B2B**: Quaisquer novos utilizadores B2B criados utilizando as novas capacidades Do [Azure AD B2B](../active-directory-b2b-what-is-azure-ad-b2b.md) também poderão redefinir as suas palavras-passe com o e-mail que registaram durante o processo de convite.

Para testar este cenário, vá com https://passwordreset.microsoftonline.com um destes utilizadores parceiros. Se tiverem um e-mail alternativo ou e-mail de autenticação definido, o reset da palavra-passe funciona como esperado.

> [!NOTE]
> As contas da Microsoft que receberam acesso ao seu inquilino Azure AD, como as de Hotmail.com, Outlook.com ou outros endereços de e-mail pessoais, não podem utilizar o Azure AD SSPR. Eles precisam de redefinir a sua palavra-passe utilizando as informações encontradas no artigo da [sua conta Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

## <a name="next-steps"></a>Passos seguintes

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
* [Acho que algo está partido. Como posso resolver problemas com a SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Métodos de autenticação do Azure AD disponíveis e a quantidade necessária"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "No local integração de passwords de reprodução e informação de resolução de problemas"
