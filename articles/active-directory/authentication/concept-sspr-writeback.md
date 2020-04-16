---
title: Reescrita de palavra-passe no local com redefinição de senha de autosserviço - Diretório Ativo Azure
description: Saiba como a mudança de palavra-passe ou o reset de eventos no Diretório Ativo do Azure pode ser reescrito para um ambiente de diretório no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 787c15c11c995c7eb30662131302658175c7f877
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393027"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Como é que a palavra-passe de autosserviço repõe o reescrita no Diretório Ativo do Azure?

O reset de senha de autosserviço do Azure Ative Directory (Azure AD) permite que os utilizadores resetas sedem as suas palavras-passe na nuvem, mas a maioria das empresas também tem um ambiente de Serviços de Domínio de Diretório Ativo (AD DS) no local onde os seus utilizadores existem. A reescrita de palavra-passe é uma funcionalidade ativada com [o Azure AD Connect](../hybrid/whatis-hybrid-identity.md) que permite que as alterações de palavra-passe na nuvem sejam reescritas para um diretório existente no local em tempo real. Nesta configuração, à medida que os utilizadores alteram ou redefiniram as suas palavras-passe utilizando SSPR na nuvem, as palavras-passe atualizadas também foram reescritas para o ambiente AD DS no local.

A reescrita de palavra-passe é suportada em ambientes que utilizam os seguintes modelos de identidade híbrida:

* [Sincronização de hash de palavra-passe](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Autenticação pass-through](../hybrid/how-to-connect-pta.md)
* [Serviços de Federação do Active Directory](../hybrid/how-to-connect-fed-management.md)

A reescrita da palavra-passe fornece as seguintes funcionalidades:

* Aplicação das políticas de password sinuosas de **domínio ativo (AD DS)** no local : Quando um utilizador repõe a sua palavra-passe, é verificado para garantir que cumpre a sua política de DS ad no local antes de a comprometer com esse diretório. Esta análise inclui a verificação do histórico, complexidade, idade, filtros de senha e quaisquer outras restrições de senha que defina em DS AD.
* **Feedback de atraso zero**: A reescrita da palavra-passe é uma operação sincronizada. Os utilizadores são notificados imediatamente se a sua palavra-passe não cumprir a apólice ou não puder ser redefinida ou alterada por qualquer motivo.
* **Suporta alterações de palavra-passe do painel de acesso e do Office 365**: Quando os utilizadores sincronizados de hash federados ou de palavra-passe vêm alterar as suas palavras-passe expiradas ou não expiradas, essas palavras-passe são reescritas em AD DS.
* Suporta a reescrita da **palavra-passe quando um administrador os repõe do portal Azure**: Quando um administrador repõe a palavra-passe de um utilizador no [portal Azure](https://portal.azure.com), se esse utilizador for federado ou a palavra-passe sincronizada, a palavra-passe é reescrita para as instalações. Esta funcionalidade não é atualmente suportada no portal de administração do Office.
* **Não requer nenhuma regra**de firewall de entrada : A reescrita de palavra-passe utiliza um relé De ônibus de serviço Azure como um canal de comunicação subjacente. Toda a comunicação está de saída sobre a porta 443.

> [!NOTE]
> As contas de administrador que existam dentro de grupos protegidos no local a d.A. podem ser utilizadas com redação de palavra-passe. Os administradores podem alterar a sua palavra-passe na nuvem, mas não podem usar o reset da palavra-passe para redefinir uma palavra-passe esquecida. Para obter mais informações sobre grupos protegidos, consulte [contas protegidas e grupos em Diretório Ativo](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

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
1. O serviço procura então o utilizador utilizando o atributo de âncora em nuvem. Para que esta procura seja bem sucedida, devem ser satisfeitas as seguintes condições:

   * O objeto do utilizador deve existir no espaço do conector ative diretório.
   * O objeto do utilizador deve ser ligado ao objeto metaverso correspondente (MV).
   * O objeto do utilizador deve estar ligado ao objeto de conector Ativo azure correspondente.
   * O link do objeto do conector ative diretório para `Microsoft.InfromADUserAccountEnabled.xxx` o MV deve ter a regra de sincronização no link.

   Quando a chamada chega da nuvem, o motor de sincronização usa o atributo **cloudAnchor** para procurar o objeto espacial do conector Azure Ative Diretório. Segue então a ligação de volta ao objeto MV e, em seguida, segue a ligação de volta ao objeto de Diretório Ativo. Como pode haver vários objetos ative diretório (multi-floresta) para o `Microsoft.InfromADUserAccountEnabled.xxx` mesmo utilizador, o motor sincronizado depende do link para escolher o correto.

1. Após a descoberta da conta de utilizador, é feita uma tentativa de redefinir a palavra-passe diretamente na floresta de Diretório Ativo apropriado.
1. Se a operação de conjunto de passwords for bem sucedida, o utilizador é informado de que a sua palavra-passe foi alterada.

   > [!NOTE]
   > Se o hash de senha do utilizador for sincronizado para o Azure AD utilizando a sincronização de hash de palavra-passe, há a possibilidade de a política de senha no local ser mais fraca do que a política de palavra-passe em nuvem. Neste caso, a política no local é aplicada. Esta política garante que a sua política no local seja aplicada na nuvem, não importa se utilizar a sincronização de hash de senha ou a federação para fornecer um único sinal.

1. Se a operação de conjunto de palavra-passe falhar, um erro leva o utilizador a tentar novamente. A operação pode falhar devido às seguintes razões:
    * O serviço foi para baixo.
    * A palavra-passe que escolheram não cumpre as políticas da organização.
    * Incapaz de encontrar o utilizador no Diretório Ativo local.

   As mensagens de erro fornecem orientações aos utilizadores para que possam tentar resolver sem a intervenção do administrador.

## <a name="password-writeback-security"></a>Segurança de redação de palavra-passe

A reescrita de palavra-passe é um serviço altamente seguro. Para garantir que a sua informação está protegida, um modelo de segurança de quatro camadas está ativado da seguinte forma:

* **Retransmissão de ônibus de serviço específico para inquilinos**
   * Quando configura o serviço, é criado um retransmissor de ônibus de serviço específico para inquilinos que está protegido por uma senha forte gerada aleatoriamente a que a Microsoft nunca tem acesso.
* **Bloqueado, criptograficamente forte, chave de encriptação de senha**
   * Após a criação do relé de ônibus de serviço, é criada uma chave simétrica forte que é usada para encriptar a palavra-passe à medida que vem sobre o fio. Esta chave só vive na loja secreta da sua empresa na nuvem, que é fortemente bloqueada e auditada, como qualquer outra palavra-passe no diretório.
* **Segurança da camada de transporte padrão da indústria (TLS)**
   1. Quando ocorre uma redefinição de palavra-passe ou operação de alteração na nuvem, a palavra-passe de texto simples é encriptada com a sua chave pública.
   1. A palavra-passe encriptada é colocada numa mensagem HTTPS que é enviada através de um canal encriptado utilizando as certs do Microsoft TLS/SSL para o seu retransmissor de ônibus de serviço.
   1. Depois de a mensagem chegar no autocarro de serviço, o seu agente no local acorda e autentica-se no autocarro de serviço utilizando a senha forte que foi gerada anteriormente.
   1. O agente no local capta a mensagem encriptada e desencripta-a utilizando a chave privada.
   1. O agente no local tenta definir a palavra-passe através da AD DS SetPassword API. Este passo é o que permite a aplicação da sua política de senhas de diretório ativo no local (como a complexidade, idade, história e filtros) na nuvem.
* **Políticas de expiração da mensagem**
   * Se a mensagem estiver no autocarro de serviço porque o seu serviço no local está em baixo, ele tem tempo para fora e é removido após vários minutos. O tempo de paragem e remoção da mensagem aumenta ainda mais a segurança.

### <a name="password-writeback-encryption-details"></a>Detalhes de encriptação de redação de palavra-passe

Depois de um utilizador submeter uma reposição de palavra-passe, o pedido de reset passa por vários passos de encriptação antes de chegar ao seu ambiente no local. Estes passos de encriptação garantem a máxima fiabilidade e segurança do serviço. São descritos da seguinte forma:

1. **Encriptação de password com chave RSA de 2048:** Depois de um utilizador submeter uma palavra-passe a ser reescrita no local, a senha submetida em si é encriptada com uma chave RSA de 2048 bits.
1. **Encriptação de nível de pacote com AES-GCM**: Todo o pacote, a palavra-passe mais os metadados necessários, é encriptado utilizando a AES-GCM. Esta encriptação impede qualquer pessoa com acesso direto ao canal ServiceBus subjacente de visualizar ou adulterar o conteúdo.
1. **Toda a comunicação ocorre sobre TLS/SSL**: Toda a comunicação com o ServiceBus acontece num canal SSL/TLS. Esta encriptação protege o conteúdo de terceiros não autorizados.
1. **Chave automática rola de seis em seis meses:** Todas as teclas rolam de seis em seis meses, ou cada vez que a redação da palavra-passe é desativada e, em seguida, reativada no Azure AD Connect, para garantir a máxima segurança e segurança do serviço.

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
   * Qualquer operação de alteração voluntária de autosserviço do utilizador final.
   * Qualquer força de autosserviço de utilizador final altere a operação de senha, por exemplo, a caducidade da palavra-passe.
   * Qualquer redefinição da palavra-passe de autosserviço do utilizador final que tenha origem no portal de reset da [palavra-passe](https://passwordreset.microsoftonline.com).

* **Operações de administrador apoiados**
   * Qualquer administrador de autosserviço de mudança voluntária operação de senha.
   * Qualquer força de autosserviço do administrador altere a operação de senha, por exemplo, a expiração da palavra-passe.
   * Qualquer redefinição da palavra-passe de autosserviço do administrador que tenha origem no portal de reset da [palavra-passe](https://passwordreset.microsoftonline.com).
   * Qualquer palavra-passe de utilizador final iniciado pelo administrador reset do [portal Azure](https://portal.azure.com).

## <a name="unsupported-writeback-operations"></a>Operações de redisonam não apoiadas

As palavras-passe não são escritas em nenhuma das seguintes situações:

* **Operações não suportadas de utilizador final**
   * Qualquer utilizador final que reajuste a sua própria palavra-passe utilizando a versão 1, a versão 2 ou a API do Microsoft Graph.
* **Operações de administrador não apoiados**
   * Qualquer palavra-passe de utilizador final iniciado pelo administrador reset a partir da versão 1, versão 2 da Versão 2 ou do Microsoft Graph API.
   * Qualquer palavra-passe de utilizador final iniciado pelo administrador reset a partir do centro de administração microsoft [365](https://admin.microsoft.com).

> [!WARNING]
> A utilização da caixa de verificação "O utilizador deve alterar a palavra-passe no próximo início de sessão" em ferramentas administrativas AD DS no local, como utilizadores e computadores de diretório ativo ou o Ative Directory Administrative Center é suportado como uma funcionalidade de pré-visualização do Azure AD Connect. Para mais informações, consulte A sincronização de hash de [palavra-passe implemente com sincronização Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Passos seguintes

Para começar com a reescrita da SSPR, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Ativar repor a palavra-passe de autosserviço (SSPR)](tutorial-enable-writeback.md)
