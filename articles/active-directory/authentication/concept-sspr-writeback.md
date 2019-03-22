---
title: Integração de repetição de escrita de palavra-passe no local com o Azure AD SSPR - Azure Active Directory
description: Obter palavras-passe na cloud a repetição de escrita no local infratstructure do AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fcf2ef10cbc8f6f54a65e596ea003a98f410a7b
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313299"
---
# <a name="what-is-password-writeback"></a>O que é a repetição de escrita de palavra-passe?

Ter uma senha com base na cloud, repor o utilitário é ótimo, mas a maioria das empresas ainda tem um diretório no local em que existem a seus usuários. Como a manutenção de suporte Microsoft tradicional no local do Active Directory (AD) em sincronia com as alterações de palavra-passe na cloud? Repetição de escrita de palavra-passe é uma funcionalidade ativada com [do Azure AD Connect](../hybrid/whatis-hybrid-identity.md) que permite que as alterações de palavra-passe na cloud para a repetição de escrita para um diretório no local existente em tempo real.

Repetição de escrita de palavra-passe é suportada em ambientes que usam:

* [Serviços de Federação do Active Directory (AD FS)](../hybrid/how-to-connect-fed-management.md)
* [Sincronização de hash de palavra-passe](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Autenticação pass-through](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> Repetição de escrita de palavra-passe deixarão de funcionar para os clientes que estão a utilizar o Azure AD Connect versões 1.0.8641.0 e mais antigo quando o [o serviço de controlo de acesso do Azure (ACS) é descontinuado a 7 de Novembro de 2018](../develop/active-directory-acs-migration.md). O Azure AD Connect versões 1.0.8641.0 e mais antigos deixará de permitir repetição de escrita de palavra-passe neste momento porque dependem de ACS para obter essa funcionalidade.
>
> Para evitar uma interrupção do serviço, a atualização de uma versão anterior do Azure AD Connect para uma versão mais recente, consulte o artigo [do Azure AD Connect: Atualizar de uma versão anterior para a mais recente](../hybrid/how-to-upgrade-previous-version.md)
>

Fornece a repetição de escrita de palavra-passe:

* **Imposição de políticas de palavra-passe do Active Directory no local**: Quando um utilizador repõe a palavra-passe, ele é verificado para assegurar cumpre a política do Active Directory no local antes de consolidá-lo para esse diretório. Esta revisão inclui a verificação do histórico, complexidade, idade, filtros de palavra-passe e outras restrições de palavra-passe que definiu no local Active Directory.
* **Comentários de atraso de zero**: Repetição de escrita de palavra-passe é uma operação síncrona. Os utilizadores são notificados imediatamente se a palavra-passe não cumpria a política ou não foi possível ser repor ou alterada por qualquer motivo.
* **Palavra-passe de suporta muda de painel de acesso e do Office 365**: Quando federadas ou sincronizados de hash de palavra-passe vêm os utilizadores para alterar as palavras-passe expiradas ou não expirada, as senhas são repetidas para seu ambiente do Active Directory local.
* **Oferece suporte a repetição de escrita de palavra-passe quando um administrador repõe-los a partir do portal do Azure**: Sempre que um administrador repõe a palavra-passe de um usuário na [portal do Azure](https://portal.azure.com), se esse utilizador está federado ou sincronizados de hash de palavra-passe, a palavra-passe é gravada no local. Esta funcionalidade não é atualmente suportada no portal de administração do Office.
* **Não requer quaisquer regras de firewall de entrada**: Repetição de escrita de palavra-passe utiliza um reencaminhamento do Service bus do Azure como um canal de comunicação subjacente. Toda a comunicação é de saída através da porta 443.

> [!Note]
> Não não possível utilizar contas de utilizador que existem em grupos protegidos no Active Directory no local com repetição de escrita de palavra-passe. As contas de administrador que existem dentro protegidos grupos locais AD pode ser utilizado com repetição de escrita de palavra-passe. Para obter mais informações sobre grupos protegidos, consulte [protegidos a contas e grupos no Active Directory](https://technet.microsoft.com/library/dn535499.aspx).
>

## <a name="licensing-requirements-for-password-writeback"></a>Requisitos de licenciamento para a repetição de escrita de palavra-passe

**Self-Service palavra-passe reposição/alteração/desbloqueio com repetição de escrita no local é uma funcionalidade premium do Azure AD**. Para obter mais informações sobre o licenciamento, consulte a [do Azure Active Directory preços site](https://azure.microsoft.com/pricing/details/active-directory/).

Para utilizar a repetição de escrita de palavra-passe, tem de ter uma das seguintes licenças atribuídas no seu inquilino:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 ou A3
* Enterprise Mobility + Security E5 ou A5
* O Microsoft 365 E3 ou A3
* O Microsoft 365 E5 ou A5
* Microsoft 365 F1
* Microsoft 365 Empresas

> [!WARNING]
> Autónomo Office 365, planos de licenciamento *não suportam "Self-Service palavra-passe reposição/alteração/desbloqueio com repetição de escrita no local"* e exige que possua um dos planos anteriores para esta funcionalidade funcione.
>

## <a name="how-password-writeback-works"></a>Como funciona a repetição de escrita de palavra-passe

Quando um hash de palavra-passe ou federado sincronizados utilizador tenta repor ou alterar a palavra-passe na cloud, ocorrem as seguintes ações:

1. É efetuada uma verificação para ver que tipo de palavra-passe tem do utilizador. Se a palavra-passe é geridos no local:
   * É efetuada uma verificação para ver se o serviço de repetição de escrita está em execução. Se estiver, o utilizador pode continuar.
   * Se o serviço de repetição de escrita estiver desativado, o usuário será informado de que a palavra-passe não pode ser reposta neste momento.
1. Em seguida, o utilizador passa as portas de autenticação adequado e alcance o **Repor palavra-passe** página.
1. O usuário seleciona uma nova palavra-passe e confirma isso.
1. Quando o utilizador seleciona **submeter**, a palavra-passe de texto sem formatação é encriptada com uma chave simétrica criada durante o processo de configuração da repetição de escrita.
1. A palavra-passe encriptada está incluído numa carga que é enviada por um canal HTTPS para o reencaminhamento de barramento de serviço específico de inquilino (que é criado para durante o processo de configuração da repetição de escrita). Este reencaminhamento está protegido por uma palavra-passe gerada aleatoriamente que sabe apenas sua instalação no local.
1. Depois que a mensagem de atinge o service bus, o ponto final de reposição de palavra-passe reativado automaticamente e vê que ele tem um pedido de reposição pendente.
1. O serviço de procura, em seguida, o utilizador, utilizando o atributo de âncora de cloud. Para esta pesquisa com êxito:

   * O objeto de utilizador tem de existir no espaço conector do Active Directory.
   * O objeto de utilizador têm de estar associado ao objeto de metaverso (MV) correspondente.
   * O objeto de utilizador têm de estar associado ao objeto de conector correspondente do Azure Active Directory.
   * A ligação de objeto de conector do Active Directory para a MV tem de ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx` na ligação.
   
   Quando chega a chamada da cloud, o motor de sincronização utiliza a **cloudAnchor** atributo para procurar o objeto de espaço conector do Azure Active Directory. Em seguida, segue-se a ligação para o objeto de MV e, em seguida, segue-se a ligação para o objeto do Active Directory. Como pode haver vários objetos do Active Directory (multifloresta) para o mesmo utilizador, o motor de sincronização depende o `Microsoft.InfromADUserAccountEnabled.xxx` ligação para a escolha correta.

   > [!Note]
   > Como resultado, essa lógica de palavra-passe de repetição de escrita para trabalhar do Azure AD Connect tem de ser capaz de comunicar com o emulador do controlador (PDC) de domínio primário. Se precisar de ativar esta opção manualmente, pode ligar o Azure AD Connect para o emulador PDC. Com o botão direito a **propriedades** do conector de sincronização do Active Directory e, em seguida, selecione **configurar partições de diretório**. A partir daí, procure o **definições de ligação do controlador de domínio** secção e selecione a caixa intitulada **utilizar apenas controladores de domínio preferencial**. Mesmo que o controlador de domínio preferencial não for um emulador PDC, o Azure AD Connect tenta se conectar com o PDC para repetição de escrita de palavra-passe.

1. Depois que o usuário é encontrada a conta, é feita uma tentativa para repor a palavra-passe diretamente na floresta do Active Directory adequada.
1. Se a operação de definição de palavra-passe for bem-sucedida, o utilizador é informado da que palavra-passe foi alterada.
   > [!NOTE]
   > Se o hash de palavra-passe do utilizador está sincronizado com o Azure AD utilizando a sincronização de hash de palavra-passe, é provável que a política de palavra-passe no local é mais fraca do que a política de palavra-passe na cloud. Neste caso, é aplicada a política no local. Esta política assegura que seus locais é imposta na cloud, quer se utilizar a sincronização de hash de palavra-passe ou Federação para fornecer início de sessão único.
   >

1. Se a palavra-passe definida a operação falhar, um erro pede ao utilizador para tentar novamente. A operação pode falhar porque:
    * O serviço estava.
    * A palavra-passe que selecionou não cumpria as políticas da organização.
    * Não é possível encontrar o utilizador no local Active Directory.

      As mensagens de erro fornecem orientações aos utilizadores para que eles podem tentar resolver sem intervenção do administrador.

## <a name="password-writeback-security"></a>Segurança de repetição de escrita de palavra-passe

Repetição de escrita de palavra-passe é um serviço altamente seguro. Para garantir que suas informações estão protegidas, um modelo de segurança em camadas quatro está ativado como descreve o seguinte:

* **Reencaminhamento do barramento de serviço específico de inquilino**
   * Quando configurar o serviço, um reencaminhamento do barramento de serviço específico de inquilino é configurado que está protegido por uma senha forte gerada aleatoriamente, que a Microsoft nunca tem acesso a.
* **Chave de encriptação da palavra-passe bloqueado e criptograficamente fortes,**
   * Depois de criar reencaminhamento do service bus, é criada uma chave simétrica forte que é utilizada para encriptar a palavra-passe, pois isso vem durante a transmissão. Esta chave só reside no arquivo de segredos da sua empresa na cloud, que é bastante bloqueado e auditada, assim como qualquer outra palavra-passe no diretório.
* **Setor padrão Transport Layer Security (TLS)**
   1. Quando uma palavra-passe, repor ou alterar operação ocorre na cloud, a palavra-passe de texto sem formatação é encriptada com a chave pública.
   1. A palavra-passe encriptada é colocado numa mensagem HTTPS, que é enviada por um canal criptografado, através de certificados da Microsoft SSL para o reencaminhamento do service bus.
   1. Depois da mensagem chega no barramento de serviço, o agente no local é reativado e autentica para o service bus, utilizando a palavra-passe forte que foi gerada anteriormente.
   1. O agente no local seleciona a mensagem encriptada e desencripta a mesma, utilizando a chave privada.
   1. O agente no local tenta definir a palavra-passe através da API de SetPassword do AD DS. Este passo é o que permite a imposição da sua política de palavra-passe do Active Directory no local (por exemplo, a complexidade, idade, histórico e filtros) na cloud.
* **Políticas de expiração de mensagem**
   * Se a mensagem encontra-se no barramento de serviço porque o serviço no local está indisponível, exceder o tempo limite e é removido após alguns minutos. O limite de tempo e a remoção da mensagem aumenta ainda mais a segurança.

### <a name="password-writeback-encryption-details"></a>Detalhes de encriptação de repetição de escrita de palavra-passe

Depois de um utilizador submete uma reposição de palavra-passe, o pedido de reposição passa por vários passos de encriptação antes de chegarem no seu ambiente no local. Estes passos de encriptação garantem a fiabilidade do serviço máximo e a segurança. Estes modelos são descritos da seguinte forma:

* **Passo 1: Encriptação de palavra-passe com a chave RSA de 2048 bits**: Depois de um utilizador submete uma palavra-passe para a repetição de escrita no local, a palavra-passe submetida em si é encriptado com uma chave RSA de 2048 bits.
* **Passo 2: Encriptação de nível de pacote com o AES-GCM**: Todo o pacote, a palavra-passe e os metadados necessários, ainda é encriptado utilizando AES-GCM. Esta encriptação impede que qualquer pessoa com acesso direto para o canal de ServiceBus subjacente visualizar ou violação com o conteúdo.
* **Passo 3: Toda a comunicação ocorre através de TLS/SSL**: Toda a comunicação com o ServiceBus ocorre num canal SSL/TLS. Essa criptografia protege o conteúdo de terceiros não autorizados.
* **Agregação de chave automática ao longo de cada seis meses**: Todas as chaves de agregação ao longo de cada seis meses ou sempre que a repetição de escrita de palavra-passe está desativada e, em seguida, novamente ativada no Azure AD Connect, para garantir a segurança do serviço máximo e a segurança.

### <a name="password-writeback-bandwidth-usage"></a>Utilização de largura de banda de repetição de escrita de palavra-passe

Repetição de escrita de palavra-passe é um serviço de baixa largura de banda que apenas envia pedidos para o agente no local nas seguintes circunstâncias:

* Duas mensagens são enviadas quando o recurso está ativado ou desativado através do Azure AD Connect.
* Uma mensagem é enviada uma vez a cada cinco minutos como um heartbeat do serviço para, desde que o serviço está em execução.
* Duas mensagens são enviadas a cada hora é submetida uma nova palavra-passe:
   * A primeira mensagem é um pedido para executar a operação.
   * A segunda mensagem contém o resultado da operação e é enviada nas seguintes circunstâncias:
      * Sempre que uma nova palavra-passe é submetida durante uma reposição de palavra-passe self-service do utilizador.
      * Sempre que uma nova palavra-passe é submetida durante uma operação de alteração de palavra-passe do utilizador.
      * Sempre que uma nova palavra-passe é submetida durante uma palavra-passe de utilizador iniciadas pelo administrador de reposição (apenas a partir de portais de administração do Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Considerações de largura de banda e de tamanho de mensagem

O tamanho de cada da mensagem descrita anteriormente, normalmente, é menos de 1 KB. Mesmo que sob cargas extremas, o próprio serviço de repetição de escrita de palavra-passe está a consumir alguns kilobits por segundo de largura de banda. Uma vez que cada mensagem é enviada em tempo real, apenas quando necessário por uma operação de atualização de palavra-passe e porque o tamanho da mensagem é tão pequeno, a utilização de largura de banda da capacidade de repetição de escrita é demasiado pequena para ter um impacto considerável.

## <a name="supported-writeback-operations"></a>Operações de repetição de escrita suportadas

As palavras-passe são repetidas nas seguintes situações:

* **Operações do utilizador final suportados**
   * Operação de palavra-passe de alterar qualquer voluntária pelo utilizador final de self-service
   * Operação de palavra-passe, por exemplo, expiração de palavra-passe de alterar qualquer força do utilizador final self-service
   * Qualquer utilizador final Self-reposição palavra-passe que são originados pelo [portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com)
* **Operações de administrador suportados**
   * Operação de palavra-passe de alterar qualquer voluntária administrador de self-service
   * Operação de palavra-passe, por exemplo, expiração de palavra-passe de alterar qualquer força de self-service de administrador
   * Reposição de qualquer senha de autoatendimento de administrador que são originados pelo [portal de reposição de palavra-passe](https://passwordreset.microsoftonline.com)
   * Qualquer palavra-passe de utilizador final iniciadas pelo administrador de reposição do [portal do Azure](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Operações de repetição de escrita não suportado

Palavras-passe são *não* repetição de escrita em qualquer uma das seguintes situações:

* **Operações do utilizador final não suportado**
   * Qualquer utilizador final a repor a sua própria palavra-passe com o PowerShell versão 1, versão 2 ou o Azure AD Graph API
* **Operações de administrador não suportado**
   * Qualquer palavra-passe de utilizador final iniciadas pelo administrador de reposição do [portal de gestão do Office](https://portal.office.com)
   * Qualquer palavra-passe de utilizador final iniciadas pelo administrador de reposição da versão 1, versão 2 ou o Azure AD Graph API do PowerShell

## <a name="next-steps"></a>Passos Seguintes

Ative a repetição de escrita de palavra-passe com o Tutorial: [Ativar a repetição de escrita de palavra-passe](tutorial-enable-writeback.md)
