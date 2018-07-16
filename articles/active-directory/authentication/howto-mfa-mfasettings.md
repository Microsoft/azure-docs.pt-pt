---
title: Configurar a autenticação Multifator do Azure | Documentos da Microsoft
description: Este artigo descreve como configurar definições de multi-factor Authentication no portal do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 1d5e7683050c5e1e4216b81e23554724f12d45f9
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054714"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configurar as definições de multi-factor Authentication do Azure

Este artigo ajuda-o a gerir o Azure multi-factor Authentication agora que está em execução. Ele aborda vários tópicos ajudam-na tirar o máximo partido do Azure multi-factor Authentication. Nem todos os recursos estão disponíveis em cada [versão do multi-factor Authentication](concept-mfa-whichversion.md#what-features-do-i-need).

| Funcionalidade | Descrição | 
|:--- |:--- |
| [Bloquear e desbloquear utilizadores](#block-and-unblock-users) |Utilize a funcionalidade de bloquear/desbloquear utilizadores para impedir que os utilizadores da receção de pedidos de autenticação. |
| [Alerta de fraude](#fraud-alert) |Configure a funcionalidade de alertas de fraude, para que os usuários podem relatar fraudulentas tentativas para aceder aos respetivos recursos. |
| [Omissão de uso individual](#one-time-bypass) |Utilize a funcionalidade de omissão de uso individual para permitir que os utilizadores autenticar uma única vez pela _ignorando_ multi-factor Authentication. |
| [Mensagens de voz personalizada](#custom-voice-messages) |Utilize a funcionalidade de mensagens de voz personalizada para utilizar o seu próprio gravações ou saudações com multi-factor Authentication. |
| [Colocação em cache](#caching-in-azure-multi-factor-authentication) |Utilize a funcionalidade de colocação em cache para definir o período de tempo específico para que as tentativas de autenticação subsequentes tenha êxito automaticamente. |
| [IPs fidedignos](#trusted-ips) |Os administradores de um inquilino gerido ou Federado podem utilizar a funcionalidade de IPs fidedignos para ignorar a verificação de dois passos para os utilizadores que iniciem sessão a partir da intranet da empresa. |
| [Palavras-passe de aplicação](#app-passwords) |Utilize a funcionalidade de palavra-passe de aplicação para que uma aplicação ignorar o multi-factor Authentication e continuar a trabalhar. |
| [Lembre-se de multi-factor Authentication para browsers e dispositivos fidedignos](#remember-multi-factor-authentication-for-trusted-devices) |Utilize esta funcionalidade para não se esqueça de navegadores e dispositivos fidedignos para um determinado número de dias após um utilizador tiver com êxito com sessão iniciada com o multi-factor Authentication. |
| [Métodos de verificação selecionável](#selectable-verification-methods) |Utilize esta funcionalidade para selecionar a lista de métodos de autenticação que os utilizadores são capazes de usar. |

## <a name="block-and-unblock-users"></a>Bloquear e desbloquear utilizadores

Utilize o _bloquear e desbloquear utilizadores_ funcionalidade para impedir que os utilizadores da receção de pedidos de autenticação. Qualquer tentativa de autenticação aos utilizadores bloqueados é rejeitada automaticamente. Os utilizadores ficam bloqueados durante 90 dias a partir do momento em que são bloqueados.

### <a name="block-a-user"></a>Bloquear um utilizador

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **do Azure Active Directory** > **servidor MFA** > **bloquear/desbloquear utilizadores**.
3. Selecione **adicionar** para impedir um utilizador.
4. Selecione o **grupo de replicação**. Introduza o nome de utilizador para o utilizador bloqueado como **nome de utilizador<span></span>@domain.com**. Introduza um comentário no **motivo** campo.
5. Selecione **adicionar** para concluir a bloquear o utilizador.

### <a name="unblock-a-user"></a>Desbloquear um utilizador

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **do Azure Active Directory** > **servidor MFA** > **bloquear/desbloquear utilizadores**.
3. Selecione **desbloquear** no **ação** coluna junto ao utilizador a desbloquear.
4. Introduza um comentário no **razão para desbloquear** campo.
5. Selecione **desbloquear** para concluir a desbloquear o utilizador.

## <a name="fraud-alert"></a>Alerta de fraudes

Configurar o _alerta de fraude_ de recursos para que os usuários podem relatar fraudulentas tentativas para aceder aos respetivos recursos. Os utilizadores podem comunicar fraude tentativas com a aplicação móvel ou por meio de seu telefone.

### <a name="turn-on-fraud-alerts"></a>Ativar alertas de fraude

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **do Azure Active Directory** > **servidor MFA** > **alerta de fraude**.

   ![Ativar alertas de fraude](./media/howto-mfa-mfasettings/fraudalert.png)

3. Definir o **permitir que os utilizadores enviem alertas de fraude** definição **no**.
4. Selecione **Guardar**.

### <a name="configuration-options"></a>Opções de configuração

- **Bloquear utilizador quando é reportada fraude**: se um utilizador comunicar fraude, a conta está bloqueada durante 90 dias ou até que um administrador desbloqueia a respetiva conta. Um administrador pode rever os inícios de sessão utilizando o relatório de início de sessão e tomar as medidas adequadas para prevenir a fraude futura. Um administrador pode então [desbloquear](#unblock-a-user) a conta de utilizador.
- **Código para reportar fraude durante a saudação inicial**: quando os utilizadores recebem uma chamada telefónica para efetuar a verificação de dois passos, normalmente prima **#** para confirmar o início de sessão. Para reportar fraude, o usuário insere um código antes de prima **#**. Esse código é **0** por predefinição, mas pode personalizá-lo.

  >[!NOTE]
  >As saudações de voz padrão da Microsoft instruir que os usuários pressionem **n º 0** para submeter um alerta de fraude. Se pretender utilizar um código que **0**, registe e carregue seus próprios saudações de voz personalizada com as instruções adequadas para os seus utilizadores.
  >

### <a name="view-fraud-reports"></a>Ver relatórios de fraude

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **do Azure Active Directory** > **inícios de sessão**. O relatório de fraude agora faz parte do relatório de inícios de sessão do AD Azure padrão.

## <a name="one-time-bypass"></a>Omissão de uso individual

O _omissão de uso individual_ recurso permite que um utilizador autenticar uma única vez sem executar verificação de dois passos. A omissão é temporária e expira após um número de segundos especificado. Em situações em que a aplicação móvel ou por telefone não está a receber uma chamada telefónica ou uma notificação, pode permitir que uma omissão de uso individual para que o usuário pode acessar o recurso pretendido.

### <a name="create-a-one-time-bypass"></a>Criar uma omissão de uso individual

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **do Azure Active Directory** > **servidor MFA** > **omissão de uso individual**.

   ![Criar a omissão de uso individual](./media/howto-mfa-mfasettings/onetimebypass.png)

3. Selecione **Adicionar**.
4. Se necessário, selecione o grupo de replicação para a omissão.
5. Introduza o nome de utilizador como **nome de utilizador<span></span>@domain.com**. Introduza o número de segundos que a omissão deve durar. Introduza a razão para a omissão. 
6. Selecione **Adicionar**. O limite de tempo entra em vigor imediatamente. O utilizador tem de iniciar sessão antes de expira a omissão de uso individual. 

### <a name="view-the-one-time-bypass-report"></a>Ver o relatório de omissão de uso individual

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue até **do Active Directory** > **servidor MFA** > **omissão de uso individual**.

## <a name="custom-voice-messages"></a>Mensagens de voz personalizada
Pode utilizar o seu próprio gravações ou saudações para verificação de dois passos com o _mensagens de voz personalizada_ funcionalidade. Essas mensagens podem ser utilizadas, além de ou para substituir as gravações de Microsoft.

Antes de começar, tenha em atenção as seguintes restrições:

* Os formatos de ficheiro suportados são. wav e. mp3.
* O limite de tamanho de ficheiro é 5 MB.
* Mensagens de autenticação devem ser menores do que 20 segundos. Mensagens com mais de 20 segundos podem fazer com que a verificação falhe. O utilizador não responder antes de concluir a mensagem e a verificação de exceder o tempo limite.

### <a name="set-up-a-custom-message"></a>Configurar uma mensagem personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **do Azure Active Directory** > **servidor MFA** > **definições de chamada telefónica**.

   ![Mensagens de telefone personalizado de registo](./media/howto-mfa-mfasettings/phonecallsettings.png)

3. Selecione **adicionar saudação**.
4. Escolha o tipo de saudação. Escolha o idioma.
5. Selecione um ficheiro de som. mp3 ou. wav para carregar.
6. Selecione **Adicionar**.

## <a name="caching-in-azure-multi-factor-authentication"></a>Colocação em cache em autenticação Multifator do Azure

Pode definir um período de tempo para permitir que as tentativas de autenticação após um utilizador ser autenticado utilizando o _colocação em cache_ funcionalidade. Tentativas de autenticação subsequentes para o utilizador especificado no período de tempo êxito automaticamente. Colocação em cache é usada principalmente quando sistemas no local, por exemplo, VPN, enviam várias solicitações de verificação, enquanto o primeiro pedido ainda está em curso. Esta funcionalidade permite que os pedidos subsequentes para ter êxito automaticamente, após o utilizador ser bem sucedida a primeira verificação em curso. 

>[!NOTE]
>A funcionalidade de colocação em cache não se destina a ser utilizado para inícios de sessão para o Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Configurar a colocação em cache 

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **do Azure Active Directory** > **servidor MFA** > **regras de colocação em cache**.

   ![Configurar regras de colocação em cache](./media/howto-mfa-mfasettings/cachingrules.png)

3. Selecione **Adicionar**.
4. Selecione o **tipo de cache** na lista pendente. Introduza o número máximo de **segundos de cache**. 
5. Se necessário, selecione um tipo de autenticação e especifique uma aplicação. 
6. Selecione **Adicionar**.

## <a name="trusted-ips"></a>IPs Fidedignos

O _IPs fidedignos_ funcionalidade de multi-factor Authentication do Azure é utilizada pelos administradores de um inquilino gerido ou federado. A funcionalidade ignora a verificação de dois passos para os utilizadores que iniciem sessão a partir da intranet da empresa. A funcionalidade está disponível com a versão completa do multi-factor Authentication do Azure e não a versão gratuita para administradores. Para obter detalhes sobre como obter a versão completa do multi-factor Authentication, consulte [multi-factor Authentication](multi-factor-authentication.md).

Se sua organização implementa a extensão NPS para fornecer a MFA a observação de aplicações no local serão exibidas sempre o endereço IP de origem deve ser o servidor NPS fluxos por meio de tentar a autenticação.

| Tipo de inquilino do Azure AD | Opções de funcionalidades de IPs fidedignas |
|:--- |:--- |
| Gerido |**Intervalo de endereços IP específico**: os administradores de especificar um intervalo de endereços IP que pode ignorar a verificação de dois passos para os utilizadores que iniciem sessão a partir da intranet da empresa.|
| Federado |**Todos os utilizadores federados**: todos os utilizadores federados que iniciem sessão a partir de dentro da organização podem ignorar a verificação de dois passos. Os utilizadores ignorar a verificação através de uma afirmação que é emitida por serviços de Federação do Active Directory (AD FS).<br/>**Intervalo de endereços IP específico**: os administradores de especificar um intervalo de endereços IP que pode ignorar a verificação de dois passos para os utilizadores que iniciem sessão a partir da intranet da empresa. |

Os IPs fidedignos ignorar funciona apenas a partir de dentro da intranet da empresa. Se selecionar a **todos os utilizadores federados** opção e um utilizador inicia sessão a partir fora da intranet da empresa, o utilizador tem a autenticação com a verificação de dois passos. O processo é o mesmo, mesmo que o usuário apresentaria uma afirmação de AD FS. 

**Experiência do utilizador final dentro da rede empresarial**

Quando a funcionalidade de IPs fidedignos estiver desativada, verificação de dois passos é necessária para os fluxos de navegador. As palavras-passe de aplicação são necessárias para aplicativos de cliente avançado mais antigos. 

Quando a funcionalidade de IPs fidedignos é ativada, a verificação de dois passos é *não* necessária para os fluxos de navegador. Palavras-passe de aplicação são *não* necessários para aplicativos de cliente avançado mais antigos, desde que o usuário ainda não criou uma palavra-passe de aplicação. Depois de uma palavra-passe de aplicação está em utilização, a palavra-passe permanece necessária. 

**Experiência de utilizador final fora da rede empresarial**

Independentemente se estiver ativada a funcionalidade de IPs fidedignos, verificação de dois passos é necessária para fluxos de navegador. As palavras-passe de aplicação são necessárias para aplicativos de cliente avançado mais antigos. 

### <a name="enable-named-locations-by-using-conditional-access"></a>Ativar localizações com nome utilizando o acesso condicional

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No lado esquerdo, selecione **do Azure Active Directory** > **acesso condicional** > **localizações com nome**.
3. Selecione **nova localização**.
4. Introduza um nome para a localização.
5. Selecione **marcar como localização fidedigna**.
6. Introduza o intervalo de IP na notação CIDR, como **192.168.1.1/24**.
7. Selecione **Criar**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Ativar a funcionalidade de IPs fidedignos com o acesso condicional

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No lado esquerdo, selecione **do Azure Active Directory** > **acesso condicional** > **localizações com nome**.
3. Selecione **IPs fidedignos de MFA configurar**.
4. Sobre o **definições do serviço** página, em **IPs fidedignos**, escolha entre as duas opções seguintes:
   
   * **Para pedidos de utilizadores federados com origem na minha intranet**: Escolha esta opção, selecione a caixa de verificação. Todos os utilizadores federados que iniciar sessão a partir da rede empresarial ignorar a verificação de dois passos, utilizando uma afirmação que é emitida pelo AD FS. Certifique-se de que o AD FS tem uma regra para adicionar a afirmação de intranet para o tráfego apropriado. Se a regra de não existir, crie a seguinte regra no AD FS:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```
     
   * **Para pedidos de um intervalo específico de IPs públicos**: Escolha esta opção, introduza os endereços IP na caixa de texto utilizando a notação CIDR.
   
     * Para endereços IP que estão em xxx.xxx.xxx.1 o intervalo por meio de xxx.xxx.xxx.254, utilize a notação, como **xxx.xxx.xxx.0/24**.
     * Para um único endereço IP, utilize a notação, como **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Introduza até 50 intervalos de endereços IP. Os utilizadores que iniciem sessão a partir destes endereços IP ignorar a verificação de dois passos.

5. Selecione **Guardar**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Ativar a funcionalidade de IPs fidedignos, utilizando as definições de serviço

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No lado esquerdo, selecione **do Azure Active Directory** > **utilizadores e grupos** > **todos os utilizadores**.
3. Selecione **multi-factor Authentication**.
4. Em multi-factor Authentication, selecione **definições do serviço**.
5. Sobre o **definições do serviço** página, em **IPs fidedignos**, escolha entre as duas opções seguintes:
   
   * **Para pedidos de utilizadores federados na minha intranet**: Escolha esta opção, selecione a caixa de verificação. Todos os utilizadores federados que iniciar sessão a partir da rede empresarial ignorar a verificação de dois passos, utilizando uma afirmação que é emitida pelo AD FS. Certifique-se de que o AD FS tem uma regra para adicionar a afirmação de intranet para o tráfego apropriado. Se a regra de não existir, crie a seguinte regra no AD FS:<br/>

     ```
     c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);
     ```

   * **Para pedidos de uma sub-redes de endereço do intervalo IP especificado**: Escolha esta opção, introduza os endereços IP na caixa de texto utilizando a notação CIDR. 
     
     * Para endereços IP que estão em xxx.xxx.xxx.1 o intervalo por meio de xxx.xxx.xxx.254, utilize a notação, como **xxx.xxx.xxx.0/24**.
     * Para um único endereço IP, utilize a notação, como **xxx<span></span>.xxx.xxx.xxx/32**.
     
     Introduza até 50 intervalos de endereços IP. Os utilizadores que iniciem sessão a partir destes endereços IP ignorar a verificação de dois passos.

6. Selecione **Guardar**.

![Ativar os IPs fidedignos com definições de serviço](./media/howto-mfa-mfasettings/trustedips3.png)

## <a name="app-passwords"></a>Palavras-passe de aplicações

Alguns aplicativos, como o Office 2010 ou anterior e Apple Mail, não suportam a verificação de dois passos. As aplicações não estão configuradas para aceitar uma segunda verificação. Para utilizar estas aplicações, aproveitar o _palavras-passe de aplicação_ funcionalidade. Pode utilizar uma palavra-passe de aplicação em vez da palavra-passe tradicional para permitir que uma aplicação para ignorar a verificação de dois passos e continuar a trabalhar.

É suportada a autenticação moderna para os clientes do Microsoft Office 2013 e posterior. Clientes Office 2013, incluindo o Outlook, suporte a protocolos de autenticação moderna e podem ser ativados para funcionarem com verificação de dois passos. Depois do cliente estiver ativado, as palavras-passe de aplicação não são necessárias para o cliente.

### <a name="considerations-about-app-passwords"></a>Considerações sobre as palavras-passe de aplicação

Ao utilizar as palavras-passe de aplicação, considere os seguintes pontos importantes:

* As palavras-passe de aplicação são apenas introduziu uma vez por aplicação. Os utilizadores não têm mantenha um registo de palavras-passe ou insira-os sempre.
* A palavra-passe real é gerada automaticamente e não é fornecida pelo utilizador. A palavra-passe gerada automaticamente é mais difícil um atacante adivinhar e é mais segura.
* Existe um limite de 40 palavras-passe por utilizador. 
* Aplicativos que as palavras-passe em cache e usá-los em cenários no local, podem começar a falhar porque a palavra-passe de aplicação não se sabe fora a conta escolar ou profissional. Um exemplo deste cenário é mensagens de e-mail do Exchange que estão no local, mas o correio é arquivado na nuvem. Neste cenário, a mesma palavra-passe não funciona.
* Depois de multi-factor Authentication estiver ativada numa conta de um utilizador, palavras-passe de aplicação podem servir com clientes de mais não baseadas no browser como o Outlook e Microsoft Skype para empresas. Não não possível efetuar ações administrativas através da utilização de palavras-passe de aplicação através de aplicações não baseadas no browser, como o Windows PowerShell. Não não possível efetuar as ações, mesmo quando o utilizador tem uma conta administrativa. Para executar scripts do PowerShell, crie uma conta de serviço com uma palavra-passe segura e não a ativar a conta para verificação de dois passos.

>[!WARNING]
>As palavras-passe de aplicação não funcionam em ambientes híbridos onde os clientes comunicam com ambos os locais e na cloud pontos finais de descoberta automática. As palavras-passe de domínio são necessários para autenticar no local. As palavras-passe de aplicação são necessários para autenticar com a cloud.
>

### <a name="guidance-for-app-password-names"></a>Documentação de orientação para nomes de palavra-passe de aplicações

Nomes de palavra-passe de aplicações reflitam o dispositivo no qual eles são usados. Se tiver um portátil com aplicações não baseadas no browser, como o Outlook, Word e Excel, criar uma aplicação palavra-passe designada **Laptop** para estas aplicações. Criar outra aplicação palavra-passe designada **Desktop** para os mesmos aplicativos que são executados no computador de secretária. 

>[!NOTE]
>Recomendamos que crie uma palavra-passe por dispositivo, em vez da palavra-passe de uma aplicação por aplicação.

### <a name="federated-or-single-sign-on-app-passwords"></a>Palavras-passe de federados ou único início de sessão na aplicação

O Azure AD suporta o Federação ou início de sessão único (SSO), com locais Windows Server Active Directory Domain Services (AD DS). Se sua organização estiver federada com o Azure AD e estiver a utilizar o Azure multi-factor Authentication, considere os seguintes pontos sobre palavras-passe de aplicação.

>[!NOTE]
>Os pontos seguintes aplicam-se apenas para clientes do Federado (SSO).

* As palavras-passe de aplicação são verificadas pelo Azure AD e por isso, ignorar a Federação. Federação é utilizada ativamente apenas durante a configuração palavras-passe de aplicação.
* Não é possível contactar o fornecedor de identidade (IdP) para utilizadores federados do (SSO), ao contrário do fluxo passivo. As palavras-passe de aplicação são armazenadas na conta escolar ou profissional. Se um utilizador sai da empresa, as informações do utilizador fluem para a conta escolar ou profissional, utilizando **DirSync** em tempo real. A desativação/eliminação da conta pode demorar até três horas para sincronizar, que pode atrasar a desativação/eliminação da palavra-passe de aplicação no Azure AD.
* Definições de controlo de acesso de cliente no local não são honradas pela funcionalidade de palavras-passe de aplicação.
* Sem autenticação no local a capacidade de Registro em log/auditoria está disponível para utilização com a funcionalidade de palavras-passe de aplicação.
* Algumas arquiteturas avançadas requerem uma combinação de credenciais para verificação de dois passos com clientes. Estas credenciais podem incluir um trabalho ou nome de utilizador de conta de instituição de ensino e palavras-passe e palavras-passe de aplicação. Os requisitos dependem como a autenticação é realizada. Para clientes de autenticação em relação a uma infraestrutura no local, uma empresa ou escola conta nome de utilizador e palavra-passe necessária. Para clientes que se autenticar no Azure AD, uma palavra-passe de aplicação é necessária.

  Por exemplo, suponha que tem a arquitetura seguinte:

  * A instância no local do Active Directory está federada com o Azure AD.
  * Está com o Exchange online.
  * Está a utilizar o Skype para empresas no local.
  * Está a utilizar o Azure multi-factor Authentication.

  ![Usando palavras-passe de aplicação numa organização federada](./media/howto-mfa-mfasettings/federated.png)

  Neste cenário, utilize as seguintes credenciais:

  * Para iniciar sessão no Skype para empresas, utilize o seu trabalho ou o nome de utilizador de conta de instituição de ensino e a palavra-passe.
  * Para acessar o catálogo de endereços de um cliente do Outlook que se liga ao Exchange online, utilize uma palavra-passe de aplicação.

### <a name="allow-users-to-create-app-passwords"></a>Permitir que os utilizadores criem palavras-passe de aplicação

Por predefinição, os utilizadores não é possível criar palavras-passe de aplicação. A funcionalidade de palavras-passe de aplicação tem de estar ativada. Para conceder aos utilizadores a capacidade de criar palavras-passe de aplicação, utilize o seguinte procedimento:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No lado esquerdo, selecione **do Azure Active Directory** > **utilizadores e grupos** > **todos os utilizadores**.
3. Selecione **multi-factor Authentication**.
4. Em multi-factor Authentication, selecione **definições do serviço**.
5. Sobre o **definições do serviço** página, selecione a **permitir aos utilizadores criar palavras-passe de aplicação para iniciar sessão em aplicações não baseadas no browser** opção.

   ![Permitir que os utilizadores criem palavras-passe de aplicação](./media/howto-mfa-mfasettings/trustedips3.png)

### <a name="create-app-passwords"></a>Criar palavras-passe de aplicação

Os utilizadores podem criar palavras-passe de aplicação durante o seu registo inicial. O utilizador tem a opção de criar palavras-passe de aplicação no final do processo de registo.

Os utilizadores também podem criar palavras-passe de aplicação após o registo. As palavras-passe de aplicação podem ser alteradas através das definições no portal do Azure ou no portal do Office 365. Para obter mais informações e passos detalhados para os seus utilizadores, consulte [quais são palavras-passe de aplicação no Azure multi-factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

<a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>
## <a name="remember-multi-factor-authentication-for-trusted-devices"></a>Lembre-se de multi-factor Authentication para dispositivos fidedignos
O _Lembre-se a multi-factor Authentication_ recurso para dispositivos e browsers que sejam consideradas fidedignas pelo usuário é uma funcionalidade gratuita para todos os utilizadores de multi-factor Authentication. Os utilizadores podem ignorar as verificações subsequentes para um número especificado de dias, após eles já com êxito com sessão iniciada num dispositivo com o multi-factor Authentication. A funcionalidade aprimora a usabilidade, minimizando o número de vezes que um utilizador tem de executar a verificação de dois passos no mesmo dispositivo.

>[!IMPORTANT]
>Se um dispositivo ou a conta for comprometido, memorizar o multi-factor Authentication para dispositivos fidedignos pode afetar a segurança. Se uma conta empresarial será comprometida ou um dispositivo fidedigno for perdido ou roubado, deve [restaurar o multi-factor Authentication em todos os dispositivos](howto-mfa-userdevicesettings.md#restore-mfa-on-all-remembered-devices-for-a-user).
>
>A ação de restauro revoga o estado confiável de todos os dispositivos e é pedido ao utilizador para executar novamente a verificação de dois passos. Também pode instruir os utilizadores para restaurar o multi-factor Authentication em seus próprios dispositivos com as instruções em [gerir as definições de verificação de dois passos](../user-help/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted).
>

### <a name="how-the-feature-works"></a>Como funciona o recurso

A funcionalidade de multi-factor Authentication lembrar define um cookie persistente no navegador, quando um utilizador seleciona o **não perguntar novamente durante X dias** opção no início de sessão. Não é pedido ao utilizador novamente para o multi-factor Authentication a partir desse mesmo browser até que o cookie expira. Se o usuário abre um browser diferente no mesmo dispositivo ou limpa os cookies, se for pedidos novamente para verificar. 

O **não perguntar novamente durante X dias** opção não é apresentada em aplicações não baseadas no browser, independentemente da aplicação suportar autenticação moderna. Estas aplicações utilizam _tokens de atualização_ que fornecem novos tokens de acesso a cada hora. Quando um token de atualização é validado, o Azure AD verifica que a última verificação de dois passos ocorreu num número especificado de dias. 

A funcionalidade reduz o número de autenticações nas aplicações web, que normalmente solicitar cada vez. A funcionalidade aumenta o número de autenticações para clientes de autenticação moderna que normalmente solicitar todos os 90 dias.

>[!IMPORTANT]
>O **Lembre-se a multi-factor Authentication** funcionalidade não é compatível com o **manter sessão iniciada** funcionalidade do AD FS, quando os utilizadores efetuam a verificação de dois passos para o AD FS através do multi-factor do Azure Servidor de autenticação ou uma solução de terceiros multi-factor authentication.
>
>Se os seus utilizadores selecionarem **manter sessão iniciada** no AD FS e também marcar dispositivo como fidedignas para a multi-factor Authentication, o utilizador não é automaticamente verificado após o **Lembre-se a autenticação multifator**número de dias expirar. O Azure AD solicita uma verificação de dois passos atualizados, mas do AD FS devolve um token com a afirmação de multi-factor Authentication original e data, em vez de executar verificação de dois passos novamente. Esta reação desativa um ciclo de verificação entre o Azure AD e AD FS.
>

### <a name="enable-remember-multi-factor-authentication"></a>Lembre-se de ativar multi-factor Authentication

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No lado esquerdo, selecione **do Azure Active Directory** > **utilizadores e grupos** > **todos os utilizadores**.
3. Selecione **multi-factor Authentication**.
4. Em multi-factor Authentication, selecione **definições do serviço**.
5. Na **definições do serviço** página, em **gerir Lembre-se a autenticação multifator**, selecione o **permitir que os utilizadores memorizem a autenticação multifator em dispositivos que confiam**opção.

   ![Lembre-se de multi-factor Authentication para dispositivos fidedignos](./media/howto-mfa-mfasettings/remember.png)

6. Defina o número de dias para permitir que dispositivos fidedignos ignorar a verificação de dois passos. A predefinição é 14 dias.
7. Selecione **Guardar**.

### <a name="mark-a-device-as-trusted"></a>Marcar um dispositivo fidedigno

Depois de ativar a funcionalidade de multi-factor Authentication de lembrar, os usuários podem marcar um dispositivo como fidedigno quando iniciam sessão, selecionando **não perguntar novamente**.

![Selecione "Não perguntar novamente" para dispositivos fidedignos](./media/howto-mfa-mfasettings/trusted.png)

## <a name="selectable-verification-methods"></a>Métodos de verificação selecionável

Pode escolher os métodos de verificação que estão disponíveis para os seus utilizadores ao utilizar o _métodos de verificação selecionável_ funcionalidade. A tabela seguinte fornece uma breve descrição geral dos métodos.

Quando os utilizadores inscreverem suas contas para o Azure multi-factor Authentication, que escolher método de verificação preferencial as opções que tiver ativado. Orientações para o processo de inscrição de utilizador é fornecida no [configurar a minha conta para verificação de dois passos](../user-help/multi-factor-authentication-end-user-first-time.md).

| Método | Descrição |
|:--- |:--- |
| Ligar para telefone |Coloca uma chamada de voz automatizada. O utilizador a chamada e prime # no teclado do telefone para autenticar. O número de telefone não é sincronizado para o Active Directory no local. |
| Mensagem de texto para telefone |Envia uma mensagem de texto que contém um código de verificação. É pedido ao utilizador para introduzir o código de verificação na interface de início de sessão. Este processo é denominado SMS unidirecional. SMS bidirecional significa que o utilizador tem texto de volta um código em particular. SMS bidirecional é preterida e não suportada após a 14 de Novembro de 2018. Os utilizadores que estão configurados para SMS bidirecionais estão automaticamente no modo _chamada para o telefone_ verificação nesse momento.|
| Notificação através de aplicação móvel |Envia uma notificação push para o seu telefone ou dispositivo registado. O utilizador vê a notificação e seleciona **Verifique se** para concluir a verificação. A aplicação Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| Código de verificação da aplicação móvel |A aplicação Microsoft Authenticator gera um novo código de verificação OATH cada 30 segundos. O utilizador introduz o código de verificação a interface de início de sessão. A aplicação Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072), e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Ativar e desativar os métodos de verificação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No lado esquerdo, selecione **do Azure Active Directory** > **utilizadores e grupos** > **todos os utilizadores**.
3. Selecione **multi-factor Authentication**.
4. Em multi-factor Authentication, selecione **definições do serviço**.
5. Sobre o **definições do serviço** página, em **opções de verificação**, selecionar/anular os métodos para fornecer aos seus utilizadores.

   ![Selecione os métodos de verificação](./media/howto-mfa-mfasettings/authmethods.png)

6. Clique em **Guardar**.
