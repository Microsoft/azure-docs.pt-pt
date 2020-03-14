---
title: No local, a integração de reescrita de palavra-passe com a Azure AD SSPR - Diretório Ativo Azure
description: Obtenha senhas de nuvem escritas de volta para a infraestrutura de AD no local
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
ms.openlocfilehash: aa66299753ab11dcad280361cb5fb6f0c31ef242
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263923"
---
# <a name="what-is-password-writeback"></a>O que é a redação da palavra-passe?

Ter um utilitário de redefinição de senha baseado na nuvem é ótimo, mas a maioria das empresas ainda tem um diretório no local onde os seus utilizadores existem. Como é que a Microsoft suporta manter o Diretório Ativo (AD) tradicional no local em sincronização com as alterações de password na nuvem? A reescrita de palavra-passe é uma funcionalidade ativada com [o Azure AD Connect](../hybrid/whatis-hybrid-identity.md) que permite que as alterações de palavra-passe na nuvem sejam reescritas para um diretório existente no local em tempo real.

A reescrita de palavra-passe é suportada em ambientes que utilizam:

* [Serviços de Federação do Active Directory (AD FS)](../hybrid/how-to-connect-fed-management.md)
* [Sincronização de hash de palavra-passe](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Autenticação pass-through](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> A reversão da palavra-passe deixará de funcionar para os clientes que estão a utilizar as versões Azure AD Connect 1.0.8641.0 e mais antigas quando o serviço de Controlo de [Acesso Azure (ACS) for retirado a 7 de novembro de 2018.](../azuread-dev/active-directory-acs-migration.md) As versões Azure AD Connect 1.0.8641.0 e mais antigas deixarão de permitir a reescrita de palavra-passe nessa altura, porque dependem do ACS para essa funcionalidade.
>
> Para evitar uma perturbação no serviço, atualize de uma versão anterior do Azure AD Connect para uma versão mais recente, consulte o artigo [Azure AD Connect: Upgrade de uma versão anterior para a mais recente](../hybrid/how-to-upgrade-previous-version.md)
>

A reescrita da palavra-passe fornece:

* **Aplicação das políticas de senha**de diretório ativo no local : Quando um utilizador repõe a sua palavra-passe, é verificado para garantir que cumpre a sua política de Diretório Ativo no local antes de a comprometer com esse diretório. Esta análise inclui a verificação do histórico, complexidade, idade, filtros de senha e quaisquer outras restrições de senha que tenha definido no Diretório Ativo local.
* **Feedback de atraso zero**: A reescrita da palavra-passe é uma operação sincronizada. Os seus utilizadores são imediatamente notificados se a sua palavra-passe não cumpriu a apólice ou não puder ser redefinida ou alterada por qualquer motivo.
* **Suporta alterações de palavra-passe do painel de acesso e do Office 365**: Quando os utilizadores sincronizados com hash federados ou passwords vêm alterar as suas palavras-passe expiradas ou não expiradas, essas palavras-passe são reescritas para o ambiente do Diretório Ativo local.
* Suporta a **redação da palavra-passe quando um administrador os repõe do portal Azure**: Sempre que um administrador repõe a palavra-passe de um utilizador no [portal Azure](https://portal.azure.com), se esse utilizador for federado ou a palavra-passe sincronizada, a palavra-passe é reescrita para as instalações. Esta funcionalidade não é atualmente suportada no portal de administração do Office.
* **Não requer nenhuma regra**de firewall de entrada : A reescrita de palavra-passe utiliza um relé De ônibus de serviço Azure como um canal de comunicação subjacente. Toda a comunicação está de saída sobre a porta 443.

> [!NOTE]
> As contas de administrador que existam dentro de grupos protegidos no local a d.A. podem ser utilizadas com redação de palavra-passe. Os administradores podem alterar a sua palavra-passe na nuvem, mas não podem usar o reset da palavra-passe para redefinir uma palavra-passe esquecida. Para obter mais informações sobre grupos protegidos, consulte [contas protegidas e grupos em Diretório Ativo](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

## <a name="licensing-requirements-for-password-writeback"></a>Requisitos de licenciamento para a reescrita de palavra-passe

**Reset/Change/Unlock com a reescrita de palavras-passe de self-service é uma característica premium do Azure AD**. Para mais informações sobre o licenciamento, consulte o site de [preços do Diretório Ativo Azure.](https://azure.microsoft.com/pricing/details/active-directory/)

Para utilizar a reescrita da palavra-passe, deve ter uma das seguintes licenças atribuídas ao seu inquilino:

* Azure AD Premium P1
* Azure AD Premium P2
* Mobilidade Empresarial + Segurança E3 ou A3
* Mobilidade Empresarial + Segurança E5 ou A5
* Microsoft 365 E3 ou A3
* Microsoft 365 E5 ou A5
* Microsoft 365 F1
* Microsoft 365 Empresas

> [!WARNING]
> Os planos de licenciamento autónomos do Office 365 *não suportam "Reset/Change/Unlock with on-premis"* e exigem que tenha um dos planos anteriores para que esta funcionalidade funcione.

## <a name="how-password-writeback-works"></a>Como funciona a repetição de escrita de palavras-passe

Quando um utilizador sincronizado de hash federado ou senha tenta redefinir ou alterar a sua palavra-passe na nuvem, ocorrem as seguintes ações:

1. É feita uma verificação para ver que tipo de senha o utilizador tem. Se a palavra-passe for gerida no local:
   * É feita uma verificação para ver se o serviço de redação está a funcionar. Se for, o utilizador pode prosseguir.
   * Se o serviço de redisões estiver em baixo, o utilizador é informado de que a sua palavra-passe não pode ser redefinida neste momento.
1. Em seguida, o utilizador passa nos portões de autenticação apropriados e chega à página de **palavra-passe Reset.**
1. O utilizador seleciona uma nova senha e confirma-a.
1. Quando o utilizador seleciona **Enviar,** a palavra-passe de texto simples é encriptada com uma chave simétrica criada durante o processo de configuração de reprodução.
1. A palavra-passe encriptada está incluída numa carga útil que é enviada por um canal HTTPS para o seu retransmissor de ônibus de serviço específico do seu inquilino (que é configurado para si durante o processo de configuração de redação). Este retransmissor está protegido por uma senha gerada aleatoriamente que só a sua instalação no local sabe.
1. Depois de a mensagem chegar ao autocarro de serviço, o ponto final de reset de palavra-passe acorda automaticamente e vê que tem um pedido de reset pendente.
1. O serviço procura então o utilizador utilizando o atributo de âncora em nuvem. Para que esta procura tenha sucesso:

   * O objeto do utilizador deve existir no espaço do conector ative diretório.
   * O objeto do utilizador deve ser ligado ao objeto metaverso correspondente (MV).
   * O objeto do utilizador deve estar ligado ao objeto de conector Ativo azure correspondente.
   * O link do conector ative diretório para o MV deve ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx` no link.
   
   Quando a chamada chega da nuvem, o motor de sincronização usa o atributo **cloudAnchor** para procurar o objeto espacial do conector Azure Ative Diretório. Segue então a ligação de volta ao objeto MV e, em seguida, segue a ligação de volta ao objeto de Diretório Ativo. Como pode haver vários objetos de Diretório Ativo (multi-floresta) para o mesmo utilizador, o motor sincronizado baseia-se no link `Microsoft.InfromADUserAccountEnabled.xxx` para escolher o correto.

1. Após a descoberta da conta de utilizador, é feita uma tentativa de redefinir a palavra-passe diretamente na floresta de Diretório Ativo apropriado.
1. Se a operação de conjunto de passwords for bem sucedida, o utilizador é informado de que a sua palavra-passe foi alterada.
   > [!NOTE]
   > Se o hash de senha do utilizador for sincronizado para a AD Azure utilizando a sincronização de hash de palavra-passe, existe a possibilidade de a política de senha no local ser mais fraca do que a política de palavra-passe em nuvem. Neste caso, a política no local é aplicada. Esta política garante que a sua política no local seja aplicada na nuvem, não importa se utilizar a sincronização de hash de senha ou a federação para fornecer um único sinal.

1. Se a operação de conjunto de palavra-passe falhar, um erro leva o utilizador a tentar novamente. A operação pode falhar porque:
    * O serviço foi para baixo.
    * A palavra-passe que escolheram não cumpriu as políticas da organização.
    * Incapaz de encontrar o utilizador no Diretório Ativo local.

      As mensagens de erro fornecem orientações aos utilizadores para que possam tentar resolver sem a intervenção do administrador.

## <a name="password-writeback-security"></a>Segurança de redação de palavra-passe

A reescrita de palavra-passe é um serviço altamente seguro. Para garantir que a sua informação está protegida, um modelo de segurança de quatro camadas está ativado como o seguinte descreve:

* **Retransmissão de ônibus de serviço específico para inquilinos**
   * Quando configura o serviço, é criado um retransmissor de ônibus de serviço específico para inquilinos que está protegido por uma senha forte gerada aleatoriamente a que a Microsoft nunca tem acesso.
* **Bloqueado, criptograficamente forte, chave de encriptação de senha**
   * Após a criação do relé de ônibus de serviço, é criada uma chave simétrica forte que é usada para encriptar a palavra-passe à medida que vem sobre o fio. Esta chave só vive na loja secreta da sua empresa na nuvem, que é fortemente bloqueada e auditada, como qualquer outra palavra-passe no diretório.
* **Segurança da camada de transporte padrão da indústria (TLS)**
   1. Quando ocorre uma redefinição de palavra-passe ou operação de alteração na nuvem, a palavra-passe de texto simples é encriptada com a sua chave pública.
   1. A palavra-passe encriptada é colocada numa mensagem HTTPS que é enviada através de um canal encriptado utilizando certs Microsoft SSL para o seu retransmissor de ônibus de serviço.
   1. Depois de a mensagem chegar no autocarro de serviço, o seu agente no local acorda e autentica-se no autocarro de serviço utilizando a senha forte que foi gerada anteriormente.
   1. O agente no local capta a mensagem encriptada e desencripta-a utilizando a chave privada.
   1. O agente no local tenta definir a palavra-passe através da AD DS SetPassword API. Este passo é o que permite a aplicação da sua política de senhas de diretório ativo no local (como a complexidade, idade, história e filtros) na nuvem.
* **Políticas de expiração da mensagem**
   * Se a mensagem estiver no autocarro de serviço porque o seu serviço no local está em baixo, ele tem tempo para fora e é removido após vários minutos. O tempo de paragem e remoção da mensagem aumenta ainda mais a segurança.

### <a name="password-writeback-encryption-details"></a>Detalhes de encriptação de redação de palavra-passe

Depois de um utilizador submeter uma reposição de palavra-passe, o pedido de reset passa por vários passos de encriptação antes de chegar ao seu ambiente no local. Estes passos de encriptação garantem a máxima fiabilidade e segurança do serviço. São descritos da seguinte forma:

* **Passo 1: Encriptação de palavra-passe com chave RSA de 2048:** Depois de um utilizador submeter uma palavra-passe a ser redigida no local, a senha submetida em si é encriptada com uma chave RSA de 2048 bits.
* **Passo 2: Encriptação de nível de pacote com AES-GCM**: Todo o pacote, a palavra-passe mais os metadados necessários, é encriptado utilizando a AES-GCM. Esta encriptação impede qualquer pessoa com acesso direto ao canal ServiceBus subjacente de visualizar ou adulterar o conteúdo.
* **Passo 3: Toda a comunicação ocorre sobre TLS/SSL**: Toda a comunicação com o ServiceBus ocorre num canal SSL/TLS. Esta encriptação protege o conteúdo de terceiros não autorizados.
* **Chave automática rola de seis em seis meses:** Todas as teclas rolam de seis em seis meses, ou cada vez que a redação da palavra-passe é desativada e, em seguida, reativada no Azure AD Connect, para garantir a máxima segurança e segurança do serviço.

### <a name="password-writeback-bandwidth-usage"></a>Utilização da largura de banda de redação de palavras-passe

A reescrita de palavra-passe é um serviço de baixa largura de banda que apenas envia pedidos de volta para o agente no local nas seguintes circunstâncias:

* São enviadas duas mensagens quando a funcionalidade está ativada ou desativada através do Azure AD Connect.
* Uma mensagem é enviada uma vez a cada cinco minutos como batimento cardíaco de serviço enquanto o serviço estiver em funcionamento.
* São enviadas duas mensagens cada vez que é submetida uma nova senha:
   * A primeira mensagem é um pedido para realizar a operação.
   * A segunda mensagem contém o resultado da operação e é enviada nas seguintes circunstâncias:
      * Cada vez que uma nova senha é submetida durante um reset de senha de autosserviço do utilizador.
      * Cada vez que uma nova palavra-passe é submetida durante uma operação de alteração de palavra-passe do utilizador.
      * Cada vez que uma nova senha é submetida durante um reset de palavra-passe de utilizador iniciado pela administração (apenas a partir dos portais de administração Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Considerações de tamanho de mensagem e largura de banda

O tamanho de cada uma das mensagens descritas anteriormente é tipicamente inferior a 1 KB. Mesmo sob cargas extremas, o serviço de redação de palavra-passe em si está a consumir alguns quilobits por segundo de largura de banda. Como cada mensagem é enviada em tempo real, apenas quando exigida por uma operação de atualização de passwords, e porque o tamanho da mensagem é tão pequeno, o uso da largura de banda da capacidade de redação é demasiado pequeno para ter um impacto mensurável.

## <a name="supported-writeback-operations"></a>Operações de redisões apoiadas

As palavras-passe são redigidas em todas as seguintes situações:

* **Operações de utilizador final suportadas**
   * Qualquer operação voluntária de alteração voluntária de autosserviço do utilizador final
   * Qualquer força de autosserviço de utilizador final altere a operação de senha, por exemplo, a expiração da palavra-passe
   * Qualquer redefinição da palavra-passe de autosserviço do utilizador final que tenha origem no portal de reset de [palavra-passe](https://passwordreset.microsoftonline.com)
* **Operações de administrador apoiados**
   * Qualquer operação de mudança voluntária de autosserviço do administrador
   * Qualquer força de autosserviço do administrador altere a operação de senha, por exemplo, a expiração da palavra-passe
   * Qualquer reset de palavra-passe de autosserviço do administrador que tenha origem no portal de reset de [palavra-passe](https://passwordreset.microsoftonline.com)
   * Qualquer palavra-passe de utilizador final iniciado por administrador do [portal Azure](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Operações de redisonam não apoiadas

As palavras-passe *não* são redigidas em nenhuma das seguintes situações:

* **Operações não suportadas de utilizador final**
   * Qualquer utilizador final que reajuste a sua própria palavra-passe utilizando a versão 1, a versão 2 ou a Microsoft Graph API
* **Operações de administrador não apoiados**
   * Qualquer palavra-passe de utilizador final iniciado pelo administrador a partir da versão 1, versão 2 da PowerShell ou da Microsoft Graph API
   * Qualquer palavra-passe de utilizador final iniciado por administrador do centro de administração da [Microsoft 365](https://admin.microsoft.com)

> [!WARNING]
> A utilização da caixa de verificação "O utilizador deve alterar a palavra-passe no próximo início de sessão" em ferramentas administrativas de Diretório Ativo no local, como utilizadores e computadores de diretório ativo ou o Ative Directory Administrative Center é suportado como uma funcionalidade de pré-visualização do Azure AD Connect. Para mais informações, consulte o artigo, Implemente a sincronização de hash de [palavra-passe com a sincronização Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Passos Seguintes

Ativar a reescrita da palavra-passe utilizando o Tutorial: Permitir a reescrita da [palavra-passe](tutorial-enable-writeback.md)
