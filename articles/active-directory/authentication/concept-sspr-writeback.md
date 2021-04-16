---
title: Writeback de senha no local com redefinição da palavra-passe de autosserviço - Azure Ative Directory
description: Saiba como a mudança de palavra-passe ou o reset de eventos no Azure Ative Directory pode ser escrito de volta para um ambiente de diretório no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b8a84da331568d36b6f6910054fdb2aea76f490
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530324"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Como funciona a redefinição da palavra-passe de autosserviço no Azure Ative Directory?

O Azure Ative Directory (Azure AD) redefiniu a palavra-passe de autosserviço (SSPR) permite que os utilizadores republem as suas palavras-passe na nuvem, mas a maioria das empresas também tem um ambiente no local Ative Directory Domain Services (AD DS) onde os seus utilizadores existem. O writeback de palavra-passe é uma funcionalidade ativada com [o Azure AD Connect](../hybrid/whatis-hybrid-identity.md) que permite que as alterações de palavras-passe na nuvem sejam escritas de volta para um diretório existente no local em tempo real. Nesta configuração, à medida que os utilizadores alteram ou reiniciam as suas palavras-passe utilizando SSPR na nuvem, as palavras-passe atualizadas também foram escritas para o ambiente AD DS no local.

> [!IMPORTANT]
> Este artigo conceptual explica a um administrador como funciona a gravação da palavra-passe de autosserviço. Se é um utilizador final já registado para redefinição da palavra-passe de autosserviço e precisa de voltar à sua conta, vá a https://aka.ms/sspr .
>
> Se a sua equipa de TI não tiver ativado a capacidade de redefinir a sua própria palavra-passe, contacte o seu helpdesk para obter assistência adicional.

O writeback de palavra-passe é suportado em ambientes que utilizam os seguintes modelos de identidade híbrida:

* [Sincronização de hash de palavra-passe](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Autenticação pass-through](../hybrid/how-to-connect-pta.md)
* [Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)

A writeback da palavra-passe fornece as seguintes funcionalidades:

* **Execução das políticas de senha de serviços de diretório ativo (DS)** no local : Quando um utilizador reinicia a sua palavra-passe, é verificado para garantir que cumpre a sua política de DS AD no local antes de a comprometer com esse diretório. Esta análise inclui a verificação do histórico, complexidade, idade, filtros de senha e quaisquer outras restrições de senha que você define em DS AD.
* **Feedback de atraso zero**: A gravação de palavras-passe é uma operação sincronizada. Os utilizadores são notificados imediatamente se a sua palavra-passe não cumprir a política ou não puderem ser reiniciados ou alterados por qualquer motivo.
* **Suporta alterações de palavra-passe do painel de acesso e do Microsoft 365**: Quando os utilizadores sincronizados de hash federados ou de palavras-passe entram em validade ou não, essas palavras-passe são escritas de volta para DS AD.
* **Suporta a gravação de palavra-passe quando um administrador as reinicia a partir do portal Azure**: Quando um administrador reinicia a palavra-passe de um utilizador no [portal Azure](https://portal.azure.com), se esse utilizador for federado ou se o hash de palavra-passe estiver sincronizado, a palavra-passe é escrita de volta para o local. Esta funcionalidade não é suportada atualmente no portal de administração do Office.
* **Não requer regras de firewall de entrada**: A writeback de palavras-passe utiliza um retransmissor Azure Service Bus como um canal de comunicação subjacente. Toda a comunicação é saída sobre o porto 443.

> [!NOTE]
> As contas de administrador que existem dentro de grupos protegidos em AD no local podem ser usadas com a gravação de palavra-passe. Os administradores podem alterar a sua palavra-passe na nuvem, mas não podem usar a palavra-passe para redefinir uma palavra-passe esquecida. Para obter mais informações sobre grupos protegidos, consulte [contas e grupos protegidos em DS AD](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

Para começar com a gravação da SSPR, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Ativar o reset da palavra-passe de autosserviço (SSPR)](./tutorial-enable-sspr-writeback.md)

## <a name="how-password-writeback-works"></a>Como funciona a repetição de escrita de palavras-passe

Quando um utilizador sincronizado de haxixe federado ou de palavra-passe tenta redefinir ou alterar a sua palavra-passe na nuvem, ocorrem as seguintes ações:

1. É realizada uma verificação para ver que tipo de palavra-passe o utilizador tem. Se a palavra-passe for gerida no local:
   * É realizada uma verificação para ver se o serviço de reprodução está em funcionamento. Se for, o utilizador pode prosseguir.
   * Se o serviço de reversão estiver em baixo, o utilizador é informado de que a sua palavra-passe não pode ser reiniciada neste momento.
1. Em seguida, o utilizador passa pelos portões de autenticação apropriados e chega à página **de palavra-passe Reset.**
1. O utilizador seleciona uma nova palavra-passe e confirma-a.
1. Quando o utilizador seleciona **Enviar** por palavra-passe, a palavra-passe de texto simples é encriptada com uma chave simétrica criada durante o processo de configuração do writeback.
1. A palavra-passe encriptada está incluída numa carga útil que é enviada através de um canal HTTPS para o seu relé de autocarro de serviço específico para o arrendatário (que é configurado para si durante o processo de configuração do writeback). Este relé está protegido por uma senha gerada aleatoriamente que só a instalação no local sabe.
1. Depois de a mensagem chegar ao autocarro de serviço, o ponto final de reset de palavra-passe acorda automaticamente e vê que tem um pedido de reset pendente.
1. Em seguida, o serviço procura o utilizador utilizando o atributo âncora de nuvem. Para que esta procura tenha êxito, devem ser satisfeitas as seguintes condições:

   * O objeto do utilizador deve existir no espaço do conector AD DS.
   * O objeto do utilizador deve ser ligado ao objeto metaverso correspondente (MV).
   * O objeto do utilizador deve estar ligado ao respetivo objeto de conector Azure AD.
   * A ligação do conector AD DS ao MV deve ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx` no link.

   Quando a chamada vem da nuvem, o motor de sincronização usa o atributo **cloudAnchor** para procurar o objeto de espaço do conector AZure AD. Em seguida, segue a ligação de volta para o objeto MV, e em seguida, segue a ligação de volta para o objeto DS AD. Como pode haver vários objetos AD DS (multi-floresta) para o mesmo utilizador, o motor de sincronização depende da `Microsoft.InfromADUserAccountEnabled.xxx` ligação para escolher o correto.

1. Após a consulta da conta de utilizador, é feita uma tentativa de repor a palavra-passe diretamente na floresta DS AD apropriada.
1. Se a operação de definição de palavra-passe for bem sucedida, o utilizador é informado de que a sua palavra-passe foi alterada.

   > [!NOTE]
   > Se o hash de palavra-passe do utilizador for sincronizado com o Azure AD utilizando a sincronização de hash de palavra-passe, existe a possibilidade de a política de palavra-passe no local ser mais fraca do que a política de senha de nuvem. Neste caso, a política no local é aplicada. Esta política garante que a sua política no local é aplicada na nuvem, não importa se você usa sincronização de hash de palavra-passe ou federação para fornecer um único sinal de acesso.

1. Se a operação de definição de palavra-passe falhar, um erro leva o utilizador a tentar novamente. A operação pode falhar devido às seguintes razões:
    * O serviço estava em baixo.
    * A palavra-passe que escolheram não corresponde às políticas da organização.
    * Não é possível encontrar o utilizador em ambientes AD DS locais.

   As mensagens de erro fornecem orientação aos utilizadores para que possam tentar resolver sem a intervenção do administrador.

## <a name="password-writeback-security"></a>Segurança de writeback de palavra-passe

A gravação de palavras-passe é um serviço altamente seguro. Para garantir que as suas informações estão protegidas, um modelo de segurança de quatro níveis está ativado da seguinte forma:

* **Retransmissor de serviço-autocarro específico para inquilinos**
   * Quando configura o serviço, é criado um retransmissor de autocarro de serviço específico para inquilinos que é protegido por uma senha forte gerada aleatoriamente que a Microsoft nunca tem acesso.
* **Chave de encriptação de palavras-passe bloqueada, criptograficamente forte**
   * Após a criação do retransmissor do autocarro de serviço, é criada uma chave simétrica forte que é usada para encriptar a palavra-passe à medida que passa por cima do fio. Esta chave só vive na loja secreta da sua empresa na nuvem, que está fortemente fechada e auditada, como qualquer outra palavra-passe no diretório.
* **Segurança padrão da camada de transporte da indústria (TLS)**
   1. Quando uma operação de reset ou alteração de palavra-passe ocorre na nuvem, a palavra-passe de texto simples é encriptada com a sua chave pública.
   1. A palavra-passe encriptada é colocada numa mensagem HTTPS que é enviada através de um canal encriptado utilizando certificados microsoft TLS/SSL para o seu retransmissor de autocarro de serviço.
   1. Depois de a mensagem chegar ao autocarro de serviço, o seu agente no local acorda e autentica-se no autocarro de serviço utilizando a palavra-passe forte que foi previamente gerada.
   1. O agente no local pega na mensagem encriptada e desencripta-a usando a chave privada.
   1. O agente no local tenta definir a palavra-passe através da AD DS SetPassword API. Este passo é o que permite a aplicação da sua política de senha de DS AD no local (como a complexidade, idade, história e filtros) na nuvem.
* **Políticas de expiração de mensagens**
   * Se a mensagem se sentar no autocarro de serviço porque o seu serviço no local está avariado, ele acaba e é removido após vários minutos. O tempo limite e a remoção da mensagem aumentam ainda mais a segurança.

### <a name="password-writeback-encryption-details"></a>Detalhes de encriptação de writeback de palavra-passe

Depois de um utilizador submeter um reset de palavra-passe, o pedido de reset passa por vários passos de encriptação antes de chegar ao seu ambiente no local. Estes passos de encriptação garantem a máxima fiabilidade e segurança do serviço. São descritos da seguinte forma:

1. **Encriptação de palavra-passe com chave RSA de 2048**: Depois de um utilizador submeter uma palavra-passe a ser escrita de volta para o local, a própria palavra-passe submetida é encriptada com uma chave RSA de 2048.
1. **Encriptação ao nível do pacote com AES-GCM**: Todo o pacote, a palavra-passe mais os metadados necessários, é encriptado utilizando a AES-GCM. Esta encriptação impede que qualquer pessoa com acesso direto ao canal de Service Bus subjacente veja ou adulterar o conteúdo.
1. Toda a **comunicação ocorre sobre tls/SSL**: Toda a comunicação com o Service Bus acontece num canal SSL/TLS. Esta encriptação protege o conteúdo de terceiros não autorizados.
1. **Capotamento automático da chave de seis em seis meses**: Todas as teclas reagem de seis em seis meses, ou sempre que a gravação da palavra-passe é desativada e, em seguida, reativada no Azure AD Connect, para garantir a máxima segurança e segurança do serviço.

### <a name="password-writeback-bandwidth-usage"></a>Utilização da largura de banda de reversão de palavra-passe

O writeback de palavra-passe é um serviço de baixa largura de banda que apenas envia pedidos de volta ao agente no local nas seguintes circunstâncias:

* Duas mensagens são enviadas quando a funcionalidade está ativada ou desativada através do Azure AD Connect.
* Uma mensagem é enviada uma vez a cada cinco minutos como batimento cardíaco de serviço durante o tempo em que o serviço estiver em funcionamento.
* São enviadas duas mensagens cada vez que uma nova senha é submetida:
   * A primeira mensagem é um pedido para realizar a operação.
   * A segunda mensagem contém o resultado da operação e é enviada nas seguintes circunstâncias:
      * Cada vez que uma nova palavra-passe é submetida durante o reset da palavra-passe de autosserviço do utilizador.
      * Cada vez que uma nova palavra-passe é submetida durante uma operação de alteração de senha de utilizador.
      * Cada vez que uma nova palavra-passe é submetida durante um reset da palavra-passe do utilizador iniciada por administração (apenas a partir dos portais de administração Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Considerações sobre o tamanho da mensagem e a largura de banda

O tamanho de cada uma das mensagens descritas anteriormente é tipicamente inferior a 1 KB. Mesmo sob cargas extremas, o próprio serviço de writeback de palavra-passe está a consumir alguns quilobits por segundo de largura de banda. Porque cada mensagem é enviada em tempo real, apenas quando exigida por uma operação de atualização de passwords, e porque o tamanho da mensagem é tão pequeno, o uso da largura de banda da capacidade de gravação é demasiado pequeno para ter um impacto mensurável.

## <a name="supported-writeback-operations"></a>Operações de writeback apoiadas

As palavras-passe são escritas em todas as seguintes situações:

* **Operações de utilizador final suportadas**
   * Qualquer operação de senha de alteração voluntária de autosserviço do utilizador final.
   * Qualquer operação de autosserviço de autosserviço do utilizador final altere a operação de senha, por exemplo, a expiração da palavra-passe.
   * Qualquer redefinição da palavra-passe de autosserviço do utilizador final que tenha origem no portal de reset da [palavra-passe](https://passwordreset.microsoftonline.com).

* **Operações de administrador apoiadas**
   * Qualquer administrador que se autosserviço mude a operação de senha voluntária.
   * Qualquer administrador de autosserviço força alterar a operação de senha, por exemplo, a expiração da palavra-passe.
   * Qualquer redefinição da palavra-passe de autosserviço do administrador que tenha origem no portal de reset da [palavra-passe](https://passwordreset.microsoftonline.com).
   * Qualquer palavra-passe de utilizador final iniciada pelo administrador é reiniciada a partir do [portal Azure](https://portal.azure.com).
   * Qualquer palavra-passe de utilizador final iniciada pelo administrador é reiniciada a partir da [API do Gráfico microsoft](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http).

## <a name="unsupported-writeback-operations"></a>Operações de writeback não suportadas

As palavras-passe não estão escritas em nenhuma das seguintes situações:

* **Operações de utilizador final não suportadas**
   * Qualquer utilizador final que reinicie a sua própria palavra-passe utilizando a versão 1 do PowerShell, a versão 2 ou a API do Microsoft Graph.
* **Operações de administrador não apoiadas**
   * Qualquer palavra-passe de utilizador final iniciada pelo administrador é reiniciada a partir da versão 1 do PowerShell, versão 2 ou da API do Microsoft Graph (a API do [Microsoft Graph](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http) é suportada).
   * Qualquer palavra-passe de utilizador final iniciada pelo administrador é reiniciada a partir do [centro de administração microsoft 365](https://admin.microsoft.com).
   * Qualquer administrador não pode utilizar a ferramenta de reset de palavra-passe para redefinir a sua própria palavra-passe para a gravação de palavras-passe.

> [!WARNING]
> A utilização da caixa de verificação "O utilizador deve alterar a palavra-passe no próximo início de súmido" em ferramentas administrativas AD DS no local, como Utilizadores e Computadores de Diretório Ativo ou o Ative Directory Administrative Center é suportado como uma funcionalidade de pré-visualização do Azure AD Connect. Para obter mais informações, consulte [implementar a sincronização de hash de palavra-passe com a sincronização Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Passos seguintes

Para começar com a gravação da SSPR, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Ativar o reset da palavra-passe de autosserviço (SSPR)](./tutorial-enable-sspr-writeback.md)