---
title: Aprofundamento de redefinição de senha de autoatendimento – Azure Active Directory
description: Como funciona a redefinição de senha de autoatendimento
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
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848583"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Como funciona: redefinição de senha de autoatendimento do Azure AD

Como funciona a redefinição de senha de autoatendimento (SSPR)? O que essa opção significa na interface? Continue lendo para saber mais sobre o Azure Active Directory (Azure AD) SSPR.

## <a name="how-does-the-password-reset-portal-work"></a>Como funciona o portal de redefinição de senha?

Quando um usuário vai para o portal de redefinição de senha, um fluxo de trabalho é iniciado para determinar:

   * Como a página deve ser localizada?
   * A conta de usuário é válida?
   * A qual organização o usuário pertence?
   * Onde a senha do usuário é gerenciada?
   * O usuário está licenciado para usar o recurso?

Leia as etapas a seguir para saber mais sobre a lógica por trás da página de redefinição de senha:

1. O usuário seleciona o link **não consegue acessar sua conta** ou vai diretamente para [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
   * Com base na localidade do navegador, a experiência é renderizada no idioma apropriado. A experiência de redefinição de senha é localizada nos mesmos idiomas aos quais o Office 365 dá suporte.
   * Para exibir o portal de redefinição de senha em um idioma localizado diferente, acrescente "? MKT =" ao final da URL de redefinição de senha com o exemplo que segue a localização para espanhol [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us).
2. O usuário insere uma ID de usuário e passa um captcha.
3. O Azure AD verifica se o usuário é capaz de usar esse recurso fazendo as seguintes verificações:
   * Verifica se o usuário tem esse recurso habilitado e tem uma licença do Azure AD atribuída.
     * Se o usuário não tiver esse recurso habilitado ou tiver uma licença atribuída, será solicitado que o usuário entre em contato com o administrador para redefinir sua senha.
   * Verifica se o usuário tem os métodos de autenticação corretos definidos em sua conta de acordo com a política de administrador.
     * Se a política exigir apenas um método, ele garantirá que o usuário tenha os dados apropriados definidos para pelo menos um dos métodos de autenticação habilitados pela política de administrador.
       * Se os métodos de autenticação não estiverem configurados, o usuário será aconselhado a entrar em contato com o administrador para redefinir sua senha.
     * Se a política exigir dois métodos, ela garantirá que o usuário tenha os dados apropriados definidos para pelo menos dois dos métodos de autenticação habilitados pela política de administrador.
       * Se os métodos de autenticação não estiverem configurados, o usuário será aconselhado a entrar em contato com o administrador para redefinir sua senha.
     * Se uma função de administrador do Azure for atribuída ao usuário, a política de senha forte de duas portas será imposta. Mais informações sobre essa política podem ser encontradas na seção [diferenças de política de redefinição de administrador](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Verifica se a senha do usuário é gerenciada localmente (federada, autenticação de passagem ou hash de senha sincronizado).
     * Se o Write-back for implantado e a senha do usuário for gerenciada localmente, o usuário poderá continuar a autenticar e redefinir sua senha.
     * Se o Write-back não for implantado e a senha do usuário for gerenciada localmente, o usuário será solicitado a entrar em contato com o administrador para redefinir sua senha.
4. Se for determinado que o usuário é capaz de redefinir sua senha com êxito, o usuário será guiado pelo processo de redefinição.

## <a name="authentication-methods"></a>Métodos de autenticação

Se SSPR estiver habilitado, você deverá selecionar pelo menos uma das opções a seguir para os métodos de autenticação. Às vezes, você ouve essas opções conhecidas como "Gates". É altamente recomendável que você **escolha dois ou mais métodos de autenticação** para que os usuários tenham mais flexibilidade caso não possam acessar um quando precisarem. Detalhes adicionais sobre os métodos listados abaixo podem ser encontrados no artigo [o que são métodos de autenticação?](concept-authentication-methods.md).

* Notificação de aplicação móvel
* Código da aplicação móvel
* E-mail
* Número de telemóvel
* Telefone do escritório
* Perguntas de segurança

Os usuários só poderão redefinir sua senha se tiverem dados presentes nos métodos de autenticação habilitados pelo administrador.

> [!IMPORTANT]
> A partir de março de 2019, as opções de chamada telefônica não estarão disponíveis para os usuários de MFA e SSPR em locatários gratuitos/de avaliação do Azure AD. As mensagens SMS não são afetadas por essa alteração. A chamada telefônica continuará disponível para os usuários em locatários pagos do Azure AD. Essa alteração afeta apenas os locatários gratuitos/de avaliação do Azure AD.

> [!WARNING]
> As contas atribuídas às funções de administrador do Azure serão necessárias para usar métodos conforme definido na seção [diferenças de política de redefinição de administrador](concept-sspr-policy.md#administrator-reset-policy-differences).

![Seleção de métodos de autenticação no portal do Azure][Authentication]

### <a name="number-of-authentication-methods-required"></a>Número de métodos de autenticação necessários

Essa opção determina o número mínimo de métodos de autenticação disponíveis ou Gates que um usuário deve passar para redefinir ou desbloquear sua senha. Ele pode ser definido como um ou dois.

Os usuários podem optar por fornecer mais métodos de autenticação se o administrador habilitar esse método de autenticação.

Se um usuário não tiver os métodos mínimos necessários registrados, eles verão uma página de erro que os direciona para solicitar que um administrador Redefina sua senha.

#### <a name="mobile-app-and-sspr"></a>Aplicativo móvel e SSPR

Ao usar um aplicativo móvel, como o aplicativo Microsoft Authenticator, como um método para a redefinição de senha, você deve estar ciente das seguintes advertências:

* Quando os administradores exigem que um método seja usado para redefinir uma senha, o código de verificação é a única opção disponível.
* Quando os administradores exigem dois métodos para redefinir uma senha, os usuários são capazes de usar **o** código de notificação **ou** de verificação, além de quaisquer outros métodos habilitados.

| Número de métodos necessários para a reposição | Um | Dois |
| :---: | :---: | :---: |
| Recursos do aplicativo móvel disponíveis | Código | Código ou notificação |

Os usuários não têm a opção de registrar seu aplicativo móvel ao se registrar para redefinição de senha de autoatendimento do [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup). Os usuários podem registrar seu aplicativo móvel em [https://aka.ms/mfasetup](https://aka.ms/mfasetup)ou na nova visualização do registro de informações de segurança em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

> [!WARNING]
> Você deve habilitar o [registro convergido para redefinição de senha de autoatendimento e autenticação multifator do Azure (visualização pública)](concept-registration-mfa-sspr-converged.md) antes que os usuários possam acessar a nova experiência em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

> [!IMPORTANT]
> O aplicativo autenticador não pode ser selecionado como o único método de autenticação ao configurar uma política de 1 porta. Da mesma forma, o aplicativo autenticador e apenas um método adicional não podem ser selecionados durante a configuração de uma política de 2 Gates.
> Em seguida, ao configurar políticas de SSPR que incluem o aplicativo autenticador como um método, pelo menos um método adicional deve ser selecionado ao configurar uma política de uma porta, e pelo menos dois métodos adicionais devem ser selecionados ao configurar uma política de 2 Gates.
> O motivo para esse requisito é porque a atual experiência de registro de SSPR não inclui a opção de registrar o aplicativo autenticador. A opção para registrar o aplicativo autenticador está incluída no novo [registro convergido para redefinição de senha de autoatendimento e autenticação multifator do Azure (visualização pública)](concept-registration-mfa-sspr-converged.md).
> Permitir políticas que usam apenas o aplicativo autenticador (para políticas de 1 Gate) ou o aplicativo autenticador e apenas um método adicional (para políticas de 2 Gates) pode levar os usuários sendo impedidos de se registrarem para SSPR até que tenham sido configurados para usar o novo experiência de registro.

### <a name="change-authentication-methods"></a>Alterar métodos de autenticação

Se você começar com uma política que tem apenas um método de autenticação necessário para redefinição ou desbloqueio registrado e você alterar isso para dois métodos, o que acontecerá?

| Número de métodos registrados | Número de métodos necessários | Resultado |
| :---: | :---: | :---: |
| 1 ou mais | 1 | **Capaz** de redefinir ou desbloquear |
| 1 | 2 | **Não é possível** redefinir ou desbloquear |
| 2 ou mais | 2 | **Capaz** de redefinir ou desbloquear |

Se você alterar os tipos de métodos de autenticação que um usuário pode usar, você poderá impedir inadvertidamente que os usuários possam usar o SSPR se não tiverem a quantidade mínima de dados disponíveis.

Exemplo:
1. A política original é configurada com dois métodos de autenticação necessários. Ele usa apenas o número de telefone comercial e as perguntas de segurança. 
2. O administrador altera a política para não usar mais as perguntas de segurança, mas permite o uso de um telefone celular e um email alternativo.
3. Os usuários sem os campos telefone celular ou email alternativo preenchidos não podem redefinir suas senhas.

## <a name="registration"></a>Registo

### <a name="require-users-to-register-when-they-sign-in"></a>Exigir que os usuários se registrem ao entrarem

Habilitar essa opção exige que um usuário conclua o registro de redefinição de senha se entrar em qualquer aplicativo usando o Azure AD. Este fluxo de trabalho inclui os seguintes aplicativos:

* Office 365
* Portal do Azure
* Painel de Acesso
* Aplicativos federados
* Aplicativos personalizados usando o Azure AD

Quando a exigência de registro está desabilitada, os usuários podem registrá-lo manualmente. Eles podem visitar [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) ou selecionar o link **registrar para redefinição de senha** na guia **perfil** no painel de acesso.

> [!NOTE]
> Os usuários podem ignorar o portal de registro de redefinição de senha selecionando **Cancelar** ou fechando a janela. Mas eles serão solicitados a registrar cada vez que entrarem até que concluam seu registro.
>
> Essa interrupção não interromperá a conexão do usuário se já tiver entrado.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Definir o número de dias antes que os usuários sejam solicitados a reconfirmar suas informações de autenticação

Essa opção determina o período de tempo entre a configuração e a reconfirmação das informações de autenticação e estará disponível somente se você habilitar a opção **exigir que os usuários se registrem ao entrar** .

Os valores válidos são de 0 a 730 dias, com "0", o que significa que os usuários nunca são solicitados a reconfirmar suas informações de autenticação.

## <a name="notifications"></a>Notificações

### <a name="notify-users-on-password-resets"></a>Notificar os utilizadores sobre reposições de palavras-passe

Se essa opção for definida como **Sim**, os usuários que redefinirem sua senha receberão um email notificando-os de que a senha foi alterada. O email é enviado por meio do portal do SSPR para seus endereços de email primários e alternativos que estão no arquivo no Azure AD. Ninguém mais será notificado sobre o evento de redefinição.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notificar todos os administradores quando outros administradores redefinirem suas senhas

Se essa opção for definida como **Sim**, *todos os administradores* receberão um email para seu endereço de email principal no arquivo no Azure AD. O email notifica que outro administrador alterou sua senha usando SSPR.

Exemplo: há quatro administradores em um ambiente. O administrador A redefine sua senha usando SSPR. Os administradores B, C e D recebem um email alertando-os sobre a redefinição de senha.

## <a name="on-premises-integration"></a>Integração no local

Se você instalar, configurar e habilitar Azure AD Connect, terá as seguintes opções adicionais para integrações locais. Se essas opções estiverem esmaecidas, o Write-back não foi configurado corretamente. Para obter mais informações, consulte [Configuring password write-back](howto-sspr-writeback.md).

![A validação do write-back de senha está habilitada e funcionando][Writeback]

Esta página fornece um status rápido do cliente de write-back local, uma das seguintes mensagens é exibida com base na configuração atual:

* Seu cliente de write-back local está em execução.
* O Azure AD está online e conectado ao seu cliente de write-back local. No entanto, parece que a versão instalada do Azure AD Connect está desatualizada. Considere [atualizar Azure ad Connect](../hybrid/how-to-upgrade-previous-version.md) para garantir que você tenha os recursos de conectividade mais recentes e as correções de bugs importantes.
* Infelizmente, não é possível verificar seu status do cliente de write-back local porque a versão instalada do Azure AD Connect está desatualizada. [Atualize Azure ad Connect](../hybrid/how-to-upgrade-previous-version.md) para poder verificar seu status de conexão.
* Infelizmente, parece que não podemos nos conectar ao seu cliente de write-back local no momento. [Solucionar problemas Azure ad Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) para restaurar a conexão.
* Infelizmente, não podemos nos conectar ao seu cliente de write-back local porque o Write-back de senha não foi configurado corretamente. [Configure o Write-back de senha](howto-sspr-writeback.md) para restaurar a conexão.
* Infelizmente, parece que não podemos nos conectar ao seu cliente de write-back local no momento. Isso pode ser devido a problemas temporários em nosso fim. Se o problema persistir, [solucione Azure ad Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) para restaurar a conexão.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Write-back de senhas para seu diretório local

Esse controle determina se o Write-back de senha está habilitado para este diretório. Se Write-back estiver ativado, ele indicará o status do serviço de write-back local. Esse controle será útil se você quiser desabilitar temporariamente o Write-back de senha sem precisar reconfigurar Azure AD Connect.

* Se a opção for definida como **Sim**, o Write-back será habilitado e os usuários federados, de autenticação de passagem ou de hash de senha poderão redefinir suas senhas.
* Se a opção for definida como **não**, o Write-back será desabilitado e a autenticação federada, de passagem, ou os usuários sincronizados com hash de senha não poderão redefinir suas senhas.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Permitir que os usuários desbloqueiem contas sem redefinir a senha

Esse controle designa se os usuários que visitam o portal de redefinição de senha devem receber a opção de desbloquear suas contas de Active Directory locais sem precisar redefinir sua senha. Por padrão, o Azure AD desbloqueia as contas quando ele executa uma redefinição de senha. Você usa essa configuração para separar essas duas operações.

* Se definido como **Sim**, os usuários receberão a opção de redefinir sua senha e desbloquear a conta, ou para desbloquear a conta sem precisar redefinir a senha.
* Se definido como **não**, os usuários só poderão executar uma operação combinada de redefinição de senha e de desbloqueio de conta.

### <a name="on-premises-active-directory-password-filters"></a>Filtros de senha do Active Directory local

A redefinição de senha de autoatendimento do Azure AD executa o equivalente a uma redefinição de senha iniciada pelo administrador no Active Directory. Se você estiver usando um filtro de senha de terceiros para impor regras de senha personalizadas e precisar que esse filtro de senha seja verificado durante a redefinição de senha de autoatendimento do Azure AD, verifique se a solução de filtro de senha de terceiros está configurada para ser aplicada no cenário de redefinição de senha de administrador. A [proteção por senha do Azure ad para Windows Server Active Directory](concept-password-ban-bad-on-premises.md) é suportada por padrão.

## <a name="password-reset-for-b2b-users"></a>Redefinição de senha para usuários B2B

A redefinição de senha e a alteração têm suporte total em todas as configurações B2B (entre empresas). A redefinição de senha de usuário B2B tem suporte nos três casos a seguir:

* **Usuários de uma organização parceira com um locatário existente do Azure ad**: se a organização com a qual você está fazendo uma parceria tiver um locatário existente do Azure AD, *respeitaremos quaisquer políticas de redefinição de senha habilitadas nesse locatário*. Para que a redefinição de senha funcione, a organização parceira só precisa se certificar de que o Azure AD SSPR está habilitado. Não há custo adicional para os clientes do Office 365, e ele pode ser habilitado seguindo as etapas em nosso guia de [introdução ao gerenciamento de senhas](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) .
* **Usuários que se inscrevem através** de inscrição de autoatendimento: se a organização com a qual você está fazendo uma parceria tiver usado o recurso de [inscrição de autoatendimento](../users-groups-roles/directory-self-service-signup.md) para entrar em um locatário, permitiremos que eles redefinam a senha com o email que eles registraram.
* **Usuários B2B**: quaisquer novos usuários B2B criados usando os novos [recursos B2B do Azure ad](../active-directory-b2b-what-is-azure-ad-b2b.md) também poderão redefinir suas senhas com o email que eles registraram durante o processo de convite.

Para testar esse cenário, vá para https://passwordreset.microsoftonline.com com um desses usuários parceiros. Se eles tiverem um email alternativo ou e-mail de autenticação definido, a redefinição de senha funcionará conforme o esperado.

> [!NOTE]
> As contas da Microsoft que receberam acesso de convidado ao seu locatário do Azure AD, como as de Hotmail.com, Outlook.com ou outros endereços de email pessoais, não são capazes de usar o SSPR do Azure AD. Eles precisam redefinir sua senha usando as informações encontradas no artigo [quando você não consegue entrar no seu conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) .

## <a name="next-steps"></a>Passos seguintes

Os artigos seguintes disponibilizam informações adicionais relativamente à reposição de palavras-passe através do Azure AD:

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Repor ou alterar a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Acho que algo está quebrado. Como fazer solucionar problemas de SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Métodos de autenticação do Azure AD disponíveis e a quantidade necessária"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "Configuração de write-back de senha de integração local e informações de solução de problemas"
