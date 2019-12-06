---
title: Configurar a autenticação multifator do Azure-Azure Active Directory
description: Este artigo descreve como definir as configurações de autenticação multifator do Azure no portal do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 077032e4fe3886d5bf9a678dffdffca1a5802091
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848413"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Definir as configurações de autenticação multifator do Azure

Este artigo ajuda você a gerenciar as configurações de autenticação multifator no portal do Azure. Ele aborda vários tópicos que ajudam você a obter o máximo da autenticação multifator do Azure. Nem todos os recursos estão disponíveis em todas as versões da autenticação multifator do Azure.

Você pode acessar as configurações relacionadas à autenticação multifator do Azure da portal do Azure navegando até **Azure Active Directory** > **Security** > **MFA**.

![Portal do Azure-configurações de autenticação multifator do AD do Azure](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Definições

Algumas dessas configurações se aplicam ao servidor MFA, ao Azure MFA ou a ambas.

| Funcionalidade | Descrição |
| ------- | ----------- |
| Bloqueio de conta | Bloqueie temporariamente as contas no serviço de autenticação multifator se houver muitas tentativas de autenticação negadas em uma linha. Esse recurso só se aplica a usuários que inserem um PIN para autenticação. (Servidor MFA) |
| [Bloquear/desbloquear usuários](#block-and-unblock-users) | Usado para impedir que usuários específicos possam receber solicitações de autenticação multifator. Todas as tentativas de autenticação de utilizadores bloqueados são automaticamente negadas. Os utilizadores permanecem bloqueados durante 90 dias a partir do momento em que são bloqueados. |
| [Alerta de fraude](#fraud-alert) | Definir configurações relacionadas à capacidade dos usuários de relatar solicitações de verificação fraudulentas |
| [Notificações](#notifications) | Habilitar notificações de eventos do servidor MFA. |
| [Tokens OATH](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Usado em ambientes de MFA do Azure baseados em nuvem para gerenciar tokens OATH para usuários. |
| [Configurações de chamada telefônica](#phone-call-settings) | Defina as configurações relacionadas a chamadas telefônicas e saudações para ambientes locais e na nuvem. |
| Fornecedores | Isso mostrará todos os provedores de autenticação existentes que você possa ter associado à sua conta. Novos provedores de autenticação não podem ser criados a partir de 1º de setembro de 2018 |

## <a name="manage-mfa-server"></a>Gerir Servidor MFA

As configurações nesta seção são somente para o servidor MFA.

| Funcionalidade | Descrição |
| ------- | ----------- |
| Definições do servidor | Baixar o servidor MFA e gerar credenciais de ativação para inicializar seu ambiente |
| [Desvio único](#one-time-bypass) | Permitir que um usuário autentique sem executar a verificação em duas etapas por um período limitado. |
| [Regras de cache](#caching-rules) |  O cache é usado principalmente quando os sistemas locais, como VPN, enviam várias solicitações de verificação enquanto a primeira solicitação ainda está em andamento. Esse recurso permite que as solicitações subsequentes sejam realizadas automaticamente, depois que o usuário obtiver a primeira verificação em andamento. |
| Estado do servidor | Consulte o status de seus servidores MFA locais, incluindo versão, status, IP e data e hora da última comunicação. |

## <a name="activity-report"></a>Relatório de atividade

O relatório disponível aqui é específico para o servidor MFA (local). Para relatórios do Azure MFA (nuvem), consulte o relatório de entradas no Azure AD.

## <a name="block-and-unblock-users"></a>Bloquear e desbloquear usuários

Use o recurso _bloquear e desbloquear usuários_ para impedir que os usuários recebam solicitações de autenticação. Todas as tentativas de autenticação de utilizadores bloqueados são automaticamente negadas. Os utilizadores permanecem bloqueados durante 90 dias a partir do momento em que são bloqueados.

### <a name="block-a-user"></a>Bloquear um usuário

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **Security** > **MFA** > **bloquear/desbloquear usuários**.
3. Selecione **Adicionar** para bloquear um usuário.
4. Selecione o **grupo de replicação**. Insira o nome de usuário para a usuária bloqueada como **username\@Domain.com**. Insira um comentário no campo **motivo** .
5. Selecione **Adicionar** para concluir o bloqueio do usuário.

### <a name="unblock-a-user"></a>Desbloquear um usuário

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **Security** > **MFA** > **bloquear/desbloquear usuários**.
3. Selecione **desbloquear** na coluna **ação** ao lado do usuário a ser desbloqueado.
4. Insira um comentário no campo **motivo do desbloqueio** .
5. Selecione **desbloquear** para concluir o desbloqueio do usuário.

## <a name="fraud-alert"></a>Alerta de fraudes

Configure o recurso de _alerta de fraude_ para que os usuários possam relatar tentativas fraudulentas de acessar seus recursos. Os usuários podem relatar tentativas de fraudes usando o aplicativo móvel ou seu telefone.

### <a name="turn-on-fraud-alerts"></a>Ativar alertas de fraude

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **segurança** > **MFA** > **alerta de fraude**.
3. Defina a configuração **permitir que os usuários enviem alertas de fraude** como **ativado**.
4. Selecione **Guardar**.

### <a name="configuration-options"></a>Opções de configuração

* **Bloquear usuário quando a fraude for relatada**: se um usuário relatar fraude, sua conta ficará bloqueada por 90 dias ou até que um administrador desbloqueie sua conta. Um administrador pode examinar as entradas usando o relatório de entrada e tomar as devidas medidas para evitar futuras fraudes. Um administrador pode então [desbloquear](#unblock-a-user) a conta do usuário.
* **Código para relatar fraude durante a saudação inicial**: quando os usuários recebem uma chamada telefônica para executar a verificação em duas etapas, eles normalmente pressionam **#** para confirmar sua entrada. Para relatar fraude, o usuário insere um código antes de pressionar **#** . Esse código é **0** por padrão, mas você pode personalizá-lo.

   >[!NOTE]
   >As saudações de voz padrão da Microsoft instruem os usuários a pressionar **0 #** para enviar um alerta de fraude. Se você quiser usar um código diferente de **0**, registre e carregue suas próprias saudações de voz personalizadas com as instruções apropriadas para seus usuários.
   >

### <a name="view-fraud-reports"></a>Exibir relatórios de fraude

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Azure Active Directory** > **entradas**. O relatório de fraude agora faz parte do relatório de entradas padrão do Azure AD.

## <a name="notifications"></a>Notificações

Configure endereços de email aqui para os usuários que receberão emails de alerta de fraude.

![Exemplo de email de alerta de fraude de notificação](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Definições de chamada telefónica

### <a name="caller-id"></a>ID do chamador

**Número de ID do chamador MFA** -esse é o número que os usuários verão em seu telefone. Somente números baseados nos EUA são permitidos.

>[!NOTE]
>Quando as chamadas da autenticação multifator são colocadas por meio da rede telefônica pública, às vezes, elas são roteadas por uma operadora que não dá suporte à ID do chamador. Por isso, a ID do chamador não é garantida, embora o sistema de autenticação multifator sempre a envie.

Na Estados Unidos, se você ainda não configurou a ID do chamador MFA, as chamadas de voz da Microsoft vêm dos seguintes números: + 1 (866) 539 4191, + 1 (855) 330 8653 e + 1 (877) 668 6536. Se você estiver usando filtros de spam, certifique-se de excluir esses números.

### <a name="custom-voice-messages"></a>Mensagens de voz personalizadas

Você pode usar suas próprias gravações ou saudações para a verificação em duas etapas com o recurso de _mensagens de voz personalizadas_ . Essas mensagens podem ser usadas além de ou para substituir as gravações da Microsoft.

Antes de começar, lembre-se das seguintes restrições:

* Os formatos de arquivo com suporte são. wav e. mp3.
* O limite de tamanho do arquivo é 1 MB.
* As mensagens de autenticação devem ser menores que 20 segundos. Mensagens com mais de 20 segundos podem causar falha na verificação. O usuário pode não responder antes de a mensagem ser concluída e a verificação expirar.

### <a name="custom-message-language-behavior"></a>Comportamento da linguagem de mensagem personalizada

Quando uma mensagem de voz personalizada é reproduzida para o usuário, o idioma da mensagem depende desses fatores:

* O idioma do usuário atual.
  * O idioma detectado pelo navegador do usuário.
  * Outros cenários de autenticação podem se comportar de maneira diferente.
* O idioma de qualquer mensagem personalizada disponível.
  * Esse idioma é escolhido pelo administrador quando uma mensagem personalizada é adicionada.

Por exemplo, se houver apenas uma mensagem personalizada, com um idioma de alemão:

* Um usuário que se autentica no idioma alemão ouvirá a mensagem personalizada em alemão.
* Um usuário que se autentica em inglês ouvirá a mensagem padrão em inglês.

### <a name="set-up-a-custom-message"></a>Configurar uma mensagem personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
1. Navegue até **Azure Active Directory** > **Security** > **MFA** > **configurações de chamada telefônica**.
1. Selecione **Adicionar saudação**.
1. Escolha o tipo de saudação.
1. Escolha o idioma.
1. Selecione um arquivo de som. mp3 ou. wav para carregar.
1. Selecione **Adicionar**.

### <a name="custom-voice-message-defaults"></a>Padrões de mensagem de voz personalizada

Scripts de exemplo para a criação de mensagens personalizadas.

| Nome da mensagem | Script |
| --- | --- |
| Êxito na autenticação | Sua entrada foi verificada com êxito. Logo. |
| Prompt de extensão | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla tralha para continuar. |
| Confirmação de fraude | Um alerta de fraude foi enviado. Para desbloquear sua conta, entre em contato com o suporte técnico de ti da sua empresa. |
| Saudação de fraude (padrão) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla tralha para concluir a verificação. Se você não iniciou essa verificação, alguém pode estar tentando acessar sua conta. Pressione a tralha zero para enviar um alerta de fraude. Isso notificará a equipe de ti da sua empresa e poderá bloquear outras tentativas de verificação. |
| A fraude relatou que um alerta de fraude foi enviado. | Para desbloquear sua conta, entre em contato com o suporte técnico de ti da sua empresa. |
| Ativação | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla tralha para concluir a verificação. |
| Nova tentativa de autenticação negada | Verificação negada. |
| Repetir (padrão) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla tralha para concluir a verificação. |
| Saudação (padrão) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla tralha para concluir a verificação. |
| Saudação (PIN) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Insira seu PIN seguido pela tecla tralha para concluir a verificação. |
| Saudação de fraude (PIN) | Obrigado por usar o sistema de verificação de entrada da Microsoft.  Insira seu PIN seguido pela tecla tralha para concluir a verificação. Se você não iniciou essa verificação, alguém pode estar tentando acessar sua conta. Pressione a tralha zero para enviar um alerta de fraude. Isso notificará a equipe de ti da sua empresa e poderá bloquear outras tentativas de verificação. |
| Tentar novamente (PIN) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Insira seu PIN seguido pela tecla tralha para concluir a verificação. |
| Prompt de extensão após dígitos | Se já estiver nessa extensão, pressione a tecla tralha para continuar. |
| Autenticação negada | Desculpe, não é possível conectá-lo no momento. Tente novamente mais tarde. |
| Saudação de ativação (padrão) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla tralha para concluir a verificação. |
| Repetição de ativação (padrão) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Pressione a tecla tralha para concluir a verificação. |
| Saudação de ativação (PIN) | Obrigado por usar o sistema de verificação de entrada da Microsoft. Insira seu PIN seguido pela tecla tralha para concluir a verificação. |
| Prompt de extensão antes dos dígitos | Obrigado por usar o sistema de verificação de entrada da Microsoft. Transfira esta chamada para a extensão... |

## <a name="one-time-bypass"></a>Omissão de uso individual

O recurso de _bypass único_ permite que um usuário autentique uma única vez sem executar a verificação em duas etapas. O bypass é temporário e expira após um número especificado de segundos. Em situações em que o aplicativo móvel ou o telefone não está recebendo uma notificação ou chamada telefônica, você pode permitir um bypass único para que o usuário possa acessar o recurso desejado.

### <a name="create-a-one-time-bypass"></a>Criar um bypass único

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **Security** > **MFA** > **bypass único**.
3. Selecione **Adicionar**.
4. Se necessário, selecione o grupo de replicação para o bypass.
5. Insira o nome de usuário como **nome de usuário\@Domain.com**. Insira o número de segundos que o bypass deve durar. Insira o motivo para o bypass.
6. Selecione **Adicionar**. O limite de tempo entra em vigor imediatamente. O usuário precisa entrar antes que o bypass único expire.

### <a name="view-the-one-time-bypass-report"></a>Exibir o relatório de bypass único

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue até **Azure Active Directory** > **Security** > **MFA** > **bypass único**.

## <a name="caching-rules"></a>Regras de colocação em cache

Você pode definir um período de tempo para permitir tentativas de autenticação depois que um usuário é autenticado usando o recurso de _cache_ . As tentativas de autenticação subsequentes para o usuário dentro do período de tempo especificado têm sucesso automaticamente. O cache é usado principalmente quando os sistemas locais, como VPN, enviam várias solicitações de verificação enquanto a primeira solicitação ainda está em andamento. Esse recurso permite que as solicitações subsequentes sejam realizadas automaticamente, depois que o usuário obtiver a primeira verificação em andamento.

>[!NOTE]
>O recurso de cache não se destina a ser usado para entradas no Azure Active Directory (Azure AD).

### <a name="set-up-caching"></a>Configurar o cache

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **Azure Active Directory** > **segurança** > **MFA** > **regras de cache**.
3. Selecione **Adicionar**.
4. Selecione o **tipo de cache** na lista suspensa. Insira o número máximo de **segundos de cache**.
5. Se necessário, selecione um tipo de autenticação e especifique um aplicativo.
6. Selecione **Adicionar**.

## <a name="mfa-service-settings"></a>Configurações do serviço MFA

As configurações para senhas de aplicativo, IPs confiáveis, opções de verificação e lembrar a autenticação multifator para a autenticação multifator do Azure podem ser encontradas nas configurações de serviço. As configurações de serviço podem ser acessadas no portal do Azure navegando até **Azure Active Directory** > **segurança** > **MFA** > **introdução** > **Configurar** > **configurações adicionais de MFA baseadas em nuvem**.

![Configurações do serviço de autenticação multifator do Azure](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

Os intervalos de endereços IP confiáveis podem ser privados ou públicos.

## <a name="app-passwords"></a>Palavras-passe de aplicações

Alguns aplicativos, como o Office 2010 ou anterior e o Apple mail antes do iOS 11, não dão suporte à verificação em duas etapas. Os aplicativos não estão configurados para aceitar uma segunda verificação. Para usar esses aplicativos, aproveite o recurso de _senhas de aplicativo_ . Você pode usar uma senha de aplicativo no lugar da sua senha tradicional para permitir que um aplicativo ignore a verificação em duas etapas e continue trabalhando.

Há suporte para a autenticação moderna para os clientes Microsoft Office 2013 e posteriores. Os clientes do Office 2013, incluindo o Outlook, dão suporte a protocolos de autenticação modernos e podem ser habilitados para funcionar com a verificação em duas etapas. Depois que o cliente é habilitado, as senhas de aplicativo não são necessárias para o cliente.

>[!NOTE]
>As senhas de aplicativo não funcionam com o acesso condicional com base nas políticas de autenticação multifator e na autenticação moderna.

### <a name="considerations-about-app-passwords"></a>Considerações sobre senhas de aplicativo

Ao usar senhas de aplicativo, considere os seguintes pontos importantes:

* As senhas de aplicativo são inseridas apenas uma vez por aplicativo. Os usuários não precisam manter o controle das senhas ou digitá-las todas as vezes.
* A palavra-passe real é gerada automaticamente, não sendo fornecida pelo utilizador. A senha gerada automaticamente é mais difícil para um invasor adivinhar e é mais segura.
* Há um limite de 40 senhas por usuário.
* Os aplicativos que armazenam senhas em cache e as usam em cenários locais podem começar a falhar porque a senha do aplicativo não é conhecida fora da conta corporativa ou de estudante. Um exemplo desse cenário são os emails do Exchange que estão no local, mas o email arquivado está na nuvem. Nesse cenário, a mesma senha não funciona.
* Após a habilitação da autenticação multifator na conta de um usuário, as senhas de aplicativo podem ser usadas com a maioria dos clientes sem navegador, como o Outlook e o Microsoft Skype for Business. As ações administrativas não podem ser executadas usando senhas de aplicativo por meio de aplicativos sem navegador, como o Windows PowerShell. As ações não podem ser executadas mesmo quando o usuário tem uma conta administrativa. Para executar scripts do PowerShell, crie uma conta de serviço com uma senha forte e não habilite a conta para a verificação em duas etapas.

>[!WARNING]
>As senhas de aplicativo não funcionam em ambientes híbridos nos quais os clientes se comunicam com pontos de extremidade de descoberta automática local e na nuvem. As senhas de domínio são necessárias para autenticar no local. As senhas de aplicativo são necessárias para autenticar com a nuvem.

### <a name="guidance-for-app-password-names"></a>Diretrizes para nomes de senha de aplicativo

Os nomes de senha de aplicativo devem refletir o dispositivo no qual eles são usados. Se você tiver um laptop que tenha aplicativos sem navegador, como o Outlook, Word e Excel, crie uma senha de aplicativo chamada **laptop** para esses aplicativos. Crie outra senha de aplicativo chamada **Desktop** para os mesmos aplicativos que são executados no computador desktop.

>[!NOTE]
>Recomendamos que você crie uma senha de aplicativo por dispositivo, em vez de uma senha de aplicativo por aplicativo.

### <a name="federated-or-single-sign-on-app-passwords"></a>Senhas de aplicativo de logon único ou federado

O Azure AD dá suporte à Federação, ou SSO (logon único), com o Windows Server Active Directory Domain Services (AD DS) local. Se sua organização for federada com o Azure AD e você estiver usando a autenticação multifator do Azure, considere os seguintes pontos sobre as senhas de aplicativo.

>[!NOTE]
>Os pontos a seguir se aplicam somente a clientes federados (SSO).

* As senhas de aplicativo são verificadas pelo Azure AD e, portanto, ignoram a Federação. A Federação é usada ativamente somente ao configurar senhas de aplicativo.
* O IdP (provedor de identidade) não é contatado para usuários federados (SSO), ao contrário do fluxo passivo. As senhas de aplicativo são armazenadas na conta corporativa ou de estudante. Se um usuário sair da empresa, as informações do usuário fluirão para a conta corporativa ou de estudante usando o **DirSync** em tempo real. A desabilitação/exclusão da conta pode levar até três horas para sincronizar, o que pode atrasar a desabilitação/exclusão da senha do aplicativo no Azure AD.
* As configurações de controle de acesso do cliente local não são respeitadas pelo recurso senhas de aplicativo.
* Nenhum recurso de registro/auditoria de autenticação local está disponível para uso com o recurso de senhas de aplicativo.
* Algumas arquiteturas avançadas exigem uma combinação de credenciais para a verificação em duas etapas com clientes. Essas credenciais podem incluir um nome de usuário e senhas de conta corporativa ou de estudante e senhas de aplicativo. Os requisitos dependem de como a autenticação é executada. Para clientes que se autenticam em uma infraestrutura local, é necessário um nome de usuário e senha de conta corporativa ou de estudante. Para clientes que se autenticam no Azure AD, é necessária uma senha de aplicativo.

  Por exemplo, suponha que você tenha a seguinte arquitetura:

  * Sua instância local do Active Directory é federada com o Azure AD.
  * Você está usando o Exchange Online.
  * Você está usando o Skype for Business local.
  * Você está usando a autenticação multifator do Azure.

  Nesse cenário, você usa as seguintes credenciais:

  * Para entrar no Skype for Business, use seu nome de usuário e senha da conta corporativa ou de estudante.
  * Para acessar o catálogo de endereços de um cliente do Outlook que se conecta ao Exchange Online, use uma senha de aplicativo.

### <a name="allow-users-to-create-app-passwords"></a>Permitir que os usuários criem senhas de aplicativo

Por padrão, os usuários não podem criar senhas de aplicativo. O recurso senhas de aplicativo deve ser habilitado. Para conceder aos usuários a capacidade de criar senhas de aplicativo, use o seguinte procedimento:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Utilizadores**.
3. Selecione **Multi-Factor Authentication**.
4. Em autenticação multifator, selecione **configurações de serviço**.
5. Na página **configurações de serviço** , selecione a opção **permitir que os usuários criem senhas de aplicativo para entrar em aplicativos sem navegador** .

### <a name="create-app-passwords"></a>Criar senhas de aplicativo

Os usuários podem criar senhas de aplicativo durante seu registro inicial. O usuário tem a opção de criar senhas de aplicativo no final do processo de registro.

Os usuários também podem criar senhas de aplicativo após o registro. Para obter mais informações e etapas detalhadas para seus usuários, consulte [o que são senhas de aplicativo na autenticação multifator do Azure?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>IPs Fidedignos

O recurso _IPs confiáveis_ da autenticação multifator do Azure é usado por administradores de um locatário gerenciado ou federado. O recurso ignora a verificação em duas etapas para os usuários que entram na intranet da empresa. O recurso está disponível com a versão completa da autenticação multifator do Azure e não com a versão gratuita para administradores. Para obter detalhes sobre como obter a versão completa da autenticação multifator do Azure, consulte [autenticação multifator do Azure](multi-factor-authentication.md).

> [!NOTE]
> Os IPs confiáveis de MFA e o acesso condicional com locais nomeados só funcionam com endereços IPV4.

Se sua organização implantar a extensão NPS para fornecer MFA a aplicativos locais, o endereço IP de origem sempre parecerá ser o servidor NPS pelo qual a tentativa de autenticação flui.

| Tipo de locatário do Azure AD | Opções de recursos de IPs confiáveis |
|:--- |:--- |
| Gerido |**Intervalo específico de endereços IP**: os administradores especificam um intervalo de endereços IP que podem ignorar a verificação em duas etapas para os usuários que entram na intranet da empresa. Podem ser configurados no máximo 50 intervalos de IP confiáveis.|
| Federados |**Todos os usuários federados**: todos os usuários federados que entram de dentro da organização podem ignorar a verificação em duas etapas. Os usuários ignoram a verificação usando uma declaração emitida por Serviços de Federação do Active Directory (AD FS) (AD FS).<br/>**Intervalo específico de endereços IP**: os administradores especificam um intervalo de endereços IP que podem ignorar a verificação em duas etapas para os usuários que entram na intranet da empresa. |

O bypass de IPs confiáveis funciona somente de dentro da intranet da empresa. Se você selecionar a opção **todos os usuários federados** e um usuário entrar de fora da intranet da empresa, o usuário precisará se autenticar usando a verificação em duas etapas. O processo é o mesmo, mesmo que o usuário apresente uma declaração de AD FS. 

### <a name="end-user-experience-inside-of-corpnet"></a>Experiência do usuário final dentro do corpnet

Quando o recurso IPs confiáveis está desabilitado, a verificação em duas etapas é necessária para fluxos de navegador. As senhas de aplicativo são necessárias para aplicativos cliente avançados mais antigos.

Quando o recurso IPs confiáveis está habilitado, a verificação em duas etapas *não* é necessária para fluxos de navegador. As senhas de aplicativo *não* são necessárias para aplicativos cliente avançados mais antigos, desde que o usuário não tenha criado uma senha de aplicativo. Depois que uma senha de aplicativo estiver em uso, a senha permanecerá necessária. 

### <a name="end-user-experience-outside-corpnet"></a>Experiência do usuário final fora do corpnet

Independentemente de o recurso de IPs confiáveis estar habilitado, a verificação em duas etapas é necessária para fluxos de navegador. As senhas de aplicativo são necessárias para aplicativos cliente avançados mais antigos.

### <a name="enable-named-locations-by-using-conditional-access"></a>Habilitar locais nomeados usando o acesso condicional

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **segurança** > **acesso condicional** > **locais nomeados**.
3. Selecione **novo local**.
4. Insira um nome para o local.
5. Selecione **Marcar como local confiável**.
6. Insira o intervalo de IP na notação CIDR, como **192.168.1.1/24**.
7. Selecione **Criar**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Habilitar o recurso IPs confiáveis usando o acesso condicional

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **segurança** >  **acesso condicional** > **locais nomeados**.
3. Selecione **Configurar IPs confiáveis de MFA**.
4. Na página **configurações de serviço** , em **IPs confiáveis**, escolha uma das duas opções a seguir:

   * **Para solicitações de usuários federados provenientes da minha intranet**: para escolher essa opção, marque a caixa de seleção. Todos os usuários federados que entram na rede corporativa ignoram a verificação em duas etapas usando uma declaração emitida pelo AD FS. Verifique se AD FS tem uma regra para adicionar a declaração de intranet ao tráfego apropriado. Se a regra não existir, crie a seguinte regra no AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Para solicitações de um intervalo específico de IPS públicos**: para escolher essa opção, insira os endereços IP na caixa de texto usando a notação CIDR.
      * Para endereços IP que estão no intervalo xxx. xxx. xxx. 1 a xxx. xxx. xxx. 254, use a notação como **xxx. xxx. xxx. 0/24**.
      * Para um único endereço IP, use notação como **xxx.xxx.xxx.xxx/32**.
      * Insira até 50 intervalos de endereços IP. Os usuários que se conectam a partir desses endereços IP ignoram a verificação em duas etapas.

5. Selecione **Guardar**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Habilitar o recurso IPs confiáveis usando as configurações de serviço

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Utilizadores**.
3. Selecione **Multi-Factor Authentication**.
4. Em autenticação multifator, selecione **configurações de serviço**.
5. Na página **configurações de serviço** , em **IPs confiáveis**, escolha uma (ou ambas) das duas opções a seguir:

   * **Para solicitações de usuários federados na minha intranet**: para escolher essa opção, marque a caixa de seleção. Todos os usuários federados que entram na rede corporativa ignoram a verificação em duas etapas usando uma declaração emitida pelo AD FS. Verifique se AD FS tem uma regra para adicionar a declaração de intranet ao tráfego apropriado. Se a regra não existir, crie a seguinte regra no AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Para solicitações de um intervalo especificado de sub-redes de endereço IP**: para escolher essa opção, insira os endereços IP na caixa de texto usando a notação CIDR.
      * Para endereços IP que estão no intervalo xxx. xxx. xxx. 1 a xxx. xxx. xxx. 254, use a notação como **xxx. xxx. xxx. 0/24**.
      * Para um único endereço IP, use notação como **xxx.xxx.xxx.xxx/32**.
      * Insira até 50 intervalos de endereços IP. Os usuários que se conectam a partir desses endereços IP ignoram a verificação em duas etapas.

6. Selecione **Guardar**.

## <a name="verification-methods"></a>Métodos de verificação

Você pode escolher os métodos de verificação que estão disponíveis para seus usuários. A tabela a seguir fornece uma breve visão geral dos métodos.

Quando os usuários registram suas contas para a autenticação multifator do Azure, eles escolhem o método de verificação preferencial nas opções que você habilitou. As diretrizes para o processo de registro de usuário são fornecidas em [configurar minha conta para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-first-time.md).

| Método | Descrição |
|:--- |:--- |
| Ligar para telefone |Coloca uma chamada de voz automática. O utilizador atende a chamada e prime # no teclado do telefone para se autenticar. O número de telefone não está sincronizado com o Active Directory local. |
| Mensagem de texto para um telefone |Envia uma mensagem de texto que contém um código de verificação. O usuário é solicitado a inserir o código de verificação na interface de entrada. Esse processo é chamado de SMS unidirecional. O SMS bidirecional significa que o usuário deve fazer o texto de um código específico. O SMS bidirecional foi preterido e não tem suporte após 14 de novembro de 2018. Os administradores devem habilitar outro método para os usuários que usaram o SMS bidirecional anteriormente.|
| Notificação através da aplicação para dispositivos móveis |Envia uma notificação por push para seu telefone ou dispositivo registrado. O usuário exibe a notificação e seleciona **verificar** para concluir a verificação. O aplicativo Microsoft Authenticator está disponível para [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)e [Ios](https://go.microsoft.com/fwlink/?Linkid=825073). |
| Código de verificação da aplicação para dispositivos móveis ou token de hardware |O aplicativo Microsoft Authenticator gera um novo código de verificação OATH a cada 30 segundos. O usuário insere o código de verificação na interface de entrada. O aplicativo Microsoft Authenticator está disponível para [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)e [Ios](https://go.microsoft.com/fwlink/?Linkid=825073). |

### <a name="enable-and-disable-verification-methods"></a>Habilitar e desabilitar métodos de verificação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Utilizadores**.
3. Selecione **Multi-Factor Authentication**.
4. Em autenticação multifator, selecione **configurações de serviço**.
5. Na página **configurações de serviço** , em **Opções de verificação**, selecione/desmarque os métodos a serem fornecidos aos usuários.
6. Clique em **Guardar**.

Detalhes adicionais sobre o uso de métodos de autenticação podem ser encontrados no artigo [o que são métodos de autenticação](concept-authentication-methods.md).

## <a name="remember-multi-factor-authentication"></a>Lembrar a autenticação multifator

O recurso _lembrar a autenticação multifator_ para dispositivos e navegadores que são confiáveis para o usuário é um recurso gratuito para todos os usuários da autenticação multifator. Os utilizadores podem ignorar as verificações subsequentes por um número especificado de dias, após iniciarem sessão com sucesso num dispositivo através do Multi-Factor Authentication. O recurso melhora a usabilidade ao minimizar o número de vezes que um usuário tem de executar a verificação em duas etapas no mesmo dispositivo.

>[!IMPORTANT]
>Se uma conta ou dispositivo for comprometido, lembrar a autenticação multifator para dispositivos confiáveis pode afetar a segurança. Se uma conta corporativa for comprometida ou um dispositivo confiável for perdido ou roubado, você deverá [revogar as sessões de MFA](howto-mfa-userdevicesettings.md).
>
>A ação restaurar revoga o status confiável de todos os dispositivos e o usuário precisa executar a verificação em duas etapas novamente. Você também pode instruir seus usuários a restaurar a autenticação multifator em seus próprios dispositivos com as instruções em [gerenciar suas configurações para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

### <a name="how-the-feature-works"></a>Como o recurso funciona

O recurso lembrar autenticação multifator define um cookie persistente no navegador quando um usuário seleciona a opção **não perguntar novamente por X dias** ao entrar. O usuário não é solicitado novamente para a autenticação multifator do mesmo navegador até que o cookie expire. Se o usuário abrir um navegador diferente no mesmo dispositivo ou limpar seus cookies, eles serão solicitados novamente a verificar.

A opção **não perguntar novamente por X dias** não é mostrada em aplicativos sem navegador, independentemente de o aplicativo oferecer suporte à autenticação moderna. Esses aplicativos usam _tokens de atualização_ que fornecem novos tokens de acesso a cada hora. Quando um token de atualização é validado, o Azure AD verifica se a última verificação em duas etapas ocorreu dentro do número de dias especificado.

O recurso reduz o número de autenticações em aplicativos Web, que normalmente são notificados toda vez. O recurso aumenta o número de autenticações para clientes de autenticação modernos que normalmente solicitam a cada 90 dias. Também pode aumentar o número de autenticações quando combinadas com políticas de acesso condicional.

>[!IMPORTANT]
>O recurso **lembrar a autenticação multifator** não é compatível com o recurso **manter-me conectado** do AD FS, quando os usuários executam a verificação em duas etapas para AD FS por meio do Azure servidor de autenticação multifator ou uma solução de autenticação multifator de terceiros.
>
>Se os usuários selecionarem **Mantenha-me conectado** em AD FS e também marcarem seu dispositivo como confiável para a autenticação multifator, o usuário não será automaticamente verificado após a memorização do número de dias de expiração da **autenticação multifator** . O Azure AD solicita uma nova verificação em duas etapas, mas AD FS retorna um token com a declaração e a data originais da autenticação multifator, em vez de executar a verificação em duas etapas novamente. **Essa reação define um loop de verificação entre o Azure AD e o AD FS.**
>
>O recurso **lembrar a autenticação multifator** não é compatível com usuários B2B e não será visível para usuários B2B ao entrar nos locatários convidados.
>

### <a name="enable-remember-multi-factor-authentication"></a>Habilitar lembrar a autenticação multifator

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Active Directory** > **Utilizadores**.
3. Selecione **Multi-Factor Authentication**.
4. Em autenticação multifator, selecione **configurações de serviço**.
5. Na página **configurações de serviço** , **gerencie lembrar autenticação multifator**, selecione **permitir que os usuários se lembrem da autenticação multifator nos dispositivos em que confiam** opção.
6. Defina o número de dias para permitir que os dispositivos confiáveis ignorem a verificação em duas etapas. O padrão é 14 dias.
7. Selecione **Guardar**.

### <a name="mark-a-device-as-trusted"></a>Marcar um dispositivo como confiável

Depois de habilitar o recurso lembrar a autenticação multifator, os usuários podem marcar um dispositivo como confiável ao entrarem, selecionando **não perguntar novamente**.

## <a name="next-steps"></a>Passos seguintes

[Modificar identidade visual da página de entrada do Azure AD](../fundamentals/customize-branding.md)
