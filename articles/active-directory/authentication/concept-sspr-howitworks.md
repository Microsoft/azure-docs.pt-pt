---
title: Aprofunde-se-Azure Active Directory de reposição de palavra-passe self-service
description: Como a palavra-passe self-service reposição trabalho
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a6f475b5f1152850ec918b196883c6974f4d95
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370011"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Como funciona: Redefinição de senha de autoatendimento de AD do Azure

Como a palavra-passe self-service reposição trabalho (SSPR)? O que significa essa opção na interface do? Continue a ler para obter mais informações sobre a SSPR do Azure Active Directory (Azure AD).

|     |
| --- |
| Notificação de aplicação móvel e o código de aplicação móvel como métodos de palavra-passe self-service do Azure AD reposição são funcionalidades de pré-visualização pública do Azure Active Directory. Para obter mais informações sobre pré-visualizações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="how-does-the-password-reset-portal-work"></a>Como a palavra-passe portal de reposição?

Quando um usuário Ir para o portal de reposição de palavra-passe, um fluxo de trabalho é inicializado para determinar:

   * Como deve ser localizada a página?
   * É a conta de utilizador válido?
   * O que organização o utilizador pertence ao?
   * Em que é gerida a senha do usuário?
   * O utilizador está licenciado para utilizar a funcionalidade?

Página de reposição de leitura, os seguintes passos para saber mais sobre a lógica por trás da palavra-passe:

1. O utilizador seleciona o **não é possível aceder à sua conta** ligar ou vai diretamente para [ https://aka.ms/sspr ](https://passwordreset.microsoftonline.com).
   * Com base na localidade do navegador, a experiência é renderizada no idioma apropriado. A experiência de reposição de palavra-passe é localizada para os mesmos idiomas que suporta o Office 365.
   * Para ver a reposição de palavra-passe portal em outro idioma localizado acrescente "? mkt =" ao final da palavra-passe de reposição de URL com o exemplo a seguir a localização de mensagens em fila para o espanhol [ https://passwordreset.microsoftonline.com/?mkt=es-us ](https://passwordreset.microsoftonline.com/?mkt=es-us).
2. O usuário digita um ID de utilizador e passa um captcha.
3. O Azure AD verifica que o utilizador é capaz de utilizar esta funcionalidade, efetuando as seguintes verificações:
   * Verifica se o utilizador tem esta funcionalidade ativada e tem um Azure AD licença atribuída.
     * Se o utilizador não tenha esta funcionalidade ativada ou ter uma licença atribuída, é pedido ao utilizador que contacte o seu administrador para repor a palavra-passe.
   * Verifica se o utilizador tem os métodos de autenticação direita definidos na respetiva conta de acordo com a política do administrador.
     * Se a política requer apenas um método, em seguida, ele garante que o utilizador tem os dados apropriados definidos para, pelo menos, um dos métodos de autenticação ativados pela política de administrador.
       * Se os métodos de autenticação não estiverem configurados, o utilizador recomenda que contacte o seu administrador para repor a palavra-passe.
     * Se a política requer dois métodos, em seguida, ele garante que o utilizador tem os dados apropriados definidos para, pelo menos, dois dos métodos de autenticação ativados pela política de administrador.
       * Se os métodos de autenticação não estiverem configurados, o utilizador recomenda que contacte o seu administrador para repor a palavra-passe.
     * Se uma função de administrador do Azure é atribuída ao utilizador, a política de palavra-passe de porta de dois segura é imposta. Obter mais informações sobre esta política podem ser encontradas na seção [diferenças de política de reposição de administrador](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Verifica se a senha do usuário é gerida no local (autenticação federada de pass-through ou sincronizados de hash de palavra-passe).
     * Se a repetição de escrita é implementada e a senha do usuário é geridos no local, em seguida, o utilizador tem permissão para continuar para autenticar e repor a palavra-passe.
     * Se a repetição de escrita não estiver implementada e a senha do usuário é geridos no local, é pedido ao utilizador que contacte o seu administrador para repor a palavra-passe.
4. Se for determinado que o utilizador é capaz de reposta com êxito a palavra-passe, o utilizador é orientado através do processo de reposição.

## <a name="authentication-methods"></a>Métodos de autenticação

Se estiver ativada a SSPR, tem de selecionar pelo menos uma das seguintes opções para os métodos de autenticação. Às vezes ouvir essas opções referidas como "gates." É altamente recomendável que **escolher métodos de autenticação de dois ou mais** para que os utilizadores têm mais flexibilidade, caso sejam não é possível acessar um quando precisarem. Detalhes adicionais sobre os métodos listados abaixo podem ser encontradas no artigo [quais são os métodos de autenticação?](concept-authentication-methods.md).

* Notificação de aplicação móvel (pré-visualização)
* Código da aplicação móvel (pré-visualização)
* Email
* Número de telemóvel
* Telefone do escritório
* Perguntas de segurança

Os utilizadores só podem repor a palavra-passe, se tiverem dados presentes nos métodos de autenticação que o administrador tiver ativado.

> [!IMPORTANT]
> A partir de Março de 2019 as opções de chamada telefónica não estarão disponível para os utilizadores do MFA e o SSPR em inquilinos gratuitos/avaliação do Azure AD. Mensagens SMS não são afetadas por esta alteração. Chamada telefónica continuará a estar disponíveis para os utilizadores na pago inquilinos do Azure AD. Esta alteração só afeta o inquilinos gratuitos/avaliação do Azure AD.

> [!WARNING]
> Contas atribuídas funções de administrador do Azure será necessárias usar métodos, conforme definido na secção [diferenças de política de reposição de administrador](concept-sspr-policy.md#administrator-reset-policy-differences).

![Seleção de métodos de autenticação no portal do Azure][Authentication]

### <a name="number-of-authentication-methods-required"></a>Número de métodos de autenticação necessários

Esta opção determina o número mínimo dos métodos de autenticação disponíveis ou limites de que um utilizador deve percorrer a repor ou desbloquear a palavra-passe. Ele pode ser definido para um ou dois.

Os utilizadores podem optar por fornecer mais métodos de autenticação, se o administrador ativa a esse método de autenticação.

Se um utilizador não tem os métodos necessários mínimos registados, verão uma página de erro que os direcione para solicitar que um administrador de reposição da palavra-passe.

#### <a name="mobile-app-and-sspr-preview"></a>Aplicação móvel e SSPR (pré-visualização)

Quando utiliza uma aplicação móvel, como a aplicação Microsoft Authenticator, como um método para a reposição de palavra-passe, deve ter em consideração os seguintes avisos:

* Quando os administradores necessitam de um método a ser utilizado para repor uma palavra-passe, o código de verificação é a única opção disponível.
* Quando os administradores necessitam de dois métodos a ser utilizado para repor uma palavra-passe, os utilizadores conseguem utilizar **EITHER** notificação **ou** ativada de código de verificação, além de quaisquer outros métodos.

| Número de métodos necessários para a reposição | Um | Dois |
| :---: | :---: | :---: |
| Funcionalidades de aplicações móveis disponíveis | Código | Código ou de notificação |

Os utilizadores não têm a opção para registar a aplicação móvel quando efetuar o registo para reposição de palavra-passe self-service do [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup). Os utilizadores podem registar a sua aplicação móvel [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup), ou a nova pré-visualização de registo de informações de segurança por [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo).

> [!WARNING]
> Tem de ativar a [de registo para reposição de palavra-passe self-service e o Azure multi-factor Authentication (pré-visualização pública) de convergência](concept-registration-mfa-sspr-converged.md) antes dos utilizadores serão capazes de aceder a nova experiência em [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo).

### <a name="change-authentication-methods"></a>Alterar o método de autenticação

Se iniciar com uma política que tenha apenas um método de autenticação obrigatório para repor ou desbloquear registado e alterar a que, para dois métodos, o que acontece?

| Número de métodos registados | Número de métodos necessários | Resultado |
| :---: | :---: | :---: |
| 1 ou mais | 1 | **Capaz de** a repor ou desbloquear |
| 1 | 2 | **Não é possível** a repor ou desbloquear |
| 2 ou mais | 2 | **Capaz de** a repor ou desbloquear |

Se alterar os tipos de métodos de autenticação que um utilizador pode utilizar, poderá inadvertidamente de impedir que os utilizadores a capacidade de utilizar o SSPR se eles não têm a quantidade mínima de dados disponíveis.

Exemplo:
1. A política original está configurada com dois métodos de autenticação necessários. Utiliza apenas o número de telefone do escritório e as perguntas de segurança. 
2. O administrador altera a política para deixar de utilizar as perguntas de segurança, mas permite a utilização de um número de telefone e uma mensagem de e-mail alternativa.
3. Os utilizadores sem o telefone celular ou campos de correio eletrónico alternativo preenchidos não é possível repor as palavras-passe.

## <a name="registration"></a>Registo

### <a name="require-users-to-register-when-they-sign-in"></a>Exigir que os utilizadores se registem quando iniciam sessão

Ativar esta opção requer que um utilizador concluir o registo de reposição de palavra-passe, iniciar sessão todas as aplicações com o Azure AD. Este fluxo de trabalho inclui as seguintes aplicações:

* Office 365
* Portal do Azure
* Painel de Acesso
* Aplicações federadas
* Aplicativos personalizados com o Azure AD

Quando a necessidade de registo está desativada, os utilizadores podem registar manualmente. Podem visite [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup) ou selecione o **Registre-se para a reposição de palavra-passe** ligação sob o **perfil** separador no painel de acesso.

> [!NOTE]
> Os utilizadores podem dispensar o portal de registo de reposição de palavra-passe, selecionando **Cancelar** ou se fechar a janela. Mas, são-lhe pedido para se registar cada vez que iniciar sessão até que sejam concluídas seu registro.
>
> Este interrupção não interromper a ligação do utilizador que já estejam assinados.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Definir o número de dias antes de ser pedido aos utilizadores que voltem as informações de autenticação a

Esta opção determina o período de tempo entre a definição e reconfirmando as informações de autenticação quando e está disponível apenas se ativar a **exigir que os utilizadores se registem ao iniciar** opção.

Valores válidos são 0 para 730 dias. o, com "0", que significa que nunca ser pedido aos utilizadores que voltem as informações de autenticação a.

## <a name="notifications"></a>Notificações

### <a name="notify-users-on-password-resets"></a>Notificar os utilizadores sobre reposições de palavras-passe

Se esta opção estiver definida como **Sim**, em seguida, os utilizadores a repor a palavra-passe recebem um e-mail a notificar que a palavra-passe foi alterada. O e-mail é enviado através do portal SSPR para os respetivos endereços de e-mail principal e alternativo que estão no ficheiro no Azure AD. Ninguém mais é notificado do evento de reposição.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notificar todos os administradores quando outros administradores repõem as palavras-passe

Se esta opção estiver definida como **Sim**, em seguida, *todos os administradores* receber um e-mail para o respetivo endereço de e-mail principal registado no Azure AD. A mensagem de e-mail notifica-los que outro administrador foi alterado a palavra-passe ao utilizar o SSPR.

Exemplo: Existem quatro administradores num ambiente. O administrador A repõe a palavra-passe ao utilizar o SSPR. Os administradores B, C e D recebem um e-mail alertá-los da reposição de palavra-passe.

## <a name="on-premises-integration"></a>Integração no local

Se instalar, configura e ativar o Azure AD Connect, tem as seguintes opções adicionais para integrações no local. Se estas opções são desativadas, em seguida, repetição de escrita não foi corretamente configurada. Para obter mais informações, consulte [configurar a repetição de escrita de palavra-passe](howto-sspr-writeback.md).

![A validar a repetição de escrita de palavra-passe está ativada e a funcionar][Writeback]

Esta página fornece um rápido status do cliente de repetição de escrita no local, uma das duas mensagens seguintes é apresentada com base na configuração atual:

* O cliente de repetição de escrita no local está em execução.
* Azure AD está online e está ligado ao seu cliente de repetição de escrita no local. No entanto, parece que a versão instalada do Azure AD Connect está desatualizada. Considere [atualizar o Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) para se certificar de que tem as mais recentes funcionalidades de conectividade e correções de erros importantes.
* Infelizmente, não foi possível verificar o estado de repetição de escrita no local do cliente porque a versão instalada do Azure AD Connect está desatualizada. [Atualizar o Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) para poder verificar o estado da ligação.
* Infelizmente, parece não é possível ligar ao seu cliente de repetição de escrita no local neste momento. [Resolver problemas do Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) para restabelecer a ligação.
* Infelizmente, a foi possível ligar ao seu cliente de repetição de escrita no local porque a repetição de escrita de palavra-passe não foi corretamente configurada. [Configurar a repetição de escrita de palavra-passe](howto-sspr-writeback.md) para restabelecer a ligação.
* Infelizmente, parece não é possível ligar ao seu cliente de repetição de escrita no local neste momento. Isto pode dever-se a problemas temporários do nosso lado. Se o problema persistir, [resolver problemas relacionados com o Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) para restabelecer a ligação.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Gravar palavras-passe no seu diretório no local

Esse controle determina se a repetição de escrita de palavra-passe está ativada para este diretório. Se estiver repetição de escrita ativado, ele indica o estado do serviço de repetição de escrita no local. Esse controle será útil se pretender desativar temporariamente a repetição de escrita de palavra-passe sem ter de reconfigurar o Azure AD Connect.

* Se o parâmetro estiver definido como **Sim**, em seguida, a repetição de escrita é ativada e a autenticação federada, pass-through ou os utilizadores sincronizado de hash de palavra-passe são capazes de repor as palavras-passe.
* Se o parâmetro estiver definido como **não**, em seguida, a repetição de escrita está desativada e a autenticação federada, pass-through ou os utilizadores sincronizado de hash de palavra-passe não são possível repor as palavras-passe.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Permitir que os utilizadores desbloqueiem as contas sem repor a palavra-passe

Esse controle designa se os usuários que visitam o portal de reposição de palavra-passe devem ser dada a opção para desbloquear suas contas do Active Directory no local sem ter de repor a palavra-passe. Por predefinição, o Azure AD desbloqueia contas quando ele efetuar uma reposição de palavra-passe. Utilize esta definição para separar as duas operações.

* Se definido como **Sim**, em seguida, os utilizadores recebem a opção para repor a palavra-passe e desbloquear a conta de ou para desbloquear a conta sem ter de repor a palavra-passe.
* Se definido como **não**, em seguida, os utilizadores são só poderá efetuar uma reposição de palavra-passe combinado e operação de desbloqueio de conta.

### <a name="on-premises-active-directory-password-filters"></a>Filtros de palavra-passe do Active Directory no local

O Azure AD self-service palavra-passe reposição executa o equivalente a um iniciada pelo administrador reposição palavra-passe no Active Directory. Se estiver a utilizar um filtro de palavras-passe de terceiros para impor regras de palavra-passe personalizada, e necessita que este filtro de palavras-passe é verificado durante do Azure AD self-service de reposição de palavra-passe, certifique-se de que a solução de filtro de palavras-passe de terceiros está configurada para ser aplicadas a cenário de reposição de palavra-passe de administrador. [Proteção de palavra-passe do Azure AD para o Windows Server Active Directory](concept-password-ban-bad-on-premises.md) é suportado por predefinição.

## <a name="password-reset-for-b2b-users"></a>Palavra-passe reposta para utilizadores B2B

Alteração e reposição de palavra-passe são totalmente suportadas em todas as configurações do empresa-empresa (B2B). Reposição de palavra-passe de utilizador B2B é suportada nos seguintes três casos:

* **Os utilizadores de uma organização de parceiro com um inquilino do Azure AD existente**: Se nossa parceria com a organização tiver um inquilino do Azure AD existente, podemos *respeitar quaisquer políticas de reposição de palavra-passe estão ativadas nesse inquilino*. Palavra-passe reposta para trabalhar, a organização de parceiro precisa apenas Certifique-se de que o Azure AD SSPR está ativado. Não incorre em encargos adicionais para os clientes do Office 365 e pode ser ativada ao seguir os passos no nosso [introdução à gestão de palavra-passe](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) guia.
* **Os utilizadores que se inscrevam através de** inscrição Self-Service: Utilizado se a organização nossa parceria com o [inscrição Self-Service](../users-groups-roles/directory-self-service-signup.md) de recursos para obter um inquilino, nós deixamos que eles repor a palavra-passe com a mensagem de e-mail que registado.
* **Utilizadores B2B**: Quaisquer novos utilizadores B2B criados com a nova [capacidades do Azure AD B2B](../active-directory-b2b-what-is-azure-ad-b2b.md) também poderão repor as palavras-passe com a mensagem de e-mail que registado durante o processo de convite.

Para testar este cenário, aceda a https://passwordreset.microsoftonline.com com um destes utilizadores de parceiro. Se tiverem um correio eletrónico alternativo ou e-mail de autenticação definidos, a palavra-passe reposição funciona conforme esperado.

> [!NOTE]
> Contas da Microsoft que tem sido concedidas acesso de convidado com o seu inquilino do Azure AD, como as do Hotmail.com, Outlook.com ou outros endereços de e-mail pessoal, não são possível utilizar o Azure AD SSPR. Tem de repor a palavra-passe, utilizando as informações encontradas na [quando não pode iniciar sessão na sua conta Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) artigo.

## <a name="next-steps"></a>Passos Seguintes

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
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/sspr-authentication-methods.png "Métodos de autenticação do Azure AD disponíveis e a quantidade necessária"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "Configuração de integração de repetição de escrita de palavra-passe e informações de resolução de problemas no local"
