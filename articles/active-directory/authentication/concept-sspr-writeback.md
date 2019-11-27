---
title: Integração de write-back de senha local com o Azure AD SSPR-Azure Active Directory
description: Obter senhas de nuvem gravadas novamente na infraestrutura do AD local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 758d7122a991309504c5cac18b9aaf1268808887
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420651"
---
# <a name="what-is-password-writeback"></a>O que é o Write-back de senha?

Ter um utilitário de redefinição de senha baseado em nuvem é ótimo, mas a maioria das empresas ainda tem um diretório local onde seus usuários existem. Como a Microsoft dá suporte à manutenção de Active Directory tradicionais locais (AD) em sincronia com as alterações de senha na nuvem? O Write-back de senha é um recurso habilitado com [Azure ad Connect](../hybrid/whatis-hybrid-identity.md) que permite que as alterações de senha na nuvem sejam gravadas novamente em um diretório local existente em tempo real.

Há suporte para Write-back de senha em ambientes que usam:

* [Serviços de Federação do Active Directory (AD FS)](../hybrid/how-to-connect-fed-management.md)
* [Sincronização de hash de palavra-passe](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Autenticação pass-through](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> O Write-back de senha deixará de funcionar para clientes que estão usando Azure AD Connect versões 1.0.8641.0 e mais antigas quando o [serviço de controle de acesso do Azure (ACS) for desativado em 7 de novembro de 2018](../develop/active-directory-acs-migration.md). Azure AD Connect versões 1.0.8641.0 e mais antigas não permitirão mais write-back de senha nesse momento porque dependem do ACS para essa funcionalidade.
>
> Para evitar uma interrupção no serviço, atualize de uma versão anterior do Azure AD Connect para uma versão mais recente, consulte o artigo [Azure ad Connect: atualizar de uma versão anterior para a mais recente](../hybrid/how-to-upgrade-previous-version.md)
>

O Write-back de senha fornece:

* **Imposição de políticas de senha de Active Directory local**: quando um usuário redefine sua senha, ele é verificado para garantir que ele atenda à sua política de Active Directory local antes de confirmá-la nesse diretório. Essa análise inclui a verificação do histórico, da complexidade, da idade, dos filtros de senha e de quaisquer outras restrições de senha definidas no Active Directory local.
* **Comentários de atraso zero**: o Write-back de senha é uma operação síncrona. Os usuários serão notificados imediatamente se sua senha não atendeu à política ou não pôde ser redefinida ou alterada por qualquer motivo.
* **Dá suporte a alterações de senha do painel de acesso e do Office 365**: quando os usuários federados ou sincronizados com hash de senha são alterados para alterar suas senhas expiradas ou não expiradas, essas senhas são gravadas no ambiente de Active Directory local.
* **Dá suporte ao Write-back de senha quando um administrador os redefine da portal do Azure**: sempre que um administrador redefine a senha de um usuário no [portal do Azure](https://portal.azure.com), se esse usuário for federado ou o hash de senha sincronizado, a senha será gravada no local. Atualmente, não há suporte para essa funcionalidade no portal de administração do Office.
* **Não requer nenhuma regra de firewall de entrada**: o Write-back de senha usa uma retransmissão do barramento de serviço do Azure como um canal de comunicação subjacente. Toda a comunicação é de saída pela porta 443.

> [!NOTE]
> Contas de administrador que existem em grupos protegidos no AD local podem ser usadas com write-back de senha. Os administradores podem alterar sua senha na nuvem, mas não podem usar a redefinição de senha para redefinir uma senha esquecida. Para obter mais informações sobre grupos protegidos, consulte [contas e grupos protegidos no Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

## <a name="licensing-requirements-for-password-writeback"></a>Requisitos de licenciamento para Write-back de senha

**Redefinição/alteração/desbloqueio de senha de autoatendimento com write-back local é um recurso Premium do Azure ad**. Para obter mais informações sobre licenciamento, consulte o [site de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

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
> Os planos de licenciamento do Office 365 autônomos *não dão suporte a "redefinição/alteração/desbloqueio de senha de autoatendimento com write-back local"* e exigem que você tenha um dos planos anteriores para que essa funcionalidade funcione.

## <a name="how-password-writeback-works"></a>Como funciona o Write-back de senha

Quando um usuário federado ou sincronizado com hash de senha tenta redefinir ou alterar sua senha na nuvem, ocorrem as seguintes ações:

1. Uma verificação é executada para ver o tipo de senha que o usuário tem. Se a senha for gerenciada no local:
   * É executada uma verificação para ver se o serviço de write-back está ativo e em execução. Se for, o usuário poderá continuar.
   * Se o serviço de write-back estiver inativo, o usuário será informado de que sua senha não pode ser redefinida no momento.
1. Em seguida, o usuário passa as entradas de autenticação apropriadas e acessa a página **Redefinir senha** .
1. O usuário seleciona uma nova senha e a confirma.
1. Quando o usuário seleciona **Enviar**, a senha de texto sem formatação é criptografada com uma chave simétrica criada durante o processo de configuração de write-back.
1. A senha criptografada é incluída em uma carga que é enviada por um canal HTTPS para a retransmissão do barramento de serviço específica do locatário (que é configurada para você durante o processo de configuração de write-back). Essa retransmissão é protegida por uma senha gerada aleatoriamente que apenas sua instalação local sabe.
1. Depois que a mensagem chega ao barramento de serviço, o ponto de extremidade de redefinição de senha é automaticamente ativado e vê que ele tem uma solicitação de redefinição pendente.
1. O serviço procura o usuário usando o atributo de âncora de nuvem. Para que essa pesquisa seja realizada com sucesso:

   * O objeto de usuário deve existir no espaço do conector de Active Directory.
   * O objeto de usuário deve ser vinculado ao objeto de metaverso (MV) correspondente.
   * O objeto de usuário deve ser vinculado ao objeto de conector de Azure Active Directory correspondente.
   * O link do objeto do conector de Active Directory para o MV deve ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx` no link.
   
   Quando a chamada chega da nuvem, o mecanismo de sincronização usa o atributo **cloudAnchor** para pesquisar o objeto do espaço conector do Azure Active Directory. Em seguida, ele segue o link de volta para o objeto MV e, em seguida, segue o link de volta para o objeto Active Directory. Como pode haver vários objetos de Active Directory (várias florestas) para o mesmo usuário, o mecanismo de sincronização depende do link de `Microsoft.InfromADUserAccountEnabled.xxx` para escolher o correto.

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

* **Retransmissão do barramento de serviço específica do locatário**
   * Quando você configura o serviço, uma retransmissão do barramento de serviço específica do locatário é configurada e protegida por uma senha forte gerada aleatoriamente à qual a Microsoft nunca tem acesso.
* **Chave de criptografia de senha forte e criptograficamente bloqueada**
   * Depois que a retransmissão do barramento de serviço é criada, é criada uma chave simétrica forte que é usada para criptografar a senha à medida que ela é transmitida. Essa chave só reside no repositório secreto da sua empresa na nuvem, que é muito bloqueada e auditada, assim como qualquer outra senha no diretório.
* **Segurança de camada de transporte (TLS) padrão do setor**
   1. Quando uma operação de redefinição ou alteração de senha ocorre na nuvem, a senha de texto sem formatação é criptografada com sua chave pública.
   1. A senha criptografada é colocada em uma mensagem HTTPS enviada por um canal criptografado usando certificados SSL da Microsoft para a retransmissão do barramento de serviço.
   1. Depois que a mensagem chega no barramento de serviço, seu agente local é ativado e autenticado no barramento de serviço usando a senha forte que foi gerada anteriormente.
   1. O agente local pega a mensagem criptografada e descriptografa-a usando a chave privada.
   1. O agente local tenta definir a senha por meio da API de configuração de senha do AD DS. Esta etapa é o que permite a imposição de sua Active Directory política de senha local (como a complexidade, a idade, o histórico e os filtros) na nuvem.
* **Políticas de expiração de mensagem**
   * Se a mensagem estiver no barramento de serviço porque seu serviço local está inoperante, ele expira e é removido após vários minutos. O tempo limite e a remoção da mensagem aumenta ainda mais a segurança.

### <a name="password-writeback-encryption-details"></a>Detalhes de criptografia de write-back de senha

Depois que um usuário envia uma redefinição de senha, a solicitação de redefinição passa por várias etapas de criptografia antes de chegar em seu ambiente local. Essas etapas de criptografia garantem a confiabilidade e a segurança máximas do serviço. Eles são descritos da seguinte maneira:

* **Etapa 1: criptografia de senha com chave RSA de 2048 bits**: depois que um usuário envia uma senha para ser gravada no local, a senha enviada em si é criptografada com uma chave RSA de 2048 bits.
* **Etapa 2: criptografia em nível de pacote com AES-GCM**: o pacote inteiro, a senha mais os metadados necessários, é criptografado usando AES-GCM. Essa criptografia impede que qualquer pessoa com acesso direto ao canal ServiceBus subjacente exiba ou viole o conteúdo.
* **Etapa 3: toda a comunicação ocorre por TLS/SSL**: toda a comunicação com o ServiceBus ocorre em um canal SSL/TLS. Essa criptografia protege o conteúdo de terceiros não autorizados.
* A **chave automática é revertida a cada seis meses**: todas as chaves são transferidas a cada seis meses ou sempre que o Write-back de senha é desabilitado e, em seguida, habilitado novamente no Azure ad Connect, para garantir a segurança máxima do serviço e a segurança.

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

* **Operações do usuário final com suporte**
   * Qualquer operação de alteração de senha voluntária de autoatendimento do usuário final
   * Qualquer operação de alteração de senha de autoatendimento de usuário final, por exemplo, expiração de senha
   * Qualquer redefinição de senha de autoatendimento do usuário final originada no [portal de redefinição de senha](https://passwordreset.microsoftonline.com)
* **Operações de administrador com suporte**
   * Qualquer operação de alteração de senha voluntária de autoatendimento de administrador
   * Qualquer operação de forçar alteração de senha de autoatendimento de administrador, por exemplo, expiração de senha
   * Qualquer redefinição de senha de autoatendimento do administrador originada no [portal de redefinição de senha](https://passwordreset.microsoftonline.com)
   * Qualquer redefinição de senha do usuário final iniciada pelo administrador do [portal do Azure](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Operações de write-back sem suporte

As senhas *não* são gravadas novamente em nenhuma das seguintes situações:

* **Operações do usuário final sem suporte**
   * Qualquer usuário final que redefine sua própria senha usando o PowerShell versão 1, versão 2 ou o Azure AD API do Graph
* **Operações de administrador sem suporte**
   * Qualquer redefinição de senha do usuário final iniciada pelo administrador do PowerShell versão 1, versão 2 ou do Azure AD API do Graph
   * Qualquer redefinição de senha do usuário final iniciada pelo administrador no [centro de administração Microsoft 365](https://admin.microsoft.com)

> [!WARNING]
> Use a caixa de seleção "o usuário deve alterar a senha no próximo logon" no local Active Directory ferramentas administrativas como Active Directory usuários e computadores ou o Centro Administrativo do Active Directory tem suporte como um recurso de visualização do Azure AD Connect. Para obter mais informações, consulte o artigo [implementar a sincronização de hash de senha com Azure ad Connect sincronização](../hybrid/how-to-connect-password-hash-synchronization.md#public-preview-of-synchronizing-temporary-passwords-and-force-password-on-next-logon).

## <a name="next-steps"></a>Passos seguintes

Habilitar write-back de senha usando o tutorial: [habilitando o Write-back de senha](tutorial-enable-writeback.md)
