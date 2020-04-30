---
title: Configure Autenticação multi-factor Azure - Diretório Ativo Azure
description: Este artigo descreve como configurar as definições de autenticação de multi-factors azure no portal Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9002447df44a1f0c87ec9de03e316d7d12d8a626
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82105663"
---
# <a name="configure-azure-multi-factor-authentication-settings"></a>Configurar Definições do Multi-Factor Authentication do Azure

Este artigo ajuda-o a gerir as definições de autenticação multi-factor no portal Azure. Aborda vários tópicos que o ajudam a tirar o máximo partido da Autenticação Multi-Factor Azure. Nem todas as funcionalidades estão disponíveis em todas as versões da Autenticação Multi-Factor Do Azure.

Pode aceder a definições relacionadas com a Autenticação De Vários Fatores Azure do portal Azure, navegando para **o Azure Ative Directory** > **Security** > **MFA**.

![Portal Azure - Definições de autenticação multi-factor Azure AD](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="settings"></a>Definições

Algumas destas definições aplicam-se ao MFA Server, Ao Azure MFA, ou a ambos.

| Funcionalidade | Descrição |
| ------- | ----------- |
| Bloqueio de conta | Bloqueie temporariamente as contas no serviço de autenticação de vários fatores se houver demasiadas tentativas de autenticação negadas consecutivamente. Esta funcionalidade aplica-se apenas aos utilizadores que introduzem um PIN para autenticar. (Servidor MFA) |
| [Bloquear/desbloquear utilizadores](#block-and-unblock-users) | Usado para impedir utilizadores específicos de poderem receber pedidos de autenticação multi-factor. Todas as tentativas de autenticação de utilizadores bloqueados são automaticamente negadas. Os utilizadores permanecem bloqueados durante 90 dias a partir do momento em que são bloqueados. |
| [Alerta de fraudes](#fraud-alert) | Configure configurar configurações relacionadas com a capacidade dos utilizadores de reportar pedidos fraudulentos de verificação |
| [Notificações](#notifications) | Ativar notificações de eventos do MFA Server. |
| [Fichas do JURAMENTO](concept-authentication-methods.md#oath-hardware-tokens-public-preview) | Usado em ambientes Azure MFA baseados em nuvem para gerir tokens DE JURAMENTO para utilizadores. |
| [Definições de chamadas telefónicas](#phone-call-settings) | Configure as definições relacionadas com chamadas telefónicas e saudações para ambientes de nuvem e no local. |
| Fornecedores | Isto mostrará quaisquer fornecedores de autenticação existentes que possa ter associado à sua conta. Novos fornecedores de autenticação não podem ser criados a partir de 1 de setembro de 2018 |

## <a name="manage-mfa-server"></a>Gerir o Servidor MFA

As definições nesta secção são apenas para o Servidor MFA.

| Funcionalidade | Descrição |
| ------- | ----------- |
| Definições do servidor | Descarregue o Servidor MFA e gere credenciais de ativação para inicializar o seu ambiente |
| [Bypass único](#one-time-bypass) | Permitir que um utilizador autentique sem efetuar uma verificação em duas etapas por um período limitado. |
| [Regras de caching](#caching-rules) |  O caching é usado principalmente quando os sistemas no local, como VPN, enviam múltiplos pedidos de verificação enquanto o primeiro pedido ainda está em andamento. Esta funcionalidade permite que os pedidos subsequentes tenham sucesso automaticamente, depois de o utilizador ter sucesso na primeira verificação em curso. |
| Estado do servidor | Consulte o estado dos seus servidores MFA no local, incluindo versão, estado, IP e última hora e data de comunicação. |

## <a name="activity-report"></a>Relatório de atividades

O relatório disponível aqui é específico do MFA Server (no local). Para os relatórios do Azure MFA (cloud) consulte o relatório de inscrições em Azure AD.

## <a name="block-and-unblock-users"></a>Bloquear e desbloquear utilizadores

Utilize a funcionalidade de _bloqueio e desbloqueie os utilizadores_ para evitar que os utilizadores recebam pedidos de autenticação. Todas as tentativas de autenticação de utilizadores bloqueados são automaticamente negadas. Os utilizadores permanecem bloqueados durante 90 dias a partir do momento em que são bloqueados.

### <a name="block-a-user"></a>Bloquear um utilizador

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **azure Ative Directy** > **Security** > **MFA** > **Block/desbloqueie os utilizadores**.
3. Selecione **Adicionar** para bloquear um utilizador.
4. Selecione o **Grupo de Replicação**. Introduza o nome de utilizador para o utilizador bloqueado como **nome\@** de utilizador domain.com . Insira um comentário no campo **Razão.**
5. Selecione **Adicionar** para terminar de bloquear o utilizador.

### <a name="unblock-a-user"></a>Desbloquear um utilizador

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **azure Ative Directy** > **Security** > **MFA** > **Block/desbloqueie os utilizadores**.
3. Selecione **Desbloquear** na coluna **Ação** ao lado do utilizador para desbloquear.
4. Insira um comentário no **campo Motivo para desbloquear.**
5. Selecione **Desbloquear** para terminar de desbloquear o utilizador.

## <a name="fraud-alert"></a>Alerta de fraudes

Configure a funcionalidade de alerta de _fraude_ para que os seus utilizadores possam reportar tentativas fraudulentas de acesso aos seus recursos. Os utilizadores podem denunciar tentativas de fraude utilizando a aplicação móvel ou através do seu telemóvel.

### <a name="turn-on-fraud-alerts"></a>Ligue os alertas de fraude

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até ao**alerta**de fraude de**segurança** > do **Diretório** > Ativo Azure**MFA** > .
3. Defina o **permitir que os utilizadores enviem alertas** de fraude para **On**.
4. Selecione **Guardar**.

### <a name="configuration-options"></a>Opções de configuração

* **Bloquear o utilizador quando a fraude é reportada**: Se um utilizador reportar fraude, a sua conta fica bloqueada durante 90 dias ou até que um administrador desbloqueie a sua conta. Um administrador pode rever os sign-ins utilizando o relatório de inscrição e tomar medidas adequadas para evitar futuras fraudes. Um administrador pode então [desbloquear](#unblock-a-user) a conta do utilizador.
* **Código para denunciar fraude durante**a saudação inicial : Quando os utilizadores **#** recebem uma chamada telefónica para efetuar a verificação em duas etapas, normalmente pressionam para confirmar o seu registo. Para denunciar fraude, o utilizador introduz **#** um código antes de premir . Este código é **0** por padrão, mas pode personalizá-lo.

   >[!NOTE]
   >As saudações de voz padrão da Microsoft instruem os utilizadores a pressionar **0#** para enviar um alerta de fraude. Se pretender utilizar um código diferente de **0**, grave e carregue as suas próprias saudações de voz personalizadas com instruções apropriadas para os seus utilizadores.
   >

### <a name="view-fraud-reports"></a>Ver relatórios de fraude

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione Detalhes de**autenticação****de insugações** > de **diretórios** > ativos azure . O relatório de fraude faz agora parte do relatório padrão de sign-ins da AD Azure e vai aparecer no "Detalhe de **Resultados",** como o MFA negou, Código de Fraude introduzido.
 
## <a name="notifications"></a>Notificações

Configure aqui endereços de e-mail para utilizadores que receberão e-mails de alerta de fraude em**Notificações** > de**Autenticação Multi-Factor**de > **Segurança** >  **do Diretório Ativo do Azure**.

![Amostra de e-mail de alerta de fraude de notificação](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

## <a name="phone-call-settings"></a>Definições de chamadas telefónicas

### <a name="caller-id"></a>ID do chamador

Número de identificação do **chamador MFA** - Este é o número que os seus utilizadores verão no seu telefone. Só são permitidos números baseados nos EUA.

>[!NOTE]
>Quando as chamadas de autenticação multi-factor são colocadas através da rede telefónica pública, por vezes são encaminhadas através de uma transportadora que não suporta o ID do chamador. Por isso, o ID do chamador não está garantido, mesmo que o sistema de autenticação multi-factor o envie sempre.

Nos Estados Unidos, se ainda não configuraram o ID do Chamador MFA, as chamadas de voz da Microsoft provêm dos seguintes números: +1 (866) 539 4191, +1 (855) 330 8653 e +1 (877) 668 6536. Se utilizar filtros de spam, certifique-se de excluir estes números.

### <a name="custom-voice-messages"></a>Mensagens de voz personalizadas

Pode utilizar as suas próprias gravações ou saudações para verificação em duas etapas com a funcionalidade de _mensagens_ de voz personalizadas. Estas mensagens podem ser utilizadas para além ou para substituir as gravações da Microsoft.

Antes de começar, esteja ciente das seguintes restrições:

* Os formatos de ficheiro suportados são .wav e .mp3.
* O limite de tamanho do ficheiro é de 1 MB.
* As mensagens de autenticação devem ser inferiores a 20 segundos. As mensagens com mais de 20 segundos podem fazer com que a verificação falhe. O utilizador pode não responder antes de a mensagem terminar e os tempos de verificação fora.

### <a name="custom-message-language-behavior"></a>Comportamento de linguagem de mensagem personalizada

Quando uma mensagem de voz personalizada é reproduzida para o utilizador, o idioma da mensagem depende destes fatores:

* A linguagem do utilizador atual.
  * O idioma detetado pelo navegador do utilizador.
  * Outros cenários de autenticação podem comportar-se de forma diferente.
* A linguagem de quaisquer mensagens personalizadas disponíveis.
  * Esta língua é escolhida pelo administrador, quando uma mensagem personalizada é adicionada.

Por exemplo, se houver apenas uma mensagem personalizada, com uma língua de alemão:

* Um utilizador que autentica no idioma alemão ouvirá a mensagem alemã personalizada.
* Um utilizador que autentica em inglês ouvirá a mensagem padrão em inglês.

### <a name="set-up-a-custom-message"></a>Configurar uma mensagem personalizada

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
1. Navegue nas**definições**de chamada telefónica do **Azure Ative Directory** > **Security** > **MFA** > .
1. Selecione **Adicionar saudação**.
1. Escolha o tipo de saudação.
1. Escolha a língua.
1. Selecione um ficheiro de som .mp3 ou .wav para carregar.
1. Selecione **Adicionar**.

### <a name="custom-voice-message-defaults"></a>Padrão de mensagem de voz personalizada

Experimente scripts para criar mensagens personalizadas.

| Nome da mensagem | Script |
| --- | --- |
| Autenticação bem sucedida | A sua inscrição foi verificada com sucesso. Até logo. |
| Pedido de extensão | Obrigado por usar o sistema de verificação de sessão da Microsoft. Por favor, pressione a chave da libra para continuar. |
| Confirmação de Fraude | Foi apresentado um alerta de fraude. Para desbloquear a sua conta, contacte o balcão de ajuda informática da sua empresa. |
| Saudação de fraude (Standard) | Obrigado por usar o sistema de verificação de sessão da Microsoft. Por favor, pressione a chave da libra para terminar a sua verificação. Se não iniciou esta verificação, alguém pode estar a tentar aceder à sua conta. Por favor, pressione zero libra para enviar um alerta de fraude. Isto notificará a equipa de TI da sua empresa e bloqueará novas tentativas de verificação. |
| Fraude informou que um alerta de fraude foi apresentado. | Para desbloquear a sua conta, contacte o balcão de ajuda informática da sua empresa. |
| Ativação | Obrigado por utilizar o sistema de verificação de sessão da Microsoft. Por favor, pressione a chave da libra para terminar a sua verificação. |
| Autenticação negada novatentativa | Verificação negada. |
| Retry (Standard) | Obrigado por utilizar o sistema de verificação de sessão da Microsoft. Por favor, pressione a chave da libra para terminar a sua verificação. |
| Saudação (Standard) | Obrigado por utilizar o sistema de verificação de sessão da Microsoft. Por favor, pressione a chave da libra para terminar a sua verificação. |
| Saudação (PIN) | Obrigado por usar o sistema de verificação de sessão da Microsoft. Introduza o PIN seguido da chave da libra para terminar a sua verificação. |
| Saudação de fraude (PIN) | Obrigado por usar o sistema de verificação de sessão da Microsoft.  Introduza o PIN seguido da chave da libra para terminar a sua verificação. Se não iniciou esta verificação, alguém pode estar a tentar aceder à sua conta. Por favor, pressione zero libra para enviar um alerta de fraude. Isto notificará a equipa de TI da sua empresa e bloqueará novas tentativas de verificação. |
| Retry (PIN) | Obrigado por usar o sistema de verificação de sessão da Microsoft. Introduza o PIN seguido da chave da libra para terminar a sua verificação. |
| Pedido de extensão após dígitos | Se já estiver nesta extensão, pressione a tecla libra para continuar. |
| Autenticação negada | Desculpe, não podemos inscrevê-lo neste momento. Tente novamente mais tarde. |
| Saudação de ativação (Standard) | Obrigado por utilizar o sistema de verificação de sessão da Microsoft. Por favor, pressione a chave da libra para terminar a sua verificação. |
| Retry de ativação (Standard) | Obrigado por utilizar o sistema de verificação de sessão da Microsoft. Por favor, pressione a chave da libra para terminar a sua verificação. |
| Saudação de ativação (PIN) | Obrigado por usar o sistema de verificação de sessão da Microsoft. Introduza o PIN seguido da chave da libra para terminar a sua verificação. |
| Pedido de extensão antes dos dígitos | Obrigado por usar o sistema de verificação de sessão da Microsoft. Por favor, transfira esta chamada para a extensão... |

## <a name="one-time-bypass"></a>Bypass único

A funcionalidade _de bypass única_ permite que um utilizador autentique uma única vez sem efetuar uma verificação em duas etapas. O bypass é temporário e expira após um número especificado de segundos. Em situações em que a aplicação móvel ou o telemóvel não estão a receber uma notificação ou chamada telefónica, pode permitir um bypass único para que o utilizador possa aceder ao recurso pretendido.

### <a name="create-a-one-time-bypass"></a>Criar um bypass único

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue até **azure ative diretório** > **Segurança** > **MFA** > Bypass**one time**.
3. Selecione **Adicionar**.
4. Se necessário, selecione o grupo de replicação para o bypass.
5. Introduza o nome de utilizador como **nome de\@utilizador domain.com**. Introduza o número de segundos que o bypass deve durar. Insira a razão do bypass.
6. Selecione **Adicionar**. O prazo entra em vigor imediatamente. O utilizador tem de iniciar sessão antes que o bypass único expire.

### <a name="view-the-one-time-bypass-report"></a>Veja o relatório de bypass único

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue até **azure ative diretório** > **Segurança** > **MFA** > Bypass**one time**.

## <a name="caching-rules"></a>Regras de caching

Pode definir um período de tempo para permitir tentativas de autenticação após a autenticação de um utilizador utilizando a função _de cache._ As tentativas de autenticação subsequentes para o utilizador dentro do período de tempo especificado são automaticamente bem sucedidas. O caching é usado principalmente quando os sistemas no local, como VPN, enviam múltiplos pedidos de verificação enquanto o primeiro pedido ainda está em andamento. Esta funcionalidade permite que os pedidos subsequentes tenham sucesso automaticamente, depois de o utilizador ter sucesso na primeira verificação em curso.

>[!NOTE]
>A função de cache não se destina a ser utilizada para inscrições para o Azure Ative Directory (Azure AD).

### <a name="set-up-caching"></a>Configurar o cache

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Navegue pelas regras de**Segurança** > Ativa do **Diretório Azure** > **MFA** > **Caching**.
3. Selecione **Adicionar**.
4. Selecione o **tipo de cache** da lista de lançamentos. Introduza o número máximo de **segundos**de cache .
5. Se necessário, selecione um tipo de autenticação e especifique uma aplicação.
6. Selecione **Adicionar**.

## <a name="mfa-service-settings"></a>Definições de serviço MFA

As definições para senhas de aplicação, IPs fidedignos, opções de verificação e lembre-se da autenticação de vários fatores para a autenticação de multifactor estoque do Azure podem ser encontradas nas definições do serviço. As definições de serviço podem ser acedidas a partir do portal Azure navegando para **O Azure Ative Directory** > **Security** > **MFA** > **Getting started** > **Configure** > **Adicional based MFA settings**.

![Definições do serviço de autenticação de vários fatores Azure](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-service-settings.png)

As gamas de endereços IP confiáveis podem ser privadas ou públicas.

## <a name="app-passwords"></a>Palavras-passe da aplicação

Algumas aplicações, como o Office 2010 ou anterior e o Apple Mail antes do iOS 11, não suportam a verificação em duas etapas. As aplicações não estão configuradas para aceitar uma segunda verificação. Para utilizar estas aplicações, aproveite a funcionalidade de senhas da _aplicação._ Pode utilizar uma palavra-passe de aplicação no lugar da sua senha tradicional para permitir que uma aplicação ignore a verificação em duas etapas e continue a trabalhar.

A autenticação moderna é suportada para os clientes do Microsoft Office 2013 e posteriormente. Os clientes do Office 2013, incluindo o Outlook, suportam protocolos de autenticação modernos e podem ser habilitados a trabalhar com verificação em duas etapas. Depois de o cliente estar ativado, as palavras-passe da aplicação não são necessárias para o cliente.

>[!NOTE]
>As palavras-passe das aplicações não funcionam com políticas de autenticação multifactor baseadas no Acesso Condicional e na autenticação moderna.

### <a name="considerations-about-app-passwords"></a>Considerações sobre senhas de aplicativo

Ao utilizar as palavras-passe da aplicação, considere os seguintes pontos importantes:

* As palavras-passe da aplicação só são introduzidas uma vez por aplicação. Os utilizadores não têm de acompanhar sempre as palavras-passe ou inseri-las.
* A senha real é gerada automaticamente e não é fornecida pelo utilizador. A palavra-passe gerada automaticamente é mais difícil para um intruso adivinhar e é mais segura.
* Existe um limite de 40 palavras-passe por utilizador.
* As aplicações que cache passwords e usá-las em cenários no local podem começar a falhar porque a palavra-passe da aplicação não é conhecida fora do trabalho ou da conta escolar. Um exemplo deste cenário é a Troca de e-mails que estão no local, mas o correio arquivado está na nuvem. Neste cenário, a mesma senha não funciona.
* Depois de a Autenticação Multi-Factor ser ativada na conta de um utilizador, as palavras-passe das aplicações podem ser usadas com a maioria dos clientes não navegadores, como o Outlook e o Microsoft Skype para o Negócios. As ações administrativas não podem ser realizadas utilizando senhas de aplicação através de aplicações não-navegadoras, como o Windows PowerShell. As ações não podem ser realizadas mesmo quando o utilizador tem uma conta administrativa. Para executar scripts PowerShell, crie uma conta de serviço com uma senha forte e não ative a conta para verificação em duas etapas.

>[!WARNING]
>As palavras-passe das aplicações não funcionam em ambientes híbridos onde os clientes comunicam tanto com as instalações como com os pontos finais de descoberta automática em nuvem. As palavras-passe de domínio são necessárias para autenticar no local. As palavras-passe da aplicação são necessárias para autenticar com a nuvem.

### <a name="guidance-for-app-password-names"></a>Orientação para nomes de palavras-passe de aplicativos

Os nomes das palavras-passe da aplicação devem refletir o dispositivo no qual são utilizados. Se tiver um portátil que tenha aplicações não-navegadoras como Outlook, Word e Excel, crie uma palavra-passe de aplicação chamada **Laptop** para estas aplicações. Crie outra palavra-passe de aplicação chamada **Desktop** para as mesmas aplicações que funcionam no seu computador de secretária.

>[!NOTE]
>Recomendamos que crie uma palavra-passe de aplicação por dispositivo, em vez de uma palavra-passe de aplicação por aplicação.

### <a name="federated-or-single-sign-on-app-passwords"></a>Senhas de aplicação de inscrição federadas ou únicas

A Azure AD suporta a federação, ou única inscrição (SSO), com serviços de domínio ativo do Windows Server (AD DS). Se a sua organização estiver federada com a AD Azure e estiver a utilizar a Autenticação Multi-Factor Azure, considere os seguintes pontos sobre as palavras-passe das aplicações.

>[!NOTE]
>Os seguintes pontos aplicam-se apenas aos clientes federados (SSO).

* As palavras-passe das aplicações são verificadas pela Azure AD e, portanto, pela federação de bypass. A Federação só é utilizada ativamente na configuração de senhas de aplicação.
* O Fornecedor de Identidade (IDP) não é contactado para utilizadores federados (SSO), ao contrário do fluxo passivo. As palavras-passe da aplicação são armazenadas na conta de trabalho ou escolar. Se um utilizador deixar a empresa, a informação do utilizador flui para o trabalho ou para a conta escolar utilizando o **DirSync** em tempo real. A desativação/eliminação da conta pode demorar até três horas a sincronizar, o que pode atrasar a desativação/eliminação da palavra-passe da aplicação em Azure AD.
* As definições de Controlo de Acesso do cliente no local não são honradas pela funcionalidade de senhas da aplicação.
* Não existe nenhuma capacidade de autenticação/auditoria de autenticação no local para utilização com a funcionalidade de senhas da aplicação.
* Algumas arquiteturas avançadas requerem uma combinação de credenciais para verificação em duas etapas com os clientes. Estas credenciais podem incluir um nome de utilizador de conta de trabalho ou de conta escolar e senhas de aplicação. Os requisitos dependem da forma como a autenticação é realizada. Para clientes que autenticam contra uma infraestrutura no local, um nome de utilizador de conta de trabalho ou de escola e uma senha de senha necessária. Para clientes que autenticam contra o Azure AD, é necessária uma senha de aplicação.

  Por exemplo, suponha que tenha a seguinte arquitetura:

  * A sua instância no local de Diretório Ativo é federada com a Azure AD.
  * Estás a usar o Exchange online.
  * Estás a usar o Skype para negócios no local.
  * Está a usar a autenticação Azure Multi-Factor.

  Neste cenário, utiliza as seguintes credenciais:

  * Para iniciar sessão no Skype for Business, use o seu nome de utilizador e senha de conta de trabalho ou de conta escolar.
  * Para aceder ao livro de endereços de um cliente do Outlook que se conecta ao Exchange online, utilize uma palavra-passe da aplicação.

### <a name="allow-users-to-create-app-passwords"></a>Permitir que os utilizadores criem senhas de aplicação

Por padrão, os utilizadores não podem criar senhas de aplicação. A função de palavras-passe da aplicação deve ser ativada. Para dar aos utilizadores a capacidade de criar palavras-passe de aplicações, utilize o seguinte procedimento:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Utilizadores de Diretório** > Ativo Azure **.**
3. **Selecione a autenticação de vários fatores.**
4. Em termos de autenticação de vários fatores, selecione **as definições**de serviço .
5. Na página definições de **serviço,** selecione os **utilizadores para criar palavras-passe de aplicações para iniciar sessão na opção de aplicações não-navegadoras.**

### <a name="create-app-passwords"></a>Criar senhas de aplicativo

Os utilizadores podem criar senhas de aplicação durante o seu registo inicial. O utilizador tem a opção de criar senhas de aplicação no final do processo de registo.

Os utilizadores também podem criar senhas de aplicação após o registo. Para mais informações e passos detalhados para os seus utilizadores, consulte [as palavras-passe das aplicações na Autenticação Multi-Factor Do Azure?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="trusted-ips"></a>IPs Fidedignos

A funcionalidade _de IPs fidedigno_ da Autenticação Multi-Factor Azure é utilizada por administradores de um inquilino gerido ou federado. A funcionalidade ignora a verificação em duas etapas para os utilizadores que iniciam a inscrição na intranet da empresa. A funcionalidade está disponível com a versão completa da Autenticação Multi-Factor Azure, e não com a versão gratuita para administradores. Para mais detalhes sobre como obter a versão completa da Autenticação Multi-Factor Azure, consulte [a autenticação de multi-factors Azure](multi-factor-authentication.md).

> [!NOTE]
> Os IPs fidedignos do MFA e o Acesso Condicional apenas funcionam com endereços IPV4.

Se a sua organização implementar a extensão NPS para fornecer MFA às aplicações no local, note que o endereço IP de origem será sempre o servidor NPS que a tentativa de autenticação flui.

| Tipo de inquilino azure AD | Opções de funcionalidades de iPs fidedignos |
|:--- |:--- |
| Geridos |**Gama específica de endereços IP**: Os administradores especificam uma gama de endereços IP que podem contornar a verificação em duas etapas para os utilizadores que iniciam a inscrição na intranet da empresa. Pode configurar um máximo de 50 gamas IP fidedignas.|
| Federados |**Todos os Utilizadores Federados**: Todos os utilizadores federados que se inscrevam no interior da organização podem contornar a verificação em duas etapas. Os utilizadores contornam a verificação utilizando uma alegação emitida pelos Serviços da Federação de Diretórios Ativos (AD FS).<br/>**Gama específica de endereços IP**: Os administradores especificam uma gama de endereços IP que podem contornar a verificação em duas etapas para os utilizadores que iniciam a inscrição na intranet da empresa. |

O bypass de IPs fidedigno funciona apenas a partir do interior da intranet da empresa. Se selecionar a opção **Todos os Utilizadores Federados** e um utilizador entrar de fora da intranet da empresa, o utilizador tem de autenticar utilizando uma verificação em duas etapas. O processo é o mesmo mesmo mesmo que o utilizador apresente uma reclamação AD FS. 

### <a name="end-user-experience-inside-of-corpnet"></a>Experiência de utilizador final dentro da corpnet

Quando a funcionalidade IPs fidedigna é desativada, é necessária uma verificação em duas etapas para os fluxos de navegador. As palavras-passe das aplicações são necessárias para aplicações de clientes ricos mais antigos.

Quando a funcionalidade IPs fidedigna está ativada, a verificação em duas etapas *não* é necessária para os fluxos de navegador. As palavras-passe das aplicações *não* são necessárias para aplicações de clientes mais antigas e ricas, desde que o utilizador não tenha criado uma palavra-passe da aplicação. Depois de uma palavra-passe da aplicação estar a ser utilizada, a palavra-passe continua a ser necessária. 

### <a name="end-user-experience-outside-corpnet"></a>Experiência de utilizador final fora do corpnet

Independentemente de a funcionalidade IPs fidedigna estar ativada, é necessária uma verificação em duas etapas para os fluxos de navegador. As palavras-passe das aplicações são necessárias para aplicações de clientes ricos mais antigos.

### <a name="enable-named-locations-by-using-conditional-access"></a>Ativar locais nomeados utilizando acesso condicional

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Ative Diretório Ative** > **Directy Security** > Conditional**Access** > Local **.**
3. Selecione **Nova localização**.
4. Insira um nome para a localização.
5. Selecione **Mark como localização fidedigna**.
6. Introduza a gama IP na notação CIDR como **192.168.1.1/24**.
7. Selecione **Criar**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Ativar a funcionalidade IPs fidedigno utilizando o Acesso Condicional

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Azure Ative Diretório Ative** > **Directy Security** >  Conditional**Access** > Local **.**
3. **Selecione Configure MFA fidedigno de IPs**.
4. Na página Definições de **Serviço,** em **IPs fidedignos,** escolha entre qualquer uma das duas opções seguintes:

   * **Para pedidos de utilizadores federados originários da minha intranet**: Para escolher esta opção, selecione a caixa de verificação. Todos os utilizadores federados que se inscrevam na rede corporativa contornam a verificação em duas etapas utilizando uma reclamação emitida pela AD FS. Certifique-se de que a AD FS tem uma regra para adicionar a alegação intranet ao tráfego apropriado. Se a regra não existir, crie a seguinte regra em AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Para pedidos de uma gama específica de IPs públicos**: Para escolher esta opção, introduza os endereços IP na caixa de texto utilizando notação CIDR.
      * Para endereços IP que estejam na gama xxx.xxx.xxx.1 até xxx.xxx.xxx.254, use notação como **xxx.xxx.xxx.0.0/24**.
      * Para um único endereço IP, utilize notação como **xxx.xxx.xxx.xxx/32**.
      * Insira até 50 intervalos de endereçoIP. Os utilizadores que iniciarem sessão a partir destes endereços IP contornam a verificação em duas etapas.

5. Selecione **Guardar**.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Ativar a função IPs fidedigna utilizando definições de serviço

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Utilizadores de Diretório** > Ativo Azure **.**
3. **Selecione a autenticação de vários fatores.**
4. Em termos de autenticação de vários fatores, selecione **as definições**de serviço .
5. Na página definições de **serviço,** em **IPs fidedignos,** escolha uma (ou ambas) das seguintes duas opções:

   * **Para pedidos de utilizadores federados na minha intranet**: Para escolher esta opção, selecione a caixa de verificação. Todos os utilizadores federados que se inscrevam na rede corporativa contornam a verificação em duas etapas utilizando uma reclamação emitida pela AD FS. Certifique-se de que a AD FS tem uma regra para adicionar a alegação intranet ao tráfego apropriado. Se a regra não existir, crie a seguinte regra em AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Para pedidos de uma gama especificada de sub-redes de endereçoip**: Para escolher esta opção, insira os endereços IP na caixa de texto utilizando notação CIDR.
      * Para endereços IP que estejam na gama xxx.xxx.xxx.1 até xxx.xxx.xxx.254, use notação como **xxx.xxx.xxx.0.0/24**.
      * Para um único endereço IP, utilize notação como **xxx.xxx.xxx.xxx/32**.
      * Insira até 50 intervalos de endereçoIP. Os utilizadores que iniciarem sessão a partir destes endereços IP contornam a verificação em duas etapas.

6. Selecione **Guardar**.

## <a name="verification-methods"></a>Métodos de verificação

Pode escolher os métodos de verificação disponíveis para os seus utilizadores. A tabela que se segue fornece uma breve visão geral dos métodos.

Quando os seus utilizadores matriculam as suas contas para a Autenticação Multi-Factor Azure, escolhem o seu método de verificação preferido a partir das opções que ativou. A orientação para o processo de inscrição do utilizador é fornecida em Configurar a [minha conta para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-first-time.md).

| Método | Descrição |
|:--- |:--- |
| Chamada para telefone |Coloca uma chamada de voz automatizada. O utilizador atende a chamada e prime # no teclado do telefone para se autenticar. O número de telefone não é sincronizado para o Diretório Ativo no local. |
| Mensagem de texto para telefone |Envia uma mensagem de texto que contém um código de verificação. O utilizador é solicitado a introduzir o código de verificação na interface de entrada. Este processo chama-se SMS de ida. SMS bidirecional significa que o utilizador deve enviar um código específico por sms. O SMS bidirecional é depreciado e não suportado depois de 14 de novembro de 2018. Os administradores devem ativar outro método para os utilizadores que utilizaram sMS bidirecionais.|
| Notificação através de aplicação móvel |Envia uma notificação push para o seu telefone ou dispositivo registado. O utilizador vê a notificação e seleciona **Verificar** para completar a verificação. A aplicação Microsoft Authenticator está disponível para [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)e [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |
| Código de verificação de aplicativo móvel ou ficha de hardware |A aplicação Microsoft Authenticator gera um novo código de verificação DOPR a cada 30 segundos. O utilizador introduz o código de verificação na interface de entrada. A aplicação Microsoft Authenticator está disponível para [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072)e [iOS.](https://go.microsoft.com/fwlink/?Linkid=825073) |

### <a name="enable-and-disable-verification-methods"></a>Ativar e desativar métodos de verificação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Utilizadores de Diretório** > Ativo Azure **.**
3. **Selecione a autenticação de vários fatores.**
4. Em termos de autenticação de vários fatores, selecione **as definições**de serviço .
5. Na página Definições de **Serviço,** sob opções de **verificação,** selecione/desselecione os métodos a fornecer aos seus utilizadores.
6. Clique em **Guardar**.

Detalhes adicionais sobre a utilização de métodos de autenticação podem ser encontrados no artigo Quais são os métodos de [autenticação.](concept-authentication-methods.md)

## <a name="remember-multi-factor-authentication"></a>Lembre-se da autenticação de vários fatores

A funcionalidade _de autenticação multi-factor para_ dispositivos e navegadores que são confiáveis pelo utilizador é uma funcionalidade gratuita para todos os utilizadores de Autenticação Multi-Factor. Os utilizadores podem ignorar as verificações subsequentes por um número especificado de dias, após iniciarem sessão com sucesso num dispositivo através do Multi-Factor Authentication. A funcionalidade aumenta a usabilidade minimizando o número de vezes que um utilizador tem de realizar uma verificação em duas etapas no mesmo dispositivo.

>[!IMPORTANT]
>Se uma conta ou dispositivo estiver comprometido, lembrar-se da autenticação multi-factor para dispositivos fidedignos pode afetar a segurança. Se uma conta corporativa ficar comprometida ou se um dispositivo de confiança for perdido ou roubado, deverá [revogar as Sessões de MFA](howto-mfa-userdevicesettings.md).
>
>A ação de restauro revoga o estado de confiança de todos os dispositivos, e o utilizador é obrigado a realizar novamente a verificação em duas etapas. Também pode instruir os seus utilizadores a restaurar a Autenticação Multi-Factor nos seus próprios dispositivos com as instruções em Gerir as [suas definições para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device).

### <a name="how-the-feature-works"></a>Como funciona a funcionalidade

A funcionalidade de autenticação multi-factor de relembrar define um cookie persistente no navegador quando um utilizador seleciona a **opção Não pedir novamente por X dias** no início do sessão. O utilizador não é solicitado novamente para a Autenticação Multi-Factor a partir desse mesmo navegador até que o cookie expire. Se o utilizador abrir um navegador diferente no mesmo dispositivo ou limpar os seus cookies, é solicitado novamente a verificar.

A opção **Não pedir novamente por X days** não é mostrada em aplicações não-navegador, independentemente de a aplicação suportar a autenticação moderna. Estas aplicações usam _fichas de atualização_ que fornecem novos tokens de acesso a cada hora. Quando uma ficha de atualização é validada, a AD Azure verifica que a última verificação em duas etapas ocorreu dentro do número de dias especificado.

A funcionalidade reduz o número de autenticações em aplicações web, o que normalmente solicita sempre. A funcionalidade aumenta o número de autenticações para clientes de autenticação moderna que normalmente solicitam a cada 90 dias. Pode também aumentar o número de autenticações quando combinado com as políticas de Acesso Condicional.

>[!IMPORTANT]
>A função **de autenticação multi-factor lembre-se** não é compatível com o **manter-me assinado na** funcionalidade de AD FS, quando os utilizadores realizam uma verificação em duas etapas para AD FS através do Azure Multi-Factor Authentication Server ou de uma solução de autenticação multifactor de terceiros.
>
>Se os seus utilizadores selecionarem **manter-me inscrito no** AD FS e também marcarem o seu dispositivo como confiável para a Autenticação Multi-Factor, o utilizador não é verificado automaticamente após o número de dias de autenticação de **vários fatores de lembre-se** expirar. A Azure AD solicita uma nova verificação em duas etapas, mas a AD FS devolve um sinal com a reivindicação e data originais de autenticação multi-factor, em vez de realizar novamente a verificação em duas etapas. **Esta reação desencadeia um ciclo de verificação entre a AD Azure e a AD FS.**
>
>A função **de autenticação multi-factor lembre-se** não é compatível com utilizadores B2B e não será visível para os utilizadores B2B ao assinar nos inquilinos convidados.
>

### <a name="enable-remember-multi-factor-authentication"></a>Ativar lembre-se da autenticação de vários fatores

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. À esquerda, selecione **Utilizadores de Diretório** > Ativo Azure **.**
3. **Selecione a autenticação de vários fatores.**
4. Em termos de autenticação de vários fatores, selecione **as definições**de serviço .
5. Na página Definições de **Serviço,** **gerencie a autenticação de vários fatores,** selecione os utilizadores de permitir que se lembrem da **autenticação de vários fatores nos dispositivos em que confiam** na opção.
6. Detete o número de dias para permitir que dispositivos fidedignos contornem a verificação em duas etapas. O padrão é de 14 dias.
7. Selecione **Guardar**.

### <a name="mark-a-device-as-trusted"></a>Marque um dispositivo como confiável

Depois de ativar a função de autenticação multi-factor de relembrar, os utilizadores podem marcar um dispositivo como confiável quando iniciarem o seu sessão selecionando **Não volte a perguntar**.

## <a name="next-steps"></a>Passos seguintes

[Modificar o branding de página de entrada de anúncios da Azure AD](../fundamentals/customize-branding.md)
