---
title: Integração de write-back de senha local com o Azure AD SSPR-Azure Active Directory
description: Obter senhas de nuvem gravadas novamente na infraestrutura do AD local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c92048d2fce4a098da1e707ec8f7d75479d563f1
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161584"
---
# <a name="what-is-password-writeback"></a>O que é o Write-back de senha?

Ter um utilitário de redefinição de senha baseado em nuvem é ótimo, mas a maioria das empresas ainda tem um diretório local onde seus usuários existem. Como a Microsoft dá suporte à manutenção de Active Directory tradicionais locais (AD) em sincronia com as alterações de senha na nuvem? A reescrita de palavra-passe é uma funcionalidade ativada com [o Azure AD Connect](../hybrid/whatis-hybrid-identity.md) que permite que as alterações de palavra-passe na nuvem sejam reescritas para um diretório existente no local em tempo real.

Há suporte para Write-back de senha em ambientes que usam:

* [Serviços de Federação do Active Directory (AD FS)](../hybrid/how-to-connect-fed-management.md)
* [Sincronização de hash de palavra-passe](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Autenticação pass-through](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> A reversão da palavra-passe deixará de funcionar para os clientes que estão a utilizar as versões Azure AD Connect 1.0.8641.0 e mais antigas quando o serviço de Controlo de [Acesso Azure (ACS) for retirado a 7 de novembro de 2018.](../azuread-dev/active-directory-acs-migration.md) Azure AD Connect versões 1.0.8641.0 e mais antigas não permitirão mais write-back de senha nesse momento porque dependem do ACS para essa funcionalidade.
>
> Para evitar uma perturbação no serviço, atualize de uma versão anterior do Azure AD Connect para uma versão mais recente, consulte o artigo [Azure AD Connect: Upgrade de uma versão anterior para a mais recente](../hybrid/how-to-upgrade-previous-version.md)
>

O Write-back de senha fornece:

* **Aplicação das políticas de senha**de diretório ativo no local : Quando um utilizador repõe a sua palavra-passe, é verificado para garantir que cumpre a sua política de Diretório Ativo no local antes de a comprometer com esse diretório. Essa análise inclui a verificação do histórico, da complexidade, da idade, dos filtros de senha e de quaisquer outras restrições de senha definidas no Active Directory local.
* **Feedback de atraso zero**: A reescrita da palavra-passe é uma operação sincronizada. Os usuários serão notificados imediatamente se sua senha não atendeu à política ou não pôde ser redefinida ou alterada por qualquer motivo.
* **Suporta alterações de palavra-passe do painel de acesso e do Office 365**: Quando os utilizadores sincronizados com hash federados ou passwords vêm alterar as suas palavras-passe expiradas ou não expiradas, essas palavras-passe são reescritas para o ambiente do Diretório Ativo local.
* Suporta a **redação da palavra-passe quando um administrador os repõe do portal Azure**: Sempre que um administrador repõe a palavra-passe de um utilizador no [portal Azure](https://portal.azure.com), se esse utilizador for federado ou a palavra-passe sincronizada, a palavra-passe é reescrita para as instalações. Atualmente, não há suporte para essa funcionalidade no portal de administração do Office.
* **Não requer nenhuma regra**de firewall de entrada : A reescrita de palavra-passe utiliza um relé De ônibus de serviço Azure como um canal de comunicação subjacente. Toda a comunicação é de saída pela porta 443.

> [!NOTE]
> Contas de administrador que existem em grupos protegidos no AD local podem ser usadas com write-back de senha. Os administradores podem alterar sua senha na nuvem, mas não podem usar a redefinição de senha para redefinir uma senha esquecida. Para obter mais informações sobre grupos protegidos, consulte [contas protegidas e grupos em Diretório Ativo](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

## <a name="licensing-requirements-for-password-writeback"></a>Requisitos de licenciamento para Write-back de senha

**Reset/Change/Unlock com a reescrita de palavras-passe de self-service é uma característica premium do Azure AD**. Para mais informações sobre o licenciamento, consulte o site de [preços do Diretório Ativo Azure.](https://azure.microsoft.com/pricing/details/active-directory/)

Para usar o Write-back de senha, você deve ter uma das seguintes licenças atribuídas em seu locatário:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 ou a3
* Enterprise Mobility + Security E5 ou a5
* Microsoft 365 E3 ou a3
* Microsoft 365 E5 ou a5
* Microsoft 365 F1
* Microsoft 365 Empresas

> [!WARNING]
> Os planos de licenciamento autónomos do Office 365 *não suportam "Reset/Change/Unlock with on-premis"* e exigem que tenha um dos planos anteriores para que esta funcionalidade funcione.

## <a name="how-password-writeback-works"></a>Como funciona o Write-back de senha

Quando um usuário federado ou sincronizado com hash de senha tenta redefinir ou alterar sua senha na nuvem, ocorrem as seguintes ações:

1. Uma verificação é executada para ver o tipo de senha que o usuário tem. Se a senha for gerenciada no local:
   * É executada uma verificação para ver se o serviço de write-back está ativo e em execução. Se for, o usuário poderá continuar.
   * Se o serviço de write-back estiver inativo, o usuário será informado de que sua senha não pode ser redefinida no momento.
1. Em seguida, o utilizador passa nos portões de autenticação apropriados e chega à página de **palavra-passe Reset.**
1. O usuário seleciona uma nova senha e a confirma.
1. Quando o utilizador seleciona **Enviar,** a palavra-passe de texto simples é encriptada com uma chave simétrica criada durante o processo de configuração de reprodução.
1. A senha criptografada é incluída em uma carga que é enviada por um canal HTTPS para a retransmissão do barramento de serviço específica do locatário (que é configurada para você durante o processo de configuração de write-back). Essa retransmissão é protegida por uma senha gerada aleatoriamente que apenas sua instalação local sabe.
1. Depois que a mensagem chega ao barramento de serviço, o ponto de extremidade de redefinição de senha é automaticamente ativado e vê que ele tem uma solicitação de redefinição pendente.
1. O serviço procura o usuário usando o atributo de âncora de nuvem. Para que essa pesquisa seja realizada com sucesso:

   * O objeto de usuário deve existir no espaço do conector de Active Directory.
   * O objeto de usuário deve ser vinculado ao objeto de metaverso (MV) correspondente.
   * O objeto de usuário deve ser vinculado ao objeto de conector de Azure Active Directory correspondente.
   * O link do conector ative diretório para o MV deve ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx` no link.
   
   Quando a chamada chega da nuvem, o motor de sincronização usa o atributo **cloudAnchor** para procurar o objeto espacial do conector Azure Ative Diretório. Em seguida, ele segue o link de volta para o objeto MV e, em seguida, segue o link de volta para o objeto Active Directory. Como pode haver vários objetos de Diretório Ativo (multi-floresta) para o mesmo utilizador, o motor sincronizado baseia-se no link `Microsoft.InfromADUserAccountEnabled.xxx` para escolher o correto.

1. Depois que a conta de usuário for encontrada, uma tentativa de redefinir a senha diretamente na floresta de Active Directory apropriada será feita.
1. Se a operação de definição de senha for bem-sucedida, o usuário será informado de que sua senha foi alterada.
   > [!NOTE]
   > Se o hash de senha do usuário for sincronizado com o Azure AD usando a sincronização de hash de senha, haverá a possibilidade de que a política de senha local seja mais fraca do que a política de senha de nuvem. Nesse caso, a política local é imposta. Essa política garante que sua política local seja imposta na nuvem, não importa se você usar a sincronização de hash de senha ou a Federação para fornecer logon único.

1. Se a operação de definição de senha falhar, um erro solicitará que o usuário tente novamente. A operação pode falhar porque:
    * O serviço estava inoperante.
    * A senha selecionada não atendeu às políticas da organização.
    * Não é possível localizar o usuário no Active Directory local.

      As mensagens de erro fornecem orientação aos usuários para que eles possam tentar resolver sem a intervenção do administrador.

## <a name="password-writeback-security"></a>Segurança de write-back de senha

O Write-back de senha é um serviço altamente seguro. Para garantir que suas informações estejam protegidas, um modelo de segurança de quatro camadas é habilitado como descrito a seguir:

* **Retransmissão de ônibus de serviço específico para inquilinos**
   * Quando você configura o serviço, uma retransmissão do barramento de serviço específica do locatário é configurada e protegida por uma senha forte gerada aleatoriamente à qual a Microsoft nunca tem acesso.
* **Bloqueado, criptograficamente forte, chave de encriptação de senha**
   * Depois que a retransmissão do barramento de serviço é criada, é criada uma chave simétrica forte que é usada para criptografar a senha à medida que ela é transmitida. Essa chave só reside no repositório secreto da sua empresa na nuvem, que é muito bloqueada e auditada, assim como qualquer outra senha no diretório.
* **Segurança da camada de transporte padrão da indústria (TLS)**
   1. Quando uma operação de redefinição ou alteração de senha ocorre na nuvem, a senha de texto sem formatação é criptografada com sua chave pública.
   1. A senha criptografada é colocada em uma mensagem HTTPS enviada por um canal criptografado usando certificados SSL da Microsoft para a retransmissão do barramento de serviço.
   1. Depois que a mensagem chega no barramento de serviço, seu agente local é ativado e autenticado no barramento de serviço usando a senha forte que foi gerada anteriormente.
   1. O agente local pega a mensagem criptografada e descriptografa-a usando a chave privada.
   1. O agente local tenta definir a senha por meio da API de configuração de senha do AD DS. Esta etapa é o que permite a imposição de sua Active Directory política de senha local (como a complexidade, a idade, o histórico e os filtros) na nuvem.
* **Políticas de expiração da mensagem**
   * Se a mensagem estiver no barramento de serviço porque seu serviço local está inoperante, ele expira e é removido após vários minutos. O tempo limite e a remoção da mensagem aumenta ainda mais a segurança.

### <a name="password-writeback-encryption-details"></a>Detalhes de criptografia de write-back de senha

Depois que um usuário envia uma redefinição de senha, a solicitação de redefinição passa por várias etapas de criptografia antes de chegar em seu ambiente local. Essas etapas de criptografia garantem a confiabilidade e a segurança máximas do serviço. Eles são descritos da seguinte maneira:

* **Passo 1: Encriptação de palavra-passe com chave RSA de 2048:** Depois de um utilizador submeter uma palavra-passe a ser redigida no local, a senha submetida em si é encriptada com uma chave RSA de 2048 bits.
* **Passo 2: Encriptação de nível de pacote com AES-GCM**: Todo o pacote, a palavra-passe mais os metadados necessários, é encriptado utilizando a AES-GCM. Essa criptografia impede que qualquer pessoa com acesso direto ao canal ServiceBus subjacente exiba ou viole o conteúdo.
* **Passo 3: Toda a comunicação ocorre sobre TLS/SSL**: Toda a comunicação com o ServiceBus ocorre num canal SSL/TLS. Essa criptografia protege o conteúdo de terceiros não autorizados.
* **Chave automática rola de seis em seis meses:** Todas as teclas rolam de seis em seis meses, ou cada vez que a redação da palavra-passe é desativada e, em seguida, reativada no Azure AD Connect, para garantir a máxima segurança e segurança do serviço.

### <a name="password-writeback-bandwidth-usage"></a>Uso de largura de banda de write-back

O Write-back de senha é um serviço de baixa largura de banda que envia solicitações de volta para o agente local nas seguintes circunstâncias:

* Duas mensagens são enviadas quando o recurso é habilitado ou desabilitado por meio de Azure AD Connect.
* Uma mensagem é enviada uma vez a cada cinco minutos como uma pulsação de serviço, desde que o serviço esteja em execução.
* Duas mensagens são enviadas cada vez que uma nova senha é enviada:
   * A primeira mensagem é uma solicitação para executar a operação.
   * A segunda mensagem contém o resultado da operação e é enviada nas seguintes circunstâncias:
      * Cada vez que uma nova senha é enviada durante uma redefinição de senha de autoatendimento do usuário.
      * Cada vez que uma nova senha é enviada durante uma operação de alteração de senha do usuário.
      * Cada vez que uma nova senha é enviada durante uma redefinição de senha de usuário iniciada pelo administrador (somente dos portais de administração do Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Considerações sobre tamanho da mensagem e largura de banda

O tamanho de cada uma das mensagens descritas anteriormente geralmente é inferior a 1 KB. Mesmo sob cargas extremas, o próprio serviço de write-back de senha está consumindo alguns kilobits por segundo de largura de banda. Como cada mensagem é enviada em tempo real, somente quando exigido por uma operação de atualização de senha, e como o tamanho da mensagem é tão pequeno, o uso da largura de banda do recurso de write-back é muito pequeno para ter um impacto mensurável.

## <a name="supported-writeback-operations"></a>Operações de write-back com suporte

As senhas são gravadas novamente em todas as seguintes situações:

* **Operações de utilizador final suportadas**
   * Qualquer operação de alteração de senha voluntária de autoatendimento do usuário final
   * Qualquer operação de alteração de senha de autoatendimento de usuário final, por exemplo, expiração de senha
   * Qualquer redefinição da palavra-passe de autosserviço do utilizador final que tenha origem no portal de reset de [palavra-passe](https://passwordreset.microsoftonline.com)
* **Operações de administrador apoiados**
   * Qualquer operação de alteração de senha voluntária de autoatendimento de administrador
   * Qualquer operação de forçar alteração de senha de autoatendimento de administrador, por exemplo, expiração de senha
   * Qualquer reset de palavra-passe de autosserviço do administrador que tenha origem no portal de reset de [palavra-passe](https://passwordreset.microsoftonline.com)
   * Qualquer palavra-passe de utilizador final iniciado por administrador do [portal Azure](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Operações de write-back sem suporte

As palavras-passe *não* são redigidas em nenhuma das seguintes situações:

* **Operações não suportadas de utilizador final**
   * Qualquer usuário final que redefine sua própria senha usando o PowerShell versão 1, versão 2 ou o Azure AD API do Graph
* **Operações de administrador não apoiados**
   * Qualquer redefinição de senha do usuário final iniciada pelo administrador do PowerShell versão 1, versão 2 ou do Azure AD API do Graph
   * Qualquer palavra-passe de utilizador final iniciado por administrador do centro de administração da [Microsoft 365](https://admin.microsoft.com)

> [!WARNING]
> Use a caixa de seleção "o usuário deve alterar a senha no próximo logon" no local Active Directory ferramentas administrativas como Active Directory usuários e computadores ou o Centro Administrativo do Active Directory tem suporte como um recurso de visualização do Azure AD Connect. Para mais informações, consulte o artigo, Implemente a sincronização de hash de [palavra-passe com a sincronização Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md#public-preview-of-synchronizing-temporary-passwords-and-force-password-change-on-next-logon).

## <a name="next-steps"></a>Passos seguintes

Ativar a reescrita da palavra-passe utilizando o Tutorial: Permitir a reescrita da [palavra-passe](tutorial-enable-writeback.md)
